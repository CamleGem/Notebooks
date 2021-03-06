# Bash Shell

>**Author: TonyYang**  
>**Date：2020/04/05**

##### 查看Shell版本

如果bash
shell是你的登录shell，那么在你看到shell提示符以前，它要按照处理链条完成一系列处理。
在引导系统时，第一个需要运行的进程是init进程，pid为1，它衍生出一个gtty进程，该进程打开一个终端窗口，提供一块用户空间给标准输入，标准输出，标准错误，并把提示符显示在屏幕上，然后执行/bin/login程序提示输入密码，加密并验证密码，建立一个初始环境，启动登录shell，/etc/passwd文件的最后一项。bash寻找系统文件/etc/profile并执行其中的命令，然后在用户目录下寻找名字叫做.bash_profile的初始化文件。执行完.bash_profile文件中的命令以后，就处理用户环境文件中的命令，这个文件通常称为.bashrc,最后默认提示符美元符号将显示在你的屏幕上，等待输入命令。


####

```shell
$ bash --version
$ echo $BASH_VERSION

```

##### 改变当前工作的shell

```shell
chsh 命令
```

| 选项               | 功能                                |
|:------------------|:-----------------------------------|
| -l, --list-shells | 打印/etc/shells中合法shell的清单并退出 |
| -s, --shell       | 指定登录shell                        |
| -u, --help        | 打印使用方法信息并退出                 |
| -v, --version     | 打印版本信息并退出                    |

note：
当省略了参数以后，chsh会一直要求用户输入登录shell的完整路径名，如果不是完整路径，则会失败且无效。


#### shell 环境

一个进程的环境是由变量，打开的文件，当前的工作目录，函数，
资源限制和信号等组成。这些特征的定义是从一个shell到另外一个shell继承来的，用于配置工作环境。用户shell配置定义在shell的初始化文件中。

##### 初始化文件

bash
shell有很多初始化文件，这些初始化文件中的设置作为当前shell的一部分存在，读取哪些初始化文件取决于是否是登录shell，是否是交互模式（非登录Shell），或者非交互模式（shell脚本）。
在登录到系统，shell提示符出现在屏幕上之前，
系统初始化文件/etc/profile就被读取了，接着，如果用户主目录下存在
bash_profile文件，那么这个文件就会也被读取，它的作用是设置用户别名和函数，并建立用户指定的环境变量和启动脚本。
如果用户没有.bash_profile文件，但是存在.bash_login文件，那么就读取后者，如果后者不存在，
但是有profile，那么就读取.profile文件。

```
/etc/profile
该文件是由系统管理程序建立的一个泛系统的初始文件, 这是一个全局的配置文件, 系统登录就会加载该配置文件.

~/.bash_profile文件
假如用户的根目录里存在这个文件, bash就在读取完/etc/profile后加载这个配置文件.如果这个文件不存在, bash就会查找另一个用户自定义文件.bash_login并且加载它. 如果.bash_login文件也不存在, 则加载.profile(如果该文件存在的话). 只有.bash_profile, .bash_login, .profile三个文件中的一个会被加载. bash会检查用户是否有一个.bashrc文件,如果有就加载该配置文件.

~/.bashrc
该文件中通常包含用户自定义的别名和函数
当创建一个该用户的字shell时, 只会加载该配置文件, 上面提到的几个文件不会被再次加载.

/etc/bashrc
该文件属于全局文件, 任何用户在登陆之前都会读取这个配置文件,全局的函数和别名定义在该文件中.

~/.profile
.profile文件是一个用户自定义初始化文件, 保存在用户的主目录下, 一旦用户运行shell, 或者bash登录系统, 而以上的初始化文件都找不到的时候,就会读取该文件.它允许用户定制自己的shell环境,环境和终端设置通常保存在该文件中.窗口应用程序和数据库应用程序也都是从这里开始初始化的.

~/.bash_logout
用户退出登录, 即退出所登录的shell之时, 若存在一个名为~/.bash_logout的文件, 就执行它.该文件通常包含清除临时文件, 截断历史文件和记录退出登录时间等作用的文件.

```

##### 如果防止执行启动文件的选项。参考下面

```
如果bash被带 -noprofile选项的命令激活(如 bash -noprofile), /etc/profile, ~/.bash_login或~/.profile的启动文件将不会执行.
如果激活时带的是-p选项(譬如 bash -p), 则bash不会读用户的~/.profile文件.
假如它是作为sh被激活, 则bash就尽可能模仿Bourne shell环境.对于一个登陆shell, 它仅执行/etc/profile和~/.profile, -noprofile可能会继续使用而使该行为不能运行.假如shell是作为sh激活的,就不会执行任何其他的启动文件.
```


#### 用内置的set和shopt命令来设置bash的选项

```
set -o 选项. 使用-o选项开关, set命令就能选择是否接受选项.而使用各种选项就能自定义自己的shell环境.它们的开关与否通常都在BASH_ENV(ENV)文件内设定.例如, set -o noclobber也可以写成set -C.见下表:
```

#### 内置 set 选项表

|        选项名         | 开关缩写 | 功能                                                                                          |
|:--------------------:|:-------:|:---------------------------------------------------------------------------------------------|
|      allexport       |   -a    | 打开此开关，自动标记新的或者修改要传出的变量，直到重新关闭                                            |
|     braceexpand      |   -B    | 将花括号的展开式设置为默认值                                                                     |
|        emacs         |         | 进行命令行编辑，使用emacs内置编辑器，是默认设置                                                     |
|       errexit        |   -e    | 假如一个命令返回一个非0的退出状态值（运行失误）后退出 ，则读入启动文件的时候不做任何设置                   |
|      hisexpand       |   -H    | 当进行历史命令替换时，使！和！！符号有效。这是默认设置                                                |
|       history        |         | 使命令行历史记录开关打开。这个是个默认值                                                           |
|      ignoreeof       |         | 防止退出shell的时候使得EOF(ctrl-D)失效。必须输入退出命令。当设置shell变量时也同理操作。IGNOREEOF=10     |
|       keyword        |   -k    | 将键盘参数至于环境中，并作为一个命令看待                                                           |
| interactive-comments |         | 在交互状态下的shell中，以#开头的命令行是注解                                                       |
|       monitor        |   -m    | 运行工作控制                                                                                   |
|      noclobber       |   -C    | 防止重定向时文件被覆盖                                                                          |
|        noexec        |   -n    | 读入但不执行命令。该选项用于检查脚本语法错误，但在交互环境下失效                                       |
|        noglob        |   -f    | 使路径扩展式失效，也就是说，关闭了通配符                                                           |
|        notify        |   -b    | 后台工作完成后通知用户                                                                          |
|       nounset        |   -u    | 当扩展一个未设置的变量时，系统报错                                                                |
|        onecmd        |   -t    | 读入并执行完一个命令后退出                                                                       |
|       physical       |   -P    | 如果已经设置， 当键入cd或pwd时就不跟随符号链接，而使用实际目录                                        |
|        posix         |         | 如果默认操作没有匹配posix标准，则shell行为会发生改变                                                |
|      privileged      |   -p    | 设置之后，shell就不读入.profile或ENV文件，而且shell函数也不从环境中继承了。对于setuid脚本而言，则自动设置 |
|       verbose        |   -v    | 打开verbose模式进行调试                                                                        |
|          vi          |         | 使用vi内置编辑器来进行命令行编辑                                                                  |
|        xtrace        |   -x    | 打开”输出显示“模式以进行调试                                                                     |

```shell
set -o option #Turns on the option
set +o option #Turns off the option
set -[a-zA-Z] #Abbreviation for an option; the minus turns it on
set +[a-zA-Z] #Abbreviation for an option; the plus turns it off

Note: 有些选项有缩写形式可以使用第二种方式进行设置,与第一种的结果一样,但是如果选项没有缩写形式,则无法使用第二种方式进行设置.

set -o 命令可以列出所有的选项已经选项的开关状态.
```

#### shopt内置命令:

是用于新版本的bash中的命令，其作用和set命令基本相同。shopt命令很多方面都是和set一样的，但是对shell的设置增加了很多扩充。

```shell
shopt -p #输出所有可用的选项设置命令
shopt -u option #表明选项没有被设置
shopt -s option #表明目前已经被设置的选项
shopt -o option #显示选项的状态, 但是只能针对set所设置的选项。

```

#### 提示符

在交互模式下，shell的提示符提示用户输入。当用户看见提示符的时候，就知道输入命令了。bash有四种提示符：最基本的是"$"提示符；第二种是">";
第三，第四种分别是PS3和PS4.当shell在交互模式下运行的时候，提示符就会出现在屏幕上。当然你也可以替换提示符。当然了一般就是linux命令。变量PS2
是第二种提示符，默认的是">"符号。假如你输入的是部分，或称为不完全的命令，并敲了回车，">"符号就会出现。你也可以用其他的符号来变换第一或第二种提示
符。
***基本提示符***。即$符号，是默认的基本提示符。你也可以替换为其他的提示符。一般而言，提示符可在/etc/bashrc或在用户的初始文件.bash_profile里
转换/定义。

```shell
例如将提示符设置为以下格式:
$ PS1="$(uname -n) >" #=> chef-client >
```

***用特殊转义序列设置提示符***。可以通过在提示符字符串插入特殊用反斜线转义的字符序列来定制提示符。下表列出了特殊序列。

|         反斜杠序列         | 功能                                                            |
|:-------------------------:|:--------------------------------------------------------------|
|            \t             | 以"HH:MM\:SS"格式显示的当前时间                                   |
|            \d             | 以"Weekday Month Date" 格式显示的日期（"Tue May 26")             |
|            \n             | 换行符                                                         |
|            \s             | shell的名称，$0(接着的部分是终结斜线）的基本名（basename)            |
|            \w             | 当前工作目录                                                    |
|            \W             | 当前工作目录的基本名                                              |
|            \n             | 当前用户的用户名                                                 |
|            \h             | 主机名                                                         |
|            \#             | 本命令的命令个数                                                 |
|            \!             | 本命令的历史序列号                                               |
|            \$             | 如果有效UID为0，则为#， 否则为$                                   |
|           \nnn            | 八进制数字对应的ASCII字符                                         |
|            \\             | 一个反斜杠                                                      |
|            \[             | 非打印字符串序列的起始符，这些非打印字符串可用于在提示符中嵌入终端控制序列 |
|            \]             | 非打印字符串序列的结束符                                          |
| 以下为bash 2.x版本中的新序列 |                                                                |
|            \a             |                                                               |
|            \@             | 以12小时AM/PM格式显示当前时间                                     |
|            \H             |                                                               |
|            \T             | 以12小时HH:MM\:SS格式显示当前时间                                 |
|            \e             | ASCII转义字符（033）                                            |
|            \v             | bash版本号，如2.03                                              |
|            \V             | bash版本号，如2.03.0                                            |

```shell
$ PS1="[\u@\h \W]\\$" #=>[root@chef-client ~]$
这里用反斜线转义来设置bash主提示符.

$ PS1="\W: \d" #=> ~: Sun Apr 05 >

```

***次提示符***。PS2变量被赋值为次提示符字符串。其值在标准错误中显示，默认情况下为监视器。
当没有完成输入或期望更多的输入时就会出现这个提示符。默认次提示符为">".

```shell
$ echo "hello
> there"
hello 
there
#=> 这里由于命令没有输入完整，键入换行符后就出现了次级提示符。直到输入闭合的双引号才会显示次级提示符。
$ PS2="---->"
$ echo "hello
----> there"
hello
there

$ PS2="\s:PS2 > "
$ echo "hello
bash:PS2 > there"
hello
there

```

***搜索路径*** .bash用PATH变量来定位在命令行键入的命令.路径是用冒号分隔的目录列表,
用于shell对命令进行搜索.默认路径依赖于各自的系统实现，并由安装bash的管理员进行设置。搜索在路径中自左而右进行。路径末尾的点符号表示当前的工作目录，如果在所有路径目录列表中都没有发现要找的命令。shell将向标准错误输出信息"filename
not found". 若使用bash shell，通常在.bash_profile中设置，若使用的是Bourne
Shell，则在profile中设置。
如果在路径没有点符号，那么在当前工作目录下执行命令或脚本时，就必须在其前面加上./。如./programme_name。只有这样shell才能找到该程序。

```shell
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin:.

#setting the PATH
$ PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin:
$ export PATH
$ A.sh   #error
$ ./A.sh #right 

#去掉路径末尾的点符号，然后重新赋值给PATH，然后将其导出为全局变量。
#此时若存在一个脚本A.sh, 若直接运行脚本，则会失败，因为bash无法找到该脚本。
#若路径当中有点符号，则可以直接运行A.sh脚本，此时能够正常运行。
```

***Note:PATH变量的值路径由冒号分隔的元素列表组成，搜索时从左到右。路径末尾的点符号代表当前工作路径。路径中不带点符号是出于安全考虑，因此默认情况下，系统的路径是不带点的。***

***hash命令***。hash命令控制内部哈希表，该表用于提高shell搜索命令的效率。shell不会为你每次键入的命令都对路径搜索一次。而是在第一次键入一个命令后，用搜索路径查找该命令，并把它存储在shell所属内存的一个表中。当下一次使用相同命令时，shell就会从哈希表中查找它。这样访问一个命令的要比全路径搜索快的多。如果你知道自己将要经常的使用某个命令，也可在哈希表中添加命令，当然也可从哈希表中移除某个命令。hash的输出结果显示了shell使用该表找到某个命令的次数以及该命令的全路径名。用带-r选项的hash命令可清空哈希表。--参数则禁止对剩余的参数进行检查。哈希算法由bash自动实现。
尽管可以关闭它，但是如果没有什么特别的原因，最好别那样做。

```shell
输入命令hash
$ hash 
hits  command
4	/usr/bin/cat
3	/usr/bin/man

清空哈希表
$ hash -r #加上参数-r可以清空hash表
hits command
hash: hash table empty

在哈希表中添加命令find
Note: 可以将一些外部命令添加进去来提高运行的效率, 但是内部命令本身集成在shell中, 因此可以不添加.
$ hash find
$ hits command
0    /usr/bin/find

```

***source或者.***: source命令是bash
shell的内置命令。点命令就是一个点符号，（从Bourne
shell而来）是source的另外一个名称。这两个命令都以一个脚本为参数,
该脚本将作为当前shell的环境解释执行, 即不会启动一个新的子进程.
所有在脚本中设置的变量将成为当前shell环境的一部分.
同样的，当前脚本中设置的变量也将成为脚本的环境,
source(或点)命令通常用于重新执行刚修改过的初始文件，如.bash_profile和.profile等等。例如，如果在登陆后对.bash_profile中的EDITOR和TERM变量进行了修改，则可以用source命令重新执行.bash_profile中的命令而不用注销并重新登录。像.bash_profile或其他类似的shell脚本这样，文件无需可执行权限即可用source或点命令执行。

```shell
$ source .bash_profile
$ . .bash_profile
以上两种方式都可以对初始化修改过的文件.
Note: 如果.bash_profile直接作为脚本运行, 会启动一个子shell, 这样就会在子shell中设置变量, 当前退出子shell时, 父shell未受到这些变量设置的影响.
```

#### 命令行

