# Linux Shell

[TOC]

## 理解 shell

### shell 的父子关系

#### 父 shell
* 用于登录某个虚拟控制器终端或在 GUI 中运行终端仿真器时所启动的默认的交互 shell，是一个 父 shell。

#### 子 shell
* 在 CLI 提示符后输入 /bin/bash 命令或其它等效的 bash 命令时，会创建一个新的 shell 程序。 这个 shell 程序被称为子 shell (child shell)。在生成 子shell 进程时，只有部分父进程的环境被复制到 子shell 环境中。   
* 子shell (child shell，也叫 subshell) 可以从 父shell 中创建，也可以从另一个子 shell 中创建。

### 进程列表

#### 命令列表:
* 用分号 (;) 分隔起来的一组命令。

#### 进程列表
* 进程列表是用括号包围起来的一组命令，它能够创建出 子shell 来执行这些命令，甚至可以在进程列表中嵌套括号来创建 子shell的 子shell。
* 进程列表是一种命令分组 (command grouping)。 另一种命令分组是将命令放入花括号中，并在命令列表尾部加上分号 (;)。语法为 { command }。使用花括号进行命令分组并不会像进程列表那样创建出子 shell。
* 要想知道是否生成了子 shell，得借助一个使用了环境变量的命令。即: `echo $BASH_SUBSHELL`，如果该命令返回0，就表明没有子 shell，如果返回1或者更大的数字，就表明存在子 shell。

### 别出心裁的子shell用法

#### sleep
delay for a specified amount of time

####  在CLI中运用子 shell 的创造性方法之一就是将进程列表置入后台模式

#### 协程
* 协程可以同时做两件事。它在后台生成一个子 shell，并在这个子 shell 中执行命令。要进行协程处理，得使用 coproc 命令，还有要在 子shell 中执行的命令。如：

  ```bash
  coproc sleep 10
  ```
* 默认情况下使用 jobs 命令查看使用了 coproc 命令的协程时显示的 COPROC 是
coproc 命令给进程起的名字。可以使用命令的扩展语法设置这个名字，如：
	```bash
  coproc My_Job { sleep 10; }
  #扩展语法写起来有点麻烦。必须确保在第一个花括号 ( { ) 和命令名之间有一个空格。还必须保证命令以分号 ( ; ) 结尾。另外，分号和闭花括号 ( } ) 之间也得有一个空格。
  ```
  ** 理解 shell 的内建命令

*** 外部命令

    外部命令, 有时候也被称为文件系统命令, 是存在于 bash shell 之外的程序. 它们并
    不是 shell 程序的一部分. 外部命令程序通常位于 /bin, /usr/bin, /sbin 或
    /usr/sbin 中
    
    当外部命令执行时, 会创建出一个子进程. 这种操作被称为衍生 ( forking ).

**** which

     shows the full path of (shell) commands

***** usage

****** -a 

       Print all matching executables in PATH, not just the first

**** type

     write a description of command type

***** usage

****** 不加任何参数

       不加任何参数时, type 会显示出 name 是外部命令还是 bash 内置命令
       * file: 表示为外部命令;
       * alias: 表示该命令为命令别名所设置的名称;
       * builtin: 表示该命令为 bash 内置的命令功能;

****** -p

       如果后面接的 name 为外部命令时, 才会显示完整文件名 

****** -a

       会由 PATH 变量定义的路径中, 将所有含 -a 后字符串的命令都列出来

*** 内建命令

    内建命令和外部命令的区别在于前者不需要使用子进程来执行. 它们已经和shell编译
    成了一体, 作为shell工具的组成部分存在. 不需要借助外部程序文件来运行.
    
    对于有多种实现的命令, 如果想要使用其外部命令实现, 直接指明对应的文件就可以
    了.

**** 使用history命令

     bash 命令的历史记录是先存放在内存中, 当 shell 退出时才被写入到历史文件中.
    
     可以在退出 shell 会话之前强制将命令历史记录写入 .bash_history 文件. 要实现
     强制写入, 需要使用 history 命令的 -a 选项.

***** HISTSIZE 环境变量

      保存在 bash 历史记录中的命令数

