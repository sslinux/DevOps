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
- 通过alias命令实现：

a、alias ： 显示当前shell进程所有可用的命令别名；

b、定义别名，格式为： alias NAME='VALUE'  

定义别名NAME，其执行相当于执行命令VALUE，VALUE中可包含命令、选项以及参数；仅当前会话有效，不建议使用；

c、通过修改配置文件定义命令别名：

    当前用户：~/.bashrc
    全局用户：/etc/bashrc

- Bash进程重新读取配置文件：
~~~shell
    source /path/to/config_file 

    . /path/to/config_file
~~~
- 撤销别名： unalias
```
    unalias [-a] name [name...]
```
- Note:

    对于定义了别名的命令，要使用原命令，可通过\COMMAND的方式使用；

- Example:

~~~shell
[root@sslinux sslinux]# alias
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
[root@sslinux sslinux]# grep alias /root/.bashrc
# User specific aliases and functions
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
~~~

[返回目录](#目录)

## <span id="07通配符glob">07通配符glob</span>

Bash中用于文件名"通配"
- 通配符： *,?,[]

    1) * 任意长度的任意字符；

        a * b 
~~~shell
[root@sslinux sslinux]# ls -ld /etc/au*
drwxr-x---. 3 root root 41 Sep 3 22:05 /etc/audisp
drwxr-x---. 3 root root 79 Sep 3 22:09 /etc/audit
~~~

    2)  ? 任意单个字符；
        
        a?b
~~~shell
[root@sslinux sslinux]# ls -ld /etc/*d?t
drwxr-x---. 3 root root 79 Sep 3 22:09 /etc/audit
~~~       

    3) []   匹配指定范围内的任意单个字符；

        [0-9]    [a-z]   不区分大小写；
        [admin]    可以是区间形式的，也可以是离散形式的；
~~~shell
[root@sslinux sslinux]# ls -ld /etc/[ab]*
drwxr-xr-x. 2 root root 4096 Sep 3 22:05 /etc/alternatives
drwxr-xr-x. 2 root root 33 Sep 3 22:04 /etc/avahi
drwxr-xr-x. 2 root root 33 Sep 3 22:04 /etc/bash_completion.d
-rw-r--r--. 1 root root 2835 Oct 29 2014 /etc/bashrc
drwxr-xr-x. 2 root root 6 Mar 6 2015 /etc/binfmt.d
~~~
    4) [^] 匹配指定范围以外的任意单个字符；
```
        [^0-9] : 单个非数字的任意字符；
```
- 专用字符结合：(表示一类字符中的单个)

[:digit:] 任意单个数字，相当于[0-9];

[:lower:] 任意单个小写字母；

[:upper:] 任意单个大写字母；

[:alpha:] 任意单个大小写字母；

[:alnum:] 任意单个数字或字母；

[:space:] 任意空白字符；

[:punct:] 任意单个特殊字符；

- Note：

在使用[]应用专用字符集合时，外层也需要嵌套[]。

Example：
~~~shell
# ls -d /etc/*[[:digit:]]*[[:lower:]]
~~~

