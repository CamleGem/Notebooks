## 关于ruby中方法的一些知识拓展

1. 一个方法的返回值通常是方法中最后一个表达式的值，但是如果最后一个表达式的值不存在，则方法返回的值为nil。
2. 如果一个方法最后一个表达式产生多个值，返回的值需要用逗号分隔开，最后方法会以数组的形式返回多个值。为了是代码更加清晰，可以将返回的值显示的放入数组中然后返回。见下面示例：

```ruby
def name
   puts "Hello World!"
end

p name #=> nil

def value(a, b)
   return a, b
end               #=> [a, b] 两种形式结果一样，需要注意第一种形式，必须跟上return关键字，否则会报错。

def value(a, b)
   [a ,b]
end   
```

当有多个返回值时，可以使用并行赋值的方式。
```ruby
def double(a, b)
   return a, b
end

first, second = double(1, 2)
p first, second

```
#### 单实例方法
单实例方法从名字可以推断出，方法是针对单个实例来定义的，对于其他实例并不可见。
```ruby
str = "Hello"
def str.world
    puts "#{self} world"
end

str.world #=> hello world 可以调用world方法

str2 = "hello2"
str2.world #=> error,程序会报错，因为world方法仅针对hello字符串实例，而hello2字符串实例不可见

```
单实例方法定义并不是适用于所有的实例对象：
```ruby

num = 10
def num.hello
   p self
end
num.hello #=> error can not singleton

sym = :matz
def sym.hello
   p self
end

sym.hello # error can not singleton
```
在ruby中，通常将Fixnum和Symbol类以及Numeric的实例视为intermediate值，因此并不是真实的对象引用，所以不能对它们定义单实例方法。
由于在ruby中万物皆对象，因此ruby中的不同类各自所定义的方法相对于Class来说，都是单实例方法，因为ruby中不同的类本质上来说也是Class类的对象。ruby中还有Module类，我们定义的module对于Moudle类来说也是它的实例，因此不同的module自己定义的方法相对于Module来说也是单实例方法。
```
module Foo
 ...
end

p Foo.class #=> Module, Foo是Moudle类的一个实例

```
#### Undefining method
在ruby中，定义的方法可以通过undef语句来取消定义
```ruby
def foo; puts foo; end # 定义一个方法
undef foo #=> 取消定义的方法
foo #=> error, 方法不存在
```
对于类来说，类定义的方法也能被undef，以及类从父类继承而来的方法也能被undef，但是undef并不会影响父类的方法。本质上来说就是在继承某个类的方法时，屏蔽某些不需要的方法，因此并不会对父类的方法造成影响。
```ruby
class A
  def m
    ...
  end
end
Class B < A
    undef m 
end
B.new.foo #=> "foo", 打印结果，可以正常调用从A类继承而来的方法foo

class A
   def foo
      p "foo"
   end
end

class B < A
   undef_method :foo  # undef从A类继承而来的方法m，这样B的实例无法调用该方法
end

B.new.foo  #=> 报错，提示为定义该方法
```
undef和undef_method的效果一样，undef_method的参数为函数名的符号名。通常将函数undefined并不常见，通常是将继承而来的方法重新定义或者进行扩展。

#### method alias
ruby中可以为method定义一个别名，使其它不同使用场景下面更符合人类的表达方式。
别名的作用更多的用于扩展一个方法的功能，通过别名可以在新的方法里面再次被调用
```ruby
def hello
   puts "hello"
end

alias old_hello hello #定义一个hello的别名 old_hello

def hello
   puts "This is the new method"
   puts "pay attention here!"
   old_hello #这里再次调用先前定义的hello方法，因为old_hello方法是hello的别名
end

hello
```
需要注意的是，alias并不是overloading。

#### 方法的括号
在ruby中，方法的括号在大部分情况情况下是可以省略的，这样看起来会更加的简洁。一般情况下，当一个方法调用不需要参数或者只需要一个参数的时候可以省略括号，但是在少数情况下，即使方法需要多个参数，括号也依然能够省略。
```ruby
x = "hello"
x.length
x.length() #=>这两种方式在ruby中都被允许。

num = 1
1.between?1, 5
1.between?(1, 5) #=> 这两种方式在ruby中也是都被允许的
```

括号在方法调用时有两个作用：
1. 在进行方法调用时，包围参数列表
2. 对表达式进行分组