***** 输入!!, 然后按回车键就能够唤出刚刚用过的那条命令来使用

***** 你可以唤回历史列表中的任意一条命令. 只需要输入惊叹号和命令在历史列表中的编号即可.

**** 命令别名

     要查看当前可用的别名, 使用 alias 命令以及选项 -p.
    
     要注意, 因为命令别名属于内部命令, 一个别名仅在它所被定义的 shell 进程中才有
     效.

* 使用 Linux 环境变量 (environment variable)

** 全局环境变量

   全局环境变量对于shell会话和所有生成的子shell都是可见的. 局部变量则只对创建它
   们的shell可见.

   系统环境变量基本上都是使用全大写字母, 以区别于普通用户的环境变量.

*** 要查看全局变量, 可以使用 env 或 printenv 命令

    要显示个别环境变量的值, 可以使用 printenv variable 的形式 ( 没有 $ ), 但不要
    使用 env 命令.

**** env

     run a program in a modified environment

**** printenv

     print all or part of environment

*** 也可以使用 echo 显示变量的值

    在这种情况下引用某个环境变量的时候, 必须在变量前面加上一个 ~美元符~ ( $ ),
    变量名前加上 $ 能够使其作为命令行参数. 如: ls $HOME

** 局部环境变量

   局部环境变量只能在定义它们的进程中可见

** 删除环境变量

*** unset

    unset values and attributes of variables and functions

** 默认的 shell 环境变量

*** bash shell 支持的 Bourne 变量

**** CDPATH

     冒号分隔的目录列表, 作为 cd 命令的搜索路径

**** PS1

     shell 命令行界面的主提示符

**** PS2

     shell 命令行界面的次提示符

*** bash shell 环境变量

**** EMACS

     设置为 t 时, 表明 emacs shell 缓冲区正在工作, 而行编辑功能被禁止

**** RANDOM

     返回一个0~32767的随机数 (对其的赋值可作为随机数生成器的种子)

*** 设置 PATH 环境变量

    PATH 环境变量定义了用于进行命令和程序查找的目录
    
    PATH 中的目录使用冒号分隔
    
    如果命令或者程序的位置没有包括在 PATH 变量中, 那么如果不使用绝对路径的话,
    shell 是没法找到的.
    
    把新的搜索目录添加到现有的 PATH 环境变量中:
    
    #+begin_src shell
      PATH=$PATH:/home/nobelheaven/bin
    #+end_src
    
    你只需引用原来的 PATH 值, 然后再给这个字符串添加新目录就行了
    
    程序员通常的办法是将单点符 (.) 也加入 PATH 环境变量

** 定位系统环境变量

   启动 bash shell 有三种方式:

   * 登录时作为默认登录 shell
   * 作为非登录 shell 的交互式 shell
   * 作为运行脚本的非交互 shell

   如果 bash 是作为交互式 shell 启动的, 它就不会访问 /etc/profile 文件, 只会检查
   用户 HOME 目录中的 .bashrc 文件

   系统执行 shell 脚本时用的是非交互式 shell

*** BASH_ENV 环境变量
    当 shell 启动一个非交互式 shell 进程时, 它会检查这个环境变量来查看要执行的启
    动文件. 如果有指定的文件, shell 会执行该文件里的命令, 这通常包括 shell脚本变
    量设置.

   如果变量未设置:

   * printenv 命令只会返回 CLI 提示符
   * echo 命令会显示一个空行, 然后返回 CLI 提示符

*** 环境变量持久化
    
    对全局环境变量来说, 最好是在 /etc/profile.d 目录中创建一个以 .sh 结尾的文件.
    把所有新的或修改过的全局环境变量设置放在这个文件中.
    
    在大多数发行版中, 存储个人用户永久性 bash shell 变量的地方是 $HOME/.bashrc
    文件.

