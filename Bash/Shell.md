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
- [17、函数](#函数)
- [18、数组](#18数组)
- [19、bash的字符串处理工具](#19bash的字符串处理工具)
- [20、脚本的配置文件](#20、脚本的配置文件)


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
+，-，*，/，%，**

实现算术运算的方式：

    (1) let var=算术表达式
    (2) var=$[算术表达式]
    (3) var=$((算术表达式))
    (4) var=$(expr arg1 arg2 arg3...)
乘法符号在有些场景中需要转义；

- bash内建随机数生成器：$RANDOM

- 增强型赋值：
    +=，-=，*=，/=，%=

    let varOPERvalue：

        例如：letcount+=1
- 自增，自减：

    let var+=1

        let var++

    let var-=1

        let var--

- Example:
~~~shell
#!/bin/bash
spaceline1=$(grep "^[[:space:]]*$" $1 | wc -l)
spaceline2=$(grep "^[[:space:]]*$" $2 | wc -l)
echo "The sum of space line:$[$spaceline1+$spaceline2]"
~~~
[返回目录](#目录)

## <span id="14bash条件测试">14bash条件测试</span>
判断某需求是否满足，需要有测试机制来实现；

- Note：专用的测试表达式需要由测试命令辅助完成测试过程；

- 测试命令：

 test EXPRESSION

 [ EXPRESSION ]

 [[ EXPRESSION ]]

    Note: EXPRESSION前后必须有空白字符，否则报错；

## Bash的测试类型：

- 数值测试：

 -gt : 是否大于； >

 -ge：是否大于等于； >=

 -eq：是否等于 ==

 -ne：是否不能于 !=

 -lt ：是否小于 <

 -le ：是否小于等于； <=

- 字符串测试：

~~~shell

     == : 是否等于；

     >: 是否大于；

     <: 是否小于；

     != ： 是否不等于；

     =~ ：左侧字符串是否能够被右侧的PATTERN所匹配；

     - Note：此表达式一般用于[[ ]]中；

     -z “STRING” : 测试字符串是否为空，空则为真，不空则为假；

     -n “STRING” ：测试字符串是否不空，不空则为真，空则为假；
~~~

Note：在字符串比较时用到的操作数都应该使用引号；

## 文件测试：

- (a) 存在性测试：

    -a FILE

    -e FILE：文件存在性测试，存在为真，否则为假；

- (b) 存在性及类别测试

     -b FILE ：是否存在且为块设备文件；

     -c FILE ：是否存在且为字符设备文件；

     -d FILE ：是否存在且为目录文件；

     -f FILE ：是否存在且为普通文件；

     -h FILE 或 –L FILE：是否存在且为符号链接文件；

     -p FILE：是否存在且为命名管道文件；

     -S FILE ：是否存在且为套接字文件；

- (c) 文件权限测试：

     -r FILE：是否存在且可读

     -w FILE: 是否存在且可写

     -x FILE: 是否存在且可执行

- (d) 文件特殊权限测试：

     -g FILE：是否存在且拥有sgid权限；

     -u FILE：是否存在且拥有suid权限；

     -k FILE：是否存在且拥有sticky权限；

-  (e) 文件大小测试：

     -s FILE：是否存在且非空；

- (f) 文件是否打开：

     -t fd：fd表示文件描述符是否已经打开且与某终端相关

     -N FILE：文件自从上一次被读取之后是否被修改过；

     -O FILE：当前有效用户是否为文件属主；

     -G FILE：当前有效用户是否为文件属组；

- (g) 双目测试：

    FILE1 –ef FILE2 ： FILE1与FILE2是否指向同一个设备上的相同inode；

    FILE1 –nt FILE2 ：FILE是否新于FILE2；

    FILE1 –ot FILE2 ：FILE1是否旧于FILE2；

- (h) 组合测试条件：

 完成逻辑运算：

     第一种方式：

             COMMAND1 && COMMAND2

             COMMAND1 || COMMAND2

             !COMMAND

         eg：[ -e FILE ] && [ -r FILE ] 文件是否存在且是否有读权限；


     第二种方式：

             EXPRESSION1 –a EXPRESSION2

             EXPRESSION1 –o EXPRESSION2

             !EXPRESSION

     必须使用测试命令进行；

- Example：
~~~shell
# [ -z "$hostName" -o "$hostName"=="localhost.localdomain" ] && hostname www.studyhard.com
# -z判断hostName是否为空，-o表示或者，即：hostName为空或者值为localhot.localdomain的时候，使用hostname命令修改主机名；
[root@localhost kalaguiyin]# hostname
www.studyhard.com
~~~

- Example:

~~~shell
[root@kalaguiyin ~]# [ -f /bin/cat -a -x /bin/cat ] && cat /etc/fstab
#判断文件/bin/cat是否存在且是否有可执行权限，&&是短路与，如果前面执行结果为真则使用cat命令#查看文件；
#
# /etc/fstab
# Created by anaconda on Fri Jul 3 03:08:29 2015
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root / xfs defaults 0 0
UUID=3d04d82b-c52b-4184-8d64-1826db6e2eac /boot xfs defaults 0 0
/dev/mapper/centos-home /home xfs defaults 0 0
/dev/mapper/centos-swap swap swap defaults 0 0
~~~
 
- 快速查找选项定义的方法：

 man bash --> /^[[:space:]]*-f

~~~shell
# [ -f /bin/cat -a -x /bin/cat ] && cat /etc/fstab 
#如果文件存在且有执行权限，就用它查看文件内容；
~~~
[返回目录](#目录)

## <span id="15bash脚本编程之用户交互">15bash脚本编程之用户交互</span>

- read命令：

    read [option]… [name….]

         -p “prompt” 提示符；

         -t TIMEOUT 用户输入超时时间；

- 检测脚本中的语法错误：

         bash –n /path/to/some_script

- 调试执行，查看执行流程：

         bash –x /path/to/some_script

- Example:

~~~shell
#!/bin/bash
#
#Description: Test read command's grammer.
read -t 50 -p "Enter a disk special file:" diskfile #将用户输入的内容赋值给diskfile变量
[ -z "$diskfile" ] && echo "Fool" && exit 1
#判断diskfile的值是否为空，如果为空则输出，并退出；
if fdisk -l | grep "^Disk $diskfile" &> /dev/null;then
 fdisk -l $diskfile
else
 echo "Wrong disk special file."
 exit 2
fi
~~~
[返回目录](#目录)

## <span id="16流程控制">16流程控制</span>

### <span id="if语句">if语句</span>

```
if语句：
     CONDITION：
     bash命令：
```

 用命令的执行状态结果：

     成功：true，即执行状态结果值为0时；

     失败：false，即执行状态结果为1-255时；

 成功或失败的定义：取决于用到的命令；


- 单分支if：
```
 if CONDITION；then
     if-true(条件为真时的执行语句集合)
 fi
```
### Example:
~~~shell
#!/bin/bash
#if单分支语句测试；
if [ $UID -eq 0 ];then
     echo "It's amdinistrator."
fi
~~~

-  双分支if： 
```
 if CONDITION;then
     if-true
 else
     if-false
 fi
```
### Example:

~~~shell
#!/bin/bash
#
#if双分支语句测试；
if [ $UID -eq 0 ];then
 echo "It's administrator."
else
 echo "It's Comman User."
fi
~~~

- 多分支if：
```
 if CONDITION1；then
     if-true
 elif CONDITION2;then
     if-true
 elif CONDITION3;then
     if-true
 ….
 else
     all-false
 fi
```
逐条件进行判断，第一次遇为“真”条件时，执行其分支，而后结束；

- Exmaple:用户键入文件路径，脚本来判断文件类型；

~~~shell
#!/bin/bash
#
#if语句多分支语句；
read -t 20 -p "Enter a file path:" filename

if [ -z "$filename" ];then #判断变量是否为空；
 echo "Usage:Enter a file path."
 exit 2
fi

if [ ! -e $filename ];then #判断用户输入的文件是否存在；
 echo "No such file."
 exit 3
fi

if [ -f $filename ];then #判断是否为普通文件；
 echo "A common file."
elif [ -d $filename ];then #判断是否为目录；
 echo "A directory"
elif [ -L $filename ];then #判断是否为链接文件；
 echo "A symbolic file."
else
 echo "Other type."
fi
~~~

### <span id="for循环">for循环</span>
循环体：要执行的代码，可能要执行n遍；

 循环需具备进入循环的条件和退出循环的条件；

- for循环：
```
 for 变量名 in 列表；do
     循环体
 done
```

- 执行机制：

    依次将列表中的元素赋值给“变量”；每次赋值后即执行一次循环体；直到列表中的元素耗尽，循环结束；

- EXAMPLE：添加10个用户，用户名为：user1-user10：密码同用户名；

~~~shell
#!/bin/bash
#
#for循环，使用列表；
#添加10个用户，user1-user10

if [ ! $UID -eq 0 ];then #判断执行脚本的是否为root用户，若不是则直接退出；
	echo "Only root can use this script."
	exit 1
fi

for i in {1..10};do
	if id user$i &> /dev/null;then	#判断用户是否已经存在；
		echo "user$i exists"
	else
		useradd user$i
		if [ $? -eq 0 ];then   #判断前一条命令是否执行成功；
			echo "user$i" | passwd --stdin user$i &> /dev/null
			#passwd命令从标准输入获得命令，即管道前命令的执行结果；
		fi
	fi
done
~~~

- 列表生成方式：

    (1) 直接给出列表 for i in {kalaguiyin johnson rechard}

    (2) 整数列表

        (a) {start..end}

        (b) $(seq [start [step]] end)

    (3) 返回列表的命令

        $(COMMAND) --> 如：$(ls /var)

    (4) glob

        /etc/rc.d/rc3.d/K*

    (5) 变量引用

        $@ , $*  -->所有向脚本传递的参数；

- Example：判断某路径下所有文件的类型：

~~~shell
#!/bin/bash
#
#for循环使用命令返回列表；

for file in $(ls /var);do #使用命令生成列表；
 if [ -f /var/$file ];then
     echo "Common file."
 elif [ -L /var/$file ];then
     echo "Symbolic file."
 elif [ -d /var/$file ];then
     echo "Directory."
 else
     echo "Other type"
 fi
done
~~~

- Example：使用for循环统计关于tcp端口监听状态；

~~~shell
#!/bin/bash
#
#使用for循环过滤netstat命令中关于tcp的信息；
declare -i estab=0
declare -i listen=0
declare -i other=0

for state in $( netstat -tan | grep "^tcp\>" | awk '{print $NF}');do

 if [ "$state" == 'ESTABLISHED' ];then
     let estab++
 elif [ "$state" == 'LISTEN' ];then
     let listen++
 else
     let other++
 fi
done

echo "ESTABLISHED:$estab"
echo "LISTEN:$listen"
echo "Unknow:$other"
~~~

- Example: 通过ping命令探测192.168.0.1-254范围内的所有主机的在线状态；

~~~shell
#!/bin/bash
#
#通过ping命令探测192.168.0.1-254范围内的所有主机的在线状态；
net='192.168.0'
uphosts=0
donwhosts=0

for i in {1..254};do
 ping -c 1 -w 1 ${net}.${i} &> /dev/null
 if [ $? -eq 0 ];then
     echo "${net}.${i} is up."
     let uphosts++
 else
     echo "${net}.${i} is down."
     let downhosts++
 fi
done

echo "Up hosts:$uphosts."
echo "Down hosts:$downhosts."
~~~

### <span id="while循环">while循环</span>
语法：
```
 while CONDITION；do
     循环体
 done
```
```
CONDITION：循环控制条件；进入循环之前，先做一次判断；
每一次循环之后会再次做判断；条件为“true”，则执行一次循环；
直到条件测试状态为“false”终止循环；

因此：CONDTION一般应该有循环控制变量；
而此变量的值会在循环体不断地被修正，直到最终条件为false，结束循环。
```

- Example：用while求100以内所有正整数之和：

~~~shell
#!/bin/bash
#使用while求100以内正整数之和；
declare -i sum=0
declare -i i=1
while [ $i -le 100 ];do
 let sum+=$i
 let i++
done
echo $i
echo "Summary:$sum."
~~~

- Example：用while添加10个用户，user1-user10；

~~~shell
#!/bin/bash
#
#使用while循环添加10个用户；
declare -i i=1
declare -i users=0

while [ $i -le 10 ];do
 if ! id user$i &> /dev/null;then
     useradd user$i
     echo "Add user: user$i"
     let users++
 fi
 let i++
done
echo "Add $users users."
~~~

- Example：使用while循环ping指定网络内的所有主机；

~~~shell
#!/bin/bash
#
#使用while循环ping指定网段内的所有主机；
declare -i i=1
declare -i uphosts=0
declare -i downhosts=0
net='172.16.250'

while [ $i -le 254 ];do
 if ping -c 1 -w 1 ${net}.${i} &> /dev/null;then
     echo "${net}.$i is up."
     let uphosts++
 else
     echo "${net}.$i is down."
     let downhosts++
 fi
 let i++
done

echo "Up hosts:$uphosts."
echo "Down hosts:$downhosts."
~~~

- Example：使用for循环打印九九乘法表；

~~~shell
#!/bin/bash
#使用for循环打印九九乘法表；

for j in {1..9};do
 for i in $(seq 1 $j);do
     echo -e -n "${i}X${j}=$[$i*$j]\t"
 done
 echo
done
~~~

- Example：使用while循环打印九九乘法表；

~~~shell
#!/bin/bash
#
declare -i i=1
declare -i j=1

while [ $j -le 9 ];do
 while [ $i -le $j ];do
     echo -e -n "${i}X${j}=$[$i*$j]\t"
     let i++
 done

 echo
 let i=1
 let j++
done
~~~

- Example：利用RANDOM生成10个随机数字，输出这10个数字，并显示其中的最大者和最小者；

~~~shell
#!/bin/bash
#
#利用RANDOM生成10个随机数，输出，并求最大值和最小值；
declare -i max=0
declare -i min=0
declare -i i=1

while [ $i -le 9 ];do
	rand=$RANDOM
	echo $rand
	
	if [ $i -eq 1 ];then
		max=$rand
		min=$rand
	fi

	if [ $rand -gt $max ];then
		max=$rand
	fi
	if [ $rand -lt $min ];then
		min=$rand
	fi
	let i++
done

echo "MAX:$max."
echo "MIN:$min."

~~~

### <span id="until循环">until循环</span>
```
until CONDITION；do
    循环体
done
#进入条件：false
#退出条件：true

```

```
while CONDITION；do
    循环体
done
#进入条件：CONDITION为true；
#退出条件：CONDITION为false；
```

- Example：用until求100以内所有正整数之和：

~~~shell
#!/bin/bash
#
#利用until循环求100以内所有正整数之和；
declare -i i=1
declare -i sum=0

until [ $i -gt 100 ];do
	let sum+=$i
	let i++
done
echo "100以内所有正整数之和为："
echo "Sum:$sum"

~~~

- Example：用until循环打印九九乘法表；

~~~shell
#!/bin/bash
#
#使用until循环打印九九乘法表；

declare -i j=1
declare -i i=1

until [ $j -gt 9 ];do
	until [ $i -gt $j ];do
		echo -n -e "${i}X${j}=$[$i*$j]\t"
		let i++
	done
	echo
	let i=1
	let j++
done

~~~

### <span id="循环控制语句">循环控制语句</span>
循环控制语句，用于循环体中；
- 1.continue [N] : 提前结束第N层的本轮循环，而直接进入下一轮判断；

```
while CONDITION1；do
    COMMAND1
    …..
    if CONDITION2;then
	continue
    fi
    COMMANDn
    ….
done
```

- 2.break [N] : 提前结束循环；

```
while CONDITION1;do
    CMD1
    ….
    if CONDITION2;then
        break
    fi
    CMDn
    …
done

```

- Example:求100以内所有偶数之和：要求循环遍历100以内的所有正整数；

~~~shell
#!/bin/bash
#
#求100以内所有偶数之和，使用continue跳过奇数；
declare -i i=0
declare -i sum=0

until [ $i -gt 100 ];do
	let i++
	if [ $[$i%2] -eq 1 ];then
		continue
	fi
	let sum+=$i
done

echo "Even sum:$sum"

~~~

### <span id="创建死循环">创建死循环</span>

```
while true;do
	循环体
done
```

```
until false；do
	循环体
done

```

- Example：每隔3秒钟到系统上获取已经登录的用户信息；如果用户输入的用户名登录了，则记录于日志中，并退出；

~~~shell
#!/bin/bash
#
#用while造成死循环，在系统上每隔3秒判断一次用户输入的用户名是否登录；
read -p "Enter a user name:" username

while true;do
	if who | grep "^$username" &> /dev/null;then
		break
	fi
	sleep 3
done

echo "$username logggen on." >> /tmp/user.log

~~~

- Example：使用until实现上述功能：

~~~shell
#!/bin/bash
#
#用until造成死循环，循环判断用户输入的用户名是否登录；
read -p "Enter a user name:" username
until who | grep "^$username" &> /dev/null;do
	sleep 3
done
echo "$username logged on." >> /tmp/user.log
~~~

### while循环的特殊用法：（遍历文件的每一行）
```
	while read line;do
		循环体
	done < /PATH/FROM/SOMEFILE
```
依次读取/PATH/FROM/SOMEFILE文件中的每一行，且将该行赋值给变量line；

- Example:依次读取/etc/passwd文件中的每一行，找出其ID号为偶数的所有用户，显示其用户名、ID号及默认shell；

~~~shell
#!/bin/bash
#while循环的特殊用法，读取指定文件的每一行并赋值给变量；

while read line;do
	if [ $[`echo $line | cut -d: -f3` % 2] -eq 0 ];then
		echo -e -n "username:`echo $line | cut -d: -f1`\t"
		echo -e -n "uid: `echo $line | cut -d: -f3`\t"
		echo "SHELL:`echo $line | cut -d: -f7`"
	fi
done < /etc/passwd

~~~

### for循环的特殊格式：
```
	for ((控制变量初始化；条件判断表达式；控制变量的修正表达式))；do
		循环体
	done
```
此种格式和C语言等的格式是一样一样的，只是多了一对括号；

控制变量初始化：仅在运行到循环代码段时执行一次；

控制变量的修正表达式：每轮循环结束会先进行控制变量修正运算，而后再在条件判断；

- Example：求100以内所有正整数之和：

~~~shell
#!/bin/bash
#
#for循环，类似C语言格式，求100以内正整数之和；

declare -i sum=0

for ((i=1;i<=100;i++));do
	let sum+=$i
done

echo "Sum:$sum."

~~~

- Example：打印九九乘法表；

~~~shell
#!/bin/bash
#
#用类似于C语言风格的for循环打印九九乘法表；

for((j=1;j<=9;j++));do
	for((i=1;i<=j;i++));do
		echo -e -n "${i}X${j}=$[$i*$j]\t"
	done
	echo
done

~~~

### 练习：写一个脚本，完成以下任务：
(1)	显示一个如下菜单：
```
    cpu) show cpu information;
    mem) show memory information;
    disk) show disk information;
    quit) quit
```
(2)	提示用户选择选项；

(3)	显示用户选择的内容；

~~~shell
#!/bin/bash
#
#根据用户的选择，给用户显示相应的硬件信息；

cat << EOF
cpu) show cpu information;
mem) show memory information;
disk) show disk information;
quit) quit
#####################################
EOF

read -p "Enter a option:" option
while [ "$option" != 'cpu' -a "$option" != 'mem' -a "$option" != 'disk' -a "$option" != 'quit' ];do
	read -p "Wrong option,please Enter again:" option
done

if [ "$option" == 'cpu' ];then
	lscpu
elif [ "$option" == 'mem' ];then
	cat /proc/meminfo
elif [ "$option" == 'disk' ];then
	fdisk -l
else
	echo "Quit"
	exit 0
fi

~~~


### <span id="case语句">case语句</span>
```
case 变量引用 in
PAT1)
		分支1
		；；
PAT2）
		分支2
		；；
….
*)
		默认分支
		；；
esac
```

case支持glob风格的通配符：

        *: 任意长度任意字符；
        ?: 任意单个字符；
        []：指定范围内的任意单个字符；
	    a|b: a或b

Example：使用case语句改写前一个练习：
~~~shell
#!/bin/bash
#
cat << EOF
cpu) show cpu information;
mem) show memory information;
disk) show disk information;
quit) quit
============================
EOF
read -p "Enter a option: " option
while [ "$option" != 'cpu' -a "$option" != 'mem' -a "$option" != 'disk' -a "$option" != 'quit' ]; do
    read -p "Wrong option, Enter again: " option
