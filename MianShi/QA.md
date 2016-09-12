- 1、Linux如何挂载windows下的共享目录；
~~~shell
	mount.cifs //192.168.1.3/server /mnt/server -o user=administrator,pass=123456
~~~
linux下的server需要自己手动创建一个，后面的user与pass是由windows主机的账号和密码，注意空格和逗号；

- 2、 查看http的并发请求数与其TCP连接状态：
~~~shell
	netstat -n | awk '/^tcp/ {++b[$NF]} END {for(a in b) print a, b[a]}'
~~~
还有ulimit -n 查看linux系统打开最大的文件描述符，这里默认1024，不修改这里web服务器修改再大也没用。若要修改有很多办法，这里说其中一个：
~~~shell
	# vim /etc/security/limit.conf
	* soft nofile 10240
	* hard nofile 10240	
~~~
重启后生效；


- 3、用tcpdump嗅探80端口的访问看看谁最高：
~~~shell
tcpdump -i eth0 -tnn dst port 80 -c 1000 | awk -F"." '{print $1"."$2"."$3"."$4}' | sort | uniq -c | sort -nr |head -5&nbsp;
~~~

- 4、查看/var/log目录下文件数：
~~~shell
	ls /var/log -lR | grep "^-" | wc -l
~~~

- 5、查看当前系统每个IP的连接数
~~~shell
	netstat -n | awk '/^tcp/ {print $5}'| awk -F: '{print $1}' | sort | uniq -c | sort -rn
~~~

- 6、shell下32随机密码生成
~~~shell
	cat /dev/urandom | head -1 | md5sum | head -c 32 >> /pass
~~~
将生成的32位随机数保存到/pass文件里了；

- 7、统计出apche的access.log中访问量最多的5个IP
~~~shell
	cat access_log | awk '{print $1}' | sort | uniq -c | sort -n -r | head -5
~~~

- 8、如何查看二进制文件的内容：
我们一般通过hexdump命令来查看二进制文件的内容。
hexdump -C xxx(文件名) -C是参数，不同的参数有不同的意义；
~~~shell
[kalaguiyin@sslinux Apace]$ hexdump -h

Usage:
 hexdump [options] <file>...

Display file contents in hexadecimal, decimal, octal, or ascii.

Options:
 -b, --one-byte-octal      one-byte octal display		#单字节八进制显示；
 -c, --one-byte-char       one-byte character display	#单字节字符显示；
 -C, --canonical           canonical hex+ASCII display	#是比较规范的十六进制和ASCII码显示；
 -d, --two-bytes-decimal   two-byte decimal display	#双字节十进制显示；
 -o, --two-bytes-octal     two-byte octal display		#双字节八进制显示；
 -x, --two-bytes-hex       two-byte hexadecimal display	#双字节十六进制显示；
 -L, --color[=<mode>]      interpret color formatting specifiers
                             colors are enabled by default
 -e, --format <format>     format string to be used for displaying data
 -f, --format-file <file>  file that contains format strings
 -n, --length <length>     interpret only length bytes of input
 -s, --skip <offset>       skip offset bytes from the beginning
 -v, --no-squeezing        output identical lines

 -h, --help     display this help and exit
 -V, --version  output version information and exit

For more details see hexdump(1).
~~~

- 9、ps aux 中的VSZ代表什么意思，RSS代表什么意思
VSZ：虚拟内存集，进程占用的虚拟内存空间；
RSS：物理内存集，进程占用实际物理内存空间；

- 10、检测并修复/dev/hda5
fsck 用来检查和维护不一致的文件系统。若系统掉电或磁盘发生问题，可利用fsck命令对文件系统进行检查；
用法：
~~~shell
	[kalaguiyin@sslinux Apace]$ fsck --help
	fsck from util-linux 2.28
	fsck.ext4: invalid option -- 'h'
	Usage: fsck.ext4 [-panyrcdfvtDFV] [-b superblock] [-B blocksize]
			[-I inode_buffer_blocks] [-P process_inode_size]
			[-l|-L bad_blocks_file] [-C fd] [-j external_journal]
			[-E extended-options] device

	Emergency help:
	 -p                   Automatic repair (no questions)
	 -n                   Make no changes to the filesystem
	 -y                   Assume "yes" to all questions
	 -c                   Check for bad blocks and add them to the badblock list
	 -f                   Force checking even if filesystem is marked clean
	 -v                   Be verbose
	 -b superblock        Use alternative superblock
	 -B blocksize         Force blocksize when looking for superblock
	 -j external_journal  Set location of the external journal
	 -l bad_blocks_file   Add to badblocks list
	 -L bad_blocks_file   Set badblocks list