** 数组变量

   数组是能够存储多个值的变量

   要给某个环境变量设置多个值, 可以把值放在括号里, 值与值之间用空格分隔. 如:

   #+begin_src shell
     $ mytest=(one two three four five)
   #+end_src

   要引用一个单独的数组元素, 就必须用代表它在数组中位置的数值索引值. 索引值要用
   方括号括起来. 环境变量数组的索引值都是从零开始:

   #+begin_src shell
     $ echo ${mytest[2]}
     three
   #+end_src

   要显示整个数组变量, 可用星号作为通配符放在索引值的位置:

   #+begin_src shell
     $ echo ${mytest[*]}
     one two three four five
   #+end_src

   也可以改变某个索引值位置的值:

   #+begin_src shell
     $ mytest[2]=seven
     $
     $ echo ${mytest[*]}
     one two seven four five
   #+end_src

   甚至能用 unset 命令删除数组中的某个值:

   #+begin_src shell
     $ unset mytest[2]
     $
     $ echo ${mytest[*]}
     one two four five
     $
     $ echo ${mytest[2]}

     $ echo ${mytest[3]}
     four
   #+end_src

   最后, 可以在 unset 命令后跟上数组名来删除整个数组:

   #+begin_src shell
     $ unset mytest
     $
     $ echo ${mytest[*]}

     $
   #+end_src

* 理解 linux 文件权限

** useradd

*** -D
*** -m

** userdel

*** -r

** usermod

*** -l
*** -L
*** -p
*** -U
*** -g
*** -G

** passwd
** chpasswd
** chsh

*** -s 必须用 shell 的全路径名作为参数

** chfn
** chage
** finger
** 使用 linux 组

   组密码允许非组内成员通过它临时成为该组成员

   当一个用户在 /etc/passwd 文件中指定某个组作为默认组时，用户账户不会作为该组成
   员再出现在 /etc/group 文件中

*** groupadd
*** groupmod

**** -g
**** -n

** 默认文件权限

*** 八进制模式的安全性设置

    八进制模式的安全性设置先获取这３个 rwx 权限的值，然后将其转换成３位二进制值，
    用一个八进制值来表示。在这个二进制表示中，每个位置代表一个二进制位。因此，如
    果读权限是唯一置位的权限，权限值就是 r--，转换成二进制值就是１００，代表的八进
    制值是４。
    
    用户创建的文件的默认权限为６６６（-rw-rw-rw），目录的默认权限为７７７
    （rwxrwxrwx）。
    
    umask 的值指的是要从默认值减去的权限。

** 改变安全性设置

*** chmod

    可以使用八进制模式或符号模式来表示权限

**** 八进制模式（直接使用期望赋予文件的标准３位八进制权限码即可）

     如：
     chmod 790 newfile