登录后，bash
shell默认情况下即显示主提示符，美元符号($)。而shell就是命令解释器，如果shell在交互模式下运行，它会从终端读入命令将其分隔为单词。命令行由以空格(多个空格或制表符)分隔的一个或多个单词或符号组成，并以换行符（由回车键产生）为结束标志。命令的首单词是命令，后续单词为命令参数。命令可能是像ls或date这样的Linux/UNIX可执行程序，用户自定义函数，像cd和pwd这样的内置命令，和一个shell脚本。命令可能包含成为元字符的转义字符串，在分析命令行时它们将被解释。如果命令行过长，可用反斜杠加上换行符来续行，随即显示次提示符，直到命令行结束。
***处理命令的顺序***。命令行的首单词
是执行命令。命令可能是一个关键字，函数，特定的内置命令或工具，可执行程序或是一个shell脚本等。根据命令类型参照下列顺序执行命令：
1. 别名
2. 关键字（如if, function, while和until）
3. 函数
4. 内置命令
5. 可执行程序或脚本

特殊内置命令和函数在shell中定义，一次它们在当前shell的上下文中执行，这也使得它们在执行的时候速度更快。要执行像ls和date这样存储在磁盘上的脚本和可执行程序以及shell，首先得通过搜索path环境变量指定的路径在目录结构上对它们进行定位，然后再调用一个用于执行脚本的新shell。要知道你所用命令的类型---如内置命令，别名，函数或可执行程序等等---可使用内置命令type

```shell
$ type pwd
# pwd is a shell built-in
$ type test
# test is a shell built-in
$ type ll
# ll is alias to `ls -l --color=auto'
$ type if
# if is a shell keyword
$ type -path cal
# /usr/bin/cal
$ type which
# which is alias to `(alias; declare -f) | /usr/bin/which --tty-only --read-alias --read-functions --show-tilde --show-dot'
$ type greetings
# greetings is a function
# greetings () {
#  echo "welcome to my world"
# }
```

***内置命令和help命令***。内置命令就是shell内部源代码部分的命令。它们是内置的，嵌入在shell中，即可执行的，而不像date，cat和finger命令那种存储在磁盘上的经编译的二进制程序。由于没有调用磁盘操作，执行内置命令的开销会很小。内置命令优于磁盘上的程序而执行。bash添加了一个新的在线辅助系统。这样你就可以查看所有内置命令及其描述了。help本身也是一个内置命令。

```shell
$ help help
# help: help [-dms] [pattern ...]
#    Display information about builtin commands.
#    
#    Displays brief summaries of builtin commands.  If PATTERN is
#    specified, gives detailed help on all commands matching PATTERN,
#    otherwise the list of help topics is printed.
#    
#    Options:
#      -d	output short description for each topic
#      -m	display usage in pseudo-manpage format
#      -s	output only a short usage synopsis for each topic matching
#    		PATTERN
#    
#    Arguments:
#      PATTERN	Pattern specifiying a help topic
#    
#    Exit Status:
#    Returns success unless PATTERN is not found or an invalid option is given.

$ help pwd
# pwd: pwd [-LP]
#    Print the name of the current working directory.
#    
#    Options:
#      -L	print the value of $PWD if it names the current working
#    		directory
#      -P	print the physical directory, without any symbolic links
#    
#    By default, `pwd' behaves as if `-L' were specified.
#    
#    Exit Status:
#    Returns 0 unless an invalid option is given or the current directory
#    cannot be read.
Note: 除了可以使用help命令获取帮助文档, 也可以使用command --help的形式来获取.
```

***改变命令行的处理顺序***。bash提供了三个内置命令来改变命令行处理顺序:
1. command内置命令在参照命令处理顺序时，忽略别名和函数。只处理内置命令和在搜索路径中找到的命令。
2. builtin命令只查找内置命令，而忽略函数和在搜索路径中找到的命令。
3. enable内置命令可以打开/关闭built-in开关。默认情况下是运行built-ins。禁止built-in使得即使在与内置命令同名的情况下，也执行在磁盘上找到的，且不指定全路径名的可执行命令（通常的处理是，bash先于磁盘可执行命令而搜索内置命令）。-n开关可以关闭built-in。shell程序员常犯的错误是将脚本命名为test。由于test是一个内置命令，shell将会试图执行它而不是执行用户的脚本（内置命令通常先于任何可执行程序执行）。通过键入enable
   -n test可以禁止test built-in，这样就能优先执行用户的脚本了。
   没有参数，内置命令enable将列出一个所有内置命令的列表。下列每个内置命令在"shell内置命令"中有详细描述。

```shell
$ enable
#enable .
#enable :
#enable [
#enable alias
#enable bg
#enable bind
#enable break
#enable builtin
#enable caller
#enable cd
#enable command
#enable compgen
#enable complete
#enable compopt
#enable continue
#enable declare
#enable dirs
#enable disown
#enable echo
#enable enable
#enable eval
#enable exec
#enable exit
#enable export
#enable false
#enable fc
#enable fg
#enable getopts
#enable hash
#enable help
#enable history
#enable jobs
#enable kill
#enable let
#enable local
#enable logout
#enable mapfile
#enable popd
#enable printf
#enable pushd
#enable pwd
#enable read
#enable readarray
#enable readonly
#enable return
#enable set
#enable shift
#enable shopt
#enable source
#enable suspend
#enable test
#enable times
#enable trap
#enable true
#enable type
#enable typeset
#enable ulimit
#enable umask
#enable unalias
#enable unset
#enable wait

$ enable -n cd # 关闭cd命令built-in功能，使shell优先查询自定义cd函数。如果不关闭，内建命令在查询时优先级高于自定义的同名函数。
function cd() {
    builtin cd
    echo $PWD
}
此时会优先执行定义的函数而不是内置的test命令.
Note: 在定义新的函数的时候选择与操作系统相同的名字不是编程的好习惯.需要避免, 如果一定要这么做需要将内置命令的built-in禁止.
```

***退出状态***。当一个命令或程序退出后，都会向其父进程返回退出状态。退出状态是介于0~255的数字。一般约定，当程序退出且返回状态为0，即认为该程序成功执行。当退出状态为非零时，则意味着命令失败。如果shell没有找到命令，返回的退出状态为127，如果是致命信号导致了命令的退出，其退出状态为128加上导致其退出的信号值。
用上一条执行命令的返回值设置shell状态变量？程序的成功或失败由编写该程序的程序员决定。

```shell
$ grep root /etc/passwd
$ echo $?
此时输出结果为0, 表示程序成功执行.

$ grip root /etc/passwd
$ echo $?
提示没有找到命令, 且返回码为127.

$ sleep 20 ^C
$ echo $?
程序被终止, 且返回码为130
```

***命令行中多条命令***。一条命令可以含多个命令。每个命令之间由分号分隔，命令行以换行符为结束标志。退出状态为命令串中的最后一个命令的返回码。

```shell
$ ls; pwd; date
#从左到右，直到碰到换行符

$ (ls; pwd; date) > output_file
#每个命令的输出都被送到名为output_file的文件中。
```

***命令行执行条件***。条件执行由两个特殊的元字符&&和||将两个命令字符串分开的形式实现的。元字符右边的命令是否执行取决于左边命令的退出条件值。

```shell
$ grip /etc/passwd && echo "command executed successfully"
如果第一个命令串执行成功(返回码为0),&&后面的命令就会被执行, 如果返回码不为0表示命令执行失败, 则&&后面的命令不会被执行.
此时这条命令不会成功执行,因为grip命令不存在.

$ grip /etc/passwd || echo "command executed successfully"
此时这条命令会执行成功, 首先执行左边的命令, 执行失败, 然后继续执行右边的命令, 结果为"command executed successfully".
```

***后台执行命令***。
通常情况下命令都在前台运行，直到命令结束，提示符才会继续出现。有的时候等待命令结束并不是特别方便，因此可以将命令放到后台并行执行，而我们无需等待就可以继续执行下一条命令。后台执行完了以后会将结果输出到屏幕（标准输出）。因此，如果要在后台运行命令，命令的输出结果应该被重新定向到一个文件和指向其他设备的管道，如打印机，这样输出结果就不会影响你手中的工作。
！变量包含提交到后台的最后一项作业的PID（作业的进程标志数）。

```shell
$ sleep 10 
必须等待sleep执行完才能继续下一条命令.

$ sleep 10 &
此时sleep被放入后台运行, 无需等待就可以运行下一条命令.

$ echo $!
此时sleep的进程id为5147

$ kill -9 $!
杀死后台sleep进程
```

***作业控制*** 作业控制是bash
shell提供的一项强大功能，它允许你选择在前台还是后台运行程序，即作业。运作中的程序称为进行或作业，每一个进程都有一个进程id号码，称为PID。通常情况下，在命令行中键入的命令会在前台持续运行，直至通过键入Ctrl-C或Ctrl-\发送信号来终止它。有了作业控制，你就可以将作业送到后台中持续执行。你可以通过键入Ctrl-Z来停止一个作业（实际上是将作业送到后台，并挂起）。也可以让停止的进程在后台运行。可将在后台运行的程序送回前台运行，甚至可以终止正在前台和后台运行的程序。

```shell
set -m         (set job control in the .bashrc file)
set -o monitor (set job control in the .bashrc file)
bash -m -i     (set job control when invoking interactive bash)

$ sleep 20 &
[1]+  Running                 sleep 20 &

$ fg %1
将sleep放到前台运行

jobs -x echo %1
# 打印sleep的PID：5462
$ kill %1 or kill 5462
杀死sleep进程, 两种方式都可以

jobs -l #列出当前运行的后台任务
[2]+  5466 Running                 sleep 100 &

```

**作业控制**

| 命令         | 含义                               |
|:------------|:-----------------------------------|
| jobs        | 列出所有正在运行的作业                 |
| ^Z(Ctrl-Z)  | 停止（挂起）作业; 并在屏幕上显示提示符   |
| bg          | 启动后台停止的作业                    |
| fg          | 将后台程序送到前台执行                 |
| stop        | 挂起一个后台作业                      |
| stty tostop | 如果后台作业将结果输出到终端, 将其挂起   |
| kill        | 向指定的作业发送kill信号              |
| wait \[n]   | 等待指定的作业及其返回值，n为PID或作业号 |

**jobs命令参数**

| jobs命令的参数 | 含义                          |
|:-------------|:------------------------------|
| %n           | n为作业号                      |
| %string      | 以string开始的作业名            |
| %?string     | 含string的作业名               |
| %%           | 当前作业                       |
| %+           | 当前作业                       |
| %-           | 上一个作业，即当前作业的上一个作业 |
| -r           | 列出所有正在运行的作业           |
| -s           | 列出所有挂起的作业               |

***jobs选项***。在bash2.x版本中的jobs命令添加了两个新的选项，它们是-r和-s选项。-r是列出所有正在运行的作业，而-s选项则列出所有挂起的作业。
***内置命令disown***。内置命令disown（bash2.x版本)将指定的作业从作业表中移除。当作业被移除后，shell就不会将其识别为可用的作业，因此它也不能通过其进程id号来呗引用。

#### 历史命令

历史命令机制在历史列表中记录了你之前在命令行输入的命令。在登录会话中，你所键入的命令都被存储在shell命令列表内存中。你可以从历史列表中调入命令并重新执行之，而无需重新键入。内置命令history用于显示历史列表。默认的历史列表文件为.bash_history，在home目录中。
当bash开始访问历史文件时，HISTSIZE变量指定了可以从历史文件中调入多少条命令，默认值为500。HISTFILE变量保存了历史命令文件的名字(默认值为~/.bash_history)，这是历史命令存储的文件。
历史命令文件在一次次的登录会话中增长。HISTFILESIZE变量用于控制历史文件所能存储的最大行数。如果设置了该变量，在超过该数量行数时，将截断历史文件。默认大小为500.

**历史变量**

| 变量          | 含义                                                                                                                                                                                                               |    |
|:-------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---|
| FCEDIT       | 使用fc命令的Linux编辑器的路径名                                                                                                                                                                                       |    |
| HISTCMD      | 当前命令的历史号，即在历史列表中的索引号。如果没有设置HISTCMD，即使跟着重置，其功能也将失效                                                                                                                                     |    |
| HISTCONTROL  | 如果赋值为ignorespace，则以空格开头的行将不会进入历史列表。如果赋值为ignoredups，与上一条命令相同的命令行也不会进入到历史列表中。如果赋值为ignoreboth则综合了这两个选项。如果不设置或没有设置为上述值，所有经分析器读入的命令行都会存储到历史列表中。 |    |
| HISTFILE     | 指定用于存储历史列表的文件。默认值为~/.bash_history。如果不设置的话，在交互式shell退出的时候，将不会存储历史命令。                                                                                                                |    |
| HISTFILESIZE | 历史文件可存储的最大历史行数。如果该变量被赋值，那么在需要的时候，历史文件将被截断以限制在指定的行数内。默认值为500                                                                                                                  |    |
| HISTSIZE     | 在命令历史中可以存储的命令条数。默认值为500                                                                                                                                                                              |    |

**从历史文件中访问命令**
箭头键：可以用键盘上的方向键来在历史文件中上下左右移动。也可以用标准的删除，退格及修改等键来对历史文件中的命令进行编辑。在编辑命令的时候，按回车键即可重新执行该命令。

**箭头键**

| 箭头 | 含义                  |
|:----|:---------------------|
| ↑   | 上移历史列表           |
| ↓   | 下移历史列表           |
| →   | 在历史命令中向右移动关标 |
| ←   | 在历史命令中向左移动关标 |

**内置命令**。内置命令history用于显示已经键入的命令历史，命令前有event
number（事件号）.

```shell
history
# 693  echo $HISTCMD
# 694  history 
# 695  HISTCONTROL=ignoreboth
# 696  echo "hello world"
# 697  history 
# 698  echo $HISTCONTROL 
# 699  exit
# 700  echo $HISTCONTROL 
# 701  exit
# 702  history 
```

**fc命令** fc命令也成为fix命令，有两种用法：
1. 从历史列表中选择命令
2. 在vi或emacs或系统其它的编辑器中编辑命令
   第一种用法：带-l选项的fc命令可从历史列表中选择指定的命令行或某个范围的命令行。如果打开-l开关，结果将输出到屏幕。如fc
   -l，默认情况下将显示历史列表中的最后16行，fc -l
   10则显示在整个历史列表中序号为10的命令行，fc -l
   -3选择最后3行。-n开关可用于关闭历史列表中序号功能。如果打开了该开关，你就可以选择某个范围的命令行并把它们重新定向到文件中，该文件可作为一个脚本被执行。-r开关用于反转命令的顺序。
   第二种用法在后面的"命令行编辑"中进行描述。

**fc命令**

| fc参数     | 含义                 |
|:----------|:--------------------|
| -e editor | 将历史列表调入编辑器    |
| -l n-m    | 列出从n~m范围的命令    |
| -n        | 关闭历史列表的序号功能  |
| -r        | 反转命令的顺序         |
| -s string | 访问以string开头的命令 |

```shell
fc -l
#692	 history 
#693	 echo $HISTCMD
#694	 history 
#695	 HISTCONTROL=ignoreboth
#696	 echo "hello world"
#697	 history 
#698	 echo $HISTCONTROL 
#699	 exit
#700	 echo $HISTCONTROL 
#701	 exit
#702	 history 
#703	 fc -l
#704	 fc -l |wc -l
#705	 fc -l 10
#706	 hash
#707	 fc -l -3

fc -l -3
#705	 fc -l 10
#706	 hash
#707	 fc -l -3

fc -ln #序号被关闭
#	 echo $HISTCMD
#	 history 
#	 HISTCONTROL=ignoreboth
#	 echo "hello world"
#	 history 
#	 echo $HISTCONTROL 
#	 exit
#	 echo $HISTCONTROL 
#	 exit
#	 history 
#	 fc -l
#	 fc -l |wc -l
#	 fc -l 10
#	 hash
#	 fc -l -3
#	 fc -l

fc -ln -3 > saved.sh
将最后三条命令写入saved.sh文件中