~~~

- 11、Linux系统的开机启动顺序：
加载BIOS --> 读取MBR --> Boot Loader --> 加载内核 --> 用户层init --> 根据inittab文件来设定系统运行的等级(一般是3或者5，3是多用户命令行，5是图形化界面) --> init进程执行 rc.syninit --> 启动内核模块 --> 执行不同级别运行的脚本程序 --> 执行/etc/rc.d/rc.local（本地运行服务) --> 执行/bin/login,就可以登陆了。

- 12、符号链接和硬链接的区别：
我们可以把符号链接，也就是软链接，当作是windows系统里的快捷方式；
硬链接就好像是又复制了一份。
ln 3.txt 4.txt 这就是硬链接，相当于复制，不可以跨分区，但修改3，4会跟着变，若删除3，4不受任何影响；
ln -s 3.txt 4.txt 这是软链接，相当于快捷方式。修改4，3也会跟着变，若删除3，4就坏掉了。不可用了。

- 13、保存当前磁盘分区的分区表：
dd命令是一个强大的命令，在复制的同时进行转换；
~~~shell
	dd if=/dev/sda of=./mbr.txt bs=1 count=512
~~~

- 14、如何在vim中进行复制、粘贴、删除行，删除全部，按行查找和按字母查找；
命令行模式下：
光标移动到想要复制的行，按yy，移动到目标位置，按P就能粘贴了；
如果要复制多行，使用#77，#表示数字；
复制指定行， ：1，15y
删除行，移动到目标行，按dd
删除多行#dd，#表示数字；
删除全部dG，这里注意G一定要大写，表示文件末尾；
删除指定范围内的行，  :1,20d
跳转到指定行      ：90    
按字符查找： /path   这样就是找到path这个单词所在的位置，存在多个的话可以使用n/N切换到下一个或上一个；
也可以使用?path,只是查找的方向不一样而已；


- 15、手动安装grub
~~~shell
	grub-install /dev/sda
~~~

- 16、修改内核参数
~~~shell
	vim /etc/sysctl.conf	#这里修改参数；
	sysctl -p 				#刷新后可用；
~~~


- 17、在1-39内取随机数：
~~~shell
	echo $[$RANDOM%39]
~~~
RANDOM 随机数；
%39   取余数；

- 18、限制apache每秒新建连接数为1，峰值为3
每秒新建连接数一般都是由防火墙来做，apache本身好像无法设置每秒新建连接数，只能设置最大连接：
~~~shell
	iptables -A INPUT -d 172.16.100.1 -p tcp --dport 80 -m limit --limit 1/second -j ACCEPT
~~~

硬件防火墙设置更简单，有界面，可以直接填写数字。。。
最大连接，apache本身可以设置；
MaxClient 3,修改paache最大连接，前提还是要修改系统默认tcp连接数；


- 19、FTP的主动模式和被动模式
FTP协议有两种工作方式：PORT方式和PASV方式，中文意思是主动和被动式；
```
PORT(主动)方式的连接过程是：客户端向服务器的FTP端口(默认是21)发送连接请求，服务器接收连接，建立一条命令链路。
当需要传送数据时，客户端在命令链路上用PORT命令告诉服务器：“我打开了xx端口，你过来连接我”。
于是服务器从20端口向客户端的xx端口发送连接请求，建立一条数据链路来传送数据；
```

```
PASV(被动)方式的连接过程是：客户端向服务器的FTP端口(默认是21)发送连接请求，服务器接收连接，建立一条命令链路。
当需要传送数据时，服务器在命令链路上用PASV命令告诉客户端：“我打开了xx端口，你过来连接我”。
于是客户端向服务器的xx端口发送连接请求，建立一条数据链路来传送数据。
```
从上面可以看出，两种命令的链路连接方法是一样的，而数据链路的建立方法就完全不同。


