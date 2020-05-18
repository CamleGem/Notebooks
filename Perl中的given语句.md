# given ... when ... 语句
在perl 5.10和更高的版本中提供了if结构的一个替代结构，即given函数，从语言学的角度来说，这就相当于是提供了一个主题工具。它的做法是将$_设置为当前主题。然后可以使用when语句检查这个主题的各个不同值或模式。
使用Perl的v5.10及以后的版本时，就会启用这个特性:
```perl5
use v5.12;  # 至少v5.12 加载默认特性

# 如果要特别请求"switch"特性，则需要:
use feature qw(switch);
```
以上两种声明都会向Perl语言增加一些新的关键字:given, when, break, continue和default。可以使用这个新特性重写前面的例子，以下给出了这样一种方法:
```perl5
my $var;
given(<STDIN>) {
    when (/red/)   { $var = 0xFF0000 }
    when (/green/) { $var = 0x00FF00 }
    when (/blue/)  { $var = 0x0000FF;}
    default {
        warn "unknow RGB component '$_', using black instead\n";
        $var = 0x000000;
    }
}
binmode(STDOUT, "utf8");
print chr($var);

```
实际上，在v5.10中必须这样写，因为given不能返回值，而在v5.14或更高版本中，你可以返回值，利用when的语句修饰符形式，可以像下面这样写:
```perl5
my $var = do {
    given (<STDIN>) {
        0xFF0000 when /red/;
        0x00FF00 when /green/;
        0x0000FF when /blue/;
        default {
            warn "unknow RGB component '$_', using black instead\n";
            0x000000;
        }
    }
}
```
given和when的参数都在标量上下文中计算，given将其参数绑定到$_变量，用来设置其block的主题。when通过查看参数的类型，使用其参数选择希望完成哪一种模式的匹配，when的语义是只能匹配的一个超集。如果参数看起来是一个布尔表达式，则会直接计算。如果不是，则将把它传递到智能匹配操作符，解释为$_ \~\~ EXPR.
```perl5
use feature qw(switch say);

sub add {
    return $_[0] + $_[1];
}

my $result = add(1, 2);
given ($result) {
    when (0) { say "zero" }
    when (1) { say "one"  }
    when ([3..7])  { say "many" }
    when (/^\d+$/) { say "lots" }
    default { say "unwholesome" }
}
```
换句话说，when参数通常会调用智能匹配(或者你可以认为假装在调用智能匹配)
```perl5
use feature qw(:5.10);
my $n = somefunc();
given ($n) {
    # 如果!defined($n)则匹配
    when (undef) {
        say "$n is undefined"
    }
    # 如果$n eq "foo"
    when ("foo") {
        say "$n is the string foo";
    }
    # 如果$n ~~ [1, 3, 5, 7, 9]
    when ([1, 3, 5, 7, 9]) {
        say "$n is an odd digit";
        continue; # 继续下面的when子句的匹配
    }
    # 如果$n < 100则匹配
    when ($_ < 100) {
        say "$n is less than 100";
    }
    # 如果complicated_check($n)为真则匹配
    when (\&complicated_check) {
        say "a complicated check for $n is true";
    }
    # 如果以上都不匹配，则执行默认语句
    default {
        die "I do not know what to do with $n";
    }
}

```
given(EXPR)将EXPR的值赋给$_的一个词法作用域副本，而不是像没有my声明的foreach那样赋给一个动态作用域别名。这使它非常类似于do块：
```perl5
do { my $_ = EXPR; ... }
```
不过成功的when知道如何跳出这个块，当匹配成功时，会隐式调用break关键字来跳出，也可以显示使用break。由于它是词法作用域变量，所以可以使用given局部化$_的一个动态值，就像老式foreach一样。
可以使用continue关键字从一个分支跳出，直接前进到下一个语句的开始，这可能是另一个when(也可能不是):
```perl5
given ($foo) {
    when (/x/) {
        say "$foo contains an x";
        continue
    }
    say "I always get here.";
    when (/y/) { say "$foo contains y" }
    default { say "$foo does not contain a y" }
}
```
given语句同时是一个合法的表达式时(例如，这是块中的最后一个表达式), 它会计算为:
* 一旦遇到一个显示的break， 则计算为一个空列表。
* 成功的when/default子句(如果有)最后计算的表达式的值。
* 如果没有条件为true，则为given块最后计算的表达式的值。
最后一个表达式要在为given块提供的上下文中计算。