**** 符号模式

     [ugoa...][[+-=][rwxXstugo...]
     
     第一组字符定义了权限作用的对象：
    
     * u 代表用户
    
     * g 代表组
    
     * o 代表其他
    
     * a 代表上述所有
    
     第二组符号表示在原有权限基础上增加权限（＋），还是移除权限（－），或是将权
     限设置成指定值（＝）
    
     第三组符号表示：
    
     * X：如果对像是目录或者其它已有执行权限，赋予执行权限
    
     * s：运行时重新设置UID或GID
    
     * t：保留文件或目录
    
     * u：将权限设置为跟属主一样
    
     * g：将权限设置为跟属组一样
    
     * o：将权限设置为跟其他用户一样

* 构建基本脚本
** 使用多个命令

   如果要两个命令一起运行，可以把它们放在同一行中，彼此间用分号隔开
** 创建 shell 脚本文件

   在创建 shell 脚本文件时，必须在文件的第一行指定要使用的 shell。其格式为：

   #+begin_src shell
     #!/bin/bash
   #+end_src

   在通常的 shell 脚本中，井号（#）用作注释行。shell 并不会处理 shell 脚本中的注
   释行。然而，shell 脚本文件的第一行是个例外，# 后面的惊叹号会告诉 shell 用哪个
   shell 来运行脚本。
** 显示消息

   注意，默认情况下，使用 echo 显示消息时不需要使用引号将要显示的文本字符串划定
   出来。

** 使用变量

*** 用户变量

    使用等号将值赋给用户变量. 在变量, 等号和值之间不能出现空格.
    
    shell 脚本会自动决定变量值的数据类型. 在脚本的整个生命周期里, shell 脚本中定
    义的变量会一直保持着它们的值, 但在 shell 脚本结束时会被删除掉.
    
    引用一个变量值时需要使用美元符, 而引用变量来对其进行赋值时则不要使用美元符.

*** 命令替换

    有两种方法可以将命令输出赋给变量：
    
    * 反引号字符 (`), 使用一对反引号(`)将整个命令行命令围起来.
    
    * $() 格式
    
    命令替换允许你将 shell 命令的输出赋给变量.
    
    shell 会运行命令替换符号中的命令, 并将其输出赋给变量. 注意, 赋值等号和命令替
    换字符之间没有空格.
    
    命令替换会创建一个 子shell 来运行对应的命令. 在命令行提示符下使用路径 ./ 运
    行命令的话, 也会创建出 子shell; 要是运行命令的时候不加入路径, 就不会创建 子shell.

** 重定向输入和输出

*** 输出重定向

**** >

     覆盖

**** >>

     累加

*** 输入重定向

**** <

     覆盖

**** <<

     累加

**** wc

     print newline, word, and byte counts for each file
    
     默认会输出三个值：
    
     * 文本的行数
    
     * 文本的词数
    
     * 文本的字节数

**** 内联输入重定向 (inline input redirection)

     内联输入重定向符号是远小于号 (<<). 除了这个符号, 你必须指定一个文本标记来划
     分输入数据的开始和结尾.
    
     #+begin_src shell
       $ wc <<EOF
       test string 1
       test string 2
       test string 3
       EOF
     #+end_src
    
     在命令行上使用内联输入重定向时, shell 会用 PS2 环境变量中定义的次提示符来提
     示输入数据. 次提示符会持续提示, 以获取更多的输入数据, 直到你输入了作为文本
     标记的那个字符串.

** 管道

   管道被放在命令之间, 将一个命令的输出重定向到另一个命令中：

   #+begin_src shell
     command1 | command2
   #+end_src

   Linux 系统实际上会同时运行这两个命令, 在系统内部将它们连接起来. 在第一个命令
   产生输出的同时, 输出会被立即送给第二个命令. 数据传输不会用到任何中间文件或缓
   冲区.

** 执行数学运算

*** expr

    evaluate expressions
    
    在脚本和命令行中使用时有些特殊字符需转义.

*** 使用方括号

    在 bash 中, 在将一个数学运算结果赋给某个变量时, 可以用美元符和方括号 ( $[
    operation ] ) 将数学表达式围起来.
    
    #+begin_src shell
      $ var1=$[1+5]
      $ echo $var1
      6
    #+end_src
    
    在使用方括号来计算表达式时不用转义特殊符号. 
    
    Bash shell 数学运算只支持整数运算. zsh 提供了完整的浮点数算术操作.

*** 浮点解决方案

**** bc
     
     An arbitrary precision calculator language

***** usage

****** scale=num

       设置运算结果保留的小数位数.

****** -q

       不显示欢迎信息.

****** print

       打印变量和数字.

**** 在脚本中使用 bc

     可以用命令替换运行 bc 命令, 并将输出赋给一个变量：
    
     #+begin_src shell
       variable=$(echo "options; expression" | bc)
     #+end_src
    
     第一部分 options 允许你设置变量. 如果你需要不止一个变量, 可以用分号将其隔开.
     expression 参数定义了通过 bc 执行的数学表达式. 例如：var1=$(echo "scale=4;
     3.44 / 5" | bc)


     还可以使用内联输入重定向.

** 退出脚本

   shell 中运行的每个命令都使用 ~退出状态码~ (exit status) 告诉 shell 它已经运行
   完毕. 退出状态码是一个 0~255 的整数值, 在命令结束运行时由命令传给 shell. 可以
   捕获这个值并在脚本中使用.

*** 查看退出状态码
    
**** $?

     Linux 提供了一个专门的变量 $? 来保存上个已执行命令的退出状态码。
    
     对于需要进行检查的命令，必须在其运行完毕后立刻查看或使用 $? 变量。它的值会
     变成由 shell　所执行的最后一条命令的退出状态码。

**** 按照惯例，一个成功结束的命令的退出状态码是０。如果一个命令结束时有错误，退出状态码就是一个正数值。

**** Linux 错误退出状态码没有什么标准可循，但有一些可用的参考

     | 状态码 | 描述                       |
     |--------+----------------------------|
     |      0 | 命令成功结束               |
     |      1 | 一般性未知错误             |
     |      2 | 不适合的shell命令          |
     |    126 | 命令不可执行               |
     |    127 | 没找到命令                 |
     |    128 | 无效的退出参数             |
     |  128+x | 与Linux信号x相关的严重错误 |
     |    130 | 通过 Ctrl+C 终止的命令     |
     |    255 | 正常范围之外的退出状态码               |

*** exit 命令

    默认情况下，shell脚本会以脚本中的最后一个命令的退出状态码退出。
    
    exit 命令允许你在脚本结束时指定一个退出状态码。例如：exit 5。也可以在 exit
    命令的参数中使用变量。例如：
    
    var1=10 
    exit var1
    
    当指定的退出状态码大于 255 时，shell 会通过模运算 (即除以 256 后的余数) 将退
    出状态码缩减到 0~255 之间。

* 使用结构化命令

** 使用 if-then 语句

*** if-then 语句有如下格式

    #+begin_src shell
      if command
      then
          commands
      fi
    #+end_src
    
    bash shell 的 if 语句会运行 if 后面的那个命令. 如果该命令的退出状态码是 0 (
    该命令运行成功 ), 位于 then 部分的命令就会被执行. 如果该命令的退出状态码是其
    他值, then 部分的命令就不会被执行, bash shell 会继续执行脚本中的下一个命令.
    fi 语句用来表示 if-then 语句到此结束.
    
    在 then 部分, 你可以使用不止一条命令. 可以像在脚本中的其他地方一样在这里列出
    多条命令. bash shell 会将这些命令当成一个块, 如果 if 语句行的命令的退出状态
    码为 0 , 所有的命令都会被执行; 如果 if 语句行的命令的退出状态码不为 0 ,所有
    的命令都会被跳过.
    
    if-then 语句的另一种形式:
    
    #+begin_src shell
      if command; then
          commands
      fi
    #+end_src
    
    通过把分号放在待求值的命令尾部, 就可以将 then 语句放在同一行了.

** if-then-else 语句

*** if-then-else 语句在语句中提供了另外一组命令

    #+begin_src shell
      if command
      then
          commands
      else
          commands
      fi
    #+end_src
    
    当 if 语句中的命令返回退出状态码 0 时, then 部分中的命令会被执行, 这跟普通的
    if-then 语句一样. 当 if 语句中的命令返回非零退出状态码时, bash shell 会执行
    else 部分中的命令.
    
    跟 then 部分一样, else 部分可以包含多条命令. fi 语句说明 else 部分结束了.

** 嵌套 if

   嵌套的 if-then 语句位于主 if-then-else 语句的 else 代码块中. 但这种形式的嵌套
   不易阅读.

   可以使用 else 部分的另一种形式: elif. elif 使用另一个 if-then 语句延续 else
   部分:

   #+begin_src shell
     if command1
     then
         commands
     elif command2
     then
         more commands
     fi
   #+end_src

   elif 语句行提供了另一个要测试的命令, 这类似于原始的 if 语句行. 如果 elif 后命
   令的退出状态码是 0 , 则 bash 会执行第二个 then 语句部分的命令.

   记住, 在 elif 语句中, 紧跟其后的 else 语句属于 elif 代码块. 它们并不属于之前
   的 if-then 代码块.

   可以继续将多个 elif 语句串起来, 形成一个大的 if-then-elif 嵌套组合:

   #+begin_src shell
     if command1
     then
         command set 1
     elif command2
     then
         command set 2
     elif command3
     then
         command set 3
     elif command4
     then
         command set 4
     fi
   #+end_src

   每块命令都会根据命令是否会返回退出状态码 0 来执行. 记住, bash shell 会依次执
   行 if 语句, 只有第一个返回退出状态码 0 的语句中的 then 部分会被执行.

** test 命令

    test 命令提供了在 if-then 语句中测试不同条件的途径. 如果 test 命令中列出的条
    件成立, test 命令就会退出并返回退出状态码 0. 如果条件不成立, test 命令就会退
    出并返回非零的退出状态码, 这使得 if-then 语句不会再被执行.
    
    test 命令的格式:
    
    #+begin_src shell
      test condition
    #+end_src
    
    conditon 是 test 命令要测试的一系列参数和值. 当用在 if-then 语句中时, test
    命令看起来是这样的:
    
    #+begin_src shell
      if test condition
      then
          commands
      fi
    #+end_src
    
    如果不写 test 命令的 condition 部分, 它会以非零的退出状态码退出, 并执行 else
    语句块. 
    
    bash shell 提供了另一种条件测试方法, 无需在 if-then 语句中声明 test 命令:
    
    #+begin_src shell
      if [ condition ]
      then
          commands
      fi
    #+end_src
    
    方括号定义了测试条件. 注意, 第一个方括号之后和第二个方括号之前必须加上一个空
    格, 否则就会报错.
    
    test 命令可以判断三类条件:
    
    * 数值比较
    * 字符串比较
    * 文件比较

*** 数值比较
    
    test 命令的数值比较功能
    
    | 比较      | 描述                   |
    |-----------+------------------------|
    | n1 -eq n2 | 检查n1是否与n2相等     |
    | n1 -ge n2 | 检查n1是否大于或等于n2 |
    | n1 -gt n2 | 检查n1是否大于n2       |
    | n1 -le n2 | 检查n1是否小于或等于n2 |
    | n1 -lt n2 | 检查n1是否小于n2       |
    | n1 -ne n2 | 检查n1是否不等于n2     |

*** 字符串比较

    字符串比较测试
    
    | 比较         | 描述                   |
    |--------------+------------------------|
    | str1 = str2  | 检查str1是否和str2相同 |
    | str1 != str2 | 检查str1是否和str2不同 |
    | str1 < str2  | 检查str1是否比str2小   |
    | str1 > str2  | 检查str1是否比str2大   |
    | -n str1      | 检查str1的长度是否非0  |
    | -z str1      | 检查str1的长度是否为0  |
    
    在比较字符串的相等性时, 比较测试会将所有的标点和大小写情况都考虑在内
    
    大于号和小于号必须转义, 否则 shell 会把它们当作重定向符号, 把字符串值当作文
    件名.
    
    大于和小于顺序和 sort 命令所采用的不同.
    
    比较测试中使用的是标准的 ASCII 顺序, 根据每个字符的 ASCII 数值来决定排序结果.
    sort 命令使用的是系统的本地化语言设置中定义的排序顺序. 对于英语, 本地化设置
    指定了在排序顺序中小写字母出现在大写字母前.

*** 文件比较

    test 命令的文件比较功能
    
    | 比较            | 描述                                     |
    |-----------------+------------------------------------------|
    | -d file         | 检查file是否存在并是一个目录             |
    | -e file         | 检查file是否存在                         |
    | -f file         | 检查file是否存在并是一个文件             |
    | -r file         | 检查file是否存在并可读                   |
    | -s file         | 检查file是否存在并非空                   |
    | -w file         | 检查file是否存在并可写                   |
    | -x file         | 检查file是否存在并可执行                 |
    | -O file         | 检查file是否存在并属当前用户所有         |
    | -G file         | 检查file是否存在并且默认组与当前用户相同 |
    | file1 -nt file2 | 检查file1是否比file2新                   |
    | file1 -ot file2 | 检查file1是否比file2旧                   |

** 复合条件测试

   if-then 语句允许你使用布尔逻辑来组合测试, 有两种布尔运算符可用

   * [ condition1 ] && [ condition2 ]
   * [ condition1 ] || [ condition2 ]

** if-then 的高级特性

*** 使用双括号

    双括号命令允许你在比较过程中使用高级数学表达式. 双括号命令的格式如下:
    
    #+begin_src shell
      (( expression ))
    
      # expression 可以是任意的数学赋值或比较表达式
      # 注意, 不需要将双括号中表达式里的大于号转义. 这是双括号命令提供的另一个高级特性.
    #+end_src

*** 使用双方括号

    双方括号命令提供了针对字符串比较的高级特性. 双方括号命令的格式如下:
    
    #+begin_src shell
      [[ expression ]]
    #+end_src
    
    双方括号里的 expression 使用了 test 命令中采用的标准字符串比较. 但它提供了
    test 命令未提供的另一个特性: 模式匹配 ( pattern matching )
    
    在模式匹配中, 可以定义一个正则表达式来匹配字符串值.
    
    双等号将右边的字符串视为一个模式, 并应用模式匹配规则.

** case 命令

   case 命令会采用列表格式来检查单个变量的多个值:

   #+begin_src shell
     case variable in
         pattern1 | pattern2) commands1;;
         pattern3) commands2;;
         ,*) default commands;;
     esac
   #+end_src

   case 命令会将指定的变量与不同模式进行比较. 如果变量和模式是匹配的, 那么 shell
   会执行为该模式指定的命令. 可以通过竖线操作符在一行中分隔出多个模式. 星号会捕
   获所有与已知模式不匹配的值.

