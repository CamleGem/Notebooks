# Parallel::ForkManager
## NAME
Parallel::ForkManager - 一个简单的并行进程衍生(fork)管理器.
## VERSION
version 2.02
## SYNOPSIS
```perl5
use Parallel::ForkManager;

my $MAX_PROCESSES = 10; # 指定需要fork出的子进程数量

my $pm = Parallel::ForkManager->new($MAX_PROCESSES); #实例化一个

DATA_LOOP:
foreach my $data (@all_data) {
    my $pid = $pm->start and next DATA_LOOP;
    ... # 对$data做出一些处理
    $pm->finish;
}

```
## DESCRIPTION
该模块旨在用于可以并行执行的操作，在这些操作中应限制要衍生(fork)的进程数。典型的用途是用作一个下载器，它将检索下载成百上千的文件。
```perl5

use LWP::Simple;
use Parallel::ForkManager;

my @links=(
  ["http://www.foo.bar/rulez.data","rulez_data.txt"],
  ["http://new.host/more_data.doc","more_data.doc"],
);

...

# 并行下载最大的进程数
my $pm = Parallel::ForkManager->new(30);

LINKS:
foreach my $linkarray (@links) {
    $pm->start and next LINKS; # 进行fork，父进程返回子进程pid为真，因此跳过

    # 下面为子进程执行的操作，因为子进程返回pid为0，因此不会跳过循环。
    my ($link, $fn) = @$linkarray;
    warn "Cannot get $fn from $link"
        if getstore($link, $fn) != RC_OK;

    $pm->finish; # 退出子进程
}
$pm->wait_all_children; # 等待子进程退出
```
首先，你需要使用new构造函数实例化ForkManager。你必须指定要创建的最大进程数。如果指定0，则将不会衍生(fork)；这对于调试很有用。
接下来，使用$pm->start进行派生。$pm为子进程返回0，为父进程返回子进程pid(另请参见perlfunc中的fork())。注意: 如果派生失败，$pm->start将终止。
$pm->finish终止子进程（假设在start中完成了衍生）。
注意：如果您已经在子进程中，则不能使用$pm->start。如果要在子进程中管理另一组子进程，则必须实例化另一个Parallel::ForkManager对象！
## METHODS
注释字母指示该方法应在何处运行。 P代表父母，C代表孩子。
### new $processes
实例化一个新的Parallel::ForkManager对象。你必须指定要分叉的最大孩子数。如果指定0(零)，则不会衍生任何子代。这是出于调试目的。
可选的第二个参数$tempdir仅在你希望子代发回对某些数据的引用时使用（请参阅下面的“检索数据结构”）。如果未提供，则通过调用File::Temp::tempdir()进行设置。
如果临时目录不存在或不是目录，则新方法将失效。
从2.00版开始，构造函数也可以以典型的Moo/Moose方式调用。即
```perl5
my $fm = Parallel::ForkManager->new(
    max_procs => 4,
    tempdir => '...',
    child_role => 'Parallel::ForkManager::CustomChild',
);
```
### child_role
返回ForkManager对象在子进程中使用的角色的名称。默认为Parallel\:\:ForkManager\:\:Child，可以通过构造函数设置为其他值。
### start\[$process_identifier\]
此方法执行衍生。它为父进程返回子进程的pid，为子进程返回0。如果构造函数的$processes参数为0，则假定你在子进程中，则$pm->start仅返回0。
可以向此方法提供可选的$process_identifier，使用run_on_finish回调的时候,(请参阅CALLBACKS)使用它来标识完成的过程。
### start_child \[ $process_identifier, \] \&callback
类似于start方法，但将作为子代运行＆callback。如果回调函数返回任何内容，它将作为数据传递，并通过finish()返回给父进程。
### finish \[ $exit_code \[, $data_structure_reference\] \]
通过退出来关闭子进程，并接受可选的退出代码(默认退出代码为0),该退出代码可通过回调在父进程中检索。如果提供了第二个可选参数，则子进程将尝试将其内容发送回父进程。如果在调试模式下使用程序($processes == 0)，则此方法仅调用回调。
如果提供了$data_structure_reference，则将其序列化并传递给父进程。有关更多信息，请参见检索数据结构。
### set_max_procs $processes
允许你设置要维护的新的最大子进程数。
### wait_all_children
你可以调用此方法来等待所有已衍生的进程。这是一个阻塞等待。
### reap_finished_children
这是一个非阻塞调用，用于获得子代并执行独立于对start或wait_all_children的调用的回调。在不经常调用start但你希望快速执行回调的情况下使用此方法。
### is_parent
如果在子进程内，则返回false；如果在父进程内，则返回true。
### is_child
如果在子进程内，则返回true；如果在父进程内，则返回false。
### max_procs
返回对象将衍生的最大进程数。
### running_procs
返回当前被Parallel::ForkManager监控的衍生出来的进程的pids，请注意，在下一次调用start或wait_all_children之前，直到fork管理器收割子代，否则它们仍被报告为正在运行。
```perl5
my @pids = $pm->running_procs;
 my $nbr_children =- $pm->running_procs;
```
### wait_for_available_procs( $n )
等待直到$n个可用的进程数，如果$n没有提供，则默认为1。
### waitpid_blocking_sleep
返回伪阻塞调用的睡眠时间(以秒为单位)。睡眠时间可以是几分之一秒。
如果禁用，则返回0。
默认为1秒。
### set_waitpid_blocking_sleep $seconds
设置伪阻塞调用的睡眠时间(以秒为单位)设置为0表示禁用。
## CALLBACKS
你可以在代码中定义回调，这些回调在诸如启动进程或很快完成之类的事件上调用。在第一次调用start()之前声明它们。
### run_on_finish $code \[, $ pid\]
你可以定义一个子例程，该子例程在终止子进程时被调用。在父进程中调用它。
$code的参数如下：
```perl5
- pid of the process, which is terminated
- exit code of the program
- identification of the process (if provided in the "start" method)
- exit signal (0-127: signal name)
- core dump (1 if there was core dump at exit)
- datastructure reference or undef (see RETRIEVING DATASTRUCTURES)
```
### run_on_start $code
你可以定义一个子例程，该子例程在启动子进程时被调用。它在父进程中成功启动子进程后调用。
$code的参数如下：
```perl5
- pid of the process which has been started
- identification of the process (if provided in the "start" method)
```
### run_on_wait $code, \[$period\]
你可以定义一个子例程，该子例程在子进程需要等待启动时​​被调用。如果未定义$period，则每个孩子进行一次调用。如果定义了$period，则将定期调用$code，模块将在两次调用之间等待$period秒。注意，$period也可以是小数。不能保证确切的$period秒数，信号可以缩短，进程调度程序可以延长它`` 注意，$period也可以是小数。不能保证确切的$period秒数，信号可以缩短，进程调度程序可以延长它。
$code也在start和wait_all_children方法中调用。
没有参数传递给调用中的$code。
## 阻塞调用
当涉及到等待子进程终止时，Parallel::ForkManager介于衍生和硬处之间(如果你原谅可怕的双关语)。模块所依赖的基础Perl waitpid函数可以阻塞，直到一个特定进程或任何子进程终止，但不能阻塞给定组的进程部分。
这意味着，当模块等待其一个子进程终止时，它可以执行以下两项操作之一：
1. 只等待自己的子进程
这是通过使用waitpid非阻塞调用和睡眠语句通过循环完成的。代码执行以下一些工作：
```perl5
while(1) {
    if ( any of the P::FM child process terminated ) {
        return its pid
    }
 
    sleep $sleep_period
}
```
这是模块将使用的默认行为。这不是等待子进程的最有效方法，但它是确保Parallel::ForkManager不会干扰代码库的任何其他部分的最安全方法。
睡眠阶段通过方法set_waitpid_blocking_sleep设置。
2. 阻塞，直到所有子进程退出
Parallel::ForkManager能够调用waitpid，然后它会阻塞直到子进程全部退出。如果子进程并不是被监控的子进程中的某一个，那么等待会恢复。这更有效，但意味着P\:\:FM可以捕获(并丢弃)可能正在等待的代码的不同部分的终止通知。
如果这是不适用于代码库的竞态条件，则可以将waitpid_blocking_sleep周期设置为0，这将启用waitpid阻塞调用。
```perl5
my $pm = Parallel::ForkManager->new( 4 );
 
$pm->set_waitpid_blocking_sleep(0);  # true blocking calls enabled
 
for ( 1..100 ) {
    $pm->start and next;
 
    ...; # do work
 
    $pm->finish;
}
```
## 从子进程检索数据结构
从版本0.7.6开始，父进程检索数据结构的功能是新增的。
每个子进程可以选择将1个数据结构发送回父进程。所谓数据结构，是指对字符串，哈希或数组的引用。使用Storable模块的store()方法将数据结构的内容写到磁盘上的临时文件中。然后，从发送给run_on_finish回调的代码中检索引用。
数据结构可以是有意义的任何标量perl数据结构：字符串，数字值或对数组，哈希或对象的引用。
检索数据结构涉及两个步骤：
1）提供对孩子希望发送回父母的数据结构的引用，作为finish（）调用的第二个参数。由孩子决定是否将任何东西寄回给父母。
2）使用run_on_finish（）方法中提供的回调来检索数据结构引用。
请记住，数据结构检索与从方法调用返回数据结构不同。那不是实际发生的情况。给定子进程中引用的数据结构由Storable序列化并写到文件中。随后将文件读回内存，并创建一个属于父进程的新数据结构。请考虑它可能暗示的性能损失，因此请尽量减小返回的结构。
### 示例
```perl5
use Parallel::ForkManager;
use LWP::Simple;
 
my $pm = Parallel::ForkManager->new(10);
 
LINKS:
for my $link (@ARGV) {
  $pm->start and next LINKS;
  my ($fn) = $link =~ /^.*\/(.*?)$/;
  if (!$fn) {
    warn "Cannot determine filename from $fn\n";
  } else {
    $0 .= " " . $fn;
    print "Getting $fn from $link\n";
    my $rc = getstore($link, $fn);
    print "$link downloaded. response code: $rc\n";
  };
  $pm->finish;
};
```
### 回调
使用回调获取子进程退出代码的程序示例：
```perl5
use strict;
use Parallel::ForkManager;
 
my $max_procs = 5;
my @names = qw( Fred Jim Lily Steve Jessica Bob Dave Christine Rico Sara );
# hash to resolve PID's back to child specific information
 
my $pm = Parallel::ForkManager->new($max_procs);
 
# Setup a callback for when a child finishes up so we can
# get it's exit code
$pm->run_on_finish( sub {
    my ($pid, $exit_code, $ident) = @_;
    print "** $ident just got out of the pool ".
      "with PID $pid and exit code: $exit_code\n";
});
 
$pm->run_on_start( sub {
    my ($pid, $ident)=@_;
    print "** $ident started, pid: $pid\n";
});
 
$pm->run_on_wait( sub {
    print "** Have to wait for one children ...\n"
  },
  0.5
);
 
NAMES:
foreach my $child ( 0 .. $#names ) {
  my $pid = $pm->start($names[$child]) and next NAMES;
 
  # This code is the child process
  print "This is $names[$child], Child number $child\n";
  sleep ( 2 * $child );
  print "$names[$child], Child $child is about to get out...\n";
  sleep 1;
  $pm->finish($child); # pass an exit code to finish
}
 
print "Waiting for Children...\n";
$pm->wait_all_children;
print "Everybody is out of the pool!\n";
```
### 数据结构检索
在这个简单的示例中，每个子进程都发回一个字符串引用。
```perl5
use Parallel::ForkManager 0.7.6;
use strict;
 
my $pm = Parallel::ForkManager->new(2, '/server/path/to/temp/dir/');
 
# data structure retrieval and handling
$pm -> run_on_finish ( # called BEFORE the first call to start()
  sub {
    my ($pid, $exit_code, $ident, $exit_signal, $core_dump, $data_structure_reference) = @_;
 
    # retrieve data structure from child
    if (defined($data_structure_reference)) {  # children are not forced to send anything
      my $string = ${$data_structure_reference};  # child passed a string reference
      print "$string\n";
    }
    else {  # problems occurring during storage or retrieval will throw a warning
      print qq|No message received from child process $pid!\n|;
    }
  }
);
 
# prep random statement components
my @foods = ('chocolate', 'ice cream', 'peanut butter', 'pickles', 'pizza', 'bacon', 'pancakes', 'spaghetti', 'cookies');
my @preferences = ('loves', q|can't stand|, 'always wants more', 'will walk 100 miles for', 'only eats', 'would starve rather than eat');
 
# run the parallel processes
PERSONS:
foreach my $person (qw(Fred Wilma Ernie Bert Lucy Ethel Curly Moe Larry)) {
  $pm->start() and next PERSONS;
 
  # generate a random statement about food preferences
  my $statement = $person . ' ' . $preferences[int(rand @preferences)] . ' ' . $foods[int(rand @foods)];
 
  # send it back to the parent process
  $pm->finish(0, \$statement);  # note that it's a scalar REFERENCE, not the scalar itself
}
$pm->wait_all_children;
第二个数据结构检索示例演示了孩子如何决定是否发回任何东西，发送什么以及父母应如何处理检索到的任何东西。

use Parallel::ForkManager 0.7.6;
use Data::Dumper;  # to display the data structures retrieved.
use strict;
 
my $pm = Parallel::ForkManager->new(20);  # using the system temp dir $L<File::Temp::tempdir()
 
# data structure retrieval and handling
my %retrieved_responses = ();  # for collecting responses
$pm -> run_on_finish (
  sub {
    my ($pid, $exit_code, $ident, $exit_signal, $core_dump, $data_structure_reference) = @_;
 
    # see what the child sent us, if anything
    if (defined($data_structure_reference)) {  # test rather than assume child sent anything
      my $reftype = ref($data_structure_reference);
      print qq|ident "$ident" returned a "$reftype" reference.\n\n|;
      if (1) {  # simple on/off switch to display the contents
        print &Dumper($data_structure_reference) . qq|end of "$ident" sent structure\n\n|;
      }
 
      # we can also collect retrieved data structures for processing after all children have exited
      $retrieved_responses{$ident} = $data_structure_reference;
    } else {
      print qq|ident "$ident" did not send anything.\n\n|;
    }
  }
);
 
# generate a list of instructions
my @instructions = (  # a unique identifier and what the child process should send
  {'name' => '%ENV keys as a string', 'send' => 'keys'},
  {'name' => 'Send Nothing'},  # not instructing the child to send anything back to the parent
  {'name' => 'Childs %ENV', 'send' => 'all'},
  {'name' => 'Child chooses randomly', 'send' => 'random'},
  {'name' => 'Invalid send instructions', 'send' => 'Na Na Nana Na'},
  {'name' => 'ENV values in an array', 'send' => 'values'},
);
 
INSTRUCTS:
foreach my $instruction (@instructions) {
  $pm->start($instruction->{'name'}) and next INSTRUCTS;  # this time we are using an explicit, unique child process identifier
 
  # last step in child processing
  $pm->finish(0) unless $instruction->{'send'};  # no data structure is sent unless this child is told what to send.
 
  if ($instruction->{'send'} eq 'keys') {
    $pm->finish(0, \join(', ', keys %ENV));
 
  } elsif ($instruction->{'send'} eq 'values') {
    $pm->finish(0, [values %ENV]);  # kinda useless without knowing which keys they belong to...
 
  } elsif ($instruction->{'send'} eq 'all') {
    $pm->finish(0, \%ENV);  # remember, we are not "returning" anything, just copying the hash to disc
 
  # demonstrate clearly that the child determines what type of reference to send
  } elsif ($instruction->{'send'} eq 'random') {
    my $string = q|I'm just a string.|;
    my @array = qw(I am an array);
    my %hash = (type => 'associative array', synonym => 'hash', cool => 'very :)');
    my $return_choice = ('string', 'array', 'hash')[int(rand 3)];  # randomly choose return data type
    $pm->finish(0, \$string) if ($return_choice eq 'string');
    $pm->finish(0, \@array) if ($return_choice eq 'array');
    $pm->finish(0, \%hash) if ($return_choice eq 'hash');
 
  # as a responsible child, inform parent that their instruction was invalid
  } else {
    $pm->finish(0, \qq|Invalid instructions: "$instruction->{'send'}".|);  # ordinarily I wouldn't include invalid input in a response...
  }
}
$pm->wait_all_children;  # blocks until all forked processes have exited
 
# post fork processing of returned data structures
for (sort keys %retrieved_responses) {
  print qq|Post processing "$_"...\n|;
}
```
## 在衍生过程中使用RAND()
需要注意的是，所有衍生的进程都将使用相同的随机种子，因此有可能提供相同的结果（请参阅http://blogs.perl.org/users/brian_phillips/2010/06/when-rand-isnt-random.html）。如果你正在使用rand()并且希望每个衍生的子进程使用不同的种子，则可以在程序中添加以下内容：
$pm->run_on_start(sub { srand });
## 拓展
从2.0.0版开始，Parallel::ForkManager在后台使用Moo。从父对象派生一个进程时，该对象的派生实例将被修改以消耗Parallel\:\:ForkManager\:\:Child角色。所有这些使扩展Parallel\:\:ForkManager来实现任何存储/检索机制或任何其他行为都相当容易。
示例：通过Web服务存储和检索数据
```perl5
{
    package Parallel::ForkManager::Web;
 
    use HTTP::Tiny;
 
    use Moo;
    extends 'Parallel::ForkManager';
 
    has ua => (
        is => 'ro',
        lazy => 1,
        default => sub {
            HTTP::Tiny->new;
        }
    );
 
    sub store {
        my( $self, $data ) = @_;
 
        $self->ua->post( "http://.../store/$$", { body => $data } );
    }
 
    sub retrieve {
        my( $self, $kid_id ) = @_;
 
        $self->ua->get( "http://.../store/$kid_id" )->{content};
    }
 
}
 
my $fm = Parallel::ForkManager::Web->new(2);
 
$fm->run_on_finish(sub{
    my $retrieved = $_[5];
 
    print "got ", $retrieved, "\n";
});
 
$fm->start_child(sub {
    return $_**2;
}) for 1..3;
 
$fm->wait_all_children;
```
示例：让子进程以不同的方式退出
```perl5
use Parallel::ForkManager;
 
package Parallel::ForkManager::Child::PosixExit {
    use Moo::Role;
    with 'Parallel::ForkManager::Child';
 
    sub finish  { POSIX::_exit() };
}
 
my $fm = Parallel::ForkManager->new(
    max_proc   => 1,
    child_role => 'Parallel::ForkManager::Child::PosixExit'
);
```