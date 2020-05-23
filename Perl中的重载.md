# **overload(重载)**

## **描述:**
Perl的重载简而言之就是平时如果我们需要用一些常规操作符去操作对象的话，会得到与正常结果不同的结果。
例如将一个对象与一个数字相加：
```perl5
# 重载前
package MyClass;
use feature qw(say);
sub new {
    shift;
    bless \(my $var = shift) => __PACKAGE__;
}

sub add {
    my ($first, $second) = @_;
    $$first + $second;
}

my $obj = MyClass->new(10);
say 6 + $obj; #=> 140505199743230 对象本质是内存地址指向的数据结构，因为会得到奇怪的结果

# 重载后
package MyClass;
use feature qw(say);
use overload
    "+" => \&add;

sub new {
    shift;
    bless \(my $var = shift) => __PACKAGE__;
}

sub add {
    my ($first, $second) = @_;
    $$first + $second;
}

my $obj = MyClass->new(10);
say 6 + $obj; #=> 16

```
## overload Pragma
overload pragma实现操作符重载，需要为它提供操作符及其相关行为的一个键值对列表(操作符为键，而相关行为作为值)。
```perl5
package MyClass;
use feature qw(say);
use overload
    "+"   => \&add,            # 代码引用
    "<"   => 'less_than',      # 命名方法
    "abs" => sub {return @_};  # 匿名子例程
```
如果将MyClass的两个对象相加，就会调用add方法来进行处理。
如果用<来比较MyClass的两个对象，Perl会注意到这个行为指定为一个字符串，并把这个字符串解释为方法名，而不只是一个子例程名。
以上三种方式中，add必须是当前模块的方法，而第二种方式对应的行为可由基类继承而来，第三种方式则更为直接，直接指定一个具体的行为，它会自行提供。
对于一元操作符，只要这个操作符应用到某个对象上面，就会调用为该类指定的处理方法。
对于二元操作符，如+或者-, 只要第一个操作数是某个类的对象，或者当第二个操作数是该类的对象而且第一个操作数没有重载行为，就会调用为该类指定的处理方法。
回到最上面的代码示例：
```perl5
$obj + 6; # 这种形式的写法，会把对象和数字按照顺序传入方法中进行处理 
 
6 + $obj; # 这种形式的写法会交换操作数的顺序，也就是说传入方法的参数顺序与第一种形式相同

$obj1 + $obj2; # 假如有两个不同对象进行相加，并且都使用了重载，这样就会触发obj1的重载行为。
```
## **重载处理方法**
一个重载的操作符执行操作时，会调用相应的方法，并提供三个参数。前面两个参数是操作数。如果这个操作符只使用一个操作数，第二个参数则为undef。第三个参数指示前两个操作数是否交换顺序。
如果前两个参数交换了顺序，则第三个参数为true。否则第三个参数为false。如果处理方法由另一个设计赋值的处理方法触发，那么第三个参数不仅仅是false，而是undef。利用这个区别可以做一些优化：
```perl5

package MyClass;
no warnings qw(experimental);
use feature qw(say);
use overload
    "+" => \&clip_add,
    "-" => \&clip_sub;

sub new {
    my $class = shift;
    my $value = shift;
    bless \$value => $class;
}

sub clip_add {
    my ($x, $y, $swap) = @_;
    my ($value) = ref $x ? $$x : $x;
    $value += ref $y ? $$y : $y;
    $value = 255 if $value > 255;
    $value = 0 if $value < 0;
    return bless \$value => ref $x;
}

sub clip_sub {
    my ($x, $y, $swap) = @_;
    my $var = $swap ~~ undef ? "undef" : $swap;
    say "The swap value is $var";
    my ($value) = ref $x ? $$x : $x;
    $value -= ref $y ? $$y : $y;
    if ($swap) { $value = -$value }
    $value = 255 if $value > 255;
    $value = 0 if $value < 0;
    return bless \$value => ref($x);
}

my $clip_obj1 = MyClass->new(30);
my $clip_obj2 = MyClass->new(20);

my $var = 6;
my $var2 = 10;

say ${$clip_obj1 + \$var};  # equal $clip_obj1 + $var ;
say ${$clip_obj1 - \$var2}; # equal $clip_obj1 - $var;
say ${6 - $clip_obj1};
say ${$clip_obj1 - $clip_obj2};
```
可重载操作符见下表:

| 类别         | 操作符                                     |
|:-------------|:------------------------------------------|
| 转换操作符    | "" 0+ bool qr                             |
| 算术操作符    | + - * / % ** x . neg                      |
| 逻辑操作符    | !                                         |
| 位操作符      | & << >> \| ~                              |
| 赋值操作符    | += -= *= /= %= **= x= .= <<= >>= ++ --    |
| 比较操作符    | == < <= > >= != <=> lt gt le ge eq ne cmp |
| 数学函数      | atan2 cos sin exp abs log sqrt int        |
| 迭代操作符    | <>                                        |
| 文件测试操作符 | -X                                        |
| 解引用操作符   | ${} @{} %{} &{} *{}                       |
| 匹配操作符    | \~\~                                      |
| 伪操作符      | nomethod fallback =                       |

需要说明的是，neg, bool, nomethod和fallback都不是真正的perl操作符。5个解引用操作符，qr和0+看起来也不像操作符，但是它们都是合法的键，可以放在use overload的参数表中。
伪操作符只能隐示调用，另外，重载并不是发生在语法层，而是发生在语义层，关键不是要好看，而是要正确。

**转换操作符: "" 0+ bool qr:**
前面三个键分别允许perl自动转换为字符串，数字和布尔值。第四个键，则表示当一个对象被内插到一个正则表达式或者作为正则表达式时(包括=~或!~的右操作数出现)。qr子例程必须返回一个已经编译的正则表达式，或者返回一个已经编译的正则表达式引用。对返回值的进一步重载都将被忽略。
任何非字符串变量用作为字符串时都会"字符串化"。如果通过打印，内插，连接或者作为散列键都会将一个变量转换为字符串，例如打印一个对象会得到类似SCALAR(0xba5fe0)的结果。
而在数字上下文中时，例如任意的数学表达式，数组索引或者甚至作为..(范围操作符)的操作数，非数值变量转换为一个数字时会出现"数字化"。
最后一个"布尔化"，通过创建bool处理方法，可以定义对象在一个布尔上下文中(如if, unless, while, for, and, or, &&, ||, ?:或grep表达式中的代码块)应当如何解释。
需要说明的是，如果触发转换的那个操作也被重载，该重载就会立即发生。
```perl5

package People;
use overload q("") => \&as_string;
use feature qw(say);


sub new {
    my $class = shift;
    return bless { @_ } => $class;
}

sub as_string {
    my $self = shift;
    my ($key, $value, $result);
    while ( ($key, $value) = each %$self) {
        $result .= "$key => $value\n";
    }
    return $result;
}

my $obj = People->new(height => 72, weight => 165, eyes => "brown" );
print $obj;
# weight => 165
# eyes => brown
# height => 72
```
## **算术操作符: + - * / % ** x . neg**
neg是用于一元负号(-1)的一个特殊重载键。利用neg和-的区别，你可以为一元负号和二元减号指定不同的行为，二元减号通常表示减法。
如果重载-而没有重载neg，然后试图使用一元负号，Perl会为你模拟一个neg处理方法。这称为自动生成，发生自动生成的情况下，某些操作符可以从其他操作符合理地推导出，前提是重载操作符与常规操作符之间有相同的关系。由于一元负号可以表示为二元减号的一个函数，也就是说-1等同于0-1，所以-重载时，Perl并不强制重载neg，但是如果故意把二元减号定义为用第一个参数除第二个参数，一元负号就很可能抛出除零错误。
利用.操作符实现的连接可以通过字符串化处理方法自动生成。
```perl5
package Configs;
use overload
    "neg" => sub {
        return -(${+shift});
    },

    '-' => sub {
        my ($x, $y, $swap) = @_;
        my $var = $swap ~~ undef ? "undef" : $swap;
        say "The swap value is $var";
        my ($value) = ref $x ? $$x : $x;
        $value -= ref $y ? $$y : $y;
        if ($swap) {$value = -$value}
        bless \$value => ref($x);
    };
    
# 定义neg，会直接调用neg的重载行为，返回值为数字，不需要解引用    
my $obj1 = Configs->new(10);    
my $result =  -$obj1;
say $result;  # -10

use overload
    '-' => sub {
        my ($x, $y, $swap) = @_;
        my $var = $swap ~~ undef ? "undef" : $swap;
        say "The swap value is $var";
        my ($value) = ref $x ? $$x : $x;
        $value -= ref $y ? $$y : $y;
        if ($swap) {$value = -$value}
        bless \$value => ref($x);
    };   

# 没有定义neg，则在进行计算时，会调用-的重载行为，因此返回一个对象，需要解引用
my $obj1 = Configs->new(10);    
my $result =  -$obj1;
say $result;  # -10   
```
## **逻辑操作符: !:**
如果没有为!定义处理方法，可以使用bool，"",或0+处理方法自动生成处理!的处理方法。如果重载了!操作符，not操作符也会触发你请求的任何行为。
还有其他逻辑操作符为啥没出现在这是因为，大部分逻辑操作符是短路的，因此无法重载。它们是真正的控制流操作符，因此三目操作符也无法重载。