* 更多的结构化命令

** for 命令

   下面是 bash shell 中 for 命令的基本格式：

   #+begin_src shell
     for var in list
     do
         commands
     done
   #+end_src

   在 list 参数中，你需要提供迭代中要用到的一系列值。

   在 do 和 done 语句之间输入的命令可以是一条或多条标准的 bash shell 命令。在这
   些命令中，$var 变量包含着这次迭代对应的当前列表项中的值。

   *说明*

   只要你愿意，也可以将 do 语句和 for 语句放在同一行，但必须用分号将其同列表中的
   值分开：

   #+begin_src shell
     for var in list; do
   #+end_src

*** 读取列表中的复杂值

    for 命令用空格来划分列表中的每个值。如果在单独的数据值中有空格，就必须用双引
    号将这些值包起来。在某个值两边使用双引号时，shell 并不会将双引号当成值的一部
    分。

*** 更改字段分隔符

**** IFS

     内部字段分隔符 ( internal field separator )。
    
     IFS 环境变量定义了 bash shell 用作字段分隔符的一系列字符。默认情况下，bash
     shell 会将下列字符当作字段分隔符：
    
     * 空格
    
     * 制表符
    
     * 换行符

**** 临时更改 IFS 环境变量

     临时更改 IFS 环境变量，然后恢复之前的值：
    
     #+begin_src shell
       IFS.OLD=$IFS
       IFS=$'\n'
       <在代码中使用新的 IFS 值>
       IFS=$IFS.OLD
     #+end_src
    
     如果要指定多个 IFS 字符，只要将它们在赋值行串起来就行。如：
    
     #+begin_src shell
       IFS=$'\n':;"
       #将换行符、冒号、分号和双引号作为字段分隔符
     #+end_src

*** 用通配符读取目录

    可以使用 for 命令来自动遍历目录中的文件。进行此操作时，必须在文件名或路径名
    中使用通配符。它会强制 shell 使用文件扩展匹配。文件扩展匹配是生成匹配指定通
    配符的文件名或路径名的过程。

** C 语言风格的 for 命令

   以下是 bash 中 C 语言风格的 for 循环的基本格式：

   #+begin_src shell
     for (( variable assignment ; condition ; iteration process))
   #+end_src

   注意，有些部分并没有遵循 bash shell 标准的 for 命令：

   * 变量赋值可以有空格；

   * 条件中的变量不以美元符开头；

   * 迭代过程的算式未用 expr 命令格式。

** while 命令

*** while 的基本格式

    while 命令的格式是：
    
    #+begin_src shell
      while test command
      do
          other commands
      done
    #+end_src
    
    最常见的 test command 的用法是用方括号来检查循环命令中用到的 shell 变量的值。

*** 使用多个测试命令

    while 命令允许你在 while 语句行定义多个测试命令。只有最后一个测试命令的退出
    状态码会被用来决定什么时候结束循环。


