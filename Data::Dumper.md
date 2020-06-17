# Data\:\:Dumper的用法

```perl5
use Data::Dumper;

my ($foo, $bar) = ([1, 2, 3], [3, 4, 5]);
print Dumper($foo, $bar); # 正常输出引用数据结构

print Data::Dumper->Dump([ $foo, $bar ], [ qw(foo *ary) ]);

# configuration variables
{
    local $Data::Dumper::Purity = 1;
    eval Data::Dumper->Dump([ $foo, $bar ], [ qw(foo *ary) ]);
}

# 面向对象语法
my $d = Data::Dumper->new([ $foo, $bar ], [ qw(foo *ary) ]);

print $d->Dump;

$d->Purity(1)->Terse(1)->Deepcopy(1);
eval $d->Dump;
```
## 方法
* PACKAGE->new(ARRAYREF \[, ARRAYREF\])
返回一个新创建的Data\:\:Dumper对象。第一个参数是要转储的值的匿名数组。可选的第二个参数是值的匿名名称数组。名称不必以$开头，并且必须由字母数字字符组成。您可以使用*开头的名称来指定对于ARRAY和HASH引用，必须转储取消引用的类型而不是引用本身。
```perl5
{
    local $Data::Dumper::Varname = "foo"; #给转储的值指定一个新的变量名
    my $ref = [1, 2, 3];
    say Dumper $ref;
}

__END__
$foo1 = [
          1,
          2,
          3
        ];

```
* $OBJ->Dump or PACKAGE->Dump(ARRAYREF \[, ARRAYREF\])
返回存储在对象中的值的字符串形式（保留它们提供给new的顺序），但要遵循以下配置选项。在列表上下文中，它返回与提供的值相对应的字符串列表。
为了方便起见，对于第二种形式，在立即转储对象之前对其参数调用new方法。
```perl5
my $ref1 = [1, 2, 3];
my $ref2 = [4, 5, 6];
my $d = Data::Dumper->new([$ref1, $ref2], ["foo1", "foo2"]);
my @strings = $d->Dump; # 数组上下文返回两个字符串值

say scalar @strings; # 2
```
```perl5
my $ref1 = [1, 2, 3];
my $ref2 = [4, 5, 6];

my @d = Data::Dumper->Dump([$ref1, $ref2], ["foo1", "foo2"]);
say scalar @d; # 2, 数组上下文返回两个值字符串

my $d = Data::Dumper->Dump([$ref1, $ref2], ["foo1", "foo2"]);
say $d; # 返回一整个字符串

__END__
$foo1 = [
          1,
          2,
          3
        ];
$foo2 = [
          4,
          5,
          6
        ];

```
* $OBJ->Seen(\[HASHREF\])
查询或添加到已经遇到的引用的内部表中。如果需要，必须使用Reset显式清除表。此类引用不会被转储；而是将它们的名称插入到随后遇到的任何位置。这对于正确转储子例程引用特别有用。
需要名称=>值对的匿名哈希。名称与new中的规则相同。如果未提供任何参数，则将在列表上下文中返回名称=>值对的“可见”列表。否则，返回对象本身。

* $OBJ->Values(\[ARRAYREF\])
查询或替换将要转储的内部值数组。在不带参数的情况下调用时，将值作为列表返回。当引用替换值数组进行调用时，返回对象本身。与任何其他类型的参数一起调用时，将死亡。

* $OBJ->Names(\[ARRAYREF\])
查询或替换用户提供的名称的内部数组，作为要转储的值。不带参数调用时，返回名称。当使用替换名称数组调用时，返回对象本身。如果替换名称的数量超过要命名的值的数量，则不会使用多余的名称。如果替换名称的数量少于要命名的值的数量，则替换名称列表将用尽，其余值将不会重命名。与任何其他类型的参数一起调用时，将死亡。

* $OBJ->Reset
清除内部表中的“可见”引用，并返回对象本身。

## 函数
## Dumper(LIST)
根据下面的配置选项，返回列表中值的字符串化形式。这些值将在输出中命名为$VARn，其中n是数字后缀。将在列表上下文中返回字符串列表。