fc -l 15
列出15号开始的命令

fc -l 15 20
显示从号数15~20的命令
```

如果fc带有-s选项，可用一个模式字符串来重新执行先前的命令。如fc -s
rm将重新执行匹配rm模式的最近执行的命令。 **重新执行历史命令**
要执行命令列表中的命令，须使用感叹号（称为bang）如果你用两个感叹号(！!)bang，bang，则将重新执行历史列表中最后的命令。如果你在感叹号后跟一个数字，将执行该数字指定的命令。如果键入的是感叹号加一个字符或字符串，那么以该字符或字符串开头的，最近的命令就会被重新执行。(^)也被用作编辑先前命令的一个快捷方法。！的具体用法见下表：

| 条件指示符     | 含义                                                                                                                     |
|:-------------|:------------------------------------------------------------------------------------------------------------------------|
| !            | 标明历史替换的起始标志                                                                                                      |
| !!           | 重新执行上一条命令                                                                                                         |
| !N           | 重新执行历史列表中的第N条命令                                                                                                |
| !-N          | 重新执行当前命令的倒数第N条命令                                                                                              |
| !string      | 重新执行以string开头的最近一条命令                                                                                           |
| !?string?    | 重新执行包含string的最近一条命令                                                                                             |
| !?string?%   | 重新执行历史列表中含string最近的命令行参数                                                                                    |
| !$           | 在当前命令行中使用最近执行命令的最后参数                                                                                       |
| !^           | 在当前命令行中使用最近执行命令的第一个参数                                                                                     |
| !*           | 在当前命令行中使用最近执行命令的所有参数                                                                                       |
| !!string     | 将string追加到前一条命令中，并执行之                                                                                         |
| !Nstring     | 将string追加到第N条命令中，并执行之                                                                                          |
| !N:s/old/new | 替换第N条命令中第一个old字符串为new                                                                                          |
| !N:g/old/new | 将第N条命令中所有old字符串替换为new                                                                                          |
| ^old^new     | 替换最近执行命令的old字符串为new                                                                                             |
| command!N:wn | 执行当前命令，其参数由第N条命令的参数（wn）提供。wn为由0，1，..开始的数字，这些数字表示前面命令的单词标识。单词0为命令本身，1为它的第一个参数 |

```shell
$ date

$ !! #=> 执行最近执行的一条命令 date

$ !106 #=> 执行第106条命令，假设为date

$ !d #=> 执行以d开头的命令

$ dare #=> 命令不存在，执行失败

$ ^r^t #=> 将dare中的r替换为t即为date

$ echo 1 2 3
$ echo !$ # 打印最近一条命令最后一个参数3

$ echo 1 2 3
$ echo !^ # 打印最近一条命令第一个参数

$ echo 1 2 3
$ echo !* # 打印最近一条命令的所有参数

$ ls file1.txt
$ cat !761$ # 查看第761条命令的第一个参数即file1.txt的内容

$ !!:p # 显示但不执行最近执行的命令
```

**FCEDIT和编辑命令**
如果在fc命令-e参数后面跟着一个Linux编辑器的名字，即可在将历史列表中选定的命令行调入编辑器中。比如，fc
-e vim -1
-3将调用vi编辑器，并在/tmp目录中创建临时文件，将历史列表的倒数三个命令调入vi缓冲区中。可以对这些命令进行编辑也可以注释掉（在命令行钱加#即可注释之）。如果用户退出编辑器，命令将被回显并执行。
如果没有编辑器的名字，则使用FCEDIT变量的值（常在初始化文件中设置，要么是.bash_profile要么是.profile)。如果没有设置FCEDIT则使用EDITOR。如果结束编辑，并退出编辑器，则所有编辑过的命令都将被回显并执行。
**Note:无论是直接退出编辑器还是保存并退出编辑器，命令都将被执行，除非注释掉或者删除掉。**

```shell
$ FCEDIT=/usr/bin/vim

$ pwd
$ fc # 由于前面设置了FCEDIT变量的默认值，因此默认会将最近输入的pwd命令调入编辑器窗口，用户写入并退出编辑器后，键入的命令将被执行。

$ fc -1 -3 # 将最后三条命令从历史列表调入到编辑器的缓冲区。
```

#### 别名

别名是用户对命令定义的缩写，它在命令有很多参数和选项或语法难以记忆的情况下非常有用。在命令行下进行的别名设置不会被子shell所继承。别名通常在.bashrc文件中进行设置，因为在启动一个新的shell时，总是要先执行.bashrc这个文件，所以在新的shell中所有的别名都会被重置。别名也能在shell脚本中使用，除非在脚本中直接设置，否则会导致一些潜在的移植性问题。
**列出别名**
内置命令alias列出所有设置的别名。首先显示别名，接着是它所代表的的真正的命令。

```shell
alias
#alias xzegrep='xzegrep --color=auto'
#alias xzfgrep='xzfgrep --color=auto'
#alias xzgrep='xzgrep --color=auto'
#alias zegrep='zegrep --color=auto'
#alias zfgrep='zfgrep --color=auto'
#alias zgrep='zgrep --color=auto'
该别名所代表的的真正的命令列在等号(=)的后面
```

**创建别名**
alias用于创建别名。alias命令用于创建别名。第一个参数为别名的名称，即命令的昵称。余下的部分是在执行别名时所包含的一个或多个命令。bash别名不能带参数，多个命令间用分号分隔，含空格和元字符的命令则用单引号括起来。

```shell
alias m=more
alias lF='ls -alF'
alias r='fs -s'
```

**删除别名** unlias用于删除一个别名。要临时禁止别名，可在别名前加一个反斜杠。

```shell
unlias ls
\ls
# ls会被禁止，此时列出各种文件时，不会用颜色来区分
```

**管理目录栈**
如果你工作时需要cd上下某些相同的目录，就可以通过把这些目录放入目录栈并管理它来使得访问这些目录更加容易，pushd内置命令将目录放入一个栈中，并用popd命令移出它们，栈最左边的目录就是最近放入栈的目录，可用内置命令dirs来列出这些目录。
**dirs内置命令**
带-l选项的内置命令dirs以全路径名的格式显示目录栈。如果没有选项，dirs命令用一个tilde符号来标识home目录。如果带有一个+n选项，dirs显示从目录列表左边数起第n个目录项，以0为始，带-n参数的情况也类似，只不过从右以0为始数起。
**pushd和popd命令**以一个目录作为参数的pushd命令将把新的目录添加至目录栈中，同时切换到该目录。如果参数为+n,
n为数字，pushd将rotate目录栈使得栈左数第n个目录放到栈顶。如果是-n选项，则做相似的事情，不过是从右边数起。当没有参数时，pushd将交换栈顶两项，以使得在目录间前进和后退更加简单。
**popd**命令从栈顶移除一个目录项，并切换到该目录。如果带有+n选项，n为数字，popd移除从dirs命令显示列表的左数第n项。

```shell
$ pwd # /root/

$ pushd .. # 此时目录切换到/根目录，并且会把/目录把存到目录栈中
#输出结果为/ ~

$ pwd # / 此时在根目录下面

$ pushd etc #切换到ETC目录下面
#输出结果为 /etc / ~ 三个目录

$ dirs 
#输出结果为 /etc / ~

$ dirs -l
#输出结果为 /etc /  /root 以全路径名的格式显示目录栈

$ popd 
#移除栈顶目录，并且切换到下一个目录中
```



## 变量

**变量类型**
有两种类型的变量：局部变量和环境变量。局部变量仅在创建它的shell中有效，环境变量则对所有创建它的shell所派生出来的子进程都有效。某些变量由用户来创建，而另一些则是shell的特殊变量。
**命名规则**
变量名必须以字母或下划线开始，其余部分则可以由字符，数字（0-9）或下划线字符构成。而其他字符均可作为变量名的结束标志。名字是大写敏感的。**当给一个变量赋值时，不要在等号两边留下空格。如果要将变量赋值为空，要在等号后直接跟一个换行符**。创建局部变量的最简单方法时以下面的格式把值赋予变量
variable=value 例如 name=Tony
**declare内置命令**有两个内置命令declare和typeset可用于创建变量，通过选项还可以控制设置变量的方式。typeset命令（从Korn
shell而来）与declare命令完全一样。bash的文档中写道：typest命令完全兼容korn
shell，但是该命令并不支持内建命令声明。因此我们将使用declare内置命令。
没有参数的declare命令将列出所有设置的变量。通常只读变量时不能重新赋值或撤销的。如果有declare命令来创建只读变量，它们不能被撤销，也无法重新赋值。整型变量也能用declare来赋值。
declare variable=value declare name=Tony

declare选项

| 选项 | 含义                     |
|:----|:-------------------------|
| -r  | 将变量设置为只读           |
| -x  | 将变量名导出到子shell中     |
| -i  | 将变量置为整型             |
| -a  | 将变量当做数组，即给元素赋值 |
| -F  | 只列出函数名               |
| -f  | 列出函数名及其定义          |

**Note：-a -F 只在bash2.x版本实现** **局部变量和范围**
变量的范围是指变量在一个程序中的什么地方是可见的。对于shell而言，局部变量的范围限于创建变量的shell。
当给一个变量赋值时，不要在等号两个留下空格。如果要将一个变量设置空，在等号后面跟一个换行符即可。
变量前面的美元符号用于提取其存储的值。
local函数可用于创建局部变量，但这些变量只能在函数内使用。
**设置局部变量**。局部变量可通过将值赋予一个变量名来设置，或者使用declare来设置。

```shell
$ round=world or declare round=world
$ echo $round
# world

$ x= #赋值为空值
$ echo $x
# 空值
$ file.txt=100
# wrong, 变量名中的字符只能是字母，数字和下划线
$ name="Tony Yang"
$ echo $name
# Tony Yang
# 引号用于隐藏空格，否则shell会把字符串分割为两个单词
```

```shell
$ echo $$
# 父进程id
$ round=world
$ bash
# 切换到子进程
$ echo $round
# 由于是局部变量，因此子进程无法访问该变量，结果为空行
$ exit # 退出子进程
$ echo $round
# 重新可以访问变量的值
$ bash
$ round=larry
$ echo $round
# 在子进程中定义一个同名变量，然后打印它的值world
$ exit
# 退出子进程
$ echo $round
# 打印的结果为父进程的变量值world
```

**Note：set可以查看定义的变量，unset可以清空一个变量的值。子进程定义的变量，父进程无法访问。**

```shell
$ name=Tony
$ readonly name
$ echo $name
#Tony
$ unset name
# 无法清空变量，因为name是只读变量
$ name=larry
# 无法设置新值，因为name是只读变量
$ declare -r name=tony
$ unset name
$ name=larry
# 结果同上面一样
```

#### 环境变量

环境变量是能为创建它的shell及其派生子进程所用的变量，它们也经常被称为全局变量以区分与局部变量。一般约定环境变量为大写，它们是那些可以通过内置命令export导出的变量。
创建环境变量的shell称为父shell，从shell中启动的新shell称为子shell。环境变量被传送给任何从创建该变量的shell中派生的shell。这些变量可以从父shell传给子shell，再传给孙shell，以此类推，但反方向不行。即，shell能创建变量，但是它无法将变量传给其父shell，而只能传给它的子shell。某些环境变量，如HOME，LOGNAME，PATH已经SHELL，是在登录前由/bin/login程序来设置的。通常环境变量在用户主目录下的.bash_profile文件中定义。
**设置环境变量**
要设置环境变量，必须在给变量赋值或设置了变量后使用export命令，内置命令built-in加上-x选项也可以完成同样的事情（在导出一个变量时，不需要用美元符号）。

```shell
export variable=value
variable=value;export variable
declare -x variable=value
```

实例

```shell
$ export NAME=tony
$ PS1='\d:\W:$USER>';export PS1
$ declare -x TERM=linux
```

export命令选项

| 选项 | 值                                                     |
|:----|:-------------------------------------------------------|
| --  | 选项段的结束标志，余下的都是参数                            |
| -f  | name-value 形式被视为函数而不是变量                        |
| -n  | 将全局变量（已导出的）转换为局部变量，该变量将不会导出到子进程中 |
| -p  | 显示所有的全局变量                                        |

**Note：变量的继承（派生）是单向的，只能由父传子）**

```shell
$ export TERM=linux or declare -x TERM=linux
$ NAME="tony yang"
$ export NAME
$ echo $NAME

$ echo $$
$ bash
$ echo $$ # 子shell
$ echo $NAME # 依然可以访问从父shell继承的变量NAME
$ declare -x NAME="larry wall" # 在子shell中声明一个环境变量
$ echo $NAME
$ exit
$ echo $NAME #此时回到父shell，NAME的值依然为tony yang,因为环境变量是无法子传父的。
# tony yang
```

**清空变量**
如果未被设置为只读属性的话，本地变量和环境变量都可以通过使用unset命令清空

```shell
unset name; unset TERM
# unset把变量从shell内存中删除
$ declare num=100
$ set | grep num #可以查看到该变量
$ unset num; set | grep num # num变量被删除
```

**打印变量的值：echo命令**内建命令echo的作用是将其参数打印到标准输出。-e选项使得echo命令可以无限制地使用转义序列控制输出的效果。

echo选项含义

| 选项 | 解释                                                     |
|:----|:--------------------------------------------------------|
| -e  | 允许翻译如下所有的转义序列                                   |
| -n  | 把换行符压缩到输出行的末尾                                   |
| -E  | 关闭对转义符号的翻译，包括关闭对那些默认情况下翻译的转义符号的翻译 |

转义序列

| 转义符 | 解释                |
|:------|:-------------------|
| \a    | 警告（铃声）         |
| \b    | 退格                |
| \c    | 不换行打印           |
| \f    | 填表                |
| \n    | 换行                |
| \r    | 返回                |
| \t    | 制表符              |
| \v    | 垂直制表符           |
| \\    | 反斜杠              |
| \nnm  | ASCII代码为nmm的符号 |

```shell
$ echo The username is $LOGNAME
$ echo -e "\t\thello there\c"
# 打印转义序列已经字符串内容
$ echo -n "hello there"
# 打印行，不换行
$ echo -E "\t\thello world"
# 打印行，关闭转义字符的解释，安装字面内容打印
# \t\thello world
```

**printf命令**.用来格式化输出，作用是用来打印格式化字符串，效果类似c语言的printf函数。格式包括字符串本身和描述打印效果的字符。定义格式的方法是在%后面跟一个说明符，例如%f表示后面是一个浮点数，%d表示一个整数。

```shell
格式 printf format [arguments...]

$ printf '%10.2f%5d\n' 10.5 25
#      10.50   25

$ type printf
# printf is a built-in

$ printf "The number is %.2f\n" 100
# The number is 100.00
$ printf "%-20s%-15s%10.2f\n" "jody" "savage" 28
# jody                savage              28.00

$ printf "%s's average was %.1f%%.\n" "jody" $(( (80 + 70 + 90)/3 ))
# jody's average was 80.0%.
```

**变量扩展修改符（参量扩展）**。通过特定的修改符，可以检验和修改变量。这些修改符提供了一个快捷的方法来检验变量是不是被设置过，并把输出结果输出到一个变量中。见下表
变量修改符

| 修改符                     | 解释                                                                                                   |
|:--------------------------|:------------------------------------------------------------------------------------------------------|
| ${variable\:-word}        | 如果变量被设置了而且非空(null)就保持原来的值，否则设置为word                                                   |
| ${variable\:=world}       | 如果变量被设置了而且非空就保持原来的值，否则就把变量永久设置为word                                               |
| ${variable\:+world}       | 如果变量被设置了而且非空就替代word，否则什么也不做                                                            |
| ${variable\:?word}        | 如果变量被设置了而且非空就保持原来的值，否则就打印word，然后退出shell。如果word为空就打印parameter null or not set |
| ${variable\:offset        | 从offset位置开始提取变量的值得子字符串，如果offset为0就取整个字符串                                            |
| ${variable\:offset:length | 从变量值的offset位置开始提取长度为length的子字符串                                                           |

使用冒号和修改符来检验变量是否被设置，是否为空。若没有冒号，即使设置为null的变量也被认为是已被设置过值。

```shell
$ fruit=peach
$ echo ${fruit:-world}
# peach
$ echo ${newfruit:-apple}
# apple
$ name=
$ echo ${name:-joe}
# joe                #=> 这两种方法相同，结果一样
$ echo ${name:-joe}
# joe

