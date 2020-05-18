# Smart Match Operator(智能匹配操作符)

## 描述
最早在Perl 5.10.1（5.10.0版本的行为有所不同）中提供，它的参数之间进行“智能匹配”。尽管不是所有的when子句都调用智能匹配运算符，但通常在perlsyn中描述的when构造中隐式使用它。在所有Perl运算符中唯一的，smartmatch运算符可以递归。
智能匹配运算符是实验性的，其行为可能会发生变化。
它的独特之处还在于，所有其他Perl运算符都在其操作数上施加了上下文(通常是字符串或数字上下文)，将这些操作数自动转换为所施加的上下文。相反，smartmatch从其操作数的实际类型推断上下文，并使用该类型信息选择合适的比较机制。
\~\~运算符“多态地”比较其操作数，确定如何根据它们的实际类型（数字，字符串，数组，哈希等）比较它们。就像与它具有相同优先级的相等运算符一样，\~\~返回1正确，“”代表错误。通常最好将其大声读为“在...中”，“在...内部”或“包含在其中”，因为通常会在右操作数内部寻找左操作数。这使得操作数到智能匹配操作数的顺序通常与常规匹配运算符的顺序相反。换句话说，“较小”的对象通常放在左侧的操作数中，而较大的则放在右侧。
智能匹配的行为由其参数的类型来决定，如下表所示。该表的第一行表示根据操作数类型确定智能匹配行为。由于实际发生的情况主要取决于第二个操作数的类型，因此该表根据右侧操作数进行排序而不是左操作数。

| 左操作数 | 右操作数 | 伪代码和描述                                     |
|:--------|:-------|:------------------------------------------------|
| Any     | undef  | 检查Any是否是undefined的，类似于!defined Any       |
| Any     | Object | 调用 ~~ 重载一个Object, 或者Object未重载则失败(die) |

右操作数为数组:

| 左操作数                                       | 右操作数 | 伪代码和描述(在布尔上下文中计算)                                                                                |
|:----------------------------------------------|:-------|:-----------------------------------------------------------------------------------------------------------|
| ARRAY1(每个元素与另一个数组中相同索引的元素智能匹配) | ARRAY2  | 在ARRAY1和ARRAY2的成对元素上递归处理，类似于(ARRAY1\[0\] ~~ ARRAY2\[0\]) && (ARRAY1\[1\] ~~ ARRAY2\[1\]) && ... |
| HASH                                          | ARRAY  | 任意数组元素作为哈希键存在，类似于 grep { exists $HASH{$_} } ARRAY                                               |
| Regexp                                        | ARRAY  | 任意数组元素模式匹配正则表达式, 类似于 grep { /Regex/ } ARRAY                                                    |
| undef                                         | ARRAY  | 数组中存在undef的值，类似于 grep { !defined } ARRAY                                                            |
| Any                                           | ARRAY  | (如果找到一个循环引用，则退而判断引用是否相等)智能匹配每一个数组元素，类似于grep { Any ~~ $_ } ARRAY                   |

右操作数为哈希:

| 左操作数 | 右操作数 | 伪代码和描述                                                               |
|:--------|:-------|:-------------------------------------------------------------------------|
| HASH1   | HASH2  | 两个哈希所有键都相同，类似keys HASH1 == grep { exists HASH2{$_} } keys HASH2 |
| ARRAY   | HASH   | 任意数组元素都作为哈希键存在,类似grep { exists HASH->{$_} } ARRAY             |
| Regexp  | HASH   | 哈希键与Regexp模式匹配， 类似grep { /Regexp/ } keys HASH                     |
| undef   | HASH   | 总是返回false，因为undef无法作为哈希键                                        |
| Any     | HASH   | 哈希键是否存在，exists HASH->{Any}                                          |

右操作数为代码:

| 左操作数               | 右操作数 | 伪代码和描述                                                  |
|:---------------------|:--------|:-------------------------------------------------------------|
| ARRAY (空数组返回true) | CODE    | 子例程对所有数组元素返回true, 类似!grep { !CODE->($_) } ARRAY    |
| HASH  (空哈希返回true) | CODE    | 子例程对所有哈希键返回true, 类似 !grep { !CODE->($_) } keys HASH |
| Any                  | CODE    | 子例程被传递的任意参数返回true，类似 CODE->(Any)                  |

```perl5
say [] ~~ $code; # true
say +{ } ~~ $code; # true { } 哈希引用直接跟智能匹配操作符会报错，加一个+号来告知perl这是一个哈希引用
```

右操作数为正则表达式:

| 左操作数 | 右操作数 | 伪代码和描述                                           |
|:--------|:-------|:-----------------------------------------------------|
| ARRAY   | Regexp | 任意数组元素匹配正则表达式，类似grep { /Regexp/ } ARRAY    |
| HASH    | Regexp | 任意哈希键匹配正则表达式，类似grep { /Regexp/ } keys HASH |
| Any     | Regexp | 任意值匹配正则表达式，  类似Any =~ /Regexp/               |

右操作数为其他数据类型:

| 左操作数 | 右操作数                                          | 伪代码和描述                                                 |
|:--------|:-------------------------------------------------|:-----------------------------------------------------------|
| Object  | Any                                              | 在Object上调用重载\~\~, 或者如果Object未重载则退为字符串或数值比较 |
| Any     | Num                                              | 比较数值是否相等    Any == Num                               |
| Num     | numlike(可以是一个真正的数字也可以是看上去像数字的字符串 | 比较数值是否相等    Num == numlike                           |
| undef   | Any                                              | 检查是否是undefined的值，类似 !defined(Any)                   |
| Any     | Any                                              | 比较字符串是否相等  Any eq Any                                |


智能匹配会隐式对任何非祝福的哈希或数组引用进行解引用，因此HASH和ARRAY条目在这些情况下适用。对于有祝福的引用，将应用Object条目。涉及哈希的智能匹配仅考虑哈希键，而不考虑哈希值。
“like”代码条目并不总是精确的表现形式。例如，smartmatch运算符会在可能的情况下短路，但grep不会。同样，标量上下文中的grep返回匹配的数量，但是\~\~仅返回true或false。
与大多数运算符不同，smartmatch运算符知道如何特别处理undef：

```perl5
use v5.10.1;
my @array = (1, 2, 3, undef, 4, 5);
say "some elements undefined" if undef ~~ @array;
```

在修改后的标量上下文中考虑每个操作数，修改方式是将数组和哈希变量通过引用传递给运算符，该运算符对它们隐式解引。每对的两个元素都相同：

```perl5
use v5.10.1;
my %hash = (red => 1, blue => 2, green => 3,
    orange      => 4, yellow => 5, purple => 6,
    black       => 7, grey => 8, white => 9);
my @array = qw(red blue green);
say "some array elements in hash keys" if @array ~~ %hash;
say "some array elements in hash keys" if \@array ~~ \%hash;
say "red in array" if "red" ~~ @array;
say "red in array" if "red" ~~ \@array;
say "some keys end in e" if /e$/ ~~ %hash;
say "some keys end in e" if /e$/ ~~ \%hash;
```

如果第一个数组中的每个元素都与第二个数组中的对应元素智能匹配（即处于...中），则两个数组将递归地智能匹配。

```perl5

use v5.10.1;
my @little = qw(red blue green);
my @bigger = ("red", "blue", [ "orange", "green" ]);
if (@little ~~ @bigger) { # true!
    say "little is contained in bigger";
}

my @little = qw(red blue green);
my @bigger = ("red", "blue", [ "orange", "green" ]);
if (@little ~~ @bigger) { # false! 调换两个变量的位置，匹配结果有可能会发生改变。因为bigger的范围比little范围大，因此little并不包含整个bigger的元素，因为为false
    say "little is contained in bigger";
}
```

因为智能匹配运算符在嵌套数组上递归，所以仍会报告**red**在数组中。

```perl5
use v5.10.1;
my @array = qw(red blue green);
my $nested_array = [ [ [ [ [ [ [ @array ] ] ] ] ] ] ];
say "red in array" if "red" ~~ $nested_array;
```

如果两个数组彼此智能匹配，则它们是彼此值的深拷贝，如以下示例所示：

```perl5
use v5.12.0;
my @a = (0, 1, 2, [ 3, [ 4, 5 ], 6 ], 7);
my @b = (0, 1, 2, [ 3, [ 4, 5 ], 6 ], 7);
if (@a ~~ @b && @b ~~ @a) {                    # 当调换两个变量的位置以后，匹配结果依然为真，则两个数组被称为深度拷贝的
    say "a and b are deep copies of each other";
}
elsif (@a ~~ @b) {
    say "a smartmatches in b";
}
elsif (@b ~~ @a) {
    say "b smartmatches in a";
}
else {
    say "a and b don't smartmatch each other at all";
}


```

如果你要设置$b\[3\] = 4，那么它现在将报告a中的b个智能匹配，而不是报告a和b是彼此的深拷贝。这是因为@a中的相应位置包含一个数组（其中最后有一个4）。
将一个散列与另一个散列进行智能匹配会报告两个散列是否都包含相同的键，且不多且不少。这可用于查看两个记录是否具有相同的字段名称，而无需关心这些字段可能具有的值。例如：
```perl5
my @a = (0, 1, 2, [ 3, [ 4, 5 ], 6 ], 7);
my @b = (0, 1, 2, [ 3, [ 4, 5 ], 6 ], 7);

$b[3] = 4; # (0, 1, 2, 4, 7)
say "true" if @b ~~ @a; # true
```

```perl5
use v5.10.1;
sub make_dogtag {
    state $REQUIRED_FIELDS = { name => 1, rank => 1, serial_num => 1 };
    my ($class, $init_fields) = @_;
    die "Must supply (only) name, rank, and serial number"
        unless $init_fields ~~ $REQUIRED_FIELDS;
    ...
}
```
如果允许其他非必填字段，请使用ARRAY \~\~ HASH：

```perl5
use v5.10.1;
sub make_dogtag {
    state $REQUIRED_FIELDS = { name => 1, rank => 1, serial_num => 1 };
    my ($class, $init_fields) = @_;
    die "Must supply (at least) name, rank, and serial number"
        unless [ keys %{$init_fields} ] ~~ $REQUIRED_FIELDS;
    ...
}
```

smartmatch运算符最常用作when子句的隐式运算符。

#### 智能匹配对象

为了避免依赖对象的底层表示，如果只能匹配的右操作数是不重载\~\~的对象，则会引发异常“Smartmatching
a non-overloaded object breaks encapsulation”。那是因为没有办法深入查看某个东西是否在对象中。对于没有重载\~\~的对象，以下做法都是不合法的：

```perl5
%hash ~~ $object;
42 ~~ $object;
"fred" ~~ $object;
````

但是，你可以通过重载\~\~运算符来更改对象智能匹配的方式。这允许扩展通常的智能匹配语义。
允许使用对象作为左操作数，尽管不是很有用。智能匹配规则优先于重载，因此即使左侧操作数中的对象提供了智能匹配重载，也会将其忽略。左操作数是一个未重载的对象，则会对ref运算符返回的结果完成一个字符串或数值比较。那意味着

```perl5
 $object ~~ X;
````
不会使用X作为参数调用重载方法。而是正常参考上表，并且基于X的类型，可能调用也可能不调用重载。对于简单的字符串或数字，这等价于：

```perl5
$object ~~ $number;
ref($object) == $number;

$object ~~ $string;
ref($object) eq $string;
```

```perl5
use IO::Handle;
my $fh = IO::Handle->new();
if ($fh ~~ /\bIO\b/) {
    say "handle smells IOish";
}
````
这里是因为它将$fh视为类似于“IO::Handle = GLOB(0x8039e0)”的字符串，然后对其进行模式匹配。