## 配置变量和方法
使用过程接口时，可以使用几个配置变量来控制生成的输出的种类。这些变量通常位于一个块中，以使代码的其他部分不受更改的影响。
这些变量确定通过调用new方法创建的对象的默认状态，但此后不能用于更改对象的状态。应该使用等效的方法名称来查询或设置对象的内部状态。
当使用参数调用时，方法窗体将返回对象本身，以便可以将它们很好地链接在一起。
* $Data\:\:Dumper\:\:Indent or $OBJ->Indent(\[NEWVAL\])
控制缩进样式。可以将其设置为0、1、2或3。样式0会输出输出，列表项之间没有任何换行符，缩进或空格。这是最紧凑的格式，仍然可以称为有效perl。样式1输出带有换行符的可读形式，但没有花哨的缩进（结构中的每个级别仅由固定量的空格缩进）。样式2（默认值）输出一种非常易读的格式，其中考虑了哈希键的长度（因此哈希值排成一行）。样式3类似于样式2，但还使用其索引对数组的元素进行注释（但注释位于其自己的行上，因此数组输出消耗的行数是行数的两倍）。样式2是默认样式。
```perl5
my $ref1 = {
    one => [ 1, 2, 3 ],
    two => {first => 1, second => 2},
};

{
    local $Data::Dumper::Indent = 1;
    say Dumper $ref1;
}

__END__
$VAR1 = {
  'one' => [
    1,
    2,
    3
  ],
  'two' => {
    'second' => 2,
    'first' => 1
  }
};

```
```perl5
my $ref1 = {
    one => [ 1, 2, 3 ],
    two => {first => 1, second => 2},
};

{
    local $Data::Dumper::Indent = 2;
    say Dumper $ref1;
}
__END__
$VAR1 = {
          'two' => {
                     'second' => 2,
                     'first' => 1
                   },
          'one' => [
                     1,
                     2,
                     3
                   ]
        };

```
```perl5
my $ref1 = {
    one => [ 1, 2, 3 ],
    two => {first => 1, second => 2},
};

{
    local $Data::Dumper::Indent = 2;
    say Dumper $ref1;
}

__END__
$VAR1 = {
          'two' => {
                     'first' => 1,
                     'second' => 2
                   },
          'one' => [
                     #0
                     1,
                     #1
                     2,
                     #2
                     3
                   ]
        };

```
* $Data\:\:Dumper\:\:Purity or $OBJ->Purity(\[NEWVAL\])
控制可以被计算的输出以重新创建提供的引用结构的程度。将其设置为1将输出其他perl语句，这些语句将正确地重新创建嵌套引用。默认值为0。

* $Data\:\:Dumper\:\:Pad or $OBJ->Pad(\[NEWVAL\])
指定将在输出的每一行前加上前缀的字符串。默认为空字符串。
```perl5
my $ref1 = {
    one => [ 1, 2, 3 ],
    two => {first => 1, second => 2},
};

{
    local $Data::Dumper::Pad = "ok";
    say Dumper $ref1;
}
__END__
ok$VAR1 = {
ok          'two' => {
ok                     'first' => 1,
ok                     'second' => 2
ok                   },
ok          'one' => [
ok                     1,
ok                     2,
ok                     3
ok                   ]
ok        };

```
* $Data\:\:Dumper\:\:Varname or $OBJ->Varname(\[NEWVAL\])
包含用于在输出中标记变量名称的前缀。默认值为“VAR”。

* $Data\:\:Dumper\:\:Useqq or $OBJ->Useqq(\[NEWVAL\])
设置后，启用双引号表示字符串值。除空格以外的空白将表示为\[\ n \ t \ r\]，“ unsafe”字符将被反斜杠，并且不可打印字符将被输出为带引号的八进制整数。默认值为0。

