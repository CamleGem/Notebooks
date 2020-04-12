# Bash Shell

>**Author: TonyYang**  
>**Date：2020/04/05**

##### 查看Shell版本

```bash
$ bash --version
$ echo $BASH_VERSION

```

##### 改变当前工作的shell

```bash
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
shell而来）是source的另外一个名称。这两个命令都已一个脚本为参数,
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
shell默认情况下即显示主提示符，美元符号($)。而shell就是命令解释器，如果shell在交互模式下运行，它会从终端读入命令将其分隔为单词。命令行由以空格(多个空格或制表符)分隔的一个或多个单词或符号组成，并以换行符（由回车键产生）为结束标志。命令的首单词是命令，后续单词为命令参数。命令可能是像ls或date这样的Linux/UNIX可执行程序，用户自定义函数，像cd和pwd这样的内置命令，和一个shell脚本。命令可能包含成为元字符的转移字符串，在分析命令行时它们将被解释。如果命令行过长，可用反斜杠加上换行符来续行，随即显示次提示符，直到命令行结束。
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

$ enable -n test
function cd() {
    builtin cd
    echo $PWD
}
此时会优先执行定义的函数而不是内置的test命令.
Note: 在定义新的函数的时候选择与操作系统相同的名字不是编程的好习惯.需要避免, 如果一个定要这么做需要将内置命令的built-in禁止.
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
| !!string     | 将string追加到前一条命令中，并执行之                                                                                         |
| !Nstring     | 将string追加到第N条命令中，并执行之                                                                                          |
| !N:s/old/new | 替换第N条命令中第一个old字符串为new                                                                                          |
| !N:g/old/new | 将第N条命令中所有old字符串替换为new                                                                                          |
| ^old^new^    | 替换最近执行命令的old字符串为new                                                                                             |
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

$fc -1 -3 # 将最后三条命令从历史列表调入到编辑器的缓冲区。
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

#### 元字符（通配符）

元字符是用于表示某些特定而非其自身含义的特殊字符，shell元字符称为通配符（wildcard）。见下表

| 元字符   | 含义                            |
|:--------|:-------------------------------|
| \\      | 按文本含义解释后面接着的字符        |
| &       | 在后台运行进程                   |
| ;       | 命令分隔符                       |
| $       | 变量替换                        |
| ?       | 匹配一个字符                     |
| \[abc]  | 匹配一个字符集中的一个字符，如a,b,c |
| \[!abc] | 匹配一个字符集外的一个字符，如a,b,c |
| *       | 匹配零或多个字符                  |
| (cmds)  | 在子shell中执行命令              |
| {cmds}  | 在当前shell中执行命令             |

#### 文本名替换

当对命令行求值时，shell用元字符来缩写与某些字符集匹配的文件名或路径名。将元字符扩展为文件名的过程也称为文件名替换或globbing。如果使用了元字符而又没有任何文件名可匹配，shell将把该元字符视为文本字符。

| 元字符      | 含义                             |
|:-----------|:---------------------------------|
| *          | 匹配零或多个字符                   |
| ？         | 匹配一个字符                       |
| \[abc]     | 匹配一个字符集内的一个字符，如a,b,c   |
| \[!abc]    | 匹配一个字符集外的一个字符，如非a,b,c |
| {a,ile,ax} | 匹配一个字符或字符集                |
| \[!a-z]    | 匹配从a~z范围以外的一个字符          |
| \          | 转义或禁止元字符                   |

Note: *是匹配文件名中零个或任意个字符的通配符

```shell
$ ls *
# file.bak abc adg f.bak
$ ls *.bak
# file.bak f.bak
$ echo a*
# abc  adg
$ echo c*
# c* 因为目录下不存在c开头的文件，因此会把通配符视为文本字符打印出来
$ ls 
# abc abc1 abc123 abc2
$ ls  a?c?
# abc1 abc2
$ echo abc???
# abc123
$ echo ??
# ??  不存在两个字符的文件名，因此视为文本字符打印出来
```

**方括号** 这种括号用于匹配包含某个字符集或某个字符范围内的一个字符的文件名

```shell
$ ls
# abc abc1 abc123 abc122 abc2 file.bak
$ ls abc[123]
# abc1 abc2
$ ls abc[1-3]
# abc1 abc2
$ ls [a-z][a-z][a-z]
# abc
$ ls [!f-z]???
# abc1 abc2
$ ls abc12[23]
# abc122 abc123

