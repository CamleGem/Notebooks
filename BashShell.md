# Bash Shell

##### 查看Shell版本
```bash
bash --version
echo $BASH_VERSION

```

##### 改变当前工作的shell
```bash
chsh 命令
```

| 选项 |功能  |
| --- | --- |
|  -l, --list-shells| 打印/etc/shells中合法shell的清单并退出 |
| -s, --shell | 指定登录shell |
|  -u, --help| 打印使用方法信息并退出 |
| -v, --version |打印版本信息并退出  |

note： 当省略了参数以后，chsh会一直要求用户输入登录shell的完整路径名，如果不是完整路径，则会失败且无效。


#### shell 环境
一个进程的环境是由变量，打开的文件，当前的工作目录，函数， 资源限制和信号等组成。这些特征的定义是从一个shell到另外一个shell继承来的，用于配置工作环境。用户shell配置定义在shell的初始化文件中。
##### 初始化文件
bash shell有很多初始化文件，这些初始化文件中的设置作为当前shell的一部分存在，读取哪些初始化文件取决于是否是登录shell，是否是交互模式（非登录Shell），或者非交互模式（shell脚本）。
在登录到系统，shell提示符出现在屏幕上之前， 系统初始化文件/etc/profile就被读取了，接着，如果用户主目录下存在 bash_profile文件，那么这个文件就会也被读取，它的作用是设置用户别名和函数，并建立用户指定的环境变量和启动脚本。
如果用户没有.bash_profile文件，但是存在.bash_login文件，那么就读取后者，如果后者不存在， 但是有profile，那么就读取.profile文件。
```shell
/etc/profile
该文件是由系统管理程序建立的一个泛系统的初始文件，这是一个全局的配置文件，系统登录就会加载该配置文件。

~/.bash_profile文件
假如用户的根目录里存在这个文件，bash就在读取完/etc/profile后加载这个配置文件。如果这个文件不存在，bash就会查找另一个用户自定义文件.bash_login并且加载它。如果.bash_login文件也不存在，则加载.profile(如果该文件存在的话）。只有.bash_profile, .bash_login, .profile三个文件中的一个会被加载。bash会检查用户是否有一个.bashrc文件，如果有就加载该配置文件。

~/.bashrc
该文件中通常包含用户自定义的别名和函数
当创建一个该用户的字shell时，只会加载该配置文件，上面提到的几个文件不会被再次加载。

/etc/bashrc
该文件属于全局文件，任何用户在登陆之前都会读取这个配置文件，全局的函数和别名定义在该文件中。

~/.profile
.profile文件是一个用户自定义初始化文件，保存在用户的主目录下，一旦用户运行shell，或者bash登录系统，而以上的初始化文件都找不到的时候，就会读取该文件。它允许用户定制自己的shell环境，环境和终端设置通常保存在该文件中。窗口应用程序和数据库应用程序也都是从这里开始初始化的。

~/.bash_logout
用户退出登录，即退出所登录的shell之时，若存在一个名为~/.bash_logout的文件，就执行它。该文件通常包含清除临时文件，截断历史文件和记录退出登录时间等作用的文件。

```
##### 如果防止执行启动文件的选项。参考下面
```shell
如果bash被带 -noprofile选项的命令激活（如bash -noprofile），/etc/profile, ~/.bash_login或~/.profile的启动文件将不会执行。
如果激活时带的是-p选项（譬如bash -p，则bash不会读用户的~/.profile文件。
假如它是作为sh被激活， 则bash就尽可能模仿Bourne shell环境。对于一个登陆shell，它仅执行/etc/profile和~/.profile。-noprofile可能会继续使用而使该行为不能运行。假如shell是作为sh激活的，就不会执行任何其他的启动文件。
```


#### 用内置的set和shopt命令来设置bash的选项

```shell
set -o 选项。使用-o选项开关，set命令就能选择是否接受选项。而使用各种选项就能自定义自己的shell环境。它们的开关与否通常都在BASH_ENV(ENV)文件内设定。例如，set -o noclobber也可以写成set -C.见下表：
```
#### 内置 set 选项表
|选项名  |开关缩写  |功能  |
| --- | --- | --- |
|allexport  | -a | 打开此开关，自动标记新的或者修改要传出的变量，直到重新关闭 |
| braceexpand |-B  |  将花括号的展开式设置为默认值|
| emacs |  | 进行命令行编辑，使用emacs内置编辑器，是默认设置 |
| errexit | -e | 假如一个命令返回一个非0的退出状态值（运行失误）后退出 ，则读入启动文件的时候不做任何设置 |
| hisexpand | -H | 当进行历史命令替换时，使！和！！符号有效。这是默认设置 |
| history |  |使命令行历史记录开关打开。这个是个默认值  |
|  ignoreeof|  |  防止退出shell的时候使得EOF(ctrl-D)失效。必须输入退出命令。当设置shell变量时也同理操作。IGNOREEOF=10|
| keyword | -k | 将键盘参数至于环境中，并作为一个命令看待 |
| interactive-comments |  | 在交互状态下的shell中，以#开头的命令行是注解 |
| monitor | -m |  运行工作控制|
| noclobber | -C | 防止重定向时文件被覆盖 |
| noexec | -n | 读入但不执行命令。该选项用于检查脚本语法错误，但在交互环境下失效|
| noglob | -d |  使路径扩展式失效，也就是说，关闭了通配符|
| notify | -b | 后台工作完成后通知用户 |
| nounset | -u | 当扩展一个未设置的变量时，系统报错 |
| onecmd | -t | 读入并执行完一个命令后退出 |
| physical | -P | 如果已经设置， 当键入cd或pwd时就不跟随符号链接，而使用实际目录 |
| posix |  | 如果默认操作没有匹配posix标准，则shell行为会发生改变 |
|  privileged|-p  |  设置之后，shell就不读入.profile或ENV文件，而且shell函数也不从环境中继承了。对于setuid脚本而言，则自动设置|
|verbose  | -v | 打开verbose模式进行调试 |
| vi | | 使用vi内置编辑器来进行命令行编辑 |
| xtrace | -x |  打开”输出显示“模式以进行调试|
```shell
set -o option #Turns on the option
set +o option #Turns off the option
set -[a-zA-Z] #Abbreviation for an option; the minus turns it on
set +[a-zA-Z] #Abbreviation for an option; the plus turns it off

Note: 有些选项有缩写形式可以使用第二种方式进行设置，与第一种的结果一样，但是如果选项没有缩写形式，则无法使用第二种方式进行设置。

set -o 命令可以列出所有的选项已经选项的开关状态。
```
#### shopt内置命令是用于新版本的bash中的命令，其作用和set命令基本相同。shopt命令很多方面都是和set一样的，但是对shell的设置增加了很多扩充。
```shell
shopt -p #输出所有可用的选项设置命令
shopt -u option #表明选项没有被设置
shopt -s option #表明目前已经被设置的选项
shopt -o option #显示选项的状态, 但是只能针对set所设置的选项。


```