当使用括号进行方法调用时，方法后面必须立即跟上一个括号，括号与方法名之间没有空格，如果添加了空格，则ruby会把括号当做是表达式分组，而不是方法的参数列表。
```ruby
def sum (a, b = 0)
   a + b
end

puts sum 1, 2          #=> 先计算方法sum的值，然后将sum的返回值打印出来，结果为3, 这种写法在早期ruby1.8版本会产生警告，现在则不会。
puts sum(1, 2)         #=> 同上，这种写法更加清晰，结果为3
puts sum(1, 2) * 2     #=> 先计算方法sum的值，然后将计算的值与2相乘，puts再将结果打印出来，结果为6
puts(sum 1, 2)         #=> 这种写法puts会将sum的返回值当成自己的参数打印出来，结果为3，这种写法在早期ruby 1.8版本会产生一个warning，而现在的版本解释器会正常解析这种写法。
puts (sum 1, 2)        #=> 这种写法在puts和括号之间加了个空格，ruby会把括号当成表达式分组，因此会先计算括号里面表达式的值，然后交由puts打印出来，结果为3
puts sum 1, 2 * 2      #=> 先计算2*2的值为4，然后将1和4传入方法sum，得到5，然后由puts打印出来
puts sum(1, 2) * 2     #=> 先计算sum的值为3，然后将3与2相乘得到6，交由puts打印出来
puts sum (2 + 2) * 3   #=> 先计算2+2的值为4，然后与3相乘得到12，再交由sum处理，由于sum设置了默认参数，因此即使传入一个参数也不会报错，此时会得到12，然后交由puts打印出来
puts 1, sum 1, 2       #=> 这种写法会使解释器无法正常执行计算，解释器不知道按照何种顺序去解析表达式。
puts 1, sum(1, 2)      #=> 加上括号以后就可以让解释器明白sum后面的1和2是作为sum的参数列表的，然后1和sum的结果才是自己的参数列表。
puts 1, sum (1 + 2)    #=> 这种写法也是不允许的，多了一个空格会造成歧义，使解释器无法正常解析表达式。
puts 1, (sum 2, 2)     #=> 告诉ruby先计算sum函数的值，然后再将结果打印出来
```
通过以上的示例可以知道在ruby中，如果括号使用不正确会导致意想不到的结果，导致最终的结果与自己预期的结果不一致，因此最稳妥的办法还是老老实实在适当的位置加上该有的括号，避免歧义。
当然，ruby是一门和perl一样表达性非常强的语言，如果是经验丰富的rubist，那完全可以驾驭各种写法，但是对于新人还是老老实实按照正常的语法来写，可以使自己的代码更加的清晰明了。
```ruby
针对上面的示例，稳妥的写法是下面这样：
puts(sum(1, 2))    #=> 3
puts(1, sum(1, 2)) #=> 1
                       3
```
#### method parameters
ruby中的方法参数有以下几种定义方式：
1. 默认参数， 默认参数在定义的时候，不允许将一个传统参数定义在两个默认参数的中间。当有多个默认参数时，给方法指定参数的时候，参数是从左往右开始分配的。
2. 带*前缀的参数表示参数的数量可以是任意个，当传入多个参数时，会将这些参数转换为一个数组。带\*的参数不能定义在两个默认参数之间，也不能定义在默认参数的前面，但是可以定义在传统参数的前面
3. 当一个函数的返回值为数组，且这个函数的返回值作为另一个函数的参数，则可以在这个函数的前面加一个\*号，来展开数组，以便作为另一个函数的参数。

示例
```ruby

def first(a, b)
   return a + b
end

def second(a, b)
   return a, b
end
p first(*second(1, 2))
如果second前面不加*号，则会提示参数错误，加了*可以正常执行。
```
在ruby里面，enumerator也是可以splatter的对象，因此我们也可以这么做
```ruby

def max(first, *rest)
   return first > rest.max ? first : rest.max
end
p max(*"hello world".each_char)
"hello world".each_char返回的是一个enumerator对象，在它前面加上\*然后作为参数传给max方法，则它就被展开为一个数组交由max方法处理。
```
在早期的版本中，带*号的参数通常必须放在传统参数和默认参数的后面，但是现在的版本，它们之间的顺序可以变换，但是某些情况下还是会报错
```ruby
def foo2(*a, b = 10, c) 
   p [a, b, c]
end

foo2(1, 2, 3,4,5, 6 ,7) # 会报错

def foo3(b = 10, a, *c) 
   p [a, b, c]
end

foo3(1, 2, 3, 4, 5) # 会报错

def foo4(a, *b, c)
   p [a, b, c]
end

foo4(1, 2, 3, 4)

def foo5(a = 10, *b, c)
   p [a, b, c]
end

foo5(1, 2, 3, 4)
```
#### 用hash命名参数
```ruby
def sequences(args)
   a = args[:a] || 0
   b = args[:b] || 1
   c = args[:c] || 0
   ar = []
   a.times { |i|  ar << b * i + c}
   return ar
end

p sequences a: 10, b: 20, c: 30
p sequences({:a => 10, :b => 20, :c => 30})
p sequences(:a => 10, :b => 20, :c => 30)
ruby对于以上三种写法都支持，对于不加花括号的hash，称之为bare hash
```
#### block参数