## **位操作符: & << >> \| ~**
\~操作符是一个一元操作符，所有其他位操作符都是二元操作符，可以重载>>完成类似chop的工作:
```perl5
use overload
    ">>" => \&right_shift,
    q("") => sub { ${$_[0]} };

sub new {
    my $class = shift;
    my $var = shift;
    return bless \$var => $class;
}

sub right_shift {
    my ($x, $y) = @_;
    my $value = $$x;
    substr($value, -$y) = "";
    return bless \$value => ref $x;
}

my $obj1 = Configs->new('Camel');
my $ram = $obj1 >> 2;
say $ram;  # Cam
```


## **赋值操作符: += -= \*= /= %= \*\*= x= .= <<= >>= ++ --**
这些赋值操作符可以改变原来的值，也可以保留原来的值。只有当新值与原值不同时，才会把结果赋值给左操作数。这样就可以使用同一个处理方法来重载+=和+。虽然这是允许的，但是不建议这么做，因为perl总是会调用+的重载方法而假设+=没有直接重载。
连接赋值操作符(.=)可以使用字符串化再加一个正常的字符串连接自动生成。++和--操作符可以由+和-(或+=和-=)来自动生成。
实现++和--处理方法往往会改变其参数。如果希望自增操作符不光可以用于数字，也能够用于字符串可以使用以下处理方法实现:
```perl5

use overload
    q(--) => \&decrement,
    q("")  => sub { ${$_[0]} };

sub new {
    my ($class, $var) = @_;
    return \$var => $class;
}

sub decrement {
    my @string = reverse split(//, ${ $_[0] });
    my $i;
    for ($i = 0; $i < @string; $i++) {
        last unless $string[$i] =~ /a/i;
        $string[$i] = chr(ord($string[$i]) + 25);
    }
    $string[$i] = chr(ord($string[$i]) - 1);
    my $result = join( "" => reverse @string );
    $_[0] = bless \$result, __PACKAGE__;
}
```

## **比较操作符: == < <= > >= != <=> lt gt le ge eq ne cmp**

如果重载了<=>，可以用它来自动生成<. <=, >, >=, ==, !=的行为。类似的，如果重载了cmp，则可以用它自动生成lt, gt, le, ge, ne, eq的行为。
需要注意的是，即使重载了cmp，也不能像想象的那样对对象排序，因为所比较的是字符串化的对象，而不是对象本身，如果要比较真正的对象，还需要重载""。

## **数学函数: atan2 cos sin exp abs log sqrt int**
如果abs不可用，可以由<或<=>并结合一元负号或二元减号来自动生成。
可以重载的-为一元负号或abs函数自动生成缺少的处理方法，它们也可以重载。
按照传统，Perl函数int会向0取整，所以对于表现的像浮点类型的对象，可能要做同样的处理，以免让人困惑。


## **迭代操作符: <>**
通过使用readline(从一个文件句柄读取，如while(<FH>))或者glob(用于文件图案匹配，如@files = <* .*>)，可以触发<>处理方法：
```perl5
use overload
    '<>' => sub {
        my $self = shift;
        return splice @$self, rand @$self, 1;
    };

sub new {
    shift;
    bless [@_] => __PACKAGE__;
}

my $obj1 = Configs->new(1..10);
for (qw(1st 2nd 3rd 4th 5th)) {
    my $num = <$obj1>;
    say "The $_ is $num";
}
```

## **文件测试操作符: -X**