$ echo $name
# 空值
```

**Note: 以上这种方法只是临时赋值，并不会永久保存在name变量里面**

```shell
$ name=
$ echo ${name:=peter}
# peter
$ echo $name
# peter
```

**Note:
以上这种方法会永久赋值，会将值暂时的永久保留在变量里，除非手动清除或者赋新值**

```shell
$ foo=grapes
$ echo ${foo:+apple}
# apple
$ echo $foo
# grapes
```

**Note:以上这种方法与\:-方法正好相反，当foo值不为null时，会临时改变它的值，但是不是永久改变，因此再次打印foo的时候，值依然是原来的grapes**

```shell
$ echo ${namex:?"namex is undefined"}
# 因为namex为null，因此会打印后面的字符串内容即namex is undefined 
$ echo ${y:?} or echo ${y?}
# parameter null or not set
```

```shell
$ var=notebook
$ echo ${var:0:4}
# note
$ echo ${var:4:4}
# book
$ echo ${var:0:2}
# no
```

**子字符串的变量扩展**。模式匹配参数用来从字符串的前面或者后面，去掉特定的部分字符串。最常用的方法就是从路径中去点路径。

| 表达式                | 功能                                             |
|:---------------------|:------------------------------------------------|
| ${variable%pattern}  | 变量的值与模式符合smallest trailing portion就删除它 |
| ${variable%%pattern} | 变量的值与模式符合largest trailing portion就删除它  |
| ${variable#pattern}  | 变量的值与模式符合smallest leading portion就删除它  |
| ${variable##pattern} | 变量的值与模式符合largest leading portion 就删除它  |
| ${#variable}         | 替换变量中字母的个数，如果*或者@，长度就是位置参量的个数 |

```shell
$ pathname="/usr/bin/local/bin"
$ echo ${pathname%/bin*}
# 从尾部开始删除最小匹配的内容即/bin/
$ pathname = "/usr/bin/local/bin"
$ echo ${pathname%%/bin*}
# 从尾部开始删除最大匹配的内容即/bin/local/bin/
$ pathname="/home/tony/.bashrc"
$ echo ${pathname#/home}
# 从头开始,删除最小匹配的内容即/home
$ pathname="/home/tony/.bashrc"
$ echo ${pathname##*/}
# 从头开始，删除最大匹配的内容即/home/tony/
$ name="Ebenezer Scrooge"
$ echo ${#name}
# 打印赋值给变量name的字符串的字母个数，这里共有16个字母
```

**位置参量**。通常情况下，特定的内建变量，被称为位置参量，它们被用于从命令行向脚本传递参数，或者在函数中用于保存传递给函数的参数。这些变量称为位置参量是因为它们以数字1，2，3...区分，这些数字与它们在参量清单中的位置有对应关系。
shell脚本的名字保存在变量$0中，位置参量可以被set命令设置，重置和清空。

| 表达式 | 功能                    |
|:------|:-----------------------|
| $0    | 当前脚本的名字            |
| $1-9  | 位置参数1-9              |
| ${10} | 位置参量10               |
| $#    | 位置参量的个数            |
| $*    | 向所有的位置参量赋值       |
| $@    | 同$*, 有双引号时除外      |
| "$*"  | 赋值到"$1$2$3"等等       |
| "$@"  | 赋值到"$1" "$2" "$3"等等 |

```shell
$ set punky tommy bert jody
$ echo $*
# punky tommy bert jody
$ echo $@
# punky tommy bert jody
$ echo $#
# 4

$ set a b c d e f g h i j 
$ print $10
# a0 不会解释为$10，而是解释为$1 + 0，因此需要加上一个花括号来避免这种情况

$ set file1 file2 file3
$ eval echo \$$#
# file3 首先$#会被替换为3，然后eval会将字符串$3解释为位置参数$3，此时该变量的值为file3，因此结果为file3
$ set --
# 清空所有位置参量
```

**其他特殊变量**
shell有一些由单个字符组成的变量，在这些变量前加上$后就能访问这些变量，见表

| 变量 | 含义                     |
|:----|:------------------------|
| $   | shell的PID               |
| -   | 当前sh的选项              |
| ?   | 最后一个命令的退出状态值    |
| !   | 最后一个放入后台作业的PID值 |

```shell
$ echo The pid of this shell is $$

$ echo "The options for this shell are $-"
# 打印当前交互式bash的选项
$ grep root /etc/passwd
$ echo $?
# 打印最后一个命令执行的退出状态值，成功返回0，失败返回1
$ sleep 10 &
$ echo $!
# 打印最后一个被放入后台的作业PID号
```

**引用**
引用可以保护特殊的元字符不被翻译，防止参数扩展。有三种引用的方法；反斜线，单引号，双引号。下面是对于shell来说比较特殊的元字符，需要被引用

| 元字符     | 含义                       |
|:----------|:--------------------------|
| ;         | 命令分隔符                  |
| &         | 后台处理                   |
| ()        | 命令组，创建一个子shell      |
| {}        | 命令组，但是不创建子shell    |
| \|        | 管道                       |
| <         | 输入重定向                  |
| >         | 输出重定向                  |
| Newline   | 命令终止                   |
| Space/tab | 单词分隔符                  |
| $         | 变量替换                   |
| *\[]?     | 用于文件名扩展的shell的通配符 |

单引号或者双引号必须成对使用。单引号可以保护一些特殊的元字符不被翻译，例如$,*,?,>,<.双引号也可以保护一些元字符不被翻译，但是允许变量和命令替换。单引号可以保护双引号，双引号也可以保护单引号。
跟Bourne
shell不同，bash尽量让你知道是不是丢失了引号。在交互状态下，如果丢失了引号，就会出现次级提示符，进而在脚本中检查整个文件，看看是不是缺少引号，如果出现引号不匹配的情况，shell就会尝试用下一个合法的引号来匹配它。如果下一个合法的引号不能匹配该引号，脚本就会终止。引号可能是程序最大的敌人之一
**反斜线**
反斜线用来保护一个字母不被翻译，如果把反斜线放在一对引号中，它就不被翻译，反斜线可以保护美元符号，反引号和双引号中的反斜线。

```shell
$ echo where are u going\?
$ echo start on this line and \
> go to the next line.
$ echo \\
# \ 这里的反斜线用来转义第二个反斜线
$ echo '\\'
# \\
$ echo '\$5.00'
# \$5.00 所有单引号中的字母都被看成没有特殊意义的字母本身，反斜线在这里没有任何特殊含义
$ echo "\$5.00"
# 转义美元符号，按照字母意思输出，这里反斜线不会被打印，因为是用来转义美元符号的,而上面由于是在单引号里面，反斜线不会被翻译，因此反斜线会被一起打印出来
$ echo 'Don\'t you need $5.00?'
> '
# Don't you need $5.00?
# 单引号必须是成对出现的，因此即使转义了t前面的单引号，然后需要另外一个单引号来凑成一对
```

**单引号**
单引号必须成对使用，它可以保护所有的字符不被翻译。要打印一个单引号就必须使用双引号或者反斜线来引用它。

```shell
$ echo 'Hi there
> how are u?
> when will this end?
>oh'
# 由于没有出现成对单引号，因此出现了次级提示符，等待引号匹配

$ echo Don\'t you need '$5.00?'
# Don't you need $5.00? 这里对第一个单引号进行了转义，否则会与美元符号的单引号进行匹配。
$ echo 'Mother yelled, "Time to eat!"'
# Mother yelled, "Time to eat!"
```

**双引号** 双引号必须成对出现，它们运行变量和命令替换，但是保护其他符号不被翻译

```shell
$ name=Jody
$ echo "Hi $name, I am glad to meet you."
# Hi Jody, I am glad to meet you.
$ echo "Hey $name, the time is $(date)"
# Hey Jody, the time is Sun Apr 12 20:51:13 CST 2020
# 双引号中的变量和命令替换依然会正常执行，不会被禁止解释
```

#### 命令替换

在需要把命令的输出结果赋值给一个变量或者需要用字符串替换变量的输出结果时，我们可以使用变量替换，所有的shell都使用反引用的方法进行命令替换。bash有两种命令替换的方法：第一种是老的方法把命令放在反引号中，另外一种是新的korn
shell风格，把命令放在一对圆括号中，并在前面缀上一个美元符号。
bash做命令替换的方法是执行命令，并把其执行结果返回到标准输出。在传统的命令替换方式下，反斜线除了在美元符号，单引号和斜线后面的情况以外，还都保持着自己的字面意思。在korn风格的方式下，括号中的所有字符都被当做命令来看待。
命令替换时可以嵌套的，在使用的嵌套命令替换时，如果使用的旧风格，在内部反引用前必须使用反斜线转义。

```shell
格式 `Linux command` # 旧方法
     $(Linux command) # 新方法
```

```shell
$ echo "The hour  is `date +%H`"
# date命令的输出被替换到字符串中
$ name=`awk -F '{print $1}' database`
$ echo $name
# 将awk命令返回的结果赋值给name
$ ls `ls /etc`
#
$ set `date`
# set命令把date命令的输出结果赋值给位置参量，空格把相应的参量分割开来
$ echo $*
# 表示所有位置参量
$ echo $2 $6
# 打印第二个和第六个位置参量的值
$ echo `basename \`pwd\``
# 首先取pwd的值为/root，然后执行basename命令来获取基础文件名，结果为root

```

**新方法**

```shell
$ d=$(date)
$ echo $d
# Sun Apr 12 21:41:34 CST 2020
$ lines=$(cat file1)
$ echo The time is $(date +%H)
$ machine=$(uname -n)
$ echo $machine
# chef-client.com
$ pwd
$ dirname="$(basename $(pwd))"
$ echo $dirname
# root
$ echo $(cal)
# April 2020 Su Mo Tu We Th Fr Sa 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30
$ echo "$(cal)"
#    April 2020     
#Su Mo Tu We Th Fr Sa
#          1  2  3  4
# 5  6  7  8  9 10 11
#12 13 14 15 16 17 18
#19 20 21 22 23 24 25
#26 27 28 29 30
# 加双引号会把换行符保留，不加换行符，只会保留最后一个换行符。
```

#### 数学扩展

shell通过运算数学表达式和替换结果来进行数学扩展。在没有双引号和表达式嵌套的情况下，表达式可以被直接处理。
有两种数学表达式的格式

```shell
$[ expression ]
$(( expression ))
```

```shell
$ echo $[ 1 + 2 + 3 ] #方括号两边是否有空格不影响结果，可以正常运行
# 6
$ echo $(( 1 + 2 + 3 )) #括号两边是否有空格不影响结果，可以正常运行
# 6
```

#### 扩展的顺序

当进行变量扩展，命令扩展，数学表达式扩展以及路径扩展的时候，shell是遵循一定顺序的。假设变量没有被引用，那么shell的处理顺序应该如下所示
* 大括号扩展
* Tilde扩展
* 参数扩展
* 变量替换
* 命令替换
* 数学扩展
* 单词分割
* 路径扩展

#### 数组

bash2.x版本提供了创建一维数组的能力。数组允许你把一串数字，一串名字或者一串文件放在一个变量中。使用内建功能declare
-a 可以创建数组，或者在变量后面增加脚标直接创建数组如x\[0]=5,
数组的脚标是从0开始的。数组的尺寸没有限制，脚标也不必须是一定顺序的数字。获取数组中某个元素的语法是${arrayname\[index]}。如果declare命令后面有-a和-r选项，那么就创建只读数组。

```shell
declare -a variable_name
variable=(item1 item2 item3)

$ declare -a num=(45 33 100 65)
$ declare -ar names
$ names=(Ton Dick Harry) # 无法赋值，因为names是只读的变量
$ states=(ME [3]=CA CT)
$ x[0]=55
$ n[4]=100
```

向数组赋值的时候，脚标自动从0开始，每次增加1。在每次赋值时，不一定要提供多个值，也不需要按照顺序提供。清空一个数组，使用unset命令后面跟数组变量的名字。清空一个元素则使用unset命令后面是arrayname\[subscript]。
declare，local，read-only命令都可以使用-a选项创建数组。read命令通过-a选项从标准输入读入一组单词赋值给数组的元素。

```shell
$ declare -a friends
$ friends=(larry wall matz)
$ echo ${friends[0]}
# larry
$ echo ${friends[1]}
# wall
$ echo ${friends[2]}
# matz
$ echo "All friends ${friends[*]}"
# All friends larry wall matz
$ echo "Total count is ${#friends[0]}"
# Total count is 3, 表示第一个元素的长度为5
$ unset friends[0]
# 清空数组第一个元素的值
$ unset friends
# 清空数组值
```

```shell
$ x[3]=100
$ echo ${x[*]}
# 由于只有第三个元素被赋值100，因此前两个元素不存在，数组的大小仍为1.
# ${x[*]}显示数组所有元素，由于只有100，因此显示100
$ echo ${x[0]}
# 空值
$ echo ${x[3]}
# 100
$ states=(ME [3]=CA [2]=CT)
$ echo ${states[*]}
# ME CA CT
$ echo ${states[0]}
# ME
$ echo ${states[1]}
# 空值
$ echo ${states[2]}
# CT
$ echo ${states[3]}
# CA
```

#### 函数

当前shell中有一组组织在一起并命名的命令叫做bash函数。它们看起来像脚本，但是效率更高。一旦被定义，函数就成为shell内存中的一部分，可以被调用，而不必从文件中读取该段代码。函数通常在脚本的模块化书写风格中被使用。一旦被定义，函数就可以被反复使用。虽然在交互的方式下函数可以在提示符下定义，但是最多的还是在用户的初始化函数中被定义，.bash_profile。在引用前，函数必须是被定义的。
**定义函数** 定义函数有两种方式，一种是旧的Bourne
shell方式，函数名后面是一对空括号，然后是函数的定义。新的方式是使用关键字function，后面是函数名和函数定义，如果使用新的方式，那么括号是可选的。函数定义被放在一对大括号**中间**。它包括一些用分号分隔的命令。最后一个命令后面也需要分号。大括号前面需要保留一些空间。所有需要向函数传递的参数都被当做位置参量来处理。位置参量在函数中是本地变量。内建的local命令运行你在函数内部创建本地变量。函数是可以递归的，例如不断引用自身直到一定的次数。

```shell
格式 function_name () { commands; commands; }
    function function_name { commands; commands; }
    function function_name () { commands; commands; }
```

```shell
$ function greet { echo "Hello $LOGNAME, today is $(date)"; }
$ greet
# Hello root, today is Sun Apr 12 22:37:52 CST 2020
$ greet () { echo "Hello $LOGNAME, today is $(date)"; }
$ greet
# Hello root, today is Sun Apr 12 22:37:52 CST 2020
$ declare -f
# 显示当前shell中所有被定义的函数
$ declare -F
# 显示当前shell中所有被定义的函数的名字
$ export -f greet
# 将函数导出为全局函数
$ bash
$ greet
# Hello root, today is Sun Apr 12 22:37:52 CST 2020

$ function fun {
  echo "The current working directory is $PWD"
  echo "Here is a list of ur files: "
  ls
  echo "Today is $(date +%A).";
}
# 定义一个fun函数，括号中的命令写在不同行，因此无需分号，如果是写在一行就需要分号分隔，左右半边括号后面至少需要一个空格
$ function welcome { echo "Hi $1 and $2"; }
# 定义了两个位置参量的函数welcome
$ set jane anna lizzy
# 设置 位置参量
$ echo $*
# jane anna lizzy
$ welcome johan joe
# johan and joe
$ echo $1 $2
# jane anna 
$ unset -f welcome
# 清空函数，这个函数不再被定义
```

**Note:
函数的位置参量只在函数内部使用，不能供函数外部使用。在命令行设置一些位置参量，它们对于函数的位置参量没有影响。**
**列出和清空函数**
列出函数和它们的定义使用命令declare在bash2.x及以上版本中declare
-F只列出函数的名字，函数的定义将跟局部变量一起送到标准输出。unset
-f命令可以清空这些函数。

#### 标准I/O和重新定向

当shell启动，它继承三个文件：stdin，stdout和stderr。标准输入通常来自键盘。标准输出和标准错误通常是屏幕。但是很多时候你也许想从文件中读取输入或者把输出保存在文件中。这个时候你就可以使用I/O重新定向，见下表
重新定向操作符表

| 重新定向操作符 | 作用                                       |
|:-------------|:------------------------------------------|
| <            | 重新定向输入                                |
| >            | 重新定向输出                                |
| >>           | 追加输出                                    |
| 2>           | 重定向错误                                  |
| &>           | 重定向错误和输出，使它们指向同一个数据流向       |
| >&           | 重定向错误和输出，是它们指向同一个数据流向       |
| 2>&1         | 重定向错误到标准输出                          |
| 1>&2         | 重定向标准输出到标准错误                      |
| >\|          | 重定向输出的时候覆盖noclobber                 |
| <>filename   | 如果是一个文件，就把这个文件作为标准输入和标准输出 |

```shell
$ tr '[A-Z]' '[a-z]' < myfile
# 将myfile文件的内容重定向输入到tr命令，然后tr将所有大写字母转为小写字母显示到屏幕

$ ls > lsfile
# 将ls命令输出的结果重定向到文件lsfile中

$ date >> lsfile
# 将命令date的输出结果追加到文件lsfile中

$ dates 2> errfile
# dates命令不存在，因此错误消息会被重定向到errfile文件中

$ find . -name '*.txt' -print > foundit 2> /dev/null
$ find . -name \*.txt -print > foundit 2> /dev/null
# 这两种写法结果一样，需要注意的是需要对*进行转义

$ find . -name '*.txt' -print  >& foundit
$ find . -name '*.txt' -print > foundit 2>&1
# 以上两种写法结果一样，都是将标准输出和标准错误输出到同一个文件中

$ echo "File needs an argument" 1>&2
# 将标准输出重定向到标准错误, 该信息标准错误与标准输出合并在一起
```

**exec和重定向**
exec命令可以替换当前程序而不需要启动一个新的进程。exec可以改变标准输入和输出而不需要启动一个新的子进程。如果文件用exec打开，read命令就会把文件指针每次向下一行直到文件的末尾。如果要重新从文件开始阅读则必须把文件关闭再重新打开。但是，如果使用linux的cat或者sort工具，操作系统在每一个命令结束后都会将文件关闭一次。
exec命令

| exec命令        | 作用                                       |
|:---------------|:-------------------------------------------|
| exec ls        | 在shell内执行，当命令ls结束后，直接关闭shell进程 |
| exec <filea    | 将filea作为标准输入源                         |
| exec >filex    | 将标准输出定向到filex中                       |
| exec 3<datfile | 将datfile文件输入定向到文件描述符3             |
| sort <&3       | 对datfile重定向输入的内容进行分类              |
| exec 4>newfile | 将文件描述符4重新定向到newfile                 |
| ls >&4         | ls的输出被重新定向到newfile                   |
| exec 5<&4      | 创建文件描述符4的拷贝文件描述符5                |
| exec 3<&-      | 关闭文件描述符3                              |

```shell
$ exec date
# 执行完date以后退出shell，如果在登录shell中，那么执行完命令后退出登录，如果在交互窗口中运行，那么将会退出窗口

$ exec > temp
$ ls
$ pwd
$ echo hello
# 以上输出都被重定向到文件tmp中
$ exec > /dev/tty
# 再次把标准输出重定向到终端
$ echo hello
# 标准输出被显示在屏幕上
```

```shell
$ exec 3>files
# 在当前shell，将文件描述符3分配给files，并打开作为输出的重定向
$ who >&3
# 在当前shell，将who的输出输出到files中
$ date >&3
# 在当前shell，将date的输出重定向到文件描述符3，而此时fd3指向files，因此输出会被输出到files中
$ exec 3>&-
# 关闭文件描述符3
$ exec <files
# 在当前shell，将files作为标准输入源，也就是将标准输入重定向到files
$ exec <&3
# 在当前shell，创建一个标准输入的拷贝fd3
$ exec 3<&-
# 关闭文件描述符3
$ date >&3
# 报错，因为文件描述3没有重定向到任何地方
```

#### 管道

管道就是把管道符号左边命令的输出发送给管道符号右边的命令作为输入。一个管道线可以由不止一个管道组成。

```shell
$ who | wc -l

$ du .. | sort -n | sed  -n '$p'
# 输出当前目录的上层目录，每一个目录所占用的磁盘块个数通过管道输入到sort来进行排序，然后作为sed命令的输入，打印最后一行
$ (du / | sort -n | sed -n '$p') 2>/dev/null
# 如果因为权限原因无法访问一些目录，du命令将把错误信息发送到stderr，也就是屏幕。当你把整行的命令放在括号中，所有的输出都显示在屏幕上，而所有的错误信息都将送到/dev/null
```

#### here文档和重新定向输入

here文档是一种特殊形式的引用。它为需要输出的程序的，例如mail，sort和cat接收在线文本，直到遇到用户定义的结束符号为止。它通常用在shell脚本中创建菜单。需要获得输入的命令后面有<<符号，以及用户定义的结束符号，最后是换行符。下一行就将是被发送给命令的文本。当用户定义的结束符单独出现在行的最左边时，输入就结束了。结束符用来代替Control-D来结束程序的输入。
在命令行上，结束符前<<,制表符或者只有制表符。

```shell
$ cat << FINISH
> Hello world # 按下回车会出现次级提示符
> I can not wait to see u!
FINISH # 结束符前后不允许有空格
```

如果想要允许最后一个结束符前面有一个或多个空格，需要在开始的<<符号后面加上一个短横杠。

```shell
cat <<- FINISH
hello world
i can find u!
 FINISH
# 这种情况在命令行下有问题，但是在脚本里面可以正常运行
```

#### Shell调用选项

当我们启动bash命令行的时候，有一些选项可以帮助我们控制它的行为。这有两种选项：单字符选项和多字符选项。单字符选项由一个减号和一个字符组成，多字符选项由两个减号和多个字符组成，多字符选项必须出现在单字符选项之前。交互式shell通常使用-i,-s（从标准输入读）和-m（允许作业控制）选项
bash调用选项表格

| 选项            | 含义                                                                                                                              |
|:---------------|:----------------------------------------------------------------------------------------------------------------------------------|
| -c string      | 从string中读取命令string后面的参数作为位置参量，从$0启动                                                                                 |
| -D             | 被双引号括起来并以$开头的字符串列表打印到标准输出。如果本地术语集不是C或者POSIX，再加上-n选项，那么这样字符串将被作为语言翻译的目标，没有任何命令会被执行 |
| -i             | 启动交互模式，忽略TERM，QUIT和INTERRUPT                                                                                               |
| -s             | 从标准输入中取得输入，允许设置位置参量                                                                                                  |
| -r             | 启动受限制的shell                                                                                                                   |
| --             | 选项结束符信号，在这个符号以后禁止处理其他选项。这个符号后面的参数都被认为是文件名或参数                                                         |
| --dump-strings | 同-D                                                                                                                              |
| --help         | 显示内建命令的用法                                                                                                                   |
| --login        | 把bash作为登录shell                                                                                                                 |
| --noediting    | 在交互模式下不使用Readline库                                                                                                         |
| --noprofile    | 启动以后shell不读取初始化文件，如/etc/profile, ~/.bash_profile, ~/.bash_login以及~/.profile                                            |
| --norc         | 默认选项，在交互模式下，bash不会读取.bashrc文件。如果运行的shell类似sh，则该选项将默认打开                                                    |
| --posix        | 改变bash的行为符合posix1003.2标准，否则bash不会自动符合POSIX标准                                                                         |
| --quiet        | 启动过程中不显示任何信息，默认选项                                                                                                      |
| -rcfile file   | 若bash是交互式的，则用file替代初始化文件.bashrc                                                                                        |
| --restricted   | 启动受限制的shell                                                                                                                   |
| --verbose      | 打开verbose，同-v                                                                                                                  |
| --version      | 显示该bash shell的版本信息并退出                                                                                                      |

# Bash Shell编程

当命令不是从命令行开始执行而是从一个文件开始，这个文件就叫做shell脚本，这种模式叫做非交互模式。当bash开始非交互模式的时候，它就开始寻找环境变量，BASH_ENV(ENV)和启动文件（通常是.bashrc）并给它们赋值。在读取环境变量文件后，bash开始执行脚本。
**建立shell脚本的步骤**

**第一行**:
\#!被称为魔术数字,用于供内核确认哪个程序将翻译并执行这个脚本。该行必须在脚本的第一行。bash也可以由参数来调整程序的行为，参考bash选项。

**注释**:
注释以#开头，可以是单独一行，也可以与脚本名列在同一行，注释通常用来给别人或者自己看这个脚本的用途等信息。

**可执行语句和bash shell光标**:
bash脚本由linux命令，bash命令，程序结构和注释组成。

**使脚本可执行**:
当你创建一个文件后，它并没有执行的权限。你需要权限去执行它，通过chmod可以将其权限改变为可执行的。

```shell
$ chmod +x myscript
$ ls -lF myscript
# 加了-F参数会显示文件是否为可执行文件，此时会显示myscript*，表示这是一个可执行文件
```

```shell
#! /usr/bin/bash
# This is the first bash shell program of the day.
# script name: greetings
# written by: TonyYang
echo "Hello $LOGNAME, it's nice talking to you."
echo "Your present working directory is $(pwd)."
echo "You are working on a machine called $(uname -n)."
echo "Here is the list of ur files."
ls 
echo "Bye for now $LOGNAME,The time is $(date +%T)." # 这里的双引号也可以不加
```

执行上述脚本

```shell
$ chmod +x greetings
$ greetings or ./greetings
```

#### 读取用户输入

**变量**：
变量可以本地变量供当前的shell使用，也可以是环境变量。除非shell脚本还需要启动其他脚本，否则变量都用作本地变量在当前的脚本中使用。
从变量中提取值，再在变量前加美元符号。可以把变量用双引号引用，美元符号会被shell解释为变量值提取。在单引号中，变量值无法提取。

```shell
num=100 # local variable
export NUM=200 # global variable
```

**read命令**
read命令是用于从终端或者文件中读取输入的内建命令。read命令读取整行输入，每行末尾换行符被翻译为null（空字符串）。如果没有指定名称，读取的行就被赋值给一个特定的变量REPLY。你也可以使用read命令，使得程序停下来等待用户输入回车。
-r选项忽略反斜杠换行符，斜杠作为行的一部分；read命令共有四个选项：-a, -e, -p, -r

| 格式               | 含义                                                                                        |
|:------------------|:--------------------------------------------------------------------------------------------|
| read answer       | 从标准输入读取输入并赋值给变量answer                                                             |
| read first last   | 从标准输入读取输入到第一个空格或者回车，将输入的第一个单词放入变量first中，并将该行其他的输入放在变量last中 |
| read              | 从标准输入读取一行并赋值给内建变量REPLY                                                          |
| read -a arrayname | 把单词清单读入一个叫做arrayname的数组里                                                          |
| read -e           | 在命令行状态下打开命令行编辑，如果编辑器是vim，那么在提示符下就可以直接使用vim命令了                     |
| read -p           | 打印提示，等待输入，并将输入存储在REPLY中                                                         |
| read -r line      | 允许输入包含反斜杠                                                                             |

```shell
#!/usr/bin/bash
echo -e "Are u happy?"
read answer
echo "$answer is the right response"
echo -e "what is your full name?"
read first last
echo "hello $first"
echo -n "Where do u work?"
read
echo "I guess $REPLY"
#-----------------------------------
read -p "Enter ur job title:"
echo "I thought you might be an $REPLY"
echo -n "Who are ur best friends?"
read -a friends
echo "They are ${friends[*]}"
```

#### 数学计算

**declare命令**.用declare -i
命令可以将变量声明为整数。如果将字符串赋值给整数变量，bash将把0赋值给该变量。数学计算可以发生在整数变量之间。如果你想把一个浮点数赋值给整数变量，bash就会报告语法错误。整数变量可以接受不同进制的数，比如二进制，十进制以及十六进制。

```shell
$ declare -i num
$ num=hello
$ echo $num
$ num=5 + 5
$ echo $num
# bash无法识别这种格式，除非使用let命令，否则必须用引号引用或者删除空格
$ num=5+5
$ echo $num
# 10
$ num=4*6
$ echo $num
# 24
$ num="4 * 6" # 引号包围表达式，便可以在操作符和操作数之间存在空格
$ echo $num
# 24
```

```shell
$ declare -i 
#该命令可以显示所有整数变量以及它们的值。
```

**表达和使用不同的进制**。数字可以表达为二进制数，十六进制数和十进制数，范围从2进制~36进制。

```shell
$ declare -i num=017
$ echo $num # 15

$ num=2#101
$ echo $num #5

$ num=8#17
$ echo $num #15

$ num=16#b
$ echo $num # 11
```

**let命令**。let命令是bash的内建命令，用来做数学计算和数字表示法检测。

```shell
$ i=5 or let i=5
$ let i=i+1
$ echo $i

$ let "i = i + 1" # 要使用空格必须用引号包围，否则必须删除
$ echo $i

$ let "i+=1"
$ echo $i

$ i=3
$ (( i+=4 ))
$ echo $i

$ (( i=i-2 ))
$ echo $i

$ echo $(( i=i+10 ))
# 也可以直接这样来取的结果，括号里面的空格可有可无

$ echo $[ i=i+11 ]

```

#### 浮点数计算

bash支持整数的数学计算，如果需要进行更加复杂的计算，bc，awk和nawk实用程序非常有用。

```shell
$ n=$(echo "scale=3; 13/2"| bc)
$ echo $n
# 6.500

$ product=$(gawk -v x=2.45 -v y=3.123 'BEGIN{print "%.2f\n", x*y}'
$ echo $product
7.65
```

#### 位置参量与命令行参数

参考前面的内容，不做详述。这里只介绍set的另外一个用途。
**set命令和位置参量**：set命令可以用来重置位置参量，一旦重置，旧的参考变量就丢失了。恢复位置参量用命令set
--， $0永远是脚本名。

```shell
#！/usr/bin/bash
echo "The name of this script is $0"
echo "The argument are $*"
echo "The first argument is $1"
echo "The second argument is $2"
echo "The number of argument are $#"

declare -a oldargs=$*
set Jake Nicky Scott
echo All the positional parameters are $*.
echo The num of positional parameters is $#.
echo "good bye for now $1"
set $(date)
echo "The date is $2 $3 $6"
echo "The value of \$oldargs is $oldargs"
set $oldargs
echo $1 $2 $3
exit
```

```shell
$ set 'apple pie' pears peaches
$ for i in $*
> do
> echo $i
> done
# apple
# pie
# pears
# peaches
$ set 'apple pie' pears peaches
$ for i in "$*"
> do
> echo $i
> done
# apple pie pears peaches

$ set 'apple pie' pears peaches
$ for i in $@
> do
> echo $i
> done
# apple
# pie
# pears
# peaches

$ set 'apple pie' pears peaches
$ for i in "$@"
> do
> echo $i
> done
# apple pie
# pears
# peaches

#NOTE: 这里我们实际上创建了三个位置变量，通过echo $#会发现打印为3，但是当我们用for循环执行的时候，当$*不带引号且被展开时，带引号的字符串会被去掉两侧的引号，变成独立的单词，因此循环的时候执行四次，打印四次结果
# 不带引号时，效果与$@相同，但是当$*被引号包围时，里面的内容变成一个大字符串，所以只会循环一次。当$@带双引号时，当它被展开时，带引号的字符串不会被分隔成单独的单词，因此会按照字面意思打印出来。因此最后一种结果使我们想要的结果。
```

#### 条件结构和流控制

**退出状态**
条件命令运行你基于是或者否，真或假来处理一些任务。if是最简单的判断形势；if/else运行你执行两路判断，if/elif/else运行你执行多路判断。
bash运行两种类型的条件控制：命令的成功或者失败，表达式的真或者假。在两种情况下，都需要使用退出状态。退出状态值为0表示成功或者为真，否则为失败或者假。？状态变量保存了一个与退出状态值相互对应的数值。

```shell
$ name=larry
$ grep larry /etc/passwd
$ echo $? #命令执行成功，返回值为空值

$ grep wall /etc/passwd
$ echo $? #无法搜索到wall的用户，因此?变量的值为1，表示搜索失败
```

**内建test命令**
通常用内建的test命令给表达式赋值，这个命令也用来连接括号。可以使用test命令，或用一系列的括号代替test命令。只有test命令或者使用方括号时，表达式不能赋值。因为空格在在字符串中用来分隔单词，所以包括空格的单词需要使用引号。
在bash2.x以上版本时，\[\[\]\]可以用来给表达式赋值（内建混合test命令）。包含空格的字符串在整体使用时必须被引号引用，在简单的test命令中，逻辑符号&&(逻辑和)和||(逻辑或)可以替代-a或者-o选项。
虽然test命令可以给数学表达式赋值，但最好还是用let给数学表达式赋值，因为它有丰富的类c语言操作符。let命令可以利用双括号简化。
无论使用test命令，混合命令还是let命令，表达式的结果都将被检验，退出状态值是0表示成功，反之则表示失败。

```shell
# The test command

$ name=Tome
$ test $name =! Tom # 等号两边必须有空格
$ echo $?

$ [ $name = Tom ] # 第一个括号后面必须有空格，[]是test的变形，在判断字符串的时候允许使用一个或两个等号
$ echo $?

$ [ $name = [Tt]?? ] # test命令不允许使用通配符，因此该命令运行失败
$  echo $?

$ x=5
$ y=20
$ [ $x -gt $y ]
$ echo $?
# 1
$ [ $x -le $y ]
$ echo $?
# 0

```

```shell
# The compound test command
$ name=Tom
$ firend=Joseph
$ [[ $name = [Tt]om ]] #允许使用通配符
$ echo $? # 0

$ [[ $name == [Tt]om && $friend == 'Jose' ]]
$ echo $?
# 这里不支持模式匹配，因此后面一个表达式为假，因此返回假为1，如果需要模式匹配，需要开启这个开关

$ shopt -s extglob
$ [[ $name == [Tt]o+(m)y ]]
$ echo $?
# 0
```

```shell
# 总结这几个操作符的常见形式：
$ echo $[  num = num + 10 ] ✅
$ echo $[ 10 + 20 ] ✅
$ echo $(( nums = 10 + 20 )) ✅
$ echo $(( 20 + 10 )) ✅
$ echo $[[ nums = 10 + nums ]] ❌
$ [[ num = 1 + num ]] ❌
$ echo $[[ 1 + 1 ]] ❌

# 混合测试操作符不支持数学扩展



$ let "var = 1 + 2" ✅
$ echo $var
$ let var=var+3 ✅
$ echo $var

$ let var = 1 + 3 ❌
# let不支持带有空格的表达式，如果必须使用需要加字符串，或者用(())的形式来替换

$ [[ 1 > 0 && -1 < 0 ]] && echo "exectue successfully" ✅
$ [ 1 -gt 0 -a -1 -lt 0 ] && echo "exectue successfully" ✅
$ [ 1 -gt 0 ] && echo "exectue successfully" ✅
$ [ 1 -lt 2 ] && echo "exectue successfully" ✅
$ [ 1 -eq 1 ] && echo "exectue successfully" ✅

#以上这两种形式都是被允许的

$ (( 1 > 0 )) && echo "hello"

$ num=20
$ (( num > 10 )) && echo "world"
$ (( 10 == 10 )) ✅
$ (( 10 = 10 )) ❌
# 双括号里面进行变量替换时，可以省略美元符号

# [ ]和 [[ ]]和(( ))的区别主要是以下两点
# 1. [ ] 和 (( ))不支持模式匹配和通配符形式，而[[ ]]支持
# 2. [ ]中与或必须用-a 和 -o来表示，不能使用&&和||形式， 而[[ ]]和(( ))只能使用&&和||来表示与或
```

test命令操作符


| 判断操作符                | 判断是否为真 |
|:------------------------|:-----------|
| \[ string1 == string2 ] |            |
| \[ string1 = string2 ]  |            |
| \[ string1 != string2 ] |            |
| \[ string ]             |            |
| \[ -z string ]          |            |
| \[ -n string ]          |            |
| \[ -l string ]          |            |

table2

| 逻辑判断                          | 判断是否为真 |
|:---------------------------------|:-----------|
| \[ string1 -a string2 ]          |            |
| \[ string1 -o string2 ]          |            |
| \[ !string ]                     |            |
| (( exp1 && exp2 ))               |            |
| (( exp1 \|\| exp2 ))             |            |
| (( ! exp ))                      |            |
| \[\[ exp1 && exp2 \]\]           |            |
| \[\[ exp1 \|\| exp2 \]\]         |            |
| \[\[ ! exp \]\]                  |            |
| \[\[ pattern1 && pattern2 \]\]   |            |
| \[\[ pattern1 \|\| pattern2 \]\] |            |
| [\[ !pattern \]\]                |            |

NOTE: pattern可以包含元字符，在字符判断中，pattern2必须包含在引号中
双圆括号形式不支持-gt -lt等形式的操作符，只支持> < 这种形式 table3

| 整数判断            | 判断是否为真 |
|:-------------------|:-----------|
| \[ int1 -eq int2 ] |            |
| \[ int1 -gt int2 ] |            |
| \[ int1 -lt int2 ] |            |
| \[ int1 -ne int2 ] |            |
| \[ int1 -ge int2 ] |            |
| \[ int1 -le int2 ] |            |

table4

| 文件判断中的二进制操作   | 判断是否为真 |
|:---------------------|:-----------|
| \[ file1 -nt file2 ] |            |
| \[ file1 -ot file2 ] |            |
| \[ file1 -ef file2 ] |            |

table5 算术和比较操作符

| 操作符                       | 含义                  |
|:----------------------------|:---------------------|
| - +                         | 一元加减              |
| ! ~                         | 逻辑否                |
| * / %                       | 乘 除 余数            |
| +-                          | 加 减                 |
| <<, >>                      | 位左移，位右移         |
| <= >= < >                   | 比较运算符             |
| ==, !=                      | 等，不等              |
| &                           | 位逻辑与              |
| ^                           | 位逻辑非              |
| !                           | 位逻辑或              |
| &&                          | 逻辑与                |
| \|\|                        | 逻辑或                |
| = *= += -= <<= >>= &= ^= != | 快捷方式例如等同于i=1+i |

#### if命令

最简单的判断形势就是if，if结构后面的命令执行并返回退出状态值，退出状态值通常由程序的作者决定。如果退出状态值为0，命令成功就执行then后面的语句。在C
shell中，if后面的表达式必须跟c语言一样是一个布尔型的表达式。但是在其他shell中，可以是一组命令。如果命令退出状态值是0就执行关键字后面的语句块，直到遇到fi，fi终止if块。退出状态值非0，则意味着出现了错误，关键字then后面的语句被忽略。执行fi后面的语句。

```shell
格式 
if command 
then
    command
    command
fi

# using test for numbers and strings -- old format
if test expression
then
    command
    command
fi
     #or
if [ string/numeric expression ]
then
   command
   command
fi   

# using test for strings -- new format
if [[ string expression ]]
then
    command
    command
fi

# using let for numbers -- new format
if (( numeric expression ))
then
    command
    command
fi

# sample code

if grep "$name" /etc/passwd > /dev/null 2>&1
then
    echo "Find the user"
fi

# sample code 2
echo "Are u ok ?"
read answer
if [ "$answer" == y -o "$answer" == Y ]
then
   echo "Glad to hear that"
fi     

#note
if [ answer == y -o "$answer" == y ]
then 
    echo "Glad to hear that"
fi     
# 如果参数输入三个单词，则程序会运行失败，因此需要给$answer加上引号
# sample 3

if [[ ansewer == [yY]* || answer == Maybe ]]
then
    echo "Glad to hear it"
fi

# sample 4

shopt -s extglob

if [[ answer == [Nn]o?(way | t really) ]]
then
    echo Glad to hear it.
fi         
```

**exit命令和变量?**.exit命令用来结束脚本返回命令行。如果你希望在某种条件下退出脚本，可以使用该命令。exit命令的参数是一个在0~255之间的数字。如果参数是0，则表示顺利退出，而非0参数表示出现了一些问题。给exit命令的参数保存在?变量中。

```shell
#!/usr/bin/bash
if (( $# != 2 )) 
then
    echo "Usage: $0 mdays size" 1>&2
    exit 1
fi

if (( $1 < 0 || $1 > 30 ))
then
    echo "mdays is out of range"
    exit 2
fi 

if (( $2 <= 20 ))
then
    echo "size is out of range"
    exit 3
fi

find / -xdev  -mtime $1 -size=$2
```

**检验空变量**。当检验的变量值为空时，使用双引号或者test命令会失败。

```shell
#!/usr/bin/bash

if [ $name = "" ] #=> [ ! "$name" ] || [ -l "$name" ]
then
    echo "The name variable is null"
fi

remotes=$(/usr/bin/showmount)
if [ "X${remotes}" != "X" ] #这里加X是为了当remotes为空时，操作符两边仍然有占位符
then
    /usr/sbin/wall ${remotes}
    ...
fi
        
```

嵌套if命令

```shell
if command
then
    command(s)
else
    command(s)
fi        
```

```shell
#!/usr/bin/bash
echo "Please enter a name which u want to check"
read name
if grep "$name" /etc/passwd
then
    echo "find the user"
else
    echo "can not find the user"
    exit 1
fi    
```

```shell
#!/usr/bin/bash

id=$(id | gawk -F '[(=]' '{print $2}')
echo your user id is: $id
if (( $id == 0 ))
then
    echo "you are super user"
else 
    echo "you are not super user"
fi    
```

**if/elif/else命令**

```shell
if  command
then
    commands
elif command
then
    commands
else
    commands
fi            

```

sample

```shell
#!/usr/bin/bash
echo -n "How old are you? "
read age
if (( $age < 0 || $age > 120 ))
then
    echo "Welcome to our planet"
    exit 1
fi

if (( $age >= 0 && $age <= 12 ))
then
    echo " A child is a garden of verses"
elif (( $age >= 12 && $age <= 19 ))
then
   echo "Rebel without a cause"
elif (( $age >= 20 && $age <= 29 ))
then
   echo " You got the world by the tail"
elif (( $age >= 30 && $age <= 39 ))   
then
   echo "thirty something"
else
   echo "Sorry i aksed"
fi            
            

```

**文件检验**
在写脚本的时候经常的出现这样的情况--需要一个带有特定权限，特定类型或者特定属性的特定文件。你会发现，文件检验是写依赖脚本中非常必要的部分。
文件检验操作符列表

| 检验操作符    | 检验是否为真              |
|:------------|:------------------------|
| -b filename | 特定块文件                |
| -c filename | 特定字符文件              |
| -d filename | 目录存在                 |
| -e filename | 文件存在                 |
| -f filename | 非目录普通文件存在         |
| -G filename | 文件存在并属于一个有效的GID |
| -g filename | 设置set-group-ID         |
| -k filename | 粘滞位设置                |
| -L filename | 文件是一个符号链接         |
| -p filename | 文件是一个管道            |
| -O filename | 文件存在并属于一个有效的UID |
| -r filename | 文件可读                 |
| -S filename | 文件是一个套接字           |
| -s filename | 文件大小非0               |
| -t filename | fd已经在终端打开          |
| -u filename | 设置set-user-ID          |
| -w filename | 文件可写                 |
| -x filename | 文件可执行                |

```shell
#!/usr/bin/bash
file=./hello
if [ -d $file ]
then
   echo "$file is a directory"
elif [ -f $file ]
then
   if [[ -r $file &&  -w $file && -x $file ]]
   then
       echo "you have read, write,and exectue permission on $file"
   fi
else
   echo "$file is neither a file nor a directory"
fi               
```

**null命令**
null命令用冒号表示，是一个内建的什么都不做的命令，返回状态值为0.如果在if命令后面没有内容，同时又要避免产生错误信息，就需要在then后面写null语句。通常命令作为loop命令的参数来建立一个无限循环。

```shell
#!/usr/bin/bash
name=tom
if grep "$name" /etc/passwd >& /dev/null
then
   :
else
   echo "$name not found in database"
   exit 1
fi      
```

```shell
$ DATAFILE=
$ : ${DATAFILE:=$HOME/db/datafile}
$ echo $DATAFILE
$ : ${DATAFILE:=$HOME/db/junk} #这里不加null的话在终端会提示语法错误
$ echo $DATAFILE
# 结果与第一次结果相同，因为变量已经有值，因此不会再赋新的值。
```

```shell
#/usr/bin/bash
echo "Enter a number"
read number
if expr "$number" + 0 >& /dev/null
then
    :
else
   echo "You did not enter an integer value"
   exit 1
fi   
```

**case命令**
case命令是一个多路分支判断语句，可以用来替换if/elif结构。case命令会尝试用变量匹配value1,
value2.....直到匹配到。一旦一个值匹配了case变量，就执行这个值后面的语句，直到两个分号为止。然后就从esac后面开始执行。
如果case变量没有被匹配，程序就执行*)后面的语句，直到遇到；或者esac为止。*)的作用是跟在if/elif中的else的作用是一样的。case值中允许出现shell通配符和竖线作为OR操作符。

```shell
格式
case variable in
value)
     command
     ;;
value2)
     command
     ;;
*)
     command
     ;;
esac
               
```

```shell
#!/usr/bin/bash

echo -n "Choose a foreground color for your xterm window: "
read color
case $color in
[Bb]l??)
       echo "The color is blue"
       ;;
[Gg]ree*)
       echo "The color is grey"
       ;;
*)
       echo "No match color"
       ;;