* $Data\:\:Dumper\:\:Terse or $OBJ->Terse(\[NEWVAL\])
设置后，Data\:\:Dumper将发出单个非自引用值作为原子/项，而不是语句。这意味着将在可能的情况下避免使用$VARn名称，但是建议不要通过eval始终解析此类输出。
```perl5
use feature qw(say);

my $ref1 = {
    one => [ 1, 2, 3 ],
    two => {first => 1, second => 2},
};

{
    local $Data::Dumper::Terse = 1;
    say Dumper $ref1;
}
__END__
{
  'two' => {
             'first' => 1,
             'second' => 2
           },
  'one' => [
             1,
             2,
             3
           ]
}

```
* $Data\:\:Dumper\:\:Freezer or $OBJ->Freezer(\[NEWVAL\])
可以设置为方法名称，也可以设置为空字符串以禁用该功能。 Data\:\:Dumper将在尝试将其字符串化之前通过对象调用该方法。此方法可以更改对象的内容（例如，如果它包含从C分配的数据），甚至可以在另一个包中拒绝它。客户端负责确保可以通过该对象调用指定的方法，并且在调用该方法之后，该对象最终仅包含perl数据类型。默认为空字符串。
如果对象不支持指定的方法（使用UNIVERSAL\:\:can()确定），则将跳过该调用。如果该方法失败，将生成警告。

* $Data\:\:Dumper\:\:Toaster or $OBJ->Toaster(\[NEWVAL\])
可以设置为方法名称，也可以设置为空字符串以禁用该功能。 Data\:\:Dumper将使用bless(DATA，CLASS)->METHOD()语法对要转储的任何对象发出方法调用。请注意，这意味着指定的方法将必须对对象执行所需的任何修改（例如在对象中创建新状态，和/或在不同的包中重新赋予它状态），然后将其返回。客户端负责确保可以通过对象调用该方法，并返回有效的对象。默认为空字符串。

* $Data\:\:Dumper\:\:Deepcopy or $OBJ->Deepcopy(\[NEWVAL\])
可以设置为布尔值以启用结构的深层副本。然后，仅在绝对必要时（即打破参考周期）才进行交叉引用。默认值为0。

* $Data\:\:Dumper\:\:Quotekeys or $OBJ->Quotekeys(\[NEWVAL\])
可以设置为布尔值以控制是否对哈希键加引号。定义的false值将避免引用看起来像一个简单的字符串的哈希键。默认值为1，它将始终将哈希键括在引号中。

* $Data\:\:Dumper\:\:Bless or $OBJ->Bless(\[NEWVAL\])
可以设置为字符串，该字符串指定用于创建对象的bless内置运算符的替代形式。具有指定名称的函数应该存在，并且应接受与内置函数相同的参数。默认是bless。

* $Data\:\:Dumper\:\:Pair or $OBJ->Pair(\[NEWVAL\])
可以设置为字符串，该字符串指定哈希键和值之间的分隔符。要将嵌套的哈希，数组和标量值转储到JavaScript，请使用：$Data\:\:Dumper\:\:Pair ='：'; 。留给JavaScript练习以保佑读者。存在具有指定名称的函数，并且该函数接受与内置函数相同的参数。
默认值为：=>。

* $Data\:\:Dumper\:\:Maxdepth or $OBJ->Maxdepth(\[NEWVAL\])
可以设置为一个正整数，该整数指定我们不冒险进入结构的深度。设置Data\:\:Dumper\:\:Purity时不起作用(当我们经常不想看到足够多的内容时，在调试器中很有用)。默认值为0，表示没有最大深度。

* $Data\:\:Dumper\:\:Maxrecurse or $OBJ->Maxrecurse(\[NEWVAL\])
可以设置为正整数，该整数指定深度，递归到结构中将超出该深度将引发异常。这旨在作为一种安全措施，防止在转储过深的结构时perl耗尽堆栈空间。可以设置为0以消除限制。默认值为1000。

* $Data\:\:Dumper\:\:Useperl or $OBJ->Useperl(\[NEWVAL\])
可以设置为一个布尔值，该值控制是否使用Data\:\:Dumper的纯Perl实现。 Data :: Dumper模块是双重实现，几乎所有功能都用纯Perl和XS（'C'）编写。由于XS版本要快得多，因此将尽可能使用它。此选项使您可以覆盖默认行为，通常仅出于测试目的。默认值为0，这意味着将尽可能使用XS实现。