需要说明的是，与if和unless不同，失败的when语句总是计算为一个空列表。
```perl5
my $price = do {
    given ($item) {
        when ([qw(pear apple)]) { 1 }
        break when "vote";
        1e10 when /mona lisa/;
        "unknow";
    }
};

```
需要注意的是，这里必须使用do块，因为given只识别为一个语句，而它在赋值后是不合法的。
#### when语句和修饰符
given的非凡能力主要来自于各个数据类型隐含的隐示智能匹配。默认地，when(EXPR)会处理为$_的一个隐示智能匹配；也就是$_ \~\~ EXPR。不过，如果when的EXPR参数属于以下10种例外形式，则会直接计算，来得到一个布尔结果，不会发生智能匹配:
1. 用户定义的子例程调用或方法调用。
2. 形如/REGEX/, $foo =~ /REGEX/或$foo =~ EXPR的正则表达式匹配。
3. 使用显示\~\~操作符的智能匹配，如EXPR \~\~ EXPR(例如，如果需要取消when内置的智能匹配的默认多态处理，可以对$_使用一个显示的智能匹配。
4. 返回布尔关系操作符(如$_ < 10 或 $x eq "abc" )。这包括6个数值比较操作符(<, >, <=, >=, ==, !=)以及6个字符串比较操作符(lt, gt, le, ge, eq, ne)。
5. 3个内置函数:defined, exists和eof。
6. 取非的表达式，可以是!EXPR或not(EXPR)，或者是逻辑或EXPR1 xor EXPR2(这不包括按位取非和位异或操作\[\~和^\]。取非的正则表达式也属于此类，而不论采取什么写法，可以是!/REGEX/, $foo !~ /REGEX/或$foo !~ EXPR。
7. 文件测试操作符(除-s, -M, -A和-C以外，因为这些操作符会返回数字，而不是布尔结果)。
8. ..和...触发器操作符(需要注意，...中缀操作符与...省略语句完全不同，只有当你希望得到一个语句时才会识别省略语句)。
在以上8种情况中，EXPR的值直接用作一个布尔值，而不会完成智能匹配。可以把when看做一个智能的智能匹配。为了让它更智能，Perl对逻辑操作符(即"与"和"或")的操作数递归地应用这些测试，来确定是否使用智能匹配，如下所示:




* 对于EXPR1 && EXPR2 或 EXPR1 and EXPR2, 会对EXPR1和EXPR2递归地应用这个测试。只有当两个操作数都通过测试时，才会把这个表达式处理为一个布尔值。否则将使用智能匹配。
* 对于EXPR1 || EXPR2 或 EXPR1 or EXPR2, 只会对EXPR1递归的应用这个测试(例如，它本身可能是一个更高优先级的AND操作符，因此需要遵循前一条规则), 而不对EXPR2应用测试。如果EXPR1要使用智能匹配，那么EXPR2也要使用智能匹配，而不论EXPR2包含什么内容。不过，如果EXPR1不使用智能匹配，那么第二个参数也不会使用。这个与前面介绍的&&情况完全不同，所以要当心(需要说，由于//操作符左边隐含有defined函数，所以EXPR1 // EXPR2总被认为是布尔值)。
例如：
```perl5
when (/^\d+$/ && $_ < 75) { ... }
```
会被处理为一个布尔匹配，因为两个表达式都满足以上八种测试条件，因此被识别为布尔匹配。
另外：
```perl5

when ([qw(foo bar)] && /baz/) { ... }
```
这里会使用智能匹配，因为只有满足两个表达式都通过条件测试，而这里只有第二个表达式看起来像是布尔值。第一个表达式不是布尔值，所以这里智能匹配取得胜利，或者也许根本没有获胜方，除非你希望对右边的结果完成智能匹配，这将是1或""。这些given可能不会提供。
注意，顺序对于判断是否执行智能匹配非常重要。例如：
```perl5
when (/^baz/ || [qw(foo bar)]) { ... } # 当成布尔表达式判断，因为表达式1满足测试条件，因此采用布尔表达式，因此表达式2也采用布尔表达式，而表达式2总是true
when ([qw(foo bar)] || /^baz/) { ... } # 使用智能匹配，表达式1要使用智能匹配，因此表达式2也使用智能匹配
```
基于常量的布尔操作符还是进一步优化。不要这样写:
```perl5
when ("foo" or "bar") { ... }
```
以上的写法会优化为"foo", 所以永远不会考虑"bar", 如果要做类似的选择，完全可以使用一个数组引用，因为这会触发智能匹配，它有自己的"匹配任何一个的语义":
```perl5
when(["foo", "bar"]) { ... }
```
对于有多个"标签"的情况就要这样写，因为在perl中没有与C中fall-through语义对应的特性。
default就相当于when(1==1)，也就是说，它总是会匹配。由于各个分支是按照顺序计算的，所以它会最后计算，它会有一个隐示break，所以不会到达后面的代码。
作为智能匹配的一个补充，如果使用一个直接量数组或者哈希作为given的参数，它会被转为一个引用，从而不会丢失信息。所以例如given(@foo)与given(\@foo)完全相同。如果想匹配@foo的长度，则需要写为given(scalar @foo)。