```ruby
def sequences2(n, m ,c)
   ar = Array.new
   i = 0
   while i < n
      ar << yield(m, i, c)
      i += 1
   end
   return ar
end


value = sequences2(10, 20, 30) do |a, b, c|
   a * b + c
end
p value

def sequences3(n, m, c, &block)
   ar = []
   i = 0
   while i < n
      ar << block.call(n, m ,c)
      n += 1
   end
   return ar
end

value2 = sequences3(10, 20, 30) do |a, b, c|
   a * b + c
end
p value2

```
#### 使用&来调用方法
```ruby
a, b = [1, 2, 3], [1, 2, 3]
sum = a.inject { |total, var| total + var }
p b.inject(sum) { |total, var| total + var }

c, d = [1, 2, 3], [1, 2, 3]
sum = Proc.new { |total, vars| total + vars }
vs = c.inject(&sum)
p d.inject(vs, &sum)
```
调用方法的时候使用&，需要注意这个参数必须放在最后的位置，并且这个&通常放在一个proc object前面。
在Ruby 1.9中，Symbol类定义了to_proc方法，允许将符号以＆前缀并传递给迭代器。当这样传递符号时，假定它是方法的名称。 to_proc方法返回的proc对象调用其第一个参数的命名方法，并将其余的参数传递给该命名方法。规范的情况是这样的：给定一个字符串数组，创建一个这些字符串的新数组，转换为大写。 Symbol.to_proc使我们可以优雅地完成此操作，如下所示：
在方法调用的时候使用&放在Proc对象的前面，其实就等同于普通的带块方法的调用形式。在使用&的形式作为参数调用方法时，方法本身在定义的时候必须用&block的形式定义一个块参数，否则代码会报错。

示例一
```ruby
words = %w{hello world matz}
p words.map(&:upcase) #=> words.map { |str| str.upcase }


objs = Proc.new { |a| a + 2 }

def first(a)
   yield(a)
end

p first(1, &objs) # 如果方法内定义了yield，则方法也能正常运行
```
示例二：我们可以自己模拟一个map函数
```ruby

def my_map(a, &block)
   ary = []
   p block.class
   a.each do |var|
      ary << block.call(var)
   end
   return ary
end

ar = ["hello", "world", "matz"]
p my_map(ar, &:upcase)


```
#### Procs and lambdas
当我们以一个块参数定义方法的时候，是可以直接让方法返回一个proc对象的
```ruby
def makeproc(&p)
   p   # return Proc object
end

proc_object = makeproc { |x| x * x }
以这种方式创建的Proc object都是proc而不是lambda，所有Proc 对象都有call方法
```
创建Proc对象的三种方法
1. Proc.new
```ruby
def invoke(&p)
   p.call          
end

def invoke
  Proc.new.call
end 
```

2. Kernel里面有一个lambda方法，用来创建Proc对象，但是这个Proc对象是lambda，而不是proc。
在ruby 1.8里面有一个kernel.proc方法来创建lambda，但是在1.9版本被修复了，因此kernel.proc会返回一个proc而不再是lambda。
3. ->(x){ x * x }
第三种方法是lambda的另一种创建方式，小括号里面的是参数，而花括号里面的是代码块。在声明块变量的时候可以声明本地块变量，以防止被作用域内的同名变量覆盖。
在声明时，可以用分号来分隔，分号前面的为块变量，分号后面的为本地块变量。块变量可以使用默认参数的形式。
```ruby
->(x) { x + 8 }
->(x, y = 10) { x + y }
->(x, y; a, b, c) { ... } #声明本地块变量，这种情况下括号不能省略
->x, y, z { x + y + z } # 如果没有声明本地块变量，那么括号可以省略
->{ ... } #如果没有参数，则可以省略括号
def compose(f, g)
   ->(a){ f.call(g.call(a)) }
end
back= compose(->(a){ a + 1 }, ->(a){ a * a })
```

```ruby

def test_lambda
   yield
end

test_lambda &->{puts "hello"}
test_lambda { puts "hello" }
test_lambda &Proc.new{ puts "hello"}

def tsl(a, &b)
   p b.call(a)
end

objs = Proc.new { |a| a + 2 }
tsl(1, &objs)
```
#### lambda和proc的调用形式
```ruby

def foo(a, &block)
   block.call(a)
end

Proc里面定义了call方法来调用块对象，块对象不能像正常方法调用那样被调用。除了以call来调用外，ruby还加了另外几种方式来调用

block[a]
block.(a)

.()这种形式的操作符无法被重新定义，它只是call方法的语法糖，但凡是定义了call方法的对象都能以这种形式来调用，不局限于Proc对象
```
#### curry化
ruby在1.9版本加入了curry这个方法，这个方法可以返回被柯里化的lambda或者proc。当一个被柯里化的lambda或者proc以参数不足的形式调用的时候，它会返回一个应用了给定参数版本的proc或者lambda。它常用应用于函数式编程范例。
```ruby
one = lambda { |x, y|  x + y }
two = one.curry[3]
p two[20] + two[30]

three = Proc.new { |x, y, z| x + y + z }
p three.curry[1][2][4]
p three.curry[2, 3][6, 7, 8]
p three.curry[2, 3][3]
p three.curry[2, 3, 3]
```
#### proc参数个数
```ruby
four = Proc.new {|x, y| x + y }
p four.arity # 2

four1 = Proc.new {|x, *y| x + y }
p ~four1.arity # 1

four2 = Proc.new {|*x| x }
p ~four2.arity # 0

four3 = Proc.new { |x = 10, *y| x + y }
p ~four3.arity # 0

four4 = Proc.new { }
p four4.arity # 0
```
proc对象拥有一个arity的方法，这个方法可以返回这个proc对象需要多少参数，如果proc的参数前缀带*号，则该方法会返回一个负值，通过~操作符可以将这个结果转换来获取实际需要的参数值。
具体使用见上面例子。

