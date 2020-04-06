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
|:------------------|:------------------------------------|
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

|        选项名         | 开关缩写 | 功能                                                                                           |
|:--------------------:|:-------:|:----------------------------------------------------------------------------------------------|
|      allexport       |   -a    | 打开此开关，自动标记新的或者修改要传出的变量，直到重新关闭                                             |
|     braceexpand      |   -B    | 将花括号的展开式设置为默认值                                                                      |
|        emacs         |         | 进行命令行编辑，使用emacs内置编辑器，是默认设置                                                     |
|       errexit        |   -e    | 假如一个命令返回一个非0的退出状态值（运行失误）后退出 ，则读入启动文件的时候不做任何设置                   |
|      hisexpand       |   -H    | 当进行历史命令替换时，使！和！！符号有效。这是默认设置                                                |
|       history        |         | 使命令行历史记录开关打开。这个是个默认值                                                            |
|      ignoreeof       |         | 防止退出shell的时候使得EOF(ctrl-D)失效。必须输入退出命令。当设置shell变量时也同理操作。IGNOREEOF=10     |
|       keyword        |   -k    | 将键盘参数至于环境中，并作为一个命令看待                                                            |
| interactive-comments |         | 在交互状态下的shell中，以#开头的命令行是注解                                                        |
|       monitor        |   -m    | 运行工作控制                                                                                    |
|      noclobber       |   -C    | 防止重定向时文件被覆盖                                                                           |
|        noexec        |   -n    | 读入但不执行命令。该选项用于检查脚本语法错误，但在交互环境下失效                                        |
|        noglob        |   -d    | 使路径扩展式失效，也就是说，关闭了通配符                                                            |
|        notify        |   -b    | 后台工作完成后通知用户                                                                           |
|       nounset        |   -u    | 当扩展一个未设置的变量时，系统报错                                                                 |
|        onecmd        |   -t    | 读入并执行完一个命令后退出                                                                        |
|       physical       |   -P    | 如果已经设置， 当键入cd或pwd时就不跟随符号链接，而使用实际目录                                        |
|        posix         |         | 如果默认操作没有匹配posix标准，则shell行为会发生改变                                                |
|      privileged      |   -p    | 设置之后，shell就不读入.profile或ENV文件，而且shell函数也不从环境中继承了。对于setuid脚本而言，则自动设置 |
|       verbose        |   -v    | 打开verbose模式进行调试                                                                         |
|          vi          |         | 使用vi内置编辑器来进行命令行编辑                                                                  |
|        xtrace        |   -x    | 打开”输出显示“模式以进行调试                                                                      |

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

|         反斜杠序列         | 功能                                                             |
|:-------------------------:|:----------------------------------------------------------------|
|            \t             | 以"HH:MM\:SS"格式显示的当前时间                                    |
|            \d             | 以"Weekday Month Date" 格式显示的日期（"Tue May 26")              |
|            \n             | 换行符                                                          |
|            \s             | shell的名称，$0(接着的部分是终结斜线）的基本名（basename)             |
|            \w             | 当前工作目录                                                     |
|            \W             | 当前工作目录的基本名                                               |
|            \n             | 当前用户的用户名                                                  |
|            \h             | 主机名                                                          |
|            \#             | 本命令的命令个数                                                  |
|            \!             | 本命令的历史序列号                                                |
|            \$             | 如果有效UID为0，则为#， 否则为$                                    |
|           \nnn            | 八进制数字对应的ASCII字符                                          |
|            \\             | 一个反斜杠                                                       |
|            \[             | 非打印字符串序列的起始符，这些非打印字符串可用于在提示符中嵌入终端控制序列 |
|            \]             | 非打印字符串序列的结束符                                           |
| 以下为bash 2.x版本中的新序列 |                                                                 |
|            \a             |                                                                 |
|            \@             | 以12小时AM/PM格式显示当前时间                                      |
|            \H             |                                                                 |
|            \T             | 以12小时HH:MM\:SS格式显示当前时间                                  |
|            \e             | ASCII转义字符（033）                                             |
|            \v             | bash版本号，如2.03                                               |
|            \V             | bash版本号，如2.03.0                                             |

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