[返回目录](#目录)

## <span id="08bash快捷键">08bash快捷键</span>
### 编辑命令：
- Ctrl + a ：移到命令行首
- Ctrl + e ：移到命令行尾
- Ctrl + f ：按字符前移（右向）
- Ctrl + b ：按字符后移（左向）
- Alt + f ：按单词前移（右向）
- Alt + b ：按单词后移（左向）
- Ctrl + xx：在命令行首和光标之间移动
- Ctrl + u ：从光标处删除至命令行首
- Ctrl + k ：从光标处删除至命令行尾
- Ctrl + w ：从光标处删除至字首
- Alt + d ：从光标处删除至字尾
- Ctrl + d ：删除光标处的字符
- Ctrl + h ：删除光标前的字符
- Ctrl + y ：粘贴至光标后
- Alt + c ：从光标处更改为首字母大写的单词
- Alt + u ：从光标处更改为全部大写的单词
- Alt + l ：从光标处更改为全部小写的单词
- Ctrl + t ：交换光标处和之前的字符
- Alt + t ：交换光标处和之前的单词
- Alt + Backspace：与 Ctrl + w 相同类似，分隔符有些差别

### 重新执行命令：
- Ctrl + r：逆向搜索命令历史
- Ctrl + g：从历史搜索模式退出
- Ctrl + p：历史中的上一条命令
- Ctrl + n：历史中的下一条命令
- Alt + .：使用上一条命令的最后一个参数

### 控制命令：
- Ctrl + l：清屏
- Ctrl + o：执行当前命令，并选择上一条命令
- Ctrl + s：阻止屏幕输出
- Ctrl + q：允许屏幕输出
- Ctrl + c：终止命令
- Ctrl + z：挂起命令

### Bang (!) 命令
- !!：执行上一条命令
- !blah：执行最近的以 blah 开头的命令，如 !ls
- !blah:p：仅打印输出，而不执行
- !$：上一条命令的最后一个参数，与 Alt + . 相同
- !$:p：打印输出 !$ 的内容
- !*：上一条命令的所有参数
- !*:p：打印输出 !* 的内容
- ^blah：删除上一条命令中的 blah
- ^blah^foo：将上一条命令中的 blah 替换为 foo
- ^blah^foo^：将上一条命令中所有的 blah 都替换为 foo

## 友情提示：

    以上介绍的大多数 Bash 快捷键仅当在 emacs 编辑模式时有效，

    若你将 Bash 配置为 vi 编辑模式，那将遵循 vi 的按键绑定。

    Bash 默认为 emacs 编辑模式。

    如果你的 Bash 不在 emacs 编辑模式，可通过 set-o emacs 设置。

    ^S、^Q、^C、^Z 是由终端设备处理的，可用 stty 命令设置。


[返回目录](#目录)

## <span id="09bash的IO重定向及管道">09bash的io重定向及管道</span>
打开的文件都有一个fd：file descriptor(文件描述符)

    标准输入：keyboard，0

    标准输出：monitor，1

    标准错误输出：monitor，2

###I/O重定向：改变标准位置；

- 输出重定向：

     COMMAND > NEW_POS 覆盖重定向，目标文件中的原有内容会被清除；

     COMMAND >> NEW_POS 追加重定向，新内容会被追加到目标文件尾部；

- Note：

    为了在输出重定向时防止覆盖原有文件，建议使用以下设置：

    set –C ： 禁止将内容覆盖输出(>)至已有文件中,追加输出不受影响；

    此时，若确定要将重定向的内容覆盖原有文件，可使用 >| 强制覆盖；

- Example:
~~~shell
[root@localhost test1]# echo "It's dangerous" > ./result.txt #输出到文件；
[root@localhost test1]# cat result.txt
It's dangerous
[root@localhost test1]# set –C #禁止将内容覆盖输出到已有文件；
[root@localhost test1]# echo "It's very dangerous" > ./result.txt
-bash: ./result.txt: cannot overwrite existing file #提示不能覆盖已存在文件；
[root@localhost test1]# echo "It's very dangerous" >| ./result.txt #强制覆盖
[root@localhost test1]#
[root@localhost test1]# set +C #取消禁止覆盖输出到已有文件；
[root@localhost test1]# echo "It's very dangerous" > ./result.txt
[root@localhost test1]#
~~~

- 错误输出：

     2> : 覆盖重定向错误输出数据流；

     2>> ：追加重定向错误输出数据流；
~~~shell
[root@localhost test1]# lss -l /etc/ 2> ./error.txt
[root@localhost test1]# cat error.txt
-bash: lss: command not found
[root@localhost test1]# cat /etc/passwd.error 2>> ./error.txt
[root@localhost test1]# cat error.txt
-bash: lss: command not found
cat: /etc/passwd.error: No such file or directory
~~~

将标准输出和标准错误输出各自重定向至不同位置：

     COMMAND > /path/to/file.out 2> /path/to/error.out
- Example:
```
# cat /etc/passwd > ./file.out 2> ./error.out
```
- 合并输出：

     合并标准输出和错误输出为同一个数据流进行重定向；

         &> 合并覆盖重定向；

         &>> 合并追加重定向；

    格式为：

         COMMAND > /path/to/file.out 2> &1

         COMMAND >> /path/to/file.out 2>> &1

    Example:
~~~shell
    [root@localhost test1]# ls -l /etc/ > ./file.out 2>&1
    [root@localhost test1]# ls -l /etc/ &> file.out
    [root@localhost test1]# ls -l /etcc/ &> file.out
    [root@localhost test1]# cat file.out
    ls: cannot access /etcc/: No such file or directory
~~~

- 输入重定向： <
~~~shell
     HERE Documentation：<<

     # cat << EOF

     # cat > /path/to/somefile << EOF
~~~

Example: 输入重定向，输入完成后显示内容到标准输出上；
~~~shell
[root@localhost test1]# cat << EOF
> my name is kalaguiyin.
> I'm a tibetan.
> I come from Sichuan Provence.
> EOF
my name is kalaguiyin.
I'm a tibetan.
I come from Sichuan Provence.
~~~

Example：从标准输入读取输入并重定向到文件。
~~~shell
[root@localhost test1]# cat > hello.txt << EOF 
> this is a test file.
> 中华人民共和国。
> EOF
[root@localhost test1]# cat hello.txt
this is a test file.
中华人民共和国。
~~~

- 管道：

     COMMAND1 | COMMAND2 | COMMAND3 | …..

     作用：前一个命令的执行结果将作为后一个命令执行的参数；

     Note:

            最后一个命令会在当前shell进程的子shell进程中执行；
~~~shell
[root@sslinux]# cat /etc/passwd | sort -t: -k3 -n | cut -d: -f1
root
bin
daemon
adm
lp
polkitd
sslinux
~~~

[返回目录](#目录)

## <span id="10shell编程环境">10shell编程环境</span>
- 编程基础知识：

    1、程序编程风格：

        过程式：以指令为中心，数据服务于指令；

        对象式：以数据为中心，指令服务于数据；

    shell程序，提供了编程能力，解释执行，shell就是一解释器；
    
    2、程序的执行方式：

        计算机：运行二进制命令；

        编程语言：

            低级：汇编；

            高级：

                编译型语言：高级语言 -->编译器 --> 目标代码；

                解释型语言：高级语言 -->解释器 --> 机器代码；

    3、过程式编程的三种结构；

        顺序执行；

        循环执行；

        选择执行；

    4、shell编程：过程式语言，由解释器解释执行；

        编程语言的基本结构：

           数据存储： 变量、数组；

           表达式；

           语句；

    5、shell脚本：文本文件；

        首行特定格式：

            #!/bin/bash   --> shebang

            魔数：magic number，程序的执行入口，告知本程序由哪一个解释器解释执行；

    6、运行脚本的两种方式：

        a、 给予执行权限，通过具体的文件路径指定文件执行；

        b、 直接运行解释器，将脚本作为解释器程序的参数运行；

    - Example:

~~~shell
    [root@localhost test1]# vim test.sh
    [root@localhost test1]# bash test.sh
    hello,girl
    [root@localhost test1]# chmod +x test.sh
    [root@localhost test1]# ./test.sh
    hello,girl
~~~

- Bash编程：

    bash是弱类型编程，变量默认为字符型；

    把所有要存储的数据统统当做字符进行存储；

    变量不需要事先声明，可以在调用时直接赋值使用，参与运算会自动进行隐式类型转换；

    不支持浮点数； 

- 逻辑运算：

     与：&& 同为1则为1，否则为0；

     或：|| 同为0则为0，否则为1；

     非：取反，!0为1，!1为0；

    短路与运算：双目运算符前面的结果为0，则结果一定为0，后面的不执行；

    短路或运算：双目运算符前面的结果为1，则结果一定为1，后面的不执行；

[返回目录](#目录)

## <span id="11bash变量类型">11bash变量类型</span>
变量类型决定了变量的数据存储格式、存储空间大小以及变量能参与的运算种类；

- 强弱类型语言的区别：

    强类型：定义变量时必须执行类型、参与运算必须符合类型要求；调用未声明变量会产生错误；

    弱类型：无需指定类型，默认均为字符型；参与运算会自动进行隐式类型转换；变量无需事先定义即可直接调用；

- Bash中的变量的种类：

 根据变量的生效范围等标准划分：

 1、本地变量：生效范围为当前shell进程；对当前shell之外的其他shell进程，包括当前shell的子shell进程均无效；

 2、环境变量：生效范围为当前shell进程及其子进程；

 3、局部变量：生效范围为当前shell进程中某代码片断(通常指函数)

 4、位置变量：$1, $2, ...来表示，用于让脚本在脚本代码中调用通过命令行传递给它的参数；

 4、特殊变量：$?, $0, $*, $@, $#

         $? : 上一条命令的执行状态结果；
         $0 : 命令本身
         $* : 传递给脚本的所有参数；
         $@ : 传递给脚本的所有参数；
         $# : 传递给脚本的参数的个数；

## 本地变量：
1、变量赋值：name='VALUE'

    a) 在赋值时，VALUE可以使用以下引用：

       【1】可以是直接字符串；name="username"
       【2】变量引用：name=“$username”
       【3】命令引用：name=`COMMAND`,name=$(COMMAND)

2、 变量引用：${name},花括号可省略：$name

    引号引用：

         " " ： 弱引用，其中的变量引用会被替换为变量值；

         ' ' ： 强引用，其中的变量不会被替换为变量值，而保持原字符串；

查看所有已定义的变量： #set

销毁变量： # unset name

## 环境变量：
    1、变量声明、赋值：
        export name=VALUE
        declare -x name=VALUE
    2、变量引用：
        $name
        ${name}
    3、显示所有环境变量：
        export
        env
        printenv
    4、销毁环境变量：
        unset name
- Bash中内建的环境变量：
    PATH，SHELL，UID，HISTSIZE, HOME, PWD, OLD, HISTFILE, PS1

## 只读变量： 
相当于常量，变量值不可变，不能再进行赋值运算；

声明只读变量的格式：

    readonly name
    declare –r name   

## 位置变量：
在脚本代码中调用通过命令行传递给脚本的参数；

    $1,$2,…. : 对应调用第1、第2等参数；

    $0: 命令本身；

    $*: 传递给脚本的所有参数；

    $@: 传递给脚本的所有参数；

    $#: 传递给脚本的参数的个数；

[返回目录](#目录)

## <span id="12bash的配置文件">12bash的配置文件</span>
按生效范围划分，存在两类配置文件：

1、全局配置：

    /etc/profile
        /etc/profile.d/*.sh
    /etc/bashrc
2、用户配置：
   
    ~/.bash_profile
    ~/.bashrc

按功能划分：也存在两类；

1、profile类：为交互式登录的shell提供配置；

    交互式登录shell：直接通过终端输入账号密码登录；

    使用“su - UserName”或“su -l UserName”切换的用户


~~~shell
    作用于全局：/etc/profile，/etc/profile.d/*.sh

    作用于个人：~/.bash_profile

~~~

- 功用：

    （1）用于定义环境变量；

    （2）运行命令或脚本；

- 配置文件加载顺序：(相同参数，后加载的生效)

         /etc/profile --> 
         /etc/profile.d/*.sh --> 
         ~/.bash_profile --> 
         ~/.bashrc --> 
         /etc/bashrc

2、bashrc类：为非交互式登录的shell提供配置：

- 非交互式Shell：

         su UserName 
         图形界面下打开的终端
         执行脚本
~~~shell
    作用于全局：/etc/bashrc
    作用于个人：~/.bashrc
~~~

- 功用：

    （1）	定义命令别名；

    （2）	定义本地变量；


- 配置文件加载顺序：（相同参数，后加载的生效）

	~/.bashrc --> /etc/bashrc --> /etc/profile.d/*.sh

- 编辑配置文件后让定义的新配置生效的方式：

	(1) 重新启动shell进程；

	(2) 使用source或点命令(　．)进程；

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
