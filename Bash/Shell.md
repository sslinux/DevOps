# Bash基础特性及shell脚本编程
## <span id="目录">目录</span>
- [1、命令历史](#01命令历史)  
- [2、命令补全](#02命令补全)  
- [3、路径补全](#03路径补全)  
- [4、命令行展开](#04命令行展开)
- [5、命令的执行状态结果](#05命令的执行状态结果)  
- [6、命令别名](#06命令别名)
- [7、通配符glob](#07通配符glob)
- [8、bash快捷键](#08bash快捷键)
- [9、bash的IO重定向及管道](#09bash的io重定向及管道)
- [10、shell编程环境](#10shell编程环境)
- [11、bash变量类型](#11bash变量类型)
- [12、bash的配置文件](#12bash的配置文件)
- [13、bash中的算术运算符](#13bash中的算术运算符)
- [14、bash条件测试](#14bash条件测试)
- [15、bash脚本之用户交互](#15bash脚本编程之用户交互)
- [16、流程控制](#16流程控制)
- [17、数组](#17数组)
- [18、的字符串处理工具](#18的字符串处理工具)
- [19脚本的配置文件](#19脚本的配置文件)


## <span id="01命令历史">01命令历史</span>
（1） 使用命令：history

（2） 环境变量：

        a) HISTSIZE：命令历史缓冲区中记录的条数，默认为1000；

        b) HISTFILE：记录当前登录用户在logout时历史命令存放文件；

        c) HISTFILESIZE：命令历史文件记录历史的条数，默认为1000；
(3)  操作命令历史：

        a) history –d OFFSET 删除指定行的命令历史；

        b) history –c 清空命令历史缓冲区中的命令；

        c) history # 显示历史中最近的#条命令；

        d) history –a 手动追加当前会话缓冲区中的命令至历史文件中；
(4) 调用历史中的命令：

        a) !# : 重复执行第#条命令；

        b) !! : 重复执行上一条(最近一条命令；)

        c) !string : 重复执行最近一次以指定字符串开头的命令；

        d) 调用上一条命令的最后一个参数：

             i. !$

             ii. ESC, ．



（5） 控制命令历史的记录方式：

        环境变量：HISTCONTROL

        三个值：

            ignoredups：忽略重复的命令；所谓重复，一定是连续且完全相同，包括选项和参数；

            ignorespace：忽略所有以空白开头的命令，不记录；

            ignoreboth：忽略上述两项，既忽略重复的命令，也忽略空白开头的命令；

- 修改环境变量的方式：

         export 变量名=“VALUE”

         或： VARNAME=“VALUE” export VARNAME
         
[返回目录](#目录)

## <span id="02命令补全">02命令补全</span>
内部命令：直接通过shell补全；
外部命令：bash根据PATH环境变量定义的路径，自左而右地在每个路径搜寻以给定命令命名的文件，第一次找到即为要执行的命令；
- Note: 在第一次通过PATH搜寻到命令后，会将其存入hash缓存中，下次使用不再搜寻PATH，从hash中查找；

~~~shell
[root@sslinux ~]# hash
hits command
 1 /usr/sbin/ifconfig
 1 /usr/bin/vim
 1 /usr/bin/ls
~~~

Tab键补全：
若用户给出的字符在命令搜索路径中有且仅有一条命令与之相匹配，则Tab键直接补全；

若用户输入的字符在命令搜索路径中有多条命令与之相匹配，则再次Tab键可以将这些命令列出；

[返回目录](#目录)

## <span id="03路径补全">03路径补全</span>

以用户输入的字符串作为路径开头，并在其指定路径的上级目录下搜索以指定字符串开头的文件名；       

    如果唯一，则直接补全；     

    否则，再次Tab，列出所有符合条件的路径及文件；  

[返回目录](#目录)


## <span id="04命令行展开">04命令行展开</span>
1）~ ：展开为用户的主目录；
~~~shell
[root@sslinux log]# pwd
/var/log
[root@sslinux log]# cd ~
[root@sslinux ~]# pwd
/root
~~~
2）~USERNAME ： 展开为指定用户的主目录；
~~~shell
[root@sslinux ~]# pwd
/root
[root@sslinux ~]# cd ~sslinux
[root@sslinux sslinux]# pwd
/home/sslinux
~~~

3） {} ： 可承载一个以逗号分隔的列表，并将其展开为多个路径；
~~~shell
[root@localhost test]# ls
[root@localhost test]# mkdir -pv ./tmp/{a,b}/shell
mkdir: created directory `./tmp'
mkdir: created directory `./tmp/a'
mkdir: created directory `./tmp/a/shell'
mkdir: created directory `./tmp/b'
mkdir: created directory `./tmp/b/shell'
[root@localhost test]# mkdir -pv ./tmp/{tom,johnson}/hi
[root@localhost test]# tree .

└── tmp
 ├── a
 │ └── shell
 ├── b
 │ └── shell
 ├── johnson
 │ └── hi
 └── tom
 └── hi
9 directories, 0 files
~~~

[返回目录](#目录)

## <span id="05命令的执行状态结果">05命令的执行状态结果</span>
表示命令是否成功执行；

bash使用特殊变量$?保存最近一条命令的执行状态结果；

- 环境变量$?的取值：

     值： 0 ： 成功；

     1-255：失败，1,127,255为系统保留；

-  程序执行有两类结果： 

     程序的返回值；程序自身执行的输出结果；

     程序的执行状态结果；$?

~~~shell
[root@localhost test]# ls /etc/sysconfig/

[root@localhost test]# echo $?

0    #程序的执行状态结果；执行成功；

[root@localhost test]# ls /etc/sysconfig/NNNN

ls: cannot access /etc/sysconfig/NNNN: No such file or directory    #程序自身的执行结果；

[root@localhost test]# echo $?

2    #执行失败；

~~~



[返回目录](#目录)

## <span id="06命令别名">06命令别名</span>
[返回目录](#目录)

## <span id="07通配符glob">07通配符glob</span>
[返回目录](#目录)

## <span id="08bash快捷键">08bash快捷键</span>
[返回目录](#目录)

## <span id="09bash的IO重定向及管道">09bash的io重定向及管道</span>
[返回目录](#目录)

## <span id="10shell编程环境">10shell编程环境</span>
[返回目录](#目录)

## <span id="11bash变量类型">11bash变量类型</span>
[返回目录](#目录)

## <span id="12bash的配置文件">12bash的配置文件</span>
[返回目录](#目录)

## <span id="13bash中的算术运算符">13bash中的算术运算符</span>
[返回目录](#目录)

## <span id="14bash条件测试">14bash条件测试</span>
[返回目录](#目录)

## <span id="15bash脚本编程之用户交互">15bash脚本编程之用户交互</span>
[返回目录](#目录)


## <span id="16流程控制">16流程控制</span>
[返回目录](#目录)


## <span id="17数组">17数组</span>
[返回目录](#目录)


## <span id="18的字符串处理工具">18的字符串处理工具</span>
[返回目录](#目录)


## <span id="19S脚本的配置文件">19脚本的配置文件</span>
[返回目录](#目录)
