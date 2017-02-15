# sed
 

sed: Stream EDitor,流编辑器(行编辑器);

文件是存储在磁盘上的文件流，一次读取一个文件快；

sed： 按换行符截取，一次读取一行(从内存文件缓冲区中),读取到模式空间(sed自己的内部存储空间)中；

sed的模式空间；


sed命令：

    sed [options] "script" FILE ...

选项：
    -n : 静默模式；默认会输出模式空间中修改前的内容；-n不输出模式空间中的内容；
    -r ：使用扩展的正则表达式；
    -f /path/to/sed_script_file： 指定sed脚本文件；
    ```bash 
        sed -f sed.script （/^$/d）、（s@^#@@g）  #括号中为sed.script的内容；
        [root@control command-test]# sed '/^$/d' /etc/fstab | sed 's@^#@@g'
    ```
    -e 'script' -e 'script' : 指定多个编辑指令；


编辑命令：
    d ：删除；
    p ：打印；
    i \：insert,在匹配到的行前面插入；
    a \：append，在匹配到的行后面追加；
    r /path/to/somefile : 在指定位置把另外一个文件的内容插入到匹配到行后；
    w /path/to/somefile : 将符合条件的所有行保存至指定的文件中；
    = : 显示符合条件的行的行号；

地址定界：
    startline,endline
        1,3
    /pat1/,/pat2/ 从第一次匹配到pat1的行开始，到第一次被pat2匹配的行；
    /pattern/    被模式匹配到的所有行，


```bash 
$ sed '1,3d' /etc/fstab 
```

用法： sed [options] "addr1[,addr2]编辑命令" FILE 

```bash 
$ sed '/^#/d' /etc/fstab 
$ sed '/$\//d' /etc/fstab 
[root@control ~]# sed '/^#/d' /etc/httpd/conf/httpd.conf | sed '/^[[:space:]]*#/d'
[root@control ~]# sed '/^#/,/^\//d' /etc/fstab # 

[root@control ~]# sed '/^#/,/^\//p' /etc/fstab # 能匹配到的打印两次，不能匹配到的仅打印一次；
```

```bash 
# 在匹配到的行后，追加一行；
[root@control ~]# sed '/^\//a\Hello,Line\n' /etc/fstab 

#
# /etc/fstab
# Created by anaconda on Fri Jan 15 20:41:50 2016
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
Hello,Line
UUID=6712e51b-913b-442f-9331-6ed2d4333d5e /boot                   xfs     defaults        0 0
/dev/mapper/centos-home /home                   xfs     defaults        0 0
Hello,Line
/dev/mapper/centos-swap swap                    swap    defaults        0 0
Hello,Line

# 在匹配到的行前插入一行；
[root@control ~]# sed '/^\//i\Hello,Line' /etc/fstab 

#
# /etc/fstab
# Created by anaconda on Fri Jan 15 20:41:50 2016
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
Hello,Line
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=6712e51b-913b-442f-9331-6ed2d4333d5e /boot                   xfs     defaults        0 0
Hello,Line
/dev/mapper/centos-home /home                   xfs     defaults        0 0
Hello,Line
/dev/mapper/centos-swap swap                    swap    defaults        0 0
```

```bash 
# 追加多行：
[root@control ~]# sed '/^[[:upper:]]/a \first line.\nsecond line.' /etc/fstab 

#
# /etc/fstab
# Created by anaconda on Fri Jan 15 20:41:50 2016
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=6712e51b-913b-442f-9331-6ed2d4333d5e /boot                   xfs     defaults        0 0
first line.
second line.
/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
You have mail in /var/spool/mail/root
```

```bash 
# r操作，后跟一个文件路径，表示将文件的内容插入到匹配到的行后；
[root@control ~]# sed '/^UUID/r /etc/issue' /etc/fstab 

#
# /etc/fstab
# Created by anaconda on Fri Jan 15 20:41:50 2016
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=6712e51b-913b-442f-9331-6ed2d4333d5e /boot                   xfs     defaults        0 0
\S
Kernel \r on an \m

/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
```