* $Data\:\:Dumper\:\:Sortkeys or $OBJ->Sortkeys(\[NEWVAL\])
可以设置为布尔值以控制是否按排序顺序转储哈希键。值为true会导致按照Perl的默认排序顺序转储所有哈希的键。也可以设置为子例程引用，该引用将为每个转储的哈希值调用。在这种情况下，Data\:\:Dumper将为每个哈希调用一次子例程，并将其引用传递给哈希。子例程的目的是按应该转储的顺序返回对将要转储的键的数组的引用。使用此功能，你可以控制按键的顺序以及实际使用的按键。换句话说，此子例程充当筛选器，你可以通过该筛选器排除某些键不被转储。默认值为0，这意味着哈希键不会排序。

* $Data\:\:Dumper\:\:Deparse or $OBJ->Deparse(\[NEWVAL\])
可以设置为布尔值，以控制是否将代码引用转换为perl源代码。如果设置为真值，则B\:\:Deparse将用于获取代码引用的源。使用此选项将强制使用转储程序的Perl实现，因为快速的XSUB实现不支持它。
警告：仅当你知道B::Deparse将正确重构您的代码引用时，才使用此选项。

* $Data\:\:Dumper\:\:Sparseseen or $OBJ->Sparseseen(\[NEWVAL\])
默认情况下，Data\:\:Dumper会建立序列化过程中遇到的标量的“可见”散列。这非常昂贵。这种可见的哈希对于支持甚至只是检测循环引用都是必需的。它通过Seen()调用显示给用户，以进行写入和读取。
如果您作为用户不需要显式访问“看到的”哈希，则可以设置Sparseseen选项以允许Data\:\:Dumper避开为已知的标量不包含以下内容的标量构建“看到的”哈希一个参考。如果使用XS实现，则可以大大加快序列化速度。
注意：如果启用Sparseseen，那么一定不能依赖所看到的哈希的内容，因为它的内容将是实现细节！