esac                     
```

**用here文档和case命令建立菜单**。here文档和case命令通常一起使用。here文档用来建立在屏幕上显示出来的菜单选项。用户被要求在其中选择，case命令用来检验用户的选择并执行相应的命令。

```shell
$ cat <<- EDIT
    1) linux
    2) xterm
    3) sun
EDIT
 
read choice
case $choice in
1) 
   echo "linux"
   ;;
2) 
   echo "xterm"
   ;;
*)
   echo "sun"
   ;;
esac
         
```

#### 循环命令

循环命令就是反复执行一个命令或者一组命令，直到完成事前设置好的次数或者达到某种条件。bash
shell有三种循环：for循环，while循环和until循环。 **for命令**
for循环命令用于根据项目清单确定的次数执行命令。例如，你可以根据文件或者用户清单执行相同的命令。for命令后面紧跟着用户自定义变量-关键字in，然后是一个单词清单。第一次执行循环，单词列表中的第一个单词被赋值给变量。一旦单词被赋值给赋值变量，就进入循环体，执行关键字
do和done之间的命令。下一次的循环，第二个单词被赋值给变量，如此继续。循环体由do开始到done结束。当清单中的所有单词都轮换过一次以后，循环结束，程序控制继续done后面的语句。

```shell
格式

for  variable in world_list
do
  commands