#### Proc对象相等性
在比较两个Proc是否相等的时候，并不是它们的代码块相同，参数相同就表示这两个Proc对象是相等的。另外Proc对象有一个==方法来判断两者是否相等。
```ruby
first_1 = Proc.new { |x| x }
first_2 = Proc.new { |x| x }
p first_1==first_2 # false

second_1 = Proc.new { |x| x }
second_2 = second_1.dup
p second_1==second_2 # false
p second_1.__id__ == second_2.__id__  # false
```
#### proc与lambda之间的区别
proc是块的对象形式，而lambd的行为则更像是方法。调用一个proc对象更像是yielding一个块，而调用lambda更像是调用方法。在ruby1.9多了一个实例方法来判断一个proc对象到底是proc还是lambda。
这个方式是lambda?
```ruby
p Proc.new {}.lambda? # false
p lambda {}.lambda? # true
```
proc和lambda的区别还体现在控制语句
1. return语句
```ruby
def test
   proc = Proc.new { puts "hello world"; return }
   proc.call
   puts "hello god" # no execute
end

test
在proc对象里面使用return语句，它会直接从方法中返回，而不是仅仅只从块里面返回，上面的例子中，一旦proc对象返回，则整个方法也被返回，因此最后一句代码不会被执行。
再看下面一个例子：
def procBulider
   proc = Proc.new { puts "hello world"; return }
   return proc
end

test

def test2
   p = test
   p.call
   puts "hello world"
end

test2

当我们从一个proc构造方法返回一个proc对象，然后在test2方法中调用时会产生一个错误localjumpError, 因为return在块里面不但会从块返回，还会从方法中返回，因此一旦在某个调用proc对象的位置返回，而这个位置是在另外一个方法中，则会报错。
修复这个问题方法很简单，就是把return移除，如果非要使用return语句，则可以使用另一个方式，用lambda来代替，因为lambda的行为更像方法，因此在它里面调用return只会从块里面返回，而不会整个方法中返回。

def lambdaBulider
   proc = ->{ puts "hello world"; return }
   return proc
end

def test3
   p = lambdaBulider
   p.call
   puts "hello world"
end

test3 # 能够正常执行
```
2. break语句
```ruby
def test5
   p = Proc.new { break }
   p.call
   puts 'test'
end
test5 # localjumpError
在proc里面使用break依然会报错，因为break也会提前从方法中返回，因此会产生异常。

def iterator(&block)
   puts "enter iterator"
   block.call
   puts "exiting iterator"
end

def test6
   iterator { break }
end
test6 # 正常运行，但是不会打印方法最后一个表达式，因为break让方法提前退出了

在lambda里面使用break没有任何意义，它的作用就像return一样，让方法返回

def test7
   p = lambda { break }
   p.call
   puts "hello world"
end

test7 # 程序正常运行，它仅仅只是终止了lamdba，而不会终止调用这个lambda的方法

```
3. next语句
```ruby
def test8
   p = Proc.new { next 10 }
   p p.call
   puts "hello world!!"
end

test8

next语句在block，proc或者lambda的作用一样，它会从一个块返回，但是不会从方法返回，并且如果next后面跟上一个表达式的话，它会返回表达式的值。
```
4. retry语句
retry语句不允许在块中使用，无论如何调用它都会引起一个localjumpError错误
5. redo语句
redo语句在proc和lambda里面的效果一样，它会将控制权重新交给块，然后块会被重复执行。
6. raise语句
raise语句会在块里面抛出一个异常，它总是会繁衍到调用栈，繁衍到调用这个块的方法里面。
```ruby
def test8
   p = Proc.new { raise "not good" }
   p p.call
   puts "hello world!!"
end

test8 # raise not good, 方法被异常终止
```
#### proc和lambda在传参时的差异
在调用块和调用方法在传递参数时是由区别的，因为它们的调用语义不同，块在传递参数时是遵循的yield语义，而方法遵循的是invocation语义。
由于lambda是方法，因此它在参数赋值时遵循的是invocation语义，而块则是yield语义，因此在对lambda参数赋值时，必须严格遵循方法参数赋值的要求，而块则没有那样的严格限制。
当我们传递的参数不足时，块会给变量赋值nil，而当参数超出参数变量时，则它会把多余的值丢掉，当参数以数组形式传递时，它会把数组展开，把值依次赋值给参数变量，多余的值。
```ruby
pp = Proc.new { |x, y, z| p [x, y, z] }
pp.call(1, 2, 3)
pp.call(1, 2, 3, 4, 5)
pp.call([1, 2, 3]) # discard extra param

pp2 = Proc.new{ |x| p x }
pp2.call([1, 2, 3]) # return array [1, 2, 3]
```
#### closure闭包
闭包包含了两层含义：
1. 包含了可以执行的代码块
2. 变量绑定
```ruby
def multiplier(n)
   lambda { |var| var * n}
end

p = multiplier(2)
p p.call(10)
```
闭包里面的变量不是静态绑定的，而是动态绑定，因为变量的使用并不是在lambda或proc创建的时候，而是在它们被调用或执行的时候才会被使用。
```ruby

def two_closures(initialize_value)
   setter = lambda { |x| initialize_value = x }
   getter = lambda { initialize_value }
   return setter, getter
end

setValue, getValue = two_closures(200)
p setValue.call(888)
p getValue.call

def multipliers(*arg)
   arg.map { |x|  lambda { |y| y * x } }
end

multipliers(2, 3).each do |proc|
   p  proc.call(2)
end
```
#### bindings
Proc定义了一个binding方法，对proc或lambda调用这个方法会返回Binding对象。
binding不单纯是指变量和变量值得映射，它还包括其它方法执行所需要的其他信息。Binding对象本身没有自己的方法，但是它可以作为全局eval函数的第二个参数，为eval ruby代码的字符串形式提供一个上下文。
在ruby1.9版本里为Binding对象提供了一个eval方法。使用的时候更倾向于使用eval方法而不是eval函数。
```ruby
def multipliers(arg)
   lambda { |data| data.collect { |var| var * arg } }
end

p = multipliers(2)
p p.call([1, 2, 3])

p.binding.eval('arg = 3')
p p.call([1, 2, 3])

eval("n = 4", p.binding)
p p.call([1, 2, 3])
```
绑定不仅是闭包的一个功能。Kernel.bind方法返回一个绑定对象，该对象表示在您调用它的某个位置的有效绑定。