done

case "$option" in
cpu)
	lscpu 
	;;
mem)
	cat /proc/meminfo
	;;
disk)
	fdisk -l
	;;
*)
	echo "Quit..."
	exit 0
	;;
esac

~~~

[返回目录](#目录)

## <span id="17函数">17函数</span>
	函数的作用：
	过程式编程：为实现代码重用
	 	模块化编程
	 	结构化编程；

- 语法一：
```
function f_name {
	…函数体…..
}
```

- 语法二：
```
f_name() {
	…函数….
}
```

- 函数调用：函数只有被调用才会执行：

	调用：给定函数名

		函数名出现的地方，会被自动替换为函数代码；

- 函数的生命周期：被调用时创建，返回时终止；

	return命令返回自定义状态结果；

		0：成功

		1-255：失败

- Example：通过函数，创建10个用户；

~~~shell
#!/bin/bash
#
#通过调用函数添加10个用户；

function adduser {
	if id $username &> /dev/null;then
		echo "$username exists."
		return 1
	else
		useradd $username
		[ $? -eq 0 ] && echo "Add $username finished." && return 0
	fi
}

for i in {1..10};do
	username=myuser$i
	adduser
done
~~~

- Example：编写一个服务脚本：

~~~shell
#!/bin/bash
# chkconfig: - 88 12
# description: test service script
prog=$(basename $0)
lockfile=/var/lock/subsys/$prog
start() {
	if [ -e $lockfile ];then
		echo "$prog is already running."
		return 0
	else 
		touch $lockfile
		[ $? -eq 0 ] && echo "Starting $prog finished."
	fi
}
stop() {
	if [ -e $lockfile ];then
		rm -f $lockfile && echo "Stop $prog ok."
	else
		echo "$prog is stopped yet."
	fi
}
status() {
	if [ -e $lockfile ];then
		echo "$prog is running."
	else
		echo "$prog is stopped."
	fi
}
usage() {
	echo "Usage:$prog {start | stop | restart | status}"
}
if [ $# -lt 1 ];then
	usage
	exit 1
fi

case $1 in
start)
	start
	;;
stop)
	stop
	;;