***搜索路径***
.bash用PATH变量来定位在命令行键入的命令.路径是用冒号分隔的目录列表,
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
***source或者.***: source命令是bash shell的内置命令。点命令就是一个点符号，（从Bourne shell而来）是source的另外一个名称。这两个命令都已一个脚本为参数, 该脚本将作为当前shell的环境解释执行, 即不会启动一个新的子进程. 所有在脚本中设置的变量将成为当前shell环境的一部分. 同样的，当前脚本中设置的变量也将成为脚本的环境, source(或点)命令通常用于重新执行刚修改过的初始文件，如.bash_profile和.profile等等。例如，如果在登陆后对.bash_profile中的EDITOR和TERM变量进行了修改，则可以用source命令重新执行.bash_profile中的命令而不用注销并重新登录。像.bash_profile或其他类似的shell脚本这样，文件无需可执行权限即可用source或点命令执行。
```shell
$ source .bash_profile
$ . .bash_profile
以上两种方式都可以对初始化修改过的文件.
Note: 如果.bash_profile直接作为脚本运行, 会启动一个子shell, 这样就会在子shell中设置变量, 当前退出子shell时, 父shell未受到这些变量设置的影响.
```
#### 命令行
登录后，bash shell默认情况下即显示主提示符，美元符号($)。而shell就是命令解释器，如果shell在交互模式下运行，它会从终端读入命令将其分隔为单词。命令行由以空格(多个空格或制表符)分隔的一个或多个单词或符号组成，并以换行符（由回车键产生）为结束标志。命令的首单词是命令，后续单词为命令参数。命令可能是像ls或date这样的Linux/UNIX可执行程序，用户自定义函数，像cd和pwd这样的内置命令，和一个shell脚本。命令可能包含成为元字符的转移字符串，在分析命令行时它们将被解释。如果命令行过长，可用反斜杠加上换行符来续行，随即显示次提示符，直到命令行结束。
***处理命令的顺序***。命令行的首单词 是执行命令。命令可能是一个关键字，函数，特定的内置命令或工具，可执行程序或是一个shell脚本等。根据命令类型参照下列顺序执行命令：
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
3. enable内置命令可以打开/关闭built-in开关。默认情况下是运行built-ins。禁止built-in使得即使在与内置命令同名的情况下，也执行在磁盘上找到的，且不指定全路径名的可执行命令（通常的处理是，bash先于磁盘可执行命令而搜索内置命令）。-n开关可以关闭built-in。shell程序员常犯的错误是将脚本命名为test。由于test是一个内置命令，shell将会试图执行它而不是执行用户的脚本（内置命令通常先于任何可执行程序执行）。通过键入enable -n test可以禁止test built-in，这样就能优先执行用户的脚本了。
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
***后台执行命令***。 通常情况下命令都在前台运行，直到命令结束，提示符才会继续出现。有的时候等待命令结束并不是特别方便，因此可以将命令放到后台并行执行，而我们无需等待就可以继续执行下一条命令。后台执行完了以后会将结果输出到屏幕（标准输出）。因此，如果要在后台运行命令，命令的输出结果应该被重新定向到一个文件和指向其他设备的管道，如打印机，这样输出结果就不会影响你手中的工作。
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
***作业控制***
作业控制是bash shell提供的一项强大功能，它允许你选择在前台还是后台运行程序，即作业。运作中的程序称为进行或作业，每一个进程都有一个进程id号码，称为PID。通常情况下，在命令行中键入的命令会在前台持续运行，直至通过键入Ctrl-C或Ctrl-\发送信号来终止它。有了作业控制，你就可以将作业送到后台中持续执行。你可以通过键入Ctrl-Z来停止一个作业（实际上是将作业送到后台，并挂起）。也可以让停止的进程在后台运行。可将在后台运行的程序送回前台运行，甚至可以终止正在前台和后台运行的程序。
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

| 变量          | 含义                                                                                                                                                                                                               |
|:-------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FCEDIT       | 使用fc命令的Linux编辑器的路径名                                                                                                                                                                                       |
| HISTCMD      | 当前命令的历史号，即在历史列表中的索引号。如果没有设置HISTCMD，即使跟着重置，其功能也将失效                                                                                                                                     |
| HISTCONTROL  | 如果赋值为ignorespace，则以空格开头的行将不会进入历史列表。如果赋值为ignoredups，与上一条命令相同的命令行也不会进入到历史列表中。如果赋值为ignoreboth则综合了这两个选项。如果不设置或没有设置为上述值，所有经分析器读入的命令行都会存储到历史列表中。 |
| HISTFILE     | 指定用于存储历史列表的文件。默认值为~/.bash_history。如果不设置的话，在交互式shell退出的时候，将不会存储历史命令。                                                                                                                |
| HISTFILESIZE | 历史文件可存储的最大历史行数。如果该变量被赋值，那么在需要的时候，历史文件将被截断以限制在指定的行数内。默认值为500                                                                                                                  |                                                                                                                                                                                                               |
| HISTSIZE     | 在命令历史中可以存储的命令条数。默认值为500                                                                                                                                                                              |

**从历史文件中访问命令**
箭头键：可以用键盘上的方向键来在历史文件中上下左右移动。也可以用标准的删除，退格及修改等键来对历史文件中的命令进行编辑。在编辑命令的时候，按回车键即可重新执行该命令。

**箭头键**

| 箭头 | 含义                  |
|:----|:---------------------|
| ↑   | 上移历史列表           |
| ↓   | 下移历史列表           |
| →   | 在历史命令中向右移动关标 |
| ←   | 在历史命令中向左移动关标 |

**内置命令**。内置命令history用于显示已经键入的命令历史，命令前有event number（事件号）.
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
**fc命令**
fc命令也成为fix命令，有两种用法：
1. 从历史列表中选择命令
2. 在vi或emacs或系统其它的编辑器中编辑命令
第一种用法：带-l选项的fc命令可从历史列表中选择指定的命令行或某个范围的命令行。如果打开-l开关，结果将输出到屏幕。如fc -l，默认情况下将显示历史列表中的最后16行，fc -l 10则显示在整个历史列表中序号为10的命令行，fc -l -3选择最后3行。-n开关可用于关闭历史列表中序号功能。如果打开了该开关，你就可以选择某个范围的命令行并把它们重新定向到文件中，该文件可作为一个脚本被执行。-r开关用于反转命令的顺序。
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
如果fc带有-s选项，可用一个模式字符串来重新执行先前的命令。如fc -s rm将重新执行匹配rm模式的最近执行的命令。
**重新执行历史命令**
要执行命令列表中的命令，须使用感叹号（称为bang）如果你用两个感叹号(！!)bang，bang，则将重新执行历史列表中最后的命令。如果你在感叹号后跟一个数字，将执行该数字指定的命令。如果键入的是感叹号加一个字符或字符串，那么以该字符或字符串开头的，最近的命令就会被重新执行。(^)也被用作编辑先前命令的一个快捷方法。！的具体用法见下表：

| 条件指示符     | 含义 |
|:-------------|:----|
| !            |     |
| !!           |     |
| !N           |     |
| !string      |     |
| !?string?    |     |
| !?string?%   |     |
| !$           |     |
| !!string     |     |
| !Nstring     |     |
| !N:s/old/new |     |
| !N:g/old/new |     |
| ^old^new^    |     |
| command!N:wn |     |