#### Method objects
方法也能够作为Method类的对象来被调用，但是对象化的调用效率比直接调用低，Object类定义了一个method方法，传递一个字符串或者符号形式的方法名，返回一个接收者的命名方法的对象，如果方法不存在，则抛出一个异常。
除了使用method方法来获取一个Method对象，在ruby1.9中还可以使用public_method方法来获取一个Method对象，它像method方法一样工作，但是它会忽略private method和protected method。
Method类并不是Proc类的子类，它和Proc类类似，也拥有call方法，并且method对象的行为和Proc的行为类似，除了call方法，也可以使用.()的形式调用，并且参数没有限制。
```ruby
mth = 0.method(:succ)
p  mth.class.superclass
p mth.call

mth2 = 0.public_method(:succ)
p mth2.call
```
通过method对象的形式来调用method并不会改变method的invocation语义，它依然和lambda一样遵循invocation语义而不是yield语义。因为break，return语句也和在方法调用中的作用一样。
当需要一个真正的Proc对象的时候，可以让Method对象调用to_proc方法来将它转换为一个Proc对象。因此&这种形式的调用也适合于Method对象。
```ruby
def foo(var)
   var * var
end

mth3 = method(:foo)
p [1, 2, 3].map(&mth3)
```
#### define_method
除了获取一个代表方法的Method对象并将其转换为Proc的方法对象外，我们还可以使用另一种方式。define_method方法（模块）期望符号作为参数，并使用关联的块作为方法体创建具有该名称的方法。也可以传递Proc或方法对象作为第二个参数，而不是使用块。
```ruby
define_method :world, Proc.new { puts "hello world" }
define_method :hello, ->{ puts "i am lambda" }
define_method :go do |x, y|
   return x + y
end

p go(1, 2)
```
Method对象和Proc对象之间的一个重要区别是，Method对象不是闭包。Ruby的方法旨在完全独立，并且它们永远不会访问自身范围之外的局部变量。因此，方法对象保留的唯一绑定是self的值（要调用方法的对象）。
#### UboundMethod
除了Method类之外，Ruby还定义了一个未绑定方法类。顾名思义，UnboundMethod对象代表一个方法，而不绑定到要调用它的对象。由于未绑定方法未绑定，因此无法调用它，并且UnboundMethod类不定义call或\[]方法。
获取一个UboundMethod对象可以使用instance_method或者public_instance_method来获取。但是使用后者的时候，它会忽略private方法和protected方法
为了调用未绑定方法，必须首先使用bind方法将其绑定到对象。
```ruby
unound_add = Integer.instance_method(:+)  # bind方法会返回一个Method对象，因此可以调用call方法
p unound_add.bind(2).call(2)

除了上面通过上面的方法来获取一个Unbound对象，还可以使用unbind方法来获取.

unound_add = Integer.instance_method(:+)
p unound_add.bind(2).call(2)
plus_2 = unound_add.bind(3)  # 获取一个绑定的Method对象, 将方法绑定到对象2上面
unbound_plus = plus_2.unbind.bind(3) # 用unbind方法获取一个Unbound对象，然后再调用bind方法获取Method对象
p unbound_plus.call(3) 
```
#### 函数式编程
```ruby
ary = [[1, 2], [3, 4]]
sum = Proc.new { |x, y| x + y }

class << sum
   def apply(enum)
      enum.map &self
   end
   alias | apply
end
p sum|ary

module Functional
   def apply(enum)
      enum.map &self
   end
   alias | apply

   def reduce(enum)
      enum.inject &self
   end
   alias <=  reduce
end

Proc.include(Functional) # 让Proc加载模块Functional
p Proc.new {|x, y| x + y } <= [1, 2, 3, 4, 5]
p Proc.new { |x, y| x + y } | [[1, 2], [3, 4]]


module FunctionalTwo                               # create a new module which will be included into the Method and Proc class    
def compose(f)                                                                                                                    
   if self.respond_to?(:arity) && self.arity == 1  # check the method's required numbers of params                                
      lambda { |*args| self[f[*args]]  }            # lambda { |*args| self.( f.(*args) ) }                                       
   else                                                                                                                           
      lambda { |*args| self[*f[*args]] }           # lambda { |*args| self.( *f.(*args) ) }                                       
   end                                                                                                                            
end                                                                                                                               
alias * compose                                    # create a alias of compose                                                    
                                                                                                                                  
end                                                                                                                               
                                                                                                                                  
def polar(x, y)                                                                                                                   
   [Math.hypot(x, y), Math.atan2(y, x)]                                                                                           
end                                                                                                                               
                                                                                                                                  
def cartesian(magnitute, angle)                                                                                                   
   [magnitute*Math.cos(angle), magnitute*Math.sin(angle)]                                                                         
end                                                                                                                               
                                                                                                                                  
[Proc, Method].each { |cla| cla.include(FunctionalTwo)}                                                                           
p, c = method(:polar), method(:cartesian)                                                                                         
                                                                                                                                  
p ( c * p )[1, 2]    # [1.0000000000000002, 2.0]                                                                                  
```