- 20、显示/etc/inittab中以#开头，且后面跟了一个或者多个空白字符，而后又跟了任意非空白字符的行；
~~~shell
	[kalaguiyin@sslinux Apace]$ grep "^# \{1,\}[^ ]" /etc/inittab# inittab is no longer used.
	# ADDING CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
	# Ctrl-Alt-Delete is handled by /usr/lib/systemd/system/ctrl-alt-del.target
	# systemd uses 'targets' instead of runlevels. By default, there are two main targets:
	# multi-user.target: analogous to runlevel 3
	# graphical.target: analogous to runlevel 5
	# To view current default target, run:
	# systemctl get-default
	# To set a default target, run:
	# systemctl set-default TARGET.target
~~~

~~~shell
	[kalaguiyin@sslinux Apace]$ grep "^#[[:space:]]\{1,\}[^[:space:]]*" /etc/inittab
	# inittab is no longer used.
	# ADDING CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
	# Ctrl-Alt-Delete is handled by /usr/lib/systemd/system/ctrl-alt-del.target
	# systemd uses 'targets' instead of runlevels. By default, there are two main targets:
	# multi-user.target: analogous to runlevel 3
	# graphical.target: analogous to runlevel 5
	# To view current default target, run:
	# systemctl get-default
	# To set a default target, run:
	# systemctl set-default TARGET.target
~~~

- 21、显示/etc/inittab中包含了:一个数字:(即两个冒号中间一个数字)的行；
~~~shell
	grep "\:[0-9]\{1\}\:" /etc/inittab
~~~

- 22、怎么把脚本添加到系统服务里，即用service来调用；
在脚本里加入：
~~~shell
#!/bin/bash
# chkconfig: 345 85 15
# description: httpd
~~~
然后保存
chkconfig httpd -add 创建系统服务
现在就可以使用service来start or restart。


- 23、写一个脚本，实现批量创建20个用户，用户名为user1-20，密码user后面跟5个随机字符：
~~~shell
#!/bin/bash
# description: useradd
for i in `seq -f"%02g" 1 20`;do
useradd user$i
echo "user$i-`echo $RANDOM|md5sum|cut -c 1-5`" | passwd --stdin user$i > /dev/null 2>&1
done
~~~

- 24、写一个脚本，实现172.27.8.0/24网络里，当前在线的IP有哪些，能ping同则认为在线；
~~~shell
#!/bin/bash
for ip in `seq 1 255`
  do
    {
     ping -c 1 172.27.8.$ip > /dev/null 2>&1
     if [ $? -eq 0 ]; then
          echo 172.27.8.$ip UP
     else
          echo 172.27.8.$ip DOWN
     fi
   }&
done
wait
~~~

- 25、写一个脚本，判断一个指定的脚本是否有语法错误；如果有错误，则提醒用户键入Q或者q无视错误并退出，其他任何键可以通过vim打开这个指定的脚本：
~~~shell
#!/bin/bash
read -p "please input check script->" file
if [ -f $file ]; then
    sh -n $file > /dev/null 2>&1
if [ $? -ne 0 ]; then
    read -p “You input $file syntax error,[Type q to exit or Type vim toedit]” answer
    case $answer in
    q | Q)
exit 0
;;
vim)
~~~

- 26、 /var/www/html/是网站的发布目录，如何每天凌晨0点30对其进行自动备份，写出操作步骤？
~~~shell
	# crontab –e
	30 0 * * * /bin/tar –jpcv /backup/html-`date`.bzip2  /var/www/html/*
~~~


- 27、将/usr/local/test目录下大于100K 的文件转移到/tmp 目录下。
~~~shell
	#find /usr/local/test  -size +100K  -exec mv {} /tmp/ \;
~~~


- 28、简述 raid0 raid1 raid5三种工作模式的工作原理及特点。
raid0：假设有两块硬盘A与B，切出等量区块，然后依序存放到这两块磁盘当中
Raid1:说白了，同一份数据，完整保存在两块磁盘当中
Raid5:至少三块磁盘，多了一个同位检查码记录











