# Fork Part 2
### 翻译自Perl.com(Fork Yeah!)##### [Perl.com](https://www.perl.com/article/fork-yeah-part-2/)
在本文的第一部分中，我描述了如何使用Perl的fork函数编写并发程序。下面是其他几种方法。
#### WNOHANG
通常，waitpid是阻塞调用，当子进程退出时返回：

```perl5
#!/usr/bin/perl

my $pid = fork;

if ($pid == 0) {
  sleep 1;
  exit;
}

waitpid $pid, 0;  # 在子进程退出前，父进程会一直等待
```

在此示例中，waitpid的第二个参数是0，这是flag参数。但是，如果我们想要在父进程中执行额外的处理，同时偶尔检查收获的子进程，该怎么办？
POSIX模块包括WNOHANG常量，使waipid以非阻塞形式调用。与阻塞调用相反，它会立即以一个整数返回：
* -1表示对于该进程ID不存在退出的子进程，或者如果不再有子进程退出，则返回-1。
* 0表示存在子进程，但尚未更改状态, 也就是说子进程尚未退出。
* 2-32768是退出的子进程(永远不会是1, 因为1的pid是分配给init进程的)。
```perl5

#!/usr/bin/perl
use POSIX 'WNOHANG';

my $pid = fork;

if ($pid == 0) {
  sleep 1;
  exit;
}

my $kid;
do {
  # 如果是非阻塞模式，当运行程序时会被立即输出，但是如果是阻塞模式，则必须等待子进程退出，才会打印。
  print "I am father\n";
  $kid = waitpid -1, WNOHANG; #标志位设置为1，非阻塞模式
} while ($kid == 0);

```
我更改了代码以在循环中等待子进程退出，waipid以WNOHAND标志位进行非阻塞调用，以允许我在do块中执行任何额外的处理。没有WNOHANG，这将在子进程被收割以后才会循环一次，有了它，我仍然可以收割退出的子进程，但循环可能会循环数千次在此期间。
#### WUNTRACED
POSIX模块提供waitpid常量和宏。另一个常量是WUNTRACED，如果子进程停止(但未exit)，则会导致waitpid返回。
然后，父进程可以采取适当的操作：它可能记录某处停止的进程，或选择通过向子进程发送CONT信号(SIGCONT)来恢复停止的子进程：
```perl5
#!/usr/bin/perl
use POSIX ':sys_wait_h';
$SIG{INT} = sub { exit };

my $pid = fork;

if ($pid == 0) {
  while (1) {
    print "child going to sleep\n";
    sleep 10;
  }
}

print "child PID: $pid\n";
while (1) {
  my $kid = waitpid $pid, WUNTRACED;

  if (WIFSTOPPED(${^CHILD_ERROR_NATIVE})) {
    print "sending SIGCONT to child\n";
    kill 'CONT', $kid;
  }
  else {
    exit;
  }
}
````
我使用导出组sys_wait_h从POSIX模块导入多个符号。这一次，子进程和父进程都在无限循环中。如果我通过发送SIGSTOP信号来暂停子进程，waitpid将返回。父进程将通过WIFSTOPPED宏来测试子进程是否停止，如果是，则通过kill将SIGCONT发送到子进程，恢复子进程的运行。
运行脚本wuntraced.pl：
```bash
$ ./wuntraced.pl
child PID: 15013
child going to sleep
```
在另一个终端，我发送SIGSTOP信号给子进程：
```bash
$ kill -s STOP 15013
```
然后通过父进程恢复子进程：通过发送CONT信号，来让子进程继续运行
两个进程都一直运行，直到我向子级发送 SIGINT：
```bash
$ kill -s INT 15013
```
#### 组合常量
WNOHANG和WUNTRACED并非互斥：我可以通过将两个常量通过|符号来合并为的单一的flag值，来更改waitpid的行为。
```perl5
#!/usr/bin/perl
use Data::Dumper 'Dumper';
use POSIX ':sys_wait_h';

$SIG{INT} = sub { exit };
my %pids;

for (1..3) {
  my $pid = fork;
  if ($pid == 0) {
    sleep 1 while 1;
  }
  else {
    $pids{$pid} = {
      duration => undef,
      started  => time,
      stops    => 0,
    };
  }
}

while (1) {
  my $kid = waitpid -1, WNOHANG | WUNTRACED;

  # do additional processing
  print Dumper(\%pids);
  sleep 3;

  if (WIFSTOPPED(${^CHILD_ERROR_NATIVE})) {
    $pids{$kid}->{stops}++;
    kill 'CONT', $kid;
  }
  elsif ($kid > 0) {
    my $exit_time = time;
    $pids{$kid}->{duration} = $exit_time - $pids{$kid}->{started};
  }
  elsif ($kid == -1) {
    exit;
  }
}

```
此代码衍生3个子进程，这些子进程可永久运行，父代码跟踪每个子进程的统计信息：启动时间、持续时间和接收SIGSTOP的次数。父进程将通过SIGCONT恢复任何已停止的子进程。父进程每3秒打印一次统计信息，并在所有子进程退出时退出。
运行此代码，我可以通过发送SIGSTOP和SIGINT到不同的子进程并观看统计信息更新。尽管这是一个简单的示例，但通过使用WNOHANG和WUNTRACED，你可以看到他们如何将父进程的角色从被动观察者更改为能够主动管理其子进程的管理者。