done

#如果需要将for循环写成一行，则单词列表和do之间需要用分号来分隔.
for variable in world_list; do commands; done  
```

示例一

```shell
#!/usr/bin/bash
for name in Tom Dick Harry Joe
do 
   echo "Hi, $name"
done   
echo "Out of loop."

```

示例二

```shell
#!/usr/bin/bash
for person in $(cat my file)
do 
   mail $person < letter
   echo "$person was sent a letter."
done
   
echo "The letter has been sent."
```

示例三

```shell
#!/usr/bin/bash
dir=/home/tony/backupscripts
for file in backup{1..5} 
do
  if [ -f $file ] # 检查当前目录下该文件是否真实存在
  then
      cp "$file" $dir/$file.bak
      echo "The $file is backuped in $dir"
  fi
done      

```

单词列表中的$@和$*变量。在不使用双引号的时候是一样的。当使用引号的时候，$*的值是一个字符串，而$@的值是一组分开的单词。

```shell
#!/usr/bin/bash
# scriptname: greet
for name in $*
do 
  echo "Hi $name"
done  
(The Command Line)
$ greet Tom Jerry Larry Wall
# Tom
# Jerry
# Larry
# Wall
```

示例四

```shell
#!/bin/bash
#scriptname: perm_check
for filename
do
  if [[ -f $filename && ! -x $filename ]] 
  then
     chmod +x $filename
     echo "$filename now has exectue permission."
  fi