$ ls
# a.c b.c ac ab3 ab4 fumble faa
$ ls f{oo,aa,umble} # 花括号中若有空格则会导致出错信息
#  faa fumble
$ ls a{.c,c,b[3-5]}
# a.c ab3 ab4
$ mkdir /tmp/{old,new,dist,bugs}
# old new dist bugs 在/tmp目录下面创建者四个文件
$ chown root /tmp/{usr/{ex,edit},lib/{ex?.?*,how_ex}}
# 花括号也能嵌套使用，这里匹配的是/tmp/usr/ex, /tmp/usr/edit, /tmp/lib/ex1.cc, /tmp/lib/how_ex
$ echo  fo{o, um}*
# fo{o,um}* 括号里面不能有空格，如果有任何一个没有用引号括起来的空格，就不会进行括号扩展
$ echo {mam,pap,ba}a
# mama papa baa
$ echo post{script,office,ure}
# postscript postoffice posture
```

**转义字符** 要将元字符视为普通文本字符，可以用反斜杠来禁止元字符被解释扩展。

```shell
$ ls
# abc file1 youx
$ echo how are you?
# how are youx
$ echo how are  you\?
# how are you?
$ echo when does this line \
> ever end\?
# when does this line ever end? 通过添加前导反斜杠来转义换行符。
```

**波浪号和Hyphen扩展** 波浪字符被bash
shell采纳为路径扩展字符。波浪号本身代表用户主目录的全路径名。当把波浪号加在一个用户名前，则表示该用户的全路径名。
当波浪号后面跟着加号时，PWD(present workding
directory,代表当前工作目录）的值将替换波浪号所代表的的目录名，波浪号所代表的的目录名将被替换为先前的工作目录。OLDPWD指的是先前的工作目录。

```shell
$ echo ~
# /home/tony
$ echo ~jobs
# /home/jobs 表示jobs用户的家目录路径全名
$ pwd 
# /home/jobs
$ cd /etc/
$ echo ~+
# 表示工作目录的全路径即/etc
$ echo ~-
# 表示先前工作目录的全路径/home/jobs
$ echo $OLDPWD
# 保存先前工作目录的值即/home/jobs
$ cd -
# 减号引用了先前工作目录，cd命令切换到并显示先前的工作目录
```

**控制通配符** 如果设置了bash noglob变量或者使用了带-f选项的set
命令，称为globbing的文件名替换就会被禁止。这意味着所有元字符将表示其自身，而不再被视为通配符。这可能在使用grep，sed或awk程序搜索含元字符的模式时非常有用。如果没有设置globbing，所有元字符必须用反斜杠进行转义以关闭通配解释。
内置shopt命令也支持控制globbing的选项.

```shell
$ set -o noglob or set -f

$ print * ?? [] ~ $LOGNAME
# * ?? [] /home/tony tony 这里因为波浪线没有用于文件名扩展，因此还是会被继续扩展解释。

$ set +o noglob or set +f 
# 继续开启文件名元字符扩展