#### Memoizing functions
缓存函数调用的结果，当一个函数被传入参数以后总是返回相同的值，那么可以考虑使用缓存来缓存函数调用的结果。
在上面定义的模块中继续定义一个memoize方法
```ruby
def memoize
      cache = {}
      lambda do |*arg|
         unless cache.has_key?(arg)
            cache[arg] = self[*arg]
         end
         cache[arg]
      end
   end
   
fac = lambda { |x| return 1 if x == 0; x * fac[x - 1]; }.memoize
p fac.call(4)
```
#### Symbols, Procs, Methods
符号、方法和 Proc 类之间存在密切的关系。我们已经看到方法，该方法采用Symbol参数并返回方法对象。Ruby 1.9 向 Symbol 类添加了一个有用的to_proc方法。此方法允许使用&前缀符号，并作为块传递给迭代器。该符号会被假定为方法的名称。调用使用to_proc方法创建的Proc时，它将调用其第一个参数的命名方法，将任何剩余的参数传递给该命名方法。以下是如何使用它：
```ruby

class Symbol

   def to_proc
      lambda { |receiver, *args| receiver.send(self, *args) }
   end

   alias old_to_proc to_proc

   def to_proc
      lambda { |receiver, *args| receiver.method(self)[*args] }
   end
end

def kinbon(x, y)
   return x + y
end

puts :kinbon.to_proc.call('kinbon', 2, 3)
puts :kinbon.old_to_proc.call('kinbon', 2, 3)
```