键-X用于指定一个子例程来处理所有文件测试操作符，如-f, -x等。
不能单独地重载某一个文件测试操作符。为了区别不同的文件测试操作符，-后面的字母作为第二个参数传入(也就是说，传入二元操作符用来传递第二个操作数的槽中).
调用一个重载的文件测试操作符并不影响与特殊文件句柄_关联的stat值。它仍然指向上一个stat，lstat或为重载文件测试的结果。
## **解引用操作符: ${} @{} %{} &{} \*{}**
如果试图解引用标量，数组，散列，子例程和类型团引用，可以通过重载这5个符号来截取。
```perl5
use overload
    "%{}" => \&as_hash;

sub new {
    shift;
    bless [@_] => __PACKAGE__;
}

sub as_hash {
    my ($x) = @_;
    return { @$x };
}

my $obj1 = Configs->new(larry => 70, perl => '5.30');
say $obj1->{larry};
say $obj1->{perl};

# 将一个数组对象转为哈希对象
```
## **智能匹配: \~\~**
键\~\~允许你覆盖智能匹配操作符和given构造使用的智能匹配逻辑。
在一些不常见的情况下，智能匹配操作符的重载实现并没有全面控制智能匹配的行为。
```perl5

use overload
    "~~" => \&match;

sub new {
    shift;
    bless [@_] => __PACKAGE__;
}

my $obj1 = Configs->new(larry => 70, perl => '5.30');
# $obj1->match([1, 2, 3], 0); 不正确的调用
say $obj1 ~~ [1, 2, 3];
```
智能匹配优先级高于重载，因此会优先考虑智能匹配分配规律。这里会把$obj1与数组中的元素依次进行比较。

## **缺少重载处理方法时(nomethod和fallback)**
如果对一个对象应用一个未重载的操作符，Perl首先会用之前所说的规则尝试从其他重载操作符那里自动生成一个行为。如果失败，perl会寻找nomethod的重载行为，如果找到则使用，这个处理方法对于操作符来说就像AUTOLOAD子例程对于其他子例程:如果没有其他选择，则可以用它。
如果使用nomethod, nomethod键后面必须跟一个处理方法的引用，这个处理方法接受4个参数，而所有其他处理方法接受三个参数。前三个参数为其他处理方法的参数并没有不同，第四个参数是一个字符串，对应于这个缺少处理方法的操作符。它与$AUTOLOAD变量在AUTOLOAD子例程中的作用是一样的。
如果perl必须查找一个nomethod处理方法，但是找不到就会产生异常。
如果想要避免发生自动生成，或者希望自动生成失败而不导致重载，可以定义另外一个特殊的fallback重载键。它有三个有用状态:
1. undef
如果****fallback未设置，或者显示设置为undef，重载事件序列不受影响，总是先查找处理方法，尝试自动生成，最后调用nomethod处理方法。如果失败则产生异常。
2. false
如果fallback设置为一个确定值，但是为false(如0)，则不会尝试自动生成，如果存在nomethod处理方法，就会调用这个方法，不过如果不存在，就会产生一个异常。
3. true
这个与undef的行为几乎是一样的，不过如果未能通过自动生成合成一个合适的处理方法，并不会产生异常，相反，perl会采用该操作符的未重载行为，就好像类中没有定义overload pragma一样。

## **公共重载函数**
overload::StrVal(OBJ)
这个函数返回没有字符串化重载("")时OBJ会有的字符串值

overload::Overloaded(OBJ)
如果OBJ应用了重载操作符，则返回ture

overload::Method(OBJ, OPERATOR)
这个函数为处理OBJ的操作符返回其重载实现代码的一个引用。如果不存在则返回undef。

## **继承和重载**
如果重载的行为格式为字符串时，处理方法会被解释为方法，因此可以从超类继承，而另外两种形式则不会。
从一个重载类派生的任何子类本身也有这种重载行为。换句话说，重载本身是继承的。如果一个处理方法可能在多个不同的祖先类中找到，实际使用的处理方法由方法继承的通用原则来确定。
类A继承自B和C，而B和C分别重载了+操作符，那么A会优先继承B的重载行为。
由于fallback不是一个处理方法，因此不受继承规则的控制。在当前实现中，会使用第一个重载祖先的fallback值，不过会有偶发性。

## **运行时重载**
eval "use overload '+' => \&myadd";
也可以写成:
eval " no overload '+', '--'， '<='";
不过在运行时会产生问题