## 示例
```perl5
use Data::Dumper;
package Foo;
sub new {bless { 'a' => 1, 'b' => sub {return "foo"} }, $_[0]};
package Fuz;                       # a weird REF-REF-SCALAR object
sub new {bless \($_ = \'fu\'z'), $_[0]};
package main;
$foo = Foo->new;
$fuz = Fuz->new;
$boo = [ 1, [], "abcd", \*foo,
    { 1 => 'a', 023 => 'b', 0x45 => 'c' },
    \\"p\q\'r", $foo, $fuz ];
########
# simple usage
########
$bar = eval(Dumper($boo));
print($@) if $@;
print Dumper($boo), Dumper($bar); # pretty print (no array indices)
=head1
# 输出结果
$VAR1 = [
          1,
          [],
          'abcd',
          \*::foo,
          {
            '19' => 'b',
            '1' => 'a',
            '69' => 'c'
          },
          \\'pq\'r',
          bless( {
                   'state' => 'awake'
                 }, 'Foo' ),
          bless( do{\(my $o = \'fu\'z')}, 'Fuz' )
        ];

$VAR1 = [
          1,
          [],
          'abcd',
          \*::foo,
          {
            '19' => 'b',
            '1' => 'a',
            '69' => 'c'
          },
          \\'pq\'r',
          bless( {
                   'a' => 1,
                   'b' => sub { "DUMMY" }
                 }, 'Foo' ),
          bless( do{\(my $o = \'fu\'z')}, 'Fuz' )
        ];        
=cut

$Data::Dumper::Terse = 1;         # don't output names where feasible
$Data::Dumper::Indent = 0;        # turn off all pretty print
print Dumper($boo), "\n";

=head1
[1,[],'abcd',\*::foo,{'19' => 'b','69' => 'c','1' => 'a'},\\'pq\'r',bless( {'state' => 'awake'}, 'Foo' ),bless( do{\(my $o = \'fu\'z')}, 'Fuz' )]

=cut

$Data::Dumper::Indent = 1; # mild pretty print
print Dumper($boo);

=head1
[
  1,
  [],
  'abcd',
  \*::foo,
  {
    '19' => 'b',
    '69' => 'c',
    '1' => 'a'
  },
  \\'pq\'r',
  bless( {
    'state' => 'awake'
  }, 'Foo' ),
  bless( do{\(my $o = \'fu\'z')}, 'Fuz' )
]

=cut

$Data::Dumper::Indent = 3; # pretty print with array indices
print Dumper($boo);
=head1
[
  #0
  1,
  #1
  [],
  #2
  'abcd',
  #3
  \*::foo,
  #4
  {
    '19' => 'b',
    '69' => 'c',
    '1' => 'a'
  },
  #5
  \\'pq\'r',
  #6
  bless( {
           'state' => 'awake'
         }, 'Foo' ),
  #7
  bless( do{\(my $o = \'fu\'z')}, 'Fuz' )
]



=cut

$Data::Dumper::Useqq = 1; # print strings in double quotes
print Dumper($boo);

=head1
[
  #0
  1,
  #1
  [],
  #2
  "abcd",
  #3
  \*::foo,
  #4
  {
    "19" => "b",
    "69" => "c",
    "1" => "a"
  },
  #5
  \\"pq'r",
  #6
  bless( {
           "state" => "awake"
         }, 'Foo' ),
  #7
  bless( do{\(my $o = \"fu'z")}, 'Fuz' )
]



=cut

$Data::Dumper::Pair = " : "; # specify hash key/value separator
print Dumper($boo);
=head1
[
  #0
  1,
  #1
  [],
  #2
  "abcd",
  #3
  \*::foo,
  #4
  {
    "19" : "b",
    "69" : "c",
    "1" : "a"
  },
  #5
  \\"pq'r",
  #6
  bless( {
           "state" : "awake"
         }, 'Foo' ),
  #7
  bless( do{\(my $o = \"fu'z")}, 'Fuz' )
]



=cut

########
# recursive structures
########
@c = ('c');
$c = \@c;
$b = {};
$a = [ 1, $b, $c ];
$b->{a} = $a;
$b->{b} = $a->[1];
$b->{c} = $a->[2];
print Data::Dumper->Dump([ $a, $b, $c ], [ qw(a b c) ]);

=head1
[
  #0
  1,
  #1
  {
    "b" : $a->[1],
    "c" : [
             #0
             "c"
           ],
    "a" : $a
  },
  #2
  $a->[1]{"c"}
]
$a->[1]
$a->[1]{"c"}



=cut

$Data::Dumper::Purity = 1;                          # fill in the holes for eval
print Data::Dumper->Dump([ $a, $b ], [ qw(*a b) ]); # print as @a

=head1

@a = (
       1,
       {
         'b' => {},
         'c' => [
                  'c'
                ],
         'a' => []
       },
       []
     );
$a[1]{'b'} = $a[1];
$a[1]{'a'} = \@a;
$a[2] = $a[1]{'c'};
$b = $a[1];

=cut

print Data::Dumper->Dump([ $b, $a ], [ qw(*b a) ]); # print as %b

=head1

%b = (
       'c' => [
                'c'
              ],
       'a' => [
                1,
                {},
                []
              ],
       'b' => {}
     );
$b{'a'}[1] = \%b;
$b{'a'}[2] = $b{'c'};
$b{'b'} = \%b;
$a = $b{'a'};



=cut

$Data::Dumper::Deepcopy = 1;                        # avoid cross-refs
print Data::Dumper->Dump([ $b, $a ], [ qw(*b a) ]);

=head1
%b = (
       'a' => [
                1,
                {},
                [
                  'c'
                ]
              ],
       'c' => [
                'c'
              ],
       'b' => {}
     );
$b{'a'}[1] = \%b;
$b{'b'} = \%b;
$a = [
       1,
       {
         'a' => [],
         'c' => [
                  'c'
                ],
         'b' => {}
       },
       [
         'c'
       ]
     ];
$a->[1]{'a'} = $a;
$a->[1]{'b'} = $a->[1];


=cut

$Data::Dumper::Purity = 0; # avoid cross-refs
print Data::Dumper->Dump([ $b, $a ], [ qw(*b a) ]);

=head1

%b = (
       'c' => [
                'c'
              ],
       'a' => [
                1,
                \%b,
                [
                  'c'
                ]
              ],
       'b' => \%b
     );
$a = [
       1,
       {
         'c' => [
                  'c'
                ],
         'a' => $a,
         'b' => $a->[1]
       },
       [
         'c'
       ]
     ];


=cut

########
# deep structures
########
$a = "pearl";
$b = [ $a ];
$c = { 'b' => $b };
$d = [ $c ];
$e = { 'd' => $d };
$f = { 'e' => $e };
print Data::Dumper->Dump([ $f ], [ qw(f) ]);

=head1

$f = {
       'e' => {
                'd' => [
                         {
                           'b' => [
                                    'pearl'
                                  ]
                         }
                       ]
              }
     };


=cut

$Data::Dumper::Maxdepth = 3; # no deeper than 3 refs down
print Data::Dumper->Dump([ $f ], [ qw(f) ]);

=head1

$f = {
       'e' => {
                'd' => [
                         'HASH(0x7fb51e824bf8)'
                       ]
              }
     };


=cut

########
# object-oriented usage
########
$d = Data::Dumper->new([ $a, $b ], [ qw(a b) ]);
$d->Seen({ '*c' => $c }); # stash a ref without printing it
$d->Indent(3);
print $d->Dump;

=head1
$a = 'pearl';
$b = [
       #0
       'pearl'
     ];



=cut

$d->Reset->Purity(0); # empty the seen cache
print join "----\n", $d->Dump;

=head1

$a = 'pearl';
----
$b = [
       #0
       'pearl'
     ];


=cut

########
# persistence
########
package Foo;
sub new {bless { state => 'awake' }, shift}
sub Freeze {
    my $s = shift;
    print STDERR "preparing to sleep\n";
    $s->{state} = 'asleep';
    return bless $s, 'Foo::ZZZ';
}
package Foo::ZZZ;
sub Thaw {
    my $s = shift;
    print STDERR "waking up\n";
    $s->{state} = 'awake';
    return bless $s, 'Foo';
}
package main;
use Data::Dumper;
$a = Foo->new;
$b = Data::Dumper->new([ $a ], [ 'c' ]);
$b->Freezer('Freeze');
$b->Toaster('Thaw');
$c = $b->Dump;
print $c;

=head1

preparing to sleep
$c = bless( {
              'state' => 'asleep'
            }, 'Foo::ZZZ' )->Thaw();


=cut

$d = eval $c;
print Data::Dumper->Dump([ $d ], [ 'd' ]);

=head1

preparing to sleep
waking up
$d = bless( {
              'state' => 'awake'
            }, 'Foo' );


=cut

########
# symbol substitution (useful for recreating CODE refs)
########
sub foo {print "foo speaking\n"}
*other = \&foo;
$bar = [ \&other ];
$d = Data::Dumper->new([ \&other, $bar ], [ '*other', 'bar' ]);
$d->Seen({ '*foo' => \&foo });
print $d->Dump;

=head1

preparing to sleep
waking up
*other = \&foo;
$bar = [
         \&foo
       ];


=cut

########
# sorting and filtering hash keys
########
$Data::Dumper::Sortkeys = \&my_filter;
my $foo = { map {(ord, "$_$_$_")} 'I' .. 'Q' };
my $bar = { %$foo };
my $baz = { reverse %$foo };
print Dumper [ $foo, $bar, $baz ];

=head1

preparing to sleep
waking up
Sortkeys subroutine did not return ARRAYREF
Sortkeys subroutine did not return ARRAYREF
Sortkeys subroutine did not return ARRAYREF
$VAR1 = [
          {},
          {},
          {}
        ];


=cut

sub my_filter {
    my ($hash) = @_;
    # return an array ref containing the hash keys to dump
    # in the order that you want them to be dumped
    return [
        # Sort the keys of %$foo in reverse numeric order
        $hash eq $foo ? (sort {$b <=> $a} keys %$hash) :
            # Only dump the odd number keys of %$bar
            $hash eq $bar ? (grep {$_ % 2} keys %$hash) :
                # Sort keys in default order for all other hashes
                (sort keys %$hash)
    ];
}
```