restart)
	stop
	start
	;;
status)
	status
	;;
*)
	usage
esac
~~~

### 函数返回值：
函数的执行结果返回值：

	(1)	使用echo或print命令进行输出；

	(2)	函数体中调用命令的执行结果；

函数的退出状态码：

	(1)	默认取决于函数体中执行的最后一条命令的退出状态码；

	(2)	自定义退出状态码；

		使用return关键字；

函数可以接受参数：

		传递参数给函数：调用函数时，在函数名后面以空白分隔给定参数列表即可；

例如：“testfunc arg1 arg2 …”

	在函数体当中，可使用$1,$2,….调用这些参数；还可以使用$@,$*,$#等特殊变量；

~~~shell
#!/bin/bash
#
#使用带参数的函数添加10个用户；

function adduser {			#一个可接受参数的函数
	if [ $# -lt 1 ];then
		return 2   # 2: no arguments
	fi
	
	if id $1 &> /dev/null;then
		echo "$1 exists."
		return 1;
	else
		useradd $1
		[ $? -eq 0 ] && echo "Add $1 finished." && return 0
	fi	
}

while true;do		#死循环，直到输入的值为quit是退出；
	read -t 20 -p "Please input your username(quit to cancel):" username
	if [ $username == "quit" ];then
		echo "Quit."
		exit 0
	else
		adduser $username
	fi
done
~~~

- 变量作用域：

	本地变量：当前shell进程，为了执行脚本会启动专用的shell进程；因此，本地变量的作用范围是当前shell脚本程序文件；

	局部变量：函数的生命周期：函数结束时变量被自动销毁；

		如果函数中有局部变量，其名称同本地变量无关；

在函数中定义局部变量的方法：

		local NAME=VALUE

函数递归：函数直接或间接调用自身；

- Example:求N的阶乘：
```
N！=N（N-1）(N-2)….1
	N(N-1)!=N(N-1)(N-2)!
```

~~~shell
#!/bin/bash
#
#利用函数递归求N的阶乘；

fact() {
	if [ $1 -eq 0 -o $1 -eq 1 ];then
		echo 1
	else
		echo $[$1*$(fact $[$1-1])]
	fi
}
fact 5
~~~

- Example:求n阶斐波拉契数列：
~~~shell
#!/bin/bash
#
#函数递归：求n阶斐波拉契数列的第n项；

fab() {
	if [ $1 -eq 1 ];then
		echo 1
	elif [ $1 -eq 2 ];then
		echo 1
	else
		echo $[$(fab $[$1-1])+$(fab $[$1-2])]
	fi
}

fab 7
~~~

[返回目录](#目录)


## <span id="18数组">18数组</span>
### 数组：

	变量：存储单个元素的内存空间；
```
	数组：存储多个元素的连续的内存空间；
		数组名
		索引：编号从0开始，属于数值索引；
			注意：索引页可支持使用自定义的格式，而不仅仅是数值格式；
				bash的数组支持稀疏格式；
```

- 引用数组中的元素： ${ARRAY_NAME[INDEX]}

- 声明数组：

	declare –a ARRAY_NAME

	declare –A ARRAY_NAME : 关联数组；

- 数组元素的赋值：

	(1)	一次只赋值一个元素；

		ARRAY_NAME[INDEX]=VALUE

```
	例如：
			weekdays[0]="Sunday"
			weekdays[4]="Thursday"
```

	(2)	一次赋值全部元素：

			ARRAY_NAME=("VAL1" "VAL2" "VAL3" ...)

	(3)	只赋值特定元素：

			ARRAY_NAME=([0]="VAL1" [3]="VAL2" ...)

	(4)	read -a ARRAY 

- 引用数组元素：${ARRAY_NAME[INDEX]}

	注意：省略[INDEX]表示引用下标为0的元素；

- 数组的长度(数组中元素的个数)：

	${#ARRAY_NAME[*]}
	${#ARRAY_NAME[@]}

- Example:生成10个随机数保存于数组中，并找出其最大值和最小值：

~~~shell
#!/bin/bash
#
#生成10个随机数保存于数组中；

declare -a rand
declare -i max=0
declare -i min=0

for i in {0..9};do
	rand[$i]=$RANDOM
	if [ $i -eq 1 ];then
		max=${rand[$i]}
		min=${rand[$i]}
	fi
	echo ${rand[$i]}
	[ ${rand[$i]} -gt $max ] && max=${rand[$i]}
	[ ${rand[$i]} -lt $min ] && min=${rand[$i]}
done

echo "Max:$max"
echo "Min:$min"
~~~

- Example：写一个脚本：定义一个数组，数组中的元素是/var/log目录下所有以.log结尾的文件；要统计其下标为偶数的文件中的行数之和；

~~~shell
#!/bin/bash
#
#定义一个数组，数组中的元素是/var/log目录下所有以.log结尾的文件；
#要统计其下标为偶数的文件中的行数之和；

declare -a files
files=(/var/log/*.log)
declare -i lines=0

for i in $(seq 0 $[${#files[*]}-1]);do
	if [ $[$i%2] -eq 0 ];then
		let lines+=$(wc -l ${files[$i]} | cut -d' ' -f1)
	fi
done 

echo "Lines:$lines."
~~~

### 引用数组中的元素：

- 所有元素：${ARRAY[@]} , ${ARRAY[*]}
	
- 数组切片：${ARRAY[@]:offset:number}

		offset:要跳过的元素个数；

		number：要取出的元素个数，取偏移量之后的所有元素：${ARRAY[@]:offset};

- 向数组中追加元素：ARRAY[${ARRAY[*]}]

- 删除数组中的某元素：unset ARRAY[INDEX]

- 关联数组：
~~~shell
	declare –A ARRAY_NAME
	ARRAY_NAME=([index_name1]=’val1’ [index_name2]=’val2’ ….)
~~~

~~~shell
[root@kalaguiyin scripts]# declare -a array
[root@kalaguiyin scripts]# #声明一个数组，不是必要的
[root@kalaguiyin scripts]# array=(0 1 2)
[root@kalaguiyin scripts]# array=([0]=0 [1]=1 [2]=v2)
[root@kalaguiyin scripts]# array[0]=5
[root@kalaguiyin scripts]# echo $array
5
[root@kalaguiyin scripts]#
~~~

~~~shell
[root@kalaguiyin scripts]# #以空白作为分隔符拆分字符串为数组
[root@kalaguiyin scripts]# str="1 2 3"
[root@kalaguiyin scripts]# array=($str)
[root@kalaguiyin scripts]# echo $array
1
[root@kalaguiyin scripts]#
~~~

~~~shell
[root@kalaguiyin scripts]# #使用其他分隔符拆分字符串为数组，需指定IFS
[root@kalaguiyin scripts]# IFS=: array=($PATH)
[root@kalaguiyin scripts]# echo $array
/usr/local/sbin
~~~

- 引用数组元素：
	$array  ${array}  ${array[0]}  #第0个元素

	${array[n]}  #第n个元素（n从0开始计算）

- 引用整个数组：
	${array[*]}  ${array[@]}   这两种方式等同，会把数组展开。

	${array[*]}  表示把数组拼接在一起的整个字符串，如果作为参数传递，会把整个字符串作为一个参数。

	${array[@]}  如果作为参数传递，表示把数组中每个元素作为一个参数，数组有多少个元素，就会展开成多少个参数。

- 计算数组元素长度；

	${#array[*]}  ${#array[@]}   不是 ${#array}，因为它等同于 ${#array[0]}

- 遍历数组：
	for i in "${array[@]}";do 
		echo $i;
	done

[返回目录](#目录)


## <span id="19bash的字符串处理工具">19bash的字符串处理工具</span>

### 字符串切片：
- ${var:offset:number}

	取字符串最右侧的几个字符：${var: -lengh}

	Note：冒号后面必须有一空白字符；

### 基于模式取子串：
- ${var#*word}：其中word可以是指定的任意字符；

	功能：自左而右，查找var变量所存储的字符串中，第一次出现的word, 删除字符串开头至第一次出现word字符之间的所有字符；

- ${var##*word}：其中word可以是指定的任意字符；

	功能：自左而右，查找var变量所存储的字符串中出现的word，删除字符串开头至最后一次由word指定的字符之间的所有内容；

~~~shell
#bash基于模式取子串；
[root@kalaguiyin scripts]# file="/var/log/messages"
[root@kalaguiyin scripts]# echo ${file#*/}
var/log/messages
[root@kalaguiyin scripts]# echo ${file##*/}
messages
~~~	

- ${var%word*}：其中word可以是指定的任意字符；

	功能：自右而左，查找var变量所存储的字符串中，第一次出现的word, 删除字符串最后一个字符向左至第一次出现word字符之间的所有字符；

- ${var%%word*}：其中word可以是指定的任意字符；

	功能：自右而左，查找var变量所存储的字符串中出现的word,，只不过删除字符串最右侧的字符向左至最后一次出现word字符之间的所有字符；

~~~shell
[root@kalaguiyin scripts]# file="/var/log/messages" #从右至左，匹配‘/ ’
[root@kalaguiyin scripts]# echo ${file%/*}
/var/log
[root@kalaguiyin scripts]# echo ${file%%/*}    # 双%匹配并删除后为空；

[root@kalaguiyin scripts]#
~~~

- Example：url=http://www.google.com:80,分别取出协议和端口；
~~~shell
[root@kalaguiyin scripts]# url=http://www.google.com:80
[root@kalaguiyin scripts]# echo ${url##*:}    #取端口号
80
[root@kalaguiyin scripts]# echo ${url%%:*}   #取协议
http
[root@kalaguiyin scripts]#
~~~

### 查找替换：
	${var/pattern/substi}：查找var所表示的字符串中，第一次被pattern所匹配到的字符串，以substi替换之；

	${var//pattern/substi}: 查找var所表示的字符串中，所有能被pattern所匹配到的字符串，以substi替换之；

	${var/#pattern/substi}：查找var所表示的字符串中，行首被pattern所匹配到的字符串，以substi替换之；

	${var/%pattern/substi}：查找var所表示的字符串中，行尾被pattern所匹配到的字符串，以substi替换之；

~~~shell
[root@kalaguiyin scripts]# var=$(head -n 1 /etc/passwd)
[root@kalaguiyin scripts]# echo $var
root x 0 0 root /root /bin/bash
[root@kalaguiyin scripts]# echo ${var/root/ROOT}  #替换第一次匹配到的root为ROOT
ROOT x 0 0 root /root /bin/bash
[root@kalaguiyin scripts]# echo ${var//root/ROOT}  #替换所有匹配到的root为ROOT
ROOT x 0 0 ROOT /ROOT /bin/bash
~~~

~~~shell
[root@kalaguiyin scripts]# useradd bash -s /bin/bash
[root@kalaguiyin scripts]# cat /etc/passwd | grep "^bash.*bash$"
bash:x:1029:1029::/home/bash:/bin/bash
[root@kalaguiyin scripts]# line=$(cat /etc/passwd | grep "^bash.*bash$")
[root@kalaguiyin scripts]# echo $line
bash x 1029 1029  /home/bash /bin/bash
[root@kalaguiyin scripts]# echo ${line/#bash/BASH}	#替换行首的bash为BASH
BASH x 1029 1029  /home/bash /bin/bash
[root@kalaguiyin scripts]# echo ${line/%bash/BASH}	#替换行尾的bash为BASH
bash x 1029 1029  /home/bash /bin/BASH
~~~

### 查找并删除：
	${var/pattern}：查找var所表示的字符串中，删除第一次被pattern所匹配到的字符串

	${var//pattern}：查找var所表示的字符串中，删除所有被pattern所匹配到的字符串；

	${var/#pattern}：查找var所表示的字符串中，删除行首被pattern所匹配到的字符串；

	${var/%pattern}：查找var所表示的字符串中，删除行尾被pattern所匹配到的字符串；

- Example：
~~~shell
[root@kalaguiyin scripts]# line=$(tail -n 1 /etc/passwd)
[root@kalaguiyin scripts]# echo $line
bash x 1029 1029  /home/bash /bin/bash
[root@kalaguiyin scripts]# echo ${line/bash}  #查找并删除第一次匹配到的bash
 x 1029 1029  /home/bash /bin/bash
[root@kalaguiyin scripts]# echo ${line//bash}	#查找并删除所有匹配到的bash
 x 1029 1029  /home/ /bin/
[root@kalaguiyin scripts]# echo ${line/#bash}	#查找并删除匹配到的行首的bash
 x 1029 1029  /home/bash /bin/bash
[root@kalaguiyin scripts]# echo ${line/%bash}   #查找并删除匹配到的行尾bash
bash x 1029 1029  /home/bash /bin/
~~~

### 字符大小写转换：
	${var^^}：把var中的所有小写字母转换为大写；

	${var,,}：把var中的所有大写字母转换为小写；

- Example：

~~~shell
[root@kalaguiyin scripts]# line=$(tail -n 1 /etc/fstab)		#将文件最后一行的值赋值给变量
[root@kalaguiyin scripts]# echo ${line^^}		#全部转换为大写后输出
/DEV/MAPPER/CENTOS-SWAP SWAP                    SWAP    DEFAULTS        0 0
[root@kalaguiyin scripts]# line=`echo ${line^^}`	#将转换为大写后的值在赋值给变量；
[root@kalaguiyin scripts]# echo $line			#确认目前变量的值全为大写字母；
/DEV/MAPPER/CENTOS-SWAP SWAP                    SWAP    DEFAULTS        0 0
[root@kalaguiyin scripts]# echo ${line,,}		#全部转换为大写后输出；
/dev/mapper/centos-swap swap                    swap    defaults        0 0
~~~

### 变量赋值：

	${var:-value}：如果var为空或未设置，那么返回value；否则，则返回var的值；

	${var:=value}：如果var为空或未设置，那么返回value，并将value赋值给var；否则，则返回var的值；

- Example：

~~~shell
[root@kalaguiyin scripts]# echo $test		#变量值为空；

[root@kalaguiyin scripts]# echo ${test:-helloworld}	#  :- 仅返回设定值，不修改；
helloworld
[root@kalaguiyin scripts]# echo $test      #变量的值依然为空；

[root@kalaguiyin scripts]# echo ${test:=helloworld}   #  :=  返回设定值，并赋值给变量；
helloworld
[root@kalaguiyin scripts]# echo $test			# 变量值已修改；
helloworld
~~~

[返回目录](#目录)


## <span id="20S脚本的配置文件">20脚本的配置文件</span>
###步骤：
-	(1) 定义文本文件，每行定义“name=value”
-	(2) 在脚本中source此文件即可

~~~shell
[root@kalaguiyin scripts]# touch /tmp/config.test #创建配置文件；
[root@kalaguiyin scripts]# echo "name=kalaguiyin" >> /tmp/config.test #在配置文件中定义变量；
[root@kalaguiyin scripts]# vim script_configureFile.sh		#编写脚本，导入配置文件；如内容所示；
[root@kalaguiyin scripts]# bash script_configureFile.sh 	#脚本执行结果；
kalaguiyin
[root@kalaguiyin scripts]# cat script_configureFile.sh 
#!/bin/bash
#
source /tmp/config.test		#导入配置文件，脚本自身并未定义变量；

echo $name				#引用的是配置文件中的变量name
~~~
[返回目录](#目录)