```bash 
# w /path/to/file 将符合条件的所有行保存至指定的文件中，覆盖式添加；
[root@control ~]# sed -n '/^\//w /tmp/filetable.txt' /etc/fstab
[root@control ~]# cat /tmp/filetable.txt 
/dev/mapper/centos-root /                       xfs     defaults        0 0
/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
```

```bash 
#  =操作 : 显示符合条件的行的行号；
[root@control ~]# sed -n '/^\//=' /etc/fstab
9
11
12
```

编辑命令：

    s///: 查找替换；查找条件可以使用模式，但要替换的内容不行；

正则表达式：
    基本的：
        字符：
            .
            []
            [^]
        次数：
            *
            \?
            .*
            \{m,n\}
            \+
        锚定：
            ^
            $
            \<,\b 
            \>,\b 
        分组：
            \(\)
        引用：
            \1,\2,...
    扩展：
        字符： 
            .
            []
            [^]
                [[:lower:]],[[:punct:]],[[alnum:]]
        次数： 
            *
            ?
            +
            {m,n}

        锚定： 
            ^
            $
            \>,\b
            \<,\b
        或：
            a|b
        分组： 
            ()
        引用： 
            \1,\2,...
        
sed "地址定界s@查找条件@替换文本@"

地址定界若省略则表示全文件；

    修饰符：
        g: global,全局替换；
        i：ignore-case，不区分字符大小写；

```
He like his liker.
He like his lover.
She love her liker.
She love her lover.
```
1、删除以上内容当中包含"l..e"前后一致的行；
2、将文件中"l..e"前后一致的行中，最后一个l..e词首的l换成大写的L；

```bash 
[root@control command-test]# sed '/\(l..e\).*\1r/d' sed-test.txt 
He like his lover.
She love her liker.

[root@control command-test]# sed -n '/\(l..e\).*\1r/p' sed-test.txt 
He like his liker.
She love her lover.
```

```bash 
[root@control command-test]# sed 's@\(\(l\(..e\)\).*\)\2@\1L\3@' sed-test.txt 
He like his Liker.
He like his lover.
She love her liker.
She love her Lover.
```



练习：
```bash 
1、替换/etc/inittab文件中"id:3:initdefault:"一行中的数字为5；

[root@control command-test]# sed 's@\(id:\)[0-9]\(:initdefult\)@\15\2@' /etc/inittab 

2、删除/etc/init.d/functions文件中的空白行；

[root@control command-test]# sed '/^$/d' /etc/init.d/functions 

3、删除/etc/initab文件中位于行首的#；

[root@control command-test]# sed 's@^#@@g' /etc/inittab
.
4、删除/etc/rc.d/rc.sysinit文件中#后跟至少一个空白字符开头的行的行首的#和空白字符；

[root@control command-test]# sed 's@^#[[:space:]]\{1,\}@@g' /etc/rc.d/init.d/functions 

5、删除/boot/grub/grub.conf文件中行首的空白字符；

[root@control command-test]# sed 's@^[[:space:]]\{1,\}@@g' /boot/grub2/grub.cfg 

6、取出一个文件路径的目录名称，如/etc/sysconfig/network,其目录为/etc/sysconfig;功能类似dirname命令；
# 取出目录路径：
[root@control command-test]# echo /etc/sysconfig/network | sed 's@[^/]\{1,\}/\?@@'
    /sysconfig/network
[root@control command-test]# echo /etc/sysconfig/network | sed 's@[^/]\{1,\}/\?$@@'
    /etc/sysconfig/
[root@control command-test]# echo /etc/sysconfig/network-scripts/ifcfg-ens32 | sed 's@[^/]\{1,\}/\?$@@'
    /etc/sysconfig/network-scripts/
```

sed的高级用法：
    t,T,D,P,n,N,h,H,g,G 