$ shopt -s dotglob
# 开启点开头文件的匹配，默认情况下是关闭状态
$ echo *bash*
# 如果dotglob处于关闭状态，则会打印*bash*，如果开启了dotglob，则会文件名扩展，并且包含点开头的文件名
# 例如.bash_profile .profile .bashrc等隐藏文件
```

**扩展文件名globbing** 沿袭了Korn
shell的模式匹配，bash也有扩展的功能，允许正则表达式类型的语法，除非用shopt命令的extglob选项打开该功能，否则无法识别正则表达式操作符。
shopt -s extglob

扩展模式匹配表

| 正则表达式          | 含义                                                                                     |
|:------------------|:-----------------------------------------------------------------------------------------|
| abc?(2\|1)1       | ?匹配括号里的零个或一个字符。竖线代表or条件；即2或9.匹配的是abc21.abc91或abc1                     |
| abc*(\[0-9])      | *匹配括号里的零个或多个字符。匹配以abc开头，接着是零个或多个数字的模式。比如abc, abc1234, abc3, abc2 |
| abc+(\[0-9])      | +匹配括号里的一个或多个字符。匹配以abc开头，接着是一个或多个数字的模式。比如abc3，abc123             |
| no@(one\|ne)      | @匹配括号里的一项.匹配noone或none                                                            |
| no!(thing\|where) | ！匹配除了括号里模式的所有字符串。匹配no，nobody或noone，但不匹配nothing或nowhere                 |

```shell
$ shopt -s extglob
$ ls
# abc abc122 f1 f3 nonsense nothing one abc1 abc2 f2 none noone nowhere
$ ls abc?(1|2)
# abc1 abc2 abc
$ ls abc*([0-9])
# abc abc1 abc122 abc2
$ ls abc+([0-9])
# abc1 abc2 abc3
$ ls no@(thing|ne)
# nothing none
$ ls no!(thing)
# none nonsense noone nowhere
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
| ${variable\:-word}        | 如果变量被设置了而且非空(null)就替代变量的值，否则设置为word                                                   |
| ${variable\:=world}       | 如果变量被设置了而且非空就替代变量的值，否则就把变量永久设置为word                                               |
| ${variable\:+world}       | 如果变量被设置了而且非空就替代word，否则什么也不做                                                            |
| ${variable\:?word}        | 如果变量被设置了而且非空就替代变量的值，否则就打印word，然后退出shell。如果word为空就打印parameter null or not set |
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
跟Bourne shell不同，bash尽量让你知道是不是丢失了引号。在交互状态下，如果丢失了引号，就会出现次级提示符，进而在脚本中检查整个文件，看看是不是缺少引号，如果出现引号不匹配的情况，shell就会尝试用下一个合法的引号来匹配它。如果下一个合法的引号不能匹配该引号，脚本就会终止。引号可能是程序最大的敌人之一
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
**双引号**
双引号必须成对出现，它们运行变量和命令替换，但是保护其他符号不被翻译
```shell
$ name=Jody
$ echo "Hi $name, I am glad to meet you."
# Hi Jody, I am glad to meet you.
$ echo "Hey $name, the time is $(date)"
# Hey Jody, the time is Sun Apr 12 20:51:13 CST 2020
# 双引号中的变量和命令替换依然会正常执行，不会被禁止解释
```
#### 命令替换
在需要把命令的输出结果赋值给一个变量或者需要用字符串替换变量的输出结果时，我们可以使用变量替换，所有的shell都使用反引用的方法进行命令替换。bash有两种命令替换的方法：第一种是老的方法把命令放在反引号中，另外一种是新的korn shell风格，把命令放在一对圆括号中，并在前面缀上一个美元符号。
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
bash2.x版本提供了创建一维数组的能力。数组允许你把一串数字，一串名字或者一串文件放在一个变量中。使用内建功能declare -a 可以创建数组，或者在变量后面增加脚标直接创建数组如x\[0]=5, 数组的脚标是从0开始的。数组的尺寸没有限制，脚标也不必须是一定顺序的数字。获取数组中某个元素的语法是${arrayname\[index]}。如果declare命令后面有-a和-r选项，那么就创建只读数组。
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
**定义函数**
定义函数有两种方式，一种是旧的Bourne shell方式，函数名后面是一对空括号，然后是函数的定义。新的方式是使用关键字function，后面是函数名和函数定义，如果使用新的方式，那么括号是可选的。函数定义被放在一对大括号**中间**。它包括一些用分号分隔的命令。最后一个命令后面也需要分号。大括号前面需要保留一些空间。所有需要向函数传递的参数都被当做位置参量来处理。位置参量在函数中是本地变量。内建的local命令运行你在函数内部创建本地变量。函数是可以递归的，例如不断引用自身直到一定的次数。
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
**Note: 函数的位置参量只在函数内部使用，不能供函数外部使用。在命令行设置一些位置参量，它们对于函数的位置参量没有影响。**
**列出和清空函数**
列出函数和它们的定义使用命令declare在bash2.x及以上版本中declare -F只列出函数的名字，函数的定义将跟局部变量一起送到标准输出。unset -f命令可以清空这些函数。