done
(The Command Line)     
$ perm_check * # 通配符会扩展成当前目录下面的所有文件
# file1 now has exectue permission.
# file2 now has exectue permission.
```

#### while命令

while命令判断它后面的命令，如果退出状态值是0，就执行循环体内的命令，直到done，控制返回循环体的顶部，while命令将再次检验命令的退出状态，直到退出状态为非0，程序继续执行done后面的语句。

```shell
格式

while command
do
  commands
done  

```

示例一

```shell
#!/usr/bin/bash
num=0
while (( $num < 10 ))
do
  echo -n "$num"
  (( num = $num + 1 )) # or let num+=1 or (( num += 1 ))
done
```

示例二

```shell
#!/usr/bin/bash
# scriptname: quiz
echo "Who was the 2nd U.S president to be impeached?"
read answer
while [[ "$answer" != "Bill Clinton" ]]
do
  echo "wrong try again"
  read answer
done
echo "You got it"
```

示例三

```shell
#!/usr/bin/bash
# script: sayit
echo "Type q to quit."
go=start
while [ -n "$go" ]
do  
  echo -n I love you
  read word
  if [[ $word = [Qq] ]]
  then
     echo "I will always love you!"
     go= # or break
  fi  
done     

```

**until命令**
until命令的用法跟while命令的用法类似，只是在until后面的语句为假的时候执行循环体，例如一个命令的退出状态值返回为非0。当到达done关键字以后就自动返回循环体的顶部，until命令再次检验命令的退出状态值。循环一直继续，直到until后面的退出状态值为0。当退出状态值为0时，循环退出，程序从done后面继续。

```shell
until command
do
  commands
done
```

示例一

```shell
#!/usr/bin/bash
until who | grep Yang
do
  sleep 5
done
# 除非Yang登录了终端，否则程序每隔5秒循环一次  
```

示例二

```shell
#!/usr/bin/bash

let hour=0
until (( $hour > 24 )) # or [ $hour -gt 24 ]
do
   case "$hour" in
   [0-9]|1[0-1]) 
        echo "Good morning!"
        ;;
   12)
        echo "Lunch time!"
        ;;
   1[3-7])
        echo "Siesta time!"
        ;;
   *)
        echo "Good night!"
        ;;
   esac
   
   let hour+=1
done   
```

**select命令和菜单**
here文档是建立菜单的简单方法，但是bash介绍了另外一种循环机制，叫做select循环，主要的作用就是建立菜单。一个数字化的菜单显示在标准错误上，PS3用来提示用户输入。默认的PS3是#？。在PS3提示显示以后，shell就等待用户输入。输入的是该菜单中的数字，若输入被保存在指定变量REPLY中，则变量REPLY中的数字与括号右边选项清单中的字符串有着对应关系。
case命令与select命令连用，允许用户在菜单中选择，基于选择执行相应的命令。LINES和COLUMNS变量可以用来决定在终端显示的菜单的层。输出显示在标准错误上，每一个选项前都有数字和右括号，PS3提示显示在菜单的底部。因为select命令是一个循环命令，因此一定要记得用break命令退出循环或者exit命令退出脚本。

```shell
格式
select var in wordlist
do
  commands
done  
```

示例一

```shell
#!/usr/bin/bash
PS3="Select a program to exectue: "
select program in 'ls -F' pwd date
do
  $program
done
  
(The Command Line)
Select a program to exectue: 2
1) ls -F
2) pwd
3) date
# /home/yang
# 在执行脚本的时候，PS3里面的字符串会显示在菜单的底部
```

示例二

```shell
#!/usr/bin/bash
PS3="Please choose one of the three boys: "
select name in tom jerry matz
do
   case $name in
   tom)
      echo "Tom is a cool dude!"
      break
      ;;
   jerry|matz)
      echo "Jerry and matz are both wonderful"
      break
      ;;
   *)
     echo "$REPLY is not one of your choice" 1>&2 
     echo "Try again"
     ;;
   esac       
done
```

示例三

```shell
#!/usr/bin/bash
PS3="Please enter the Tom's age: "
select choice in 20 30 40
do
  case $REPLY in
  1) 
    echo "you choose the right age ${choice}!"
    break;;
  2)
    echo "you choose the wrong age ${choice}!"
    break;;
  3)
    echo "you choose the wrong age ${choice}!"
    break;;
  *)
    echo -e "$REPLY is not a valid choice, try again!\n"
    REPLAY= # cause the menu to be redisplayed
  esac
done        


```

#### looping命令

在某些情况下，需要中断循环并回到循环顶部，或者需要一种方法结束当前循环，bash提供了一个命令来处理这些情况。
**shift命令**。shift命令用来把参量列表位移指定次数。没有参数的shift把参数变量向左位移一位。一旦位移发生，被位移出列表的参数就被永远删除了。通常在while循环中，shift用来读取列表中的参量。

```shell
$ set $(date)
$ echo $# # 5
$ shift
$ echo $# #参量往左移动一位，此时参量个数减1，变为4
$ shift 2
$ echo $# #参量往左移动两位，此时参量个数继续减2，变为2
$ shift 5
# 如果尝试位移多于参量总数的次数，shell会打印错误消息.

