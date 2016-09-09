# Bash基础特性及shell脚本编程
- [1、命令历史](#01命令历史)  
- [2、命令补全](#02命令补全)  
- [3、路径补全](#03路径补全)  
- [4、命令行展开](#04命令行展开)
- [5、命令的执行状态结果](#05命令的执行状态结果)
- [6、命令别名Alias](#06命令别名Alias)
- [7、通配符glob](#07通配符glob)
- [8、Bash快捷键](#08Bash快捷键)
- [9、Bash的IO重定向及管道](#09Bash的IO重定向及管道)
- [10、shell编程环境](#10shell编程环境)
- [11、Bash变量类型](#11Bash变量类型)
- [12、Bash的配置文件](#12Bash的配置文件)
- [13、Bash中的算术运算符](#13Bash中的算术运算符)
- [14、Bash条件测试](#14Bash条件测试)
- [15、Bash脚本之用户交互](#15Bash脚本编程之用户交互)
- [16、Bash流程控制](#16Bash流程控制)
- [17、Bash数组](#17Bash数组)
- [18、Bash的字符串处理工具](#18Bash的字符串处理工具)
- [19、为脚本程序使用配置文件](#19为脚本程序使用配置文件)


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