我们可以在不同的类中定义新的方法来实现一些特殊的作用
```ruby

class Module
   alias_method :[], :instance_method

   def []=(sym, f) # 定义一个[]=方法来定义一个新的实例方法
      self.instance_eval { define_method(sym, f) }
   end
end

class UnboundMethod
   alias_method :[], :bind
end

so1 = String[:reverse]
sb1 = so1["hello"]
p sb1.call


String[:new_reverse]= ->{ self.reverse }

so3 = String[:new_reverse]["hello world!"]
p so3.call


class Symbol

   def [](obj)
      obj.method(self)
   end

   def []=(o, f)
      sym = self
      engieclass = (class << o; self end)
      engieclass.instance_eval { define_method(sym, f) }
   end

end

p :new[Array]
:eat[Array] = lambda { puts "good" }
Array.eat
```
# Class和Module
像大多数Ruby构造一样，类定义以end定界。除了定义新类之外，class关键字还会创建一个新常量来引用该类。类名和常量名相同，因此所有类名必须以大写字母开头。
像Ruby中的大多数语句一样，class是一个表达式。类表达式的值是类主体中最后一个表达式的值。通常，类中的最后一个表达式是定义方法的def语句。 def语句的值始终为nil。
#### 实例变量
ruby中的对象数据封装很严格，必须用实例方法来访问对象的属性，因此我们可以在类里面定义方法来获取对象的属性，当我们定义setter的时候，我们可以定义x=(var)这样的方法，但是不能隐示调用。
假如我们省略self，直接以这种形式x=20来调用的话，并不起作用，这会重新声明一个本地变量。
#### attr_reader, atter_writer, attr_accessor
这三个方法是由Module定义的，而Class类属于Module的子类，因此可以从Module里面继承这些方法。
#### 在自定义的类中定义iterator迭代器
通常我们可以在自己定义的类中定义自己的each方法，这样我们就可以使用Enumerator模块了，因为在ruby的Enumerator模块里面定义了许多的each的关联项，我们可以直接在自己定义的类中mix-in这个模块，这样我们就可以调用模块中的方法。
```ruby
class Pural

   include Enumerable
   attr_accessor :x, :y
   def initialize(x, y)
      @x = x
      @y = y
   end

   def [](index)
      case index
      when 0; x
      when 1; y
      end
   end
   
   def each
      yield @x
      yield @y
   end
   
end

obj = Pural.new(1, 2)
obj.each { |var| puts var }

p obj.all? { |var| var > 0 } #=> true
p obj.any? { |var| var > 1 } #=> true

```
#### 对象的相等性
我们之前定义的+运算符根本不进行类型检查：它可以与带有返回数字的x和y方法的任何参数对象一起使用。这种==方法的实现方式有所不同；而不是允许鸭子输入，它要求参数是一个Point。这是一种实现选择。上面==的实现选择定义相等性，以使对象不能等于Pural，除非它本身是Pural。实现可能比这更严格或更自由。上面的实现使用is_a？谓词测试参数的类。这允许Pural的子类的实例等于Pural。更严格的实现将使用instance_of？禁止子类实例。同样，上面的实现使用==比较X和Y的值。对于数字，==运算符允许类型转换，这意味着点（1,1）等于（1.0,1.0）。但是更严格的等式定义可以使用eql？比较值。更平等的平等定义将支持鸭子类型化。
我们在定义==方法时，方法不应该在某个对象没有定义某个方法时抛出异常，而是应该让方法返回false。
```ruby
def ==(other)
   @x == other.x && @y == other.y
   rescue
   false   
end
```
默认情况下，eql？方法与==运算符一样，测试对象标识而不是对象内容的相等性。我们经常要eql工作就像==运算符一样，我们可以使用别名实现此目的：
```ruby
class Pural
  alias eql?  ==
end
```
另一方面，我们可能需要eql有两个原因eql与==不同。首先，某些类定义eql？执行比==更严格的比较。例如，在Numeric及其子类中，==允许类型转换而eql不会。如果我们认为Pural类的用户可能希望能够以两种不同的方式比较实例，那么我们可能会遵循此操作。
```ruby
def eql?(other)
   if other.instance_of? Pural
      @x.eql?(other.x) && @y.eql?(other.y)
   else
      false
   end
end
```
顺便说一句，请注意，对于实现任意对象的集合（集合、列表、树）的任何类，这是正确的方法。==运算符应使用它们的==操作符来比较集合的成员而eql应该使用eql?来比较集合的成员。实现一个不同于==操作符的eql?方法的原因是如果您希望类的实例在用作哈希键时行为更加特别。哈希类使用eql比较哈希键（但不是值）。如果你让eql未定义，然后哈希将通过对象的身份来比较类的实例。这意味着，如果将值与键p相关联，则当你检索这个值得时候，只能使用与准确的对象p来进行检索，而对象例如q是无法检索的即使对象p==对象q.
#### 排序
要定义Pural对象的排序，我们只需要定义<=>运算符,并include Comparable模块。这样做将基于我们定义的通用<=>运算符mix-in相等和相关的操作符，应将self与传递的对象进行比较。如果self小于该对象，则应返回-1。如果两个对象相等，则应返回 0。如果self大于参数对象，则方法应返回1。如果参数对象和self的类型是无法比较的，则该方法应返回nil。
这里值得注意的是，Enumerable模块定义了几种方法，如sort、min和max，这些方法仅在枚举的对象定义<=>运算符时才起作用。