```

```shell
#!/usr/bin/bash
set $(date)
while (( $# > 0 ))
do
  echo -n "$# "
  shift
done
# 6 5 4 3 2 1
```
**内建break命令**
内建break命令用来从循环中抢先退出，但是不退出程序。执行break以后，控制从关键字done开始执行。break命令可以从内层循环中退出，所以如果你使用循环嵌套的话，break可以用一个数字作为参数，运行你指定break强行退出的循环的层数。如果你有三层嵌套循环，最外面的循环数是1，中间层的循环是2，最里面的循环数为3。在退出无限循环的时候，break非常有用。
```shell
格式
break[n]

```
示例一
```shell
#!/usr/bin/bash
while true; do
    echo Are you ready to move on\?
    read answer
    if [[ "$answer" == [Yy] ]]
    then
        break
     else
        echo "Try again"
     fi
done
echo "Here we are"           
```
**continue命令**
如果某些条件为真，continue命令控制跳转到循环顶部。所有continue命令后面的语句都将被忽略。如果嵌套循环，continue命令就跳转到最内的循环的顶部。如果一个数字作为它的参数，控制就可以在指定的任何层的循环的顶部重新开始执行。如果你有三层循环嵌套，最外面的循环号是3，中间层的循环号是2，最内的循环号是1。
```shell
#!/usr/bin/bash
for name in $(cat mail_list)
do 
  if [[ "$name" == ricard ]];then
     continue
  else
     mail $name < memo
  fi      
done

# continue后面的数字如果大于循环的嵌套层数，则会退出循环
```
**嵌套循环和循环控制**
当你使用嵌套循环的时候，continue命令和break命令都被赋予一个整数作为参数，控制流到第几层循环。
```shell
#!/usr/bin/bash
for month in Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec
do
  for week in 1 2 3 4
  do 
    echo -n "Processing the month of $month. ok?"
    read answer
    if [[ "$answer" == n || -z "$answer" ]]
    then
       continue 2
    else
       echo -n  "process week $week of $month ?"
       read answer
       if [[ "$answer" = n || -z $answer ]]
       then
           continue
       else    
          echo "Now processing week $week of $month."
          sleep 1 
          echo "Done processing..."
       fi      
    fi
  done
done    
       

```
#### I/O重定向和子shell
文件中的输入可以通过管道重新定向给一个循环，输出也可以通过管道重新定向给一个文件。shell启动一个子shell来处理I/O重定向和管道。在循环终止的时候，循环内部定义的任何变量对于脚本的其他部分来说都是不可见的。
示例一
```shell
#!/usr/bin/bash
if let $(( $# < 1 ))
then
   echo "Usage: $0 filename " >&2
   exit 1
fi

count=1
cat $1 | while read line #将cat读取的内容作为read的标准输入
do
  let $(( count == 1 )) && echo "Processing file $1..." > /dev/tty #将执行结果输出到屏幕
  echo -e "$count\t$line"
  let $(( count += 1 )) # 老式写法，也可写作(( count += 1 )) or let count+=1
done > temp$$ # $$为当前进程的PID，也称为父进程PID
mv temp$$  $1 #将临时文件重新命名为原来的文件名
exit  

```
通过管道重新定向循环的结果到一个linux命令。输出可以通过管道给另一个命令或重新定向到一个文件。
示例一
```shell
#!/usr/bin/bash
for num in 6 8 2 3 4 5
do
  echo $num
done | sort -n #将循环结果通过管道传递给sort命令，然后按照数字大小顺序从小到大排列。  
```
**在后台运行循环**。循环可以在后台运行，程序可以不等待循环的结束而连续运行。
示例一
```shell
#!/usr/bin/bash
for name in tom larry matz
do
  mail $name < memo
done & #通过&符号将循环放入后台运行
  
```
#### IFS和循环
Shell的内部域分隔符可以是空格，制表符和换行符。它可以作为命令的分隔符用在例如read，set或for等命令中。如果在列表中使用不同的分隔符，用户可以自己定义这个符号。在修改以前把IFS原始符号的值保存在另外一个变量中是一个好办法，这样可以在需要的时候恢复原来的值。
示例一
```shell
#!/usr/bin/bash
OLDIFS="$IFS" # 将原来的值赋值给一个新的变量，来作为备份。因为IFS的值是空格，因此这里需要用引号引用。
IFS=":" # 重新定义IFS的值，使命令可以以冒号作为分隔符来处理
names=Tom:Larry:Matz
for name in $names
do  
  echo "Hi $name"
done

IFS=$OLDIFS #恢复原来的值，使其可以将空格作为分隔符
set Jim Wall Jane
for person in $* # 也可以用$@，在没有引号引用的时候，效果一样
do
  echo "Hi $person"
done
```
示例二
```shell
#!/usr/bin/bash
names=Tom:Larry:Matz
for name in $names
do
  echo $name
done
# 此时的输出结果为Tom:Larry:Matz，因为默认IFS并没有设置冒号分隔符，因此循环的时候会把names的值看成整体执行输出。

IFS=":"
for name in $names
do
  echo $name
done
# 此时输出为
# Tom
# Larry
# Matz
# 切记当你需要改变默认的IFS值时，提前将它的值保存起来，以便后面再次利用它的初始值。
  
```
#### 函数

函数是在ATT的UNIX SYSTEM VR2版本开始引入到Bourne shell中的，并在Bourne again shell中得到强化。函数就是一个命令或者一组命令的名字。函数可以使程序模块化并提高效率，可以就在当前shell环境中执行。换言之，在执行像ls这样的可执行程序时并不产生子进程。你甚至可以把函数保存在文件中，而在准备使用时再把它们载入脚本。  
在使用函数时需遵循以下原则：
1. shell可以决定是执行一个别名，然后是函数，内建命令，最后才执行可执行程序。
2. 函数在使用前必须定义。
3. 函数在当前环境下运行。它跟调用它的脚本分享变量，并通过位置参量传递参数，通过local函数可以在函数内部建立本地变量。
4. 如果你在函数中使用exit命令，则可以退出整个脚本。如果函数退出，就返回到脚本中调用该函数的地方。
5. 函数中的return命令返回函数中最后一个命令的退出状态值或者给定的参数值。
6. 使用内建命令export -f可以把函数输出给子shell。
7. 使用内建命令declare -f可以显示定义的函数清单。如果只显示函数的名字，可以用命令declare -F。
8. 像变量一样，函数内部陷阱(trap)是全局的。它们可以被脚本和脚本激活的函数共享。如果是一个陷阱被定义为函数，它就可以被脚本共享。但是它可能产生意想不到的效果。
9. 如果函数保存在其他的文件中，就必须通过source或者dot命令把它们装入当前的脚本。
10. 函数可以递归。也就是说，函数可以调用自己，而且调用的次数没有限制。
```shell
格式

function function_name { commands; commands; } # 书写时函数体与花括号之间必须有空格
```
示例一
```shell

function dir { echo "Directories: "; ls -l | akw '^d' '{print $NF}'; }
function dir () { echo "Directories: "; ls -l | akw '^d' '{print $NF}'; }
# 函数名后面的括号某些情况下可以不加
```
**复位函数**：使用命令unset从内存中删除函数
```shell
格式

unset -f function_name
```
**输出函数**：函数可以输出给子shell
```shell
格式

export -f function_name
export -n function_name #取消导出一个函数
```
#### 函数参数和返回值
因为函数可以在当前shell内执行，变量对于函数和shell来说都是可见的。所以在函数内对于环境变量的任何修改都将影响shell。
**内建的local函数**: 要创建只在函数内部使用，且当函数退出时就消失的变量，可以用内建的local函数实现。

**参数**：通过为止参量可以向函数传递参数。位置参量对于函数来说是专用的。也就是说参数将不影响在函数外使用的任何位置参量。

**内建的return函数**：return函数可以用来退出函数并返回到脚本中调用该函数的地方(记住，它与exit有很大的区别，exit不但退出函数，而且还会直接退出整个脚本)。如果你没有为return命令指定参数，返回的函数值就是最后一行脚本的退出状态值。如果赋值给return命令，则这个值将保存在变量?中，这个值可以是0~256之间的一个整数。因为return命令限制只能返回一个0~256之间的整数。因此你可以使用命令替换来捕捉函数的输出。把整个函数放在括号内，前面是一个$,就是$(function_name)或者就像传统的捕捉linux命令的输出一样，通过引用把输出赋值给一个变量。
示例一
```shell
#!/usr/bin/bash
function usage { echo "error: $*" 2>&1; exit 1; }
if (( $# != 2 ))
then
    usage "$0: requires two arguments"
fi 

if [[ ! ( -r $1 && -w $1 ) ]]
then
   usage "$0: not readable and writeable"
fi
echo The arguments are $* # 这里的位置参数与函数内的同名位置函数互不影响，两个完全是相互独立的
exit
      
```
示例二
```shell
#!/usr/bin/bash

increment () { 
   local sum
   let "sum = $1 + 1"
   return $sum 
}

echo -n "The sum is "
increment 5
echo $?
echo $sum #空值，因为是函数的local变量在函数退出时就被删除了

```
示例二
```shell
#!/usr/bin/bash
function square {
    local sq
    let "sq = $1 * $1"
    echo "number to be squared is $1."
    echo "The result is $sq" # 输出内容作为返回值
}
echo -n "Give me a number to square: "
read number
value_return=$(square $number)
echo "The square value is $value_return."
```
#### 函数和source（或者dot）命令
**保存函数**：函数通常被定义在.profile中，所以当你登录时，它们就被定义了。函数可以被输出并保存在文件中。当你需要它们的时候，可以使用source命令或者dot命令和文件名来激活在这些文件中定义的函数。
示例一
```shell
#!/usr/bin/bash
#scriptname: first_one
echo "Hello World!"
function go {
    echo "I want to go fishing."
}

function fishing { 
    echo "wow, i got a fish!"
}
#在第一个脚本里面定义两个函数。

#!/usr/bin/bash
#scriptname：second_one
source first_one.sh # 在第二个脚本中导入第一个脚本定义的函数。除了用source，也可以使用.号来加载文件
go
fishing
# Hello world!
# I want to go fishing.
# wow, i got a fish!
# 如果first_one.sh里面不光定义了函数，还执行了其他命令，那么在导入文件的时候，第一个脚本里面的命令也会被一起执行。

```
source和.操作符的作用其实与我们在修改环境配置文件以后需要重新用source来初始化一下是一样的原理和作用。当我们source .bash_profile的时候，其实就是相当于执行了文件里面的命令，因此一些定义的环境变量会被导出。

#### 陷阱信号
当你的程序运行时，按下Control-C或者Control-\, 一旦该信号到达程序就立刻终止运行。但是在很多的时候，你可能并不希望在信号到达的时候，程序就立刻停止运行。而是它能希望忽略这个信号而一直运行，或者在程序退出以前，做一些清除操作。trap命令允许你控制你的程序在收到信号以后的行为。
信号的定义是由一个进程发送给另一个进程的，或者在特定的键按下以后由操作系统发送给进程，又或者在异常情况下发生时，由数字组成的非同步的消息。trap命令告诉shell根据收到的信号以不同的方式终止当前的进程。如果trap命令后面跟着一个用引号引用的命令，则在接收到指定数字后，就执行这个命令。shell总共读取两次命令字符串，一次是在设置trap的时候，一次是在信号达到的时候。如果命令字符串被双引号引用，在第一次trap设置时就执行变量和命令替换。如果是用的单引号引用，那么等到信号到达trap开始执行的时候，才运行变量和命令替换。
```shell
格式
trap 'command; command' signal-num
trap 'command; command' signal-name
```
示例一
```shell
trap  'rm file; exit 1' 0 1 2 15
trap  'rm file; exit 1' EXIT HUP INT TERM
# 两种方式都一样
```
如果在一个脚本运行过程中，系统接到一个前缀SIG，例如SIGHUP，SIGINT等等。bash允许你在信号上使用象征性名称。例如没有前缀或者用数字作为信号的名称。一个叫做EXIT的或者数字0的伪信号将在shell退出时，导致一个陷阱的触发执行。
详细的信号说明见文档。常见的信号以及它们的数值代号、说明如下：
Signal     Value     Comment
\-------------------------------
SIGHUP        1      终止进程，特别是终端退出时，此终端内的进程都将被终止
SIGINT        2      中断进程，几乎等同于sigterm，会尽可能的释放执行clean-up，释放资源，保存状态等(CTRL+C)
SIGQUIT       3      从键盘发出杀死(终止)进程的信号

SIGKILL       9      强制杀死进程，该信号不可被捕捉和忽略，进程收到该信号后不会执行任何clean-up行为，所以资源不会释放，状态不会保存
SIGTERM      15      杀死(终止)进程，几乎等同于sigint信号，会尽可能的释放执行clean-up，释放资源，保存状态等

SIGSTOP      19      该信号是不可被捕捉和忽略的进程停止信息，收到信号后会进入stopped状态
SIGTSTP      20      该信号是可被忽略的进程停止信号(CTRL+Z)

**信号复位**：trap命令后面跟一个信号或者数字，可以把信号复位为默认动作。一旦调用了函数，函数设置的陷阱可以被调用这个函数的shell识别。同时，在函数外设置的陷阱也可以被函数识别。
示例一
```shell
trap 2 or trap INT #为信号2设置默认动作。当按下Ctrl-C就会触发这个信号
# 这样用法并不推荐，可能会产生副作用。

trap - signal-list # 这种方法和上面这种方法效果一样，更加稳妥。
```
**忽略信号**：如果trap命令后面跟一对空引号，列表中的信号就会被进程所忽略。
```shell
trap " " 1 2
trap " " HUP INT
#信号1和2将被shell进行忽略
```

**陷阱列表**：通过输入trap命令，可以显示陷阱的列表和分配给陷阱的命令清单。

trap的语法格式为：
1. trap \[-lp]
2. trap cmd-body signal_list
3. trap '' signal_list
4. trap    signal_list
5. trap -  signale_list

语法说明：
语法1：-l选项用于列出当前系统支持的信号列表，和"kill -l"一样的作用。
       -p选项用于列出当前shell环境下已经布置好的陷阱。
语法2：当捕捉到给定的信号列表中的某个信号时，就执行此处给定cmd-body中的命令。
语法3：命令参数为空字符串，这时shell进程和shell进程内的子进程都会忽略信号列表中的信号。
语法4：省略命令参数，重置陷阱为启动shell时的陷阱。不建议此语法，当给定多个信号时结果会出人意料。
语法5：等价于语法4。
trap不接任何参数和选项时，默认为"-p"。


```shell
$ trap 'echo hello world' 2
$ trap # 显示默认设置的陷阱
# trap -- 'shell_session_update' EXIT
# trap -- 'echo hello world ' SIGKILL
# trap -- ' ' SIGINT

```
示例一
```shell
#!/usr/bin/bash
# scriptname: trapping.sh
trap 'echo "Control-C will not terminate $0."' INT
trap 'echo "Control-\ will not terminate $0."' QUIT
trap 'echo "Control-Z will not terminate $0."' TSTP

echo "When you are ready to exit, please enter a \"stop.\""
while true
do
  echo "go ahead --->"
  read
  if [[ $REPLY == [sS]top ]] # read命令如果没有指定变量，则输入内容默认保存在REPLY中
  then
      break
  fi
done  
(The output)
$ bash trapping.sh 
#When you are ready to exit, please enter a "stop."
#go ahead --->
#w
#go ahead --->
#e
#go ahead --->
#^ZControl-Z will not terminate trapping.sh.
#^CControl-C will not terminate trapping.sh.
#^\Control-\ will not terminate trapping.sh.
#^CControl-C will not terminate trapping.sh.
#^CControl-C will not terminate trapping.sh.
#^CControl-C will not terminate trapping.sh.
#^CControl-C will not terminate trapping.sh.
#^CControl-C will not terminate trapping.sh.
#^CControl-C will not terminate trapping.sh.
#^CControl-C will not terminate trapping.sh.
#^CControl-C will not terminate trapping.sh.
#stop #前面的三个信号都触发了设置的命令，打印出了内容，但是并不会终止脚本运行，直到输入stop终止循环。
   
```
**复位信号**：trap命令后面以信号名字或者号码作为参数，可以复位信号为默认动作。当然也可以以trap - siglist的形式来复位信号默认行为。
```shell
trap 'trap - 2' 2
# 需要按两次才能终止进程
```

**函数中的陷阱**：如果使用陷阱处理函数中的信号，一旦函数被激活，它将影响整个脚本。陷阱对于脚本来说是全局的。在下面的例子中，陷阱被设置为忽略中断ctrl-c。要终止这个脚本的循环就只能使用kill命令。它证明了在函数中使用陷阱可能出现不可预测的情况。
```shell
#!/usr/bin/bash
function trapper {
   echo "In trapper"
   trap 'echo "Caught in a trap"' INT
}

while : #等同于true
do
  echo "In the main script"
  trapper
  echo "still in main"
  sleep 5
done  

(The output)
执行以上脚本以后的输出:

#In the main script
#In trapper
#still in main
#^CCaught in a trap
#In the main script
#In trapper
#still in main
#^CCaught in a trap
#In the main script
#In trapper
#still in main
```

#### eval命令与命令行解析
eval命令可以对命令行求值，做shell替换，并执行命令行，通常在普通命令行解析不能满足要求时使用。
示例一
```shell
$ set a b c d e
$ echo The last argument is \$$#
$ set -x # 开启语法分析提示
$ eval echo The last argument is \$$#
# + eval echo The last argument is '$5'
# ++ echo The last argument is e            # eval解析后面的参数的过程
# The last argument is e 
$ set +x # 关闭语法分析提示
```
示例二
```shell
#!/usr/bin/bash

eval `/usr/bin/id | sed 's/[^a-z0-9=].*//'`
if [[ ${uid:=0} != 0 ]]
then 
    echo $0: Only can run $0
    exit 2 
fi
# 该命令会把id程序产生的输出作为sed命令输入源让sed命令进行处理。sed命令会从字符串的开始处开始查找，一个不是字母，数字和等号的符号，删除这个符号和这个符号后面的所有字符。
# 最后得到的结果为uid=xxxx, 如果当前用户为root则为0
# 接下来的命令判断当前用户是否为root用户，如果不是打印字符串，然后退出程序
```
#### 用getopts处理命令行选项
如果你的脚本你需要一些命令行选项，位置参量并不总是最有效的。例如ls命令有一些命令行选项和参数（每个选项前面需要一个前导破折号，但是参数不需要），选项可以通过许多的方式传递给程序：例如ls -l -i -F等。如果脚本需要参数，位置参量可能只用来处理参数，，例如ls -l -F -i。每一个破折号选项都相应的保存在$1, $2, $3中。但是，如果用户用一个破折号引导所有的选项会发生什么呢？-ilF就赋值给$1。getopts提供了一种像ls那样处理选项和参数的方法。getopts允许脚本程序处理各种方式组合的参数。
getopts的命令格式如下：
```shell
getopts optstring variable
```
getopts命令（注意是复数）内建于bashshell。它跟近亲getopt看起来很像，但多了一些扩展功能。与getopt不同，前者将命令行上选项和参数处理后只生成一个输出，而getopts命令能够和已有的shell参数变量配合默契。每次调用它时，它一次只处理命令行上检测到的一个参数。处理完所有的参数后，它会退出并返回一个大于0的退出状态码。这让它非常适合用解析命令行所有参数的循环中。
optstring值类似于getopt命令中的那个。有效的选项字母都会列在optstring中，如果选项字母要求有个参数值，就加一个冒号。要去掉错误消息的话，可以在optstring之前加一个冒号。getopts命令将当前参数保存在命令行中定义的variable中。getopts命令会用到两个环境变量。如果选项需要跟一个参数值，OPTARG环境变量就会保存这个值。OPTIND环境变量保存了参数列表中getopts正在处理的参数位置。这样你就能在处理完选项之后继续处理其他命令行参数了。

```shell
#!/usr/bin/env bash
#scriptname: test_opt.sh
echo
while getopts  :ab:c opt
do
  case $opt in
      a) echo "Found the -a option";;
      b) echo "Found the -b option, with value $OPTARG";;
      c) echo "Found the -c option";;
      *) echo "Unknown option: $opt";;
  esac
done
```
while语句定义了getopts命令，指明了要查找哪些命令行选项，以及每次迭代中存储它们的变量名（opt）。你会注意到在本例中case语句的用法有些不同。getopts命令解析命令行选项时会移除开头的单破折线，所以在case定义中不用单破折线。getopts命令有几个好用的功能。对新手来说，可以在参数值中包含空格
```shell
$ ./test_opt.sh -b "test1 test2" -c
# "Found the -b option, with value test1 test2"
# "Found the -c option"


```
另一个好用的功能是将选项字母和参数值放在一起使用，而不用加空格。
```shell
$ ./test_opt.sh -abtest1
# Found the -a option
# Found the -b option, with value test1
```
getopts还能够将命令行上找到的所有未定义的选项统一输出成问号。

```shell
$ ./test_opt.sh -d
# Unknow option: ?

$ ./test_opt.sh -abcde
# Found the -a option
# Found the -c option
# Unknow option: ?
# Unknow option: ?

```
optstring中未定义的选项字母会以问号形式发送给代码。  
getopts命令知道何时停止处理选项，并将参数留给你处理。在getopts处理每个选项时，它会将OPTIND环境变量值增一。在getopts完成处理时，你可以使用shift命令和OPTIND值来移动参数。

```shell

#!/usr/bin/env bash

echo
while getopts  :ab:cd opt
do
  case $opt in
      a) echo "Found the -a option";;
      b) echo "Found the -b option, with value $OPTARG";;
      c) echo "Found the -c option";;
      d) echo "Found the -d option";;
      *) echo "Unknown option: $opt";;
  esac
done

echo "$*"

echo $OPTIND
shift $[ $OPTIND - 1 ]
echo
count=1
for param in $@
do
  echo "The $count param is $param."
  let count+=1
done

# output:
#Found the -a option
#Found the -b option, with value test1
#Found the -c option
#Found the -d option
#-a -b test1 -c -d test2 test3 test4
#6
#
#The 1 param is test2.
#The 2 param is test3.
#The 3 param is test4.

```

| 选项 | 描述                          |
|:----|:------------------------------|
| -a  | 显示所有对象                    |
| -c  | 生产一个计数                    |
| -d  | 指定一个目录                    |
| -e  | 扩展一个对象                    |
| -f  | 指定读入数据的文件               |
| -h  | 显示命令的帮助信息               |
| -i  | 忽略文本大小写                  |
| -l  | 产生输出的长格式版本             |
| -n  | 使用非交互模式（批处理）          |
| -o  | 将所有输出重定向到的指定的输出文件 |
| -q  | 以安静模式运行                  |
| -r  | 递归地处理目录和文件             |
| -s  | 以安静模式运行                  |
| -v  | 生产详细输出                    |
| -x  | 排除某个对象                    |
| -y  | 对所有问题回答yes               |

#### Bash调试
在运行脚本时，可以通过可以bash加上适当的选项来开启一些测试功能。
除错选项

| 命令                | 选项      | 含义                                  |
|:-------------------|:---------|:--------------------------------------|
| bash -x scriptname | 显示选项   | 命令替换以后，执行以前显示脚本的每一行      |
| bash -v scriptname | 长选项    | 按照输入时候的样子在执行以前显示脚本的每一行 |
| bash -n scriptname | 不执行选项 | 解释但不执行命令                        |
| set -x             | 打开显示   | 跟踪脚本执行                           |
| set +x             | 关闭显示   | 关闭跟踪                               |
