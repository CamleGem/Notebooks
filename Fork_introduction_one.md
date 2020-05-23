# Fork Part 1
### 翻译自Perl.com(Fork Yeah!)##### [Perl.com](https://www.perl.com/article/fork-yeah-/)
最近在工作中，我不得不加快处理文件的Perl脚本。 Perl可以使用fork函数产生多个进程，但是除非正确管理子进程，否则事情可能会出错。我在脚本中添加了fork，并能够将脚本的吞吐率提高近10倍，但是我花了一些努力才能将其正确设置。在本文中，我将向您展示如何安全使用fork并避免一些常见错误。
N.B. Windows用户：由于fork系统调用在Windows上不可用，因此这些示例可能无法按所述方式运行，因为该行为是由Perl仿真的。
###### 一个简单的示例
```perl5
#!/usr/bin/perl
use strict;
use warnings;

my $pid = fork; # 产生两个进程，一个父进程，一个子进程
if ($pid == 0) {  # 这里运行子进程代码
    sleep 1;  
    exit;
}
waitpid $pid, 0; # 这里运行父进程代码，等待子进程返回退出。
```
该脚本使用fork创建一个子进程，该子进程将子进程的ID返回给父进程，将0返回给（新创建的）子进程。此时，两个进程正在执行剩余的代码，父代码和子代码。 if（$pid == 0）子句仅对子项为true，从而使其执行if块。 if块仅睡眠1秒钟，exit函数使子进程终止。同时，父进程跳过了if块，并调用waitpid，直到子进程退出后才返回。
N.B.我可以将睡眠调用替换为我希望子进程执行的任意处理，但是睡眠是很好的代表，因为它使分析程序更加容易。
这是一个简单的例子，它可能出什么问题？一方面，如果计算机没有足够的备用内存，则fork调用可能会失败。因此，我们需要检查这种情况：
```perl5

#!/usr/bin/perl
use strict;
use warnings;

my $pid = fork;
die "failed to fork:$!" unless defined $pid; # 检查fork是否成功

if ($pid == 0) {
    sleep 1;
    exit;    # 退出子进程，否则子进程会执行本该是父进程执行的内容
}
waitpid $pid, 0; # 父进程代码，等待子进程返回
```
我插入了一条条件die语句，如果fork失败，该语句将被抛出。但是这里有更深层次的问题吗？如果孩子没有睡一秒钟，而是调用了一个立即返回的函数，该怎么办？我们可能在父母与孩子之间进行竞赛-如果孩子在父母呼叫waitpid之前退出，那会发生什么？
认为操作系统可以将子进程的ID重复用于另一个程序并不是没有道理的，而我们的父进程会突然等待退出任意进程。根本不是我们想要的！
幸运的是，这不是风险：退出子进程时，不允许操作系统回收其资源，直到父进程对其调用wait(或waitpid)，从而夺回子进程。其次，waitpid仅适用于被调用的进程的子进程：如果我传递一个完全独立的进程的pid，waitpid会立即以-1返回。
## 多任务处理
就并发而言，这个简单的例子不是很好。它只生成一个子进程，并且我们无法在不重写代码的情况下使用其他进程进行扩展。这是我的新版本：
```perl5
#!/usr/bin/perl
use strict;
use warnings;

my $max_workers = shift || 1;
for(1..$max_workers) {
    my $pid = fork;
    die "failed to fork: $!" unless defined $pid;
    next if $pid;
    sleep 1;
    exit;
}
my $kid;
do {
    $kid = waitpid -1, 0;
} while ($kid > 0);
```
此脚本读取任务参数，或默认为1。然后，它fork $max_workers的子进程的数量。请注意，如果$pid导致父进程跳转到下一个循环迭代，则它会一遍又一遍地衍生另一个任务(子进程)，直到它退出循环。同时，子进程处理睡眠1秒，然后退出。
因此，当子进程处于睡眠状态时，父进程必须等待它们。不幸的是，现在我们有多个子进程$pid来监视，那么我应该通过哪个值来等待呢？幸运的是，waitpid有一个快捷方式，我可以传递-1作为进程ID，它会阻塞，直到任何子进程退出，返回退出子进程的pid。所以我把它包装进while循环，它将调用waitpid一遍又一遍，直到它返回-1或零，两者都表示没有更多的子进程可以收割。
此代码优于简单示例，因为它可以扩展到任意数量的子进程。但它包含（至少）两个问题。
想象一下，我们使用5个子进程运行此脚本，当计算机内存不足时，fork调用可能会失败。然后，父进程将调用die打印错误并退出，但这会使多个子进程仍在运行，并且没有父进程。这些进程成为孤儿进程，init 1进程会接管这些子进程，这将调用wait清理它们。
第二个问题与使用等待-1，0来捕获任何退出的子进程有关。想象一下，此脚本由包装程序运行，该程序捕获其输出并将其以数据流的形式传输到另一个进程。包装程序衍生子进程，该子程序将数据流的形式传输脚本的输出，然后在其自己的父进程中执行脚本，从而有效地将子进程注入脚本。这将导致我的脚本永久挂起，因为注入的子进程在脚本完成之前不会退出。
## 多任务版本二
```perl5

#!/usr/bin/perl
use strict;
use warnings;

$SIG{INT} = $SIG{TERM} = sub { exit };

my $max_workers = shift || 1;
my $parent_pid = "$$";

my @children;
for (1..$max_workers) {
  my $pid = fork;
  if (!defined $pid) {
    warn "failed to fork: $!";
    kill 'TERM', @children;
    exit;
  }
  elsif ($pid) {
    push @children, $pid;
    next;
  }
  sleep 1;
  exit;
}
wait_children();

sub wait_children {
  while (scalar @children) {
    my $pid = $children[0];
    my $kid = waitpid $pid, 0;
    warn "Reaped $pid ($kid)\n";
    shift @children;
  }
}

END {
  if ($parent_pid == $$) {     # 这里的代码在子进程退出时也会执行
    wait_children();
  }
}
```
这是多任务脚本的改进版本。我已经添加了INT和TERM的信号处理程序(按键盘上的Ctrl-C)，使Perl干净地退出。如果衍生失败，父进程将TERM发送到所有子进程，然后自行退出。我想，如果衍生发生故障，机器可能内存不足，OOM杀手不能远离，因此最好有序地关闭，而不是让进程不合时宜的结束。
子例程wait_children在父进程衍生的pids上执行waitpid阻塞调用。这样可以避免等待不是脚本创建的子进程的问题。请注意，在收割成功之前，它不会从@children中删除任何元素。这样可以避免脚本开始运行的地方产生错误，父进程衍生然后将子进程pid放入数组@children中，启动waitpid阻塞调用，然后接收INT/TERM信号，这将导致wait_children立即返回，然后在END块中再次调用，但其中一个pids现在将从@children中丢失，并成为僵尸进程。
当每个进程退出时，END块将触发。如果退出进程是父进程，它将再次调用wait_children以清理任何驻留子进程。在真实世界的脚本中，任务比睡眠更重要，这可能是添加子进程所需的任何额外清理的好地方;删除创建的任何临时文件。