#### Mutable object
通常我们会在类里面定义setter和getter来更改和获取实例变量的值，我们如果想要定义一个会改变实例变量值得方法，通常会在方法名后面加一个感叹号来提示。如果想定义一个非改变实例变量值得仿佛，则可以把 感叹号去掉，保留相同方法名。
我们也可以将它们组合起来使用。
```ruby
def add(p)
   q = self.dup
   q.add!(p)
end
```
#### Quick and Easy Mutable Classes
我们可以通过Struct类来快速的创建一个新的类，并且在创建的时候，可以为它指定访问器。我们也可以使用Class.new的形式来创建一个新的匿名类，通常我们将匿名类赋值给一个变量，这样变量名就是新创建的类名。
```ruby
使用Struct来创建一个新的子类
Struct.new("Mae", :x, :y) # 创建一个嵌套的子类，并且指定存取器
one = Struct::Mae.new(11, 22)
p one.x, one.y

Foo = Struct.new(:x, :y)
two = Foo.new(1, 2)
p [two.x, two.y]
```
```ruby
使用Class来创建一个新的匿名子类，并将其赋值给变量，变量名则为该新类的名
Foo2 = Class.new do
   def greeting
      puts "hello"
   end
end

Foo2.new.greeting
```
当使用以上方法创建新的类时，其实是创建了它们的子类，因此我们可以使用新创建的类继续创建新类的子类，以此类推
```ruby
Foo3 = Foo2.new
p Foo3.class.to_s #=> Foo3
p Foo3.superclass.to_s #=> Foo2 # Foo2的子类
```
结构还定义了数组和哈希式索引风格的 \[] 和 \[ ]= 运算符，甚至提供了each和each_pair迭代器提供循环访问，以循环遍历Struct类的实例中的值。
```ruby
Foo = Struct.new(:x, :y)
p Struct.superclass
two = Foo.new(1, 2)
p [two.x, two.y]

p two[:x] #=> 1
p two[:y] #=> 2


two.each do |a|
   p a
end   #=> 1
          2

two.each_pair do |a, b|
   p "#{a} => #{b}"  
end   #=>    "x => 1"
             "y => 2"
```
基于Struct的类具有能够运行的==运算符，可用作哈希键（尽管由于它们是可变的，因此需要谨慎），甚至定义了有用的to_s方法。
```ruby
Foo = Struct.new(:x, :y)
p Struct.superclass
one = Foo.new(1, 2)
two = Foo.new(1, 2)
one == two #=> true
ha = { one => 1 }
p ha.has_key?(two) #=> true
```
前面我们在用Struct创建一个的子类的时候，并没有定义子类自己特有的方法，在ruby中，类是开放的，可以随时随地根据需要添加新的方法进去。
```ruby
WeChat = Struct.new(:x, :y)
class WeChat

   def add!(other)
      self.x += other.x
      self.y += other.y
      self
   rescue
      false
   end

   def add(other)
      cp_obj = self.dup
      cp_obj.x += other.x
      cp_obj.y += other.y
      cp_obj
   rescue
      false
   end

   include Comparable
   def <=>(other)
      self.x**2 + self.y**2 <=> other.x**2 + other.y**2
   end
end

obj_wechat = WeChat.new(1, 2)
obj_wechat2 = WeChat.new(3, 4)
p obj_wechat.add!(obj_wechat2) #=> 更改原来的对象的值
p obj_wechat.add(obj_wechat2)  #=> 不更改原来的对象，而是返回一个新的对象
p [obj_wechat.x, obj_wechat.y]
p obj_wechat <=> obj_wechat2
```
上面定义的是可以更改的类，而我们可以使用undef来使一个类转为不可更改的类
```ruby
WeChat = Struct.new(:x, :y)
class WeChat
   undef x=, y=, \[]=
end
将setter一律undefined掉
```
我们可以在类中定义一个类方法。有四种方式来定义类方法：
```ruby
Cloud = Struct.new(:x, :y)
class << Cloud
   def sum(*arg)
      x = y = 0
      arg.each do |var|
         x += var.x
         y += var.y
      end
      return Cloud.new(x, y)
   end
end

class Cloud

   def self.sum(*arg)
      x = y = 0
      arg.each do |var|
         x += var.x
         y += var.y
      end
      return Cloud.new(x, y)
   end
end

class Cloud

   def Cloud.sum(*arg)
      x = y = 0
      arg.each do |var|
         x += var.x
         y += var.y
      end
      return Cloud.new(x, y)
   end
end

class Cloud
   class << self
      def sum(*arg)
         x = y = 0
         arg.each do |var|
            x += var.x
            y += var.y
         end
         return Cloud.new(x, y)
      end
   end
end
```

#### CONSTANT常量
类里面可以定义以大写字母命名的常量，我们可以在类中定义这些常量，也可以在类外部定义这些常量。
```ruby
class Cloud
  ONE = 10
  TWO = 20
end

Cloud::ONE = 110
Cloud::TWO = 220
p Cloud::ONE
p Cloud::TWO
```
