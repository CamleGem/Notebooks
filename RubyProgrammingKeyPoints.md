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
Struct还定义了数组和哈希式索引风格的 \[] 和 \[ ]= 运算符，甚至提供了each和each_pair迭代器提供循环访问，以循环遍历Struct类的实例中的值。
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
#### 类变量
类变量是全局的，它在定义它的类中都是全局可见的，任意不同的对象都能访问类变量。
```ruby
class Kobe
   @@count = 0
   attr_accessor :x, :y
   def initialize(x, y)
      @x = x
      @y = y

      @@count += 1

   end

   def self.get_class_var
      @@count
   end
end

one = Kobe.new(1, 2)
two = Kobe.new(2,3)
three = Kobe.new(1, 2)
p Kobe.get_class_var  #=> 3，创建了三个对象
```
#### 类实例变量
类是对象，可以像其他对象一样具有实例变量。类的实例变量（通常称为类实例变量）与类变量不同。但是它们足够相似，因此通常可以代替类变量来使用它们。在类定义内但在实例方法定义外使用的实例变量是类实例变量。与类变量一样，类实例变量与类关联，而不是与类的任何特定实例关联。类实例变量的一个缺点是它们不能像类变量那样在实例方法中使用。另一个缺点是可能将它们与普通实例变量混淆。没有独特的标点符号前缀，可能更难记住变量是与实例还是与类对象相关联.类实例变量相对于类变量的最重要优点之一是，在对现有类进行子类化时，类变量的行为令人困惑。
因为类实例变量是类对象的实例变量，因此我们也可以为它定义存取器，可以通过以下方式来定义。
```ruby
class DoubleVar
   @n = 0
   @total_one = 0
   @total_two = 0

   def initialize(x, y) # 该方法是实例方法，因此无法在里面使用类实例变量
      @x, @y = x, y
   end

   def self.new(x, y)
      @n += 1
      @total_one += x
      @total_two += y
      super
   end

   def self.report
      puts "Number of objects created: #@n"
      puts "Average X coordinate: #{@total_one.to_f/@n}"
      puts "Average Y coordinate: #{@total_two.to_f/@n}"
   end

   class << self  # 为类对象定义存取器
      attr_accessor :n, :total_one, :total_two
   end
end
obj = DoubleVar.new(1, 2)
obj2 = DoubleVar.new(2, 3)
DoubleVar.report
DoubleVar.report

output:
Number of objects created: 2
Average X coordinate: 1.5
Average Y coordinate: 2.5
```
#### Method Visibility：protected， private， public
实例方法可以有三种状态，分别是protected，private和public，默认情况下，实例方法是public方法，除非明确声明了关键字。有一个例外就是initialize方法是隐示私有方法。另一个例外是在类定义之外声明的任何“全局”方法，这些方法被定义为Object的private实例方法。可以从任何地方调用public方法，使用它没有限制。
1. private：方法是类的内部实现，它只能由该类的其他实例方法（或我们将在后面看到的其子类）调用。私有方法是在self上隐式调用的，而不能在对象上显式调用。如果m是私有方法，则必须以函数风格将其调用为m。你不能写o.m甚至self.m。
2. protected：受保护的方法就像私有方法一样，因为只能从类或其子类的实现中调用它。它与私有方法的不同之处在于，它可以在类的任何实例上显式调用，并且不限于对self的隐式调用。例如，可以使用一种受保护的方法来定义一个访问器，该访问器允许一个类的实例彼此共享内部状态，但不允许该类的用户访问该状态。受保护的方法是最不常用的定义，也是最难理解的方法。关于何时可以调用protected方法的规则可以描述如下：由类C定义的protected方法可以由对象p中的方法在对象o上被调用，当且仅当o和p的类都是C类的子类，或等于C类的子类。
方法可见性使用名为public，private和protected的三种方法声明。这些是Module类的实例方法。self指被定义的类。因此，公共的，私有的和受保护的可以像语言的关键字一样被裸露使用。但是，实际上，它们是对self的方法调用。有两种方法可以调用这些方法。不带参数的情况下，它们指定所有后续的方法定义都将具有指定的可见性。一个类可能会这样使用它们：
```ruby
第一种方式
class Foo
   # public method goes here

   private
   # private method goes here

   protected

   # protected method goes here
end
```
也可以使用一个或多个方法的名称来调用方法。参数可以是符号或者字符串形式。像这样调用时，它们会更改命名方法的可见性。在这种用法中，可见性声明必须位于方法定义之后。一种方法是在类结束时立即声明所有私有和受保护的方法。另一种方法是在定义每个私有或受保护方法后立即声明其可见性。例如，这里是带有私有实用程序方法和受保护的访问器方法的类：
```ruby
第二种方式
class  Widget

   def x
      @x
   end

   private :x

   def utility_method
      nil
   end

   protected :utility_method
end


第三种方式
class  Widget

   def x
      @x
   end
   
   def utility_method
      nil
   end

   private :x
   protected :utility_method
end
```
请记住，public，private和protected仅适用于Ruby中的方法。实例变量和类变量被封装并有效地私有，常量实际上是公开的。没有办法使实例变量可以从类外部访问（当然，除了定义访问器方法外）。而且没有办法定义外部无法访问的常量。
有时，指定一个private类方法是很有用的。例如，如果你的类定义了工厂方法，则可能需要将new方法设为私有。为此，请使用private_class_method方法，将一个或多个方法名称指定为符号：
```ruby
private_class_method :new
```
你可以使用public_class_method将private类方法再次公开。不能在没有参数的情况下调用这两种方法，而public，protected和private的方式在没有参数的情况下是能够依然被调用的。
从设计上来说，Ruby是一种非常开放的语言。指定某些方法是私有的和受保护的功能可以鼓励良好的编程风格，并防止无意中使用了不属于类的公共API的方法。但是，重要的是要了解，Ruby的元编程功能使调用private和protected方法甚至访问封装的实例变量变得很简单。调用之前代码中定义的private实例方法，我们可以使用send方法，也可以使用instance_eval方在对象的上下文中评估块：
```ruby
obj = Widget.new
obj.send :utility_method               # 运行私有方法，与第三种种方式等价
obj.instance_eval { utility_method }   # 运行私有方法
obj.instance_eval { @x }               # 读取实例变量值
```
如果你想按名称调用方法，但又不想无意中调用您不知道的私有方法，则可以（在Ruby 1.9中）使用public_send而不是send。它的工作方式类似于send，但是在使用receiver调用时不会调用私有方法。

#### 子类化和继承
包括Ruby在内的大多数面向对象的编程语言都提供了一种子类化机制，该机制使我们能够创建其行为基于但已存在的类修改而来的新类。我们将从基本术语的定义开始讨论子类。如果您使用Java，C ++或类似语言进行编程，则您可能已经熟悉这些术语。当我们定义一个类时，我们可以指定它扩展或继承自另一个类，称为超类。如果定义扩展了Gem类的Ruby类，则说Ruby是Gem的子类，而Gem是Ruby的超类。如果在定义类时未指定超类，则该类将隐式扩展Object。一个类可以具有任意数量的子类，并且每个类都有一个唯一的超类，但Object除外。
类可能具有多个子类，但只有一个超类，这意味着它们可以以树结构排列，我们称其为Ruby类层次结构。 Object类是此层次结构的根，每个类都直接或间接继承自该类。类的后代是该类的子类，再加上子类的子类，依此类推。类的祖先是超类，再加上超类的超类，依此类推直至Object。
在Ruby 1.9中，Object不再是类层次结构的根。一个名为BasicObject的新类可满足此目的，而Object是BasicObject的子类。 BasicObject是一个非常简单的类，几乎没有自己的方法，它可用作委派包装器类的超类。
在Ruby 1.9中创建类时，除非显式指定了超类，否则仍会扩展Object，并且大多数程序员将永远不需要使用或扩展BasicObject。诸如==，等于eql？，instance_eval和__send__之类的方法通常被视为Object方法，即使它们实际上是由BasicObject定义的。
在本章的前面，我们了解了如何使用Struct.new自动生成简单的类。也可以对基于Struct的类进行子类化，以便可以添加除自动生成的方法以外的方法：
扩展类的语法很简单。只需在类声明中添加<字符和超类的名称。例如：
```ruby
class Point3D < Point ＃定义Point3D类为Point的子类

end
```
```ruby
class Point3D < Struct.new('Point3D', :x, :y, :z)
   # superclass struct gives us accessor methods, ==, to_s, etc.
   # add point-specific methods here
end
```
#### overriding method
定义新类时，我们通过定义新方法向其添加新行为。同样重要的是，我们可以通过重新定义继承的方法来自定义类的继承行为。
关于面向对象的编程和子类化要理解的重要事情之一是，在调用方法时，将动态地查找它们，以便找到方法的适当定义或重新定义。也就是说，方法调用在解析时不是静态绑定的，而是在执行时查找的。
如果你以前进行过面向对象的编程，则该程序的行为可能很明显而且很琐碎。但是，如果你不熟悉它，那可能会难以理解。我们称从WorldGreeter继承的greet方法。此greet方法调用Greeting方法。在定义问候语时，greeting方法返回“ Hello”。但是，我们将WorldGreeter子类化，而我们调用greet的对象具有greet的新定义。当我们调用greet时，Ruby为被调用的对象查找该方法的适当定义，最后得到一个适当的西班牙语问候语而不是英语问候语。这种对方法的适当定义的运行时查找称为方法名称解析.
注意，定义一个抽象类来调用某些未定义的“抽象”方法也是完全合理的，这些方法留给子类来定义。抽象的反面是具体的。如果扩展抽象类的类定义了其祖先的所有抽象方法，则它是具体的。
```ruby
class WorldGreeter
   def greet
      puts "#{greeting} #{who}"
   end

   def greeting
      "Hello"
   end

   def who
      "World"
   end
end

class SpanishWorldGreeter < WorldGreeter
   def greet
      "Hola"
   end
end
```
定义一个抽象类
```ruby
class AbstractGreeter
   def greet
      puts "#{greeting} #{who}"
   end
end

class WorldGreeter < AbstractGreeter
   def greeting
      "Hello"
   end

   def who
      "World"
   end
end

WorldGreeter.new.greet
```
#### 覆盖私有方法
私有方法不能从定义它们的类外部调用。但是它们是由子类继承的。这意味着子类可以调用它们并可以覆盖它们。子类化不是自己编写的类时，一定得当心。类通常使用私有方法作为内部帮助器方法。它们不是该类的公共API的一部分，因此不希望看到。如果你还没有阅读该类的源代码，那么你甚至都不知道其定义供自己使用的私有方法的名称。如果您碰巧在子类中定义了一个与父类中的私有方法同名的方法（无论其可见性），那么你将无意中覆盖了父类的内部实用程序方法，这几乎肯定会导致意外的行为。
结论是，在Ruby中，仅当你熟悉超类的实现时才应子类化。如果只想依赖类的公共API而不是类的实现，则应该通过封装来扩展该类的功能并且委派给该类，而不是从该类继承。
#### 通过链来扩展行为
有时，当我们重写某个方法时，我们不想完全替换它，而只是想通过添加一些新代码来增强其行为。为此，我们需要一种从重写方法中调用重写方法的方法。这称为链接，它是通​​过关键字super完成的。
super的工作方式类似于特殊的方法调用：它在当前类的超类中调用与当前名称相同的方法。 （请注意，超类不必自己定义该方法，它可以从其祖先之一继承该方法。）你可以像为普通方法调用一样为super指定参数。方法链接的一个常见且重要的地方是类的initialize方法。
```ruby
class Car
   def initialize(x, y)
      @x, @y = x, y
   end
end

class Audi < Car
   def initialize(x, y, z)
      super(x, y)  # 通过继承父类的initialize方法来定义实例变量，因为定义@x,@y的代码是一样的，这样我们只需要定义额外的一个实例变量就行，这样就通过父类的方法扩展了我们的方法，而不需要完全重写。
      @z = z
   end
end

p Audi.new(1, 2, 3)
```
如果你将super用作裸露的关键字-没有参数且没有括号-那么所有传递给当前方法的参数会传递给超类方法，但是注意，传递给超类方法的是当前方法参数的当前值。如果该方法已修改其参数变量中的值，则将修改后的值传递给超类方法的调用。
```ruby
class Car
   def initialize(x, y)
      @x, @y = x, y
   end
end

class Audi < Car
   def initialize(x, y, z)
      x = 1 + x # 修改参数变量的值
      y = 2 + y # 修改参数变量的值
      super(x, y) # 将修改后的参数变量的值传递给父类方法
      @z = z
   end
end

p Audi.new(1, 2, 3)
```
与常规方法调用一样，super参数周围的括号是可选的。但是，由于裸super具有特殊含义，如果要从本身具有一个或多个参数的方法中传递零参数，则必须显式使用一对空括号。
#### 继承类方法
类方法可以像实例方法一样被继承和覆盖。假设有Point和Point3D两个父子类，如果我们的Point类定义了一个类方法sum，则我们的Point3D子类将继承该方法。也就是说，如果Point3D没有定义自己的名为sum的类方法，则表达式Point3D.sum调用与表达式Point.sum相同的方法。
从风格上讲，最好通过定义了类方法的类对象来调用类方法。看到表达式Point3D.sum的代码维护人员会去Point3D类中寻找sum方法的定义，他可能很难在Point类中找到它。在使用显式接收器调用类方法时，应避免依赖继承, 始终通过定义它的类来调用类方法。
在类方法的主体内，你可以在没有显式receiver的情况下调用该类的其他类方法-它们是在self上隐式调用的，并且类方法中self的值就是它所在的类的名字。在类方法的内部，类方法的继承很有用：即使你通过超类定义了类方法，它也允许你隐式调用类方法。最后，请注意，类方法可以使用super，就像实例方法可以调用超类中的同名方法一样。
```ruby

class Audi < Car
   def initialize(x, y, z)
      x = 1 + x # 修改参数变量的值
      y = 2 + y # 修改参数变量的值
      super(x, y) # 将修改后的参数变量的值传递给父类方法
      @z = z
   end

   def self.feeling
      super      #继承父类中的feeling方法
   end
end
```
#### 继承和实例变量
因为此代码的行为符合预期，所以你可能会想说这些变量是继承的。但是，Ruby并不是这样工作的。所有Ruby对象都有一组实例变量。这些不是由对象的类定义的，它们是在为其分配值时简单创建的。因为实例变量不是由类定义的，所以它们与子类化和继承机制无关。
在前面提到的代码中，Audi定义了一个initialize方法，该方法链接到其超类的initialize方法。链接方法将值分配给变量@x和@y，这使这些变量在Audi的特定实例中存在。
来自Java或来自其他强类型语言的编程人员可能会发现，这需要花些时间来适应，而Java或其他强类型语言中的一个类为其实例定义了一组字段。不过，实际上，这非常简单：Ruby的实例变量不会被继承，并且与继承机制无关。它们有时看起来是被继承的原因是实例变量是由首先为其分配值的方法创建的，而这些方法通常是被继承或链接的。
有一个重要的推论。由于实例变量与继承无关，因此，一个子类使用的实例变量不能“隐藏”超类中的实例变量。如果子类使用与其祖先之一使用的变量同名的实例变量，它将覆盖其祖先变量的值。可以有意进行此操作以更改祖先的行为，也可以无意间进行此操作。在后一种情况下，几乎可以确定会导致错误。与前面描述的私有方法的继承一样，这也是为什么只有在熟悉（并控制）超类的实现时才可以安全地扩展Ruby类的另一个原因。
最后，回想一下，类实例变量只是代表一个类的Class对象的实例变量。因此，它们不是继承的。此外，Point和Point3D对象（我们所讨论的是Class对象本身，而不是它们代表的类）都只是Class的实例。它们之间没有关系，也没有办法可以从另一个继承变量。

#### 继承和类变量
类变量由一个类及其所有子类共享。如果类A定义了变量@@a，则子类B可以使用该变量。尽管从表面上看，这似乎是继承，但实际上是不同的。当我们考虑设置类变量的值时，区别变得很明显。如果子类为超类已在使用的类变量分配值，则它不会创建自己的类变量的私有副本，而是会更改超类看到的值。它还会更改超类的所有其他子类看到的共享值。如果你使用-w运行Ruby1.8，它会打印一条警告。Ruby1.9不会发出此警告。
如果类使用类变量，则任何子类都可以通过更改共享类变量的值来更改类及其所有后代的行为。对于使用类实例变量而不是类变量，这是一个有力的论据。

#### 常量的继承
常量是继承的，可以被覆盖，就像实例方法那样。但是，方法的继承与常量的继承之间存在非常重要的区别。
例如，我们的TestConstTwo类可以使用其TestConst超类定义的ORIGIN常量。尽管最清楚的样式是使用其定义的类来全名称化常量，但TestConstTwo也可以使用未限定的ORIGIN甚至是TestConstTwo::ORIGIN来引用此常量
```ruby
class TestConst
   ORIGIN = 10
end

class TestConstTwo < TestConst
   p ORIGIN
   p TestConstTwo::ORIGIN
   
end
```
当像TestConstTwo这样的类重新定义常量时，常量的继承就变得很有趣。它不会改变父类同名常量的值。
```ruby
class TestConstTwo < TestConst
   p ORIGIN
   p TestConstTwo::ORIGIN
   ORIGIN = 20 # 更改常量的值
   p TestConst::ORIGIN # 父类常量值不变
   p TestConstTwo::ORIGIN # 子类常量值改变
   p ORIGIN # 非全名称取值
end
```
常量与方法之间的重要区别在于，在继承层次结构中查找常量之前，先在常量使用的词法范围内对其进行查找。这意味着，如果TestConstTwo继承使用常量ORIGIN的方法，则当子类定义其自己的ORIGIN版本时，这些继承的方法的行为将不会改变。

#### 对象的创建和初始化
##### new, allocate, and initialize
每个类都继承new类方法。此方法有两个任务：必须分配一个新对象实际上使该对象存在,并且必须初始化该对象。它将这两个任务分别委派给allocate和initialize方法。如果新方法实际上是用Ruby编写的，则它将类似于以下内容：
```ruby

def new(*arg)
   o = self.allocate  # 创建一个对象
   o.initialize(*arg) # 初始化一个对象
   return o           # 返回一个初始化后的对象
end
```
allocate是Class的实例方法，它被所有class对象继承。其目的是创建该类的新实例。你可以自己调用此方法来创建类的未初始化实例。但不要尝试覆盖它; Ruby总是直接调用此方法，而忽略用户可能定义的任何替代版本。
initialize是一个实例方法。大多数类都需要一个，并且扩展除Object以外的其他类都应使用super链接到超类的initialize方法。 initialize方法通常的任务是为对象创建实例变量，并为其设置初始化值。通常，这些实例变量的值是从客户端代码传递给new的参数，它们被传递给new方法，然后传递给initialize方法得出的。 initialize不需要返回初始化的对象。实际上，initialize的返回值被忽略。 Ruby隐式地将initialize方法设为私有，这意味着你无法在对象上显式调用它。
**Note: 类定义了两个名为new的方法。一个是Class＃new，是一个实例方法，另一个是Class\:\:new，是一个类方法。第一个是我们在这里描述的实例方法。它被所有类对象继承，成为该类的类方法，并用于创建和初始化新实例。类方法Class::new是该类类自己的方法版本，可用于创建新类。**
##### Factory method
允许以多种方式初始化类的实例通常很有用。你通常可以通过在initialize方法上提供参数默认值来做到这一点。例如，使用定义如下的initialize方法，可以使用两个或三个参数调用new：
```ruby
class Foo
   def initialize(x, y, z = nil)
      @x, @y, @z = x, y, z
   end
end
```
但是，有时参数默认值还不够，我们需要编写除new以外的工厂方法来创建类的实例。假设我们希望能够使用多种方式来初始化一个实例：
```ruby
class Foo
   def initialize(x, y, z = nil)
      @x, @y, @z = x, y, z
   end

   private_class_method :new
   def self.first(x, y)
      new(x, y)
   end

   def self.second
      new(10, 20)
   end
end
```
##### dup, clone, and initialize_copy
新对象存在的另一种方式是调用dup和clone方法来获取。这些方法会为调用它们的对象的类分配一个新的实例。然后，它们将接收者对象的所有实例变量和污点复制到新分配的对象中。clone比dup更进一步地进行复制,它还复制接收者对象的单例方法，如果原始对象被冻结，则冻结复制对象。
如果一个类定义了一个名为initialize_copy的方法，则clone和dup在从原始对象复制实例变量之后，将在复制的对象上调用该方法。克隆会在冻结复制对象之前调用initialize_copy，因此，initialize_copy仍然可以对其进行修改。）initialize_copy方法将原始对象作为参数传递，并且可以对复制的对象进行所需的任何更改。但是，它不能创建自己的副本对象。initialize_copy的返回值将被忽略。像initialize一样，Ruby确保initialize_copy始终是私有的。
当clone和dup将实例变量从原始对象复制到副本时，它们将引用复制到这些变量的值。他们不复制实际值。换句话说，这些方法执行浅复制。这就是许多类可能想要更改这些方法的行为的原因之一。下面的代码定义了initialize_copy方法以对内部状态进行更深层次的复制：
```ruby
class Foo
   def initialize(*args)
      @x = args
   end

   def initialize_copy(org)
      @x = @x.dup
   end
end
```
此处显示的类将其内部状态存储在数组中。没有initialize_copy方法，如果使用克隆或dup复制对象，则复制的对象将引用与原始对象相同的状态数组。对副本执行的更改会影响原件的状态。由于这不是我们想要的行为，因此我们必须定义initialize_copy来创建数组的副本。

```ruby
class Foo
   attr_accessor :x
   def initialize(*args)
      @x = args
   end

   def set_arg(x)
      self.x.push(x)
   end

   def get_arg
      x
   end

   def initialize_copy(org)
      @x = @x.dup
   end
end

obj = Foo.new(1, 2, 3)
obj2 = obj.dup
obj.set_arg(10)
print obj.get_arg # [1, 2, 3, 10]

print obj2.get_arg # [1, 2, 3]
```
如果不定义initialize_copy方法，则改变原始对象的数据也会改变复制的对象的数据。
某些类（例如定义枚举类型的类）可能想要严格限制存在的实例数。诸如此类的类需要将其new方法设为私有，并且可能还希望防止复制。以下代码演示了一种实现方法：
```ruby
class Season
   NAMES = %w{ Sprint Summer Autumn Winter }
   INSTANCES = []

   def initialize(n)
      @n = n
   end

   def to_s
      NAMES[@n]
   end

   NAMES.each_with_index do |name, index|
      instance = new(index)
      INSTANCES[index] = instance
      const_set name, instance
   end

   private_class_method :new, :allocate
   private :dup, :clone
end
```
这段代码涉及一些元编程技术，代码的重点是最后一行，使dup和clone方法变为私有。
防止对象复制的另一种技术是使用undef来简单地删除clone和dup方法。另一种方法是重新定义clone和dup方法，以使它们引发异常，并显示一条错误消息，明确指出不允许复制。这样的错误消息可能对正在使用你的类的程序员有所帮助。
##### marshal_dump and marshal_load
创建对象的第三种方式是，调用Marshal.load来重新创建以前使用Marshal.dump编组（或“序列化”）的对象。Marshal.dump保存对象的类，并递归排列其每个实例变量的值。这些方法效果很好，可以使用这两种方法保存和还原大多数对象。
一些类需要更改排列的方式。原因之一是提供了更紧凑的方式来表示对象的状态。另一个原因是避免保存易失性数据，例如在解组对象时只需要清除的缓存内容。你可以通过在类中定义marshal_dump实例方法来定制处理对象的方式。它应返回另一个对象（例如字符串或所选实例变量值的数组），以代替接收器对象进行排列处理。
当然，如果定义自定义的marshal_dump方法，则必须定义匹配的marshal_load方法。 marshal_load将在该类的新分配（但已分配）但未初始化的实例上调用。它将传递由marshal_dump返回的对象的重构副本，并且它必须根据传递对象的状态来初始化接收器对象的状态。
通常，我们可以通过将整数打包到字符串中来减少封送处理对象的大小，从而减少一些字节。
如果你正在编写一个类（例如前面显示的Season类），但是你已经为其禁用了clone和dup方法，则你还需要实现自定义的封送处理方法，因为转储和加载对象是创建对象副本的简便方法。你可以通过定义引发异常的marshal_dump和marshal_load方法来完全防止封送处理，但是这样做很繁琐。一个更优雅的解决方案是自定义解编组，以便Marshal.load返回现有对象，而不是创建副本。
为此，我们必须定义一对不同的自定义封送处理方法，因为marshal_load的返回值将被忽略。 _dump是一个实例方法，必须以字符串形式返回对象的状态。匹配的_load方法是一个类方法，它接受_dump返回的字符串并返回一个对象。允许_load创建新对象或返回对现有对象的引用。
为了允许对Season对象进行封送处理，但防止复制，我们将以下方法添加到类中：
```ruby
class Season
   NAMES = %w{ Sprint Summer Autumn Winter }
   INSTANCES = []

   def initialize(n)
      @n = n
   end

   def to_s
      NAMES[@n]
   end

   NAMES.each_with_index do |name, index|
      instance = new(index)
      INSTANCES[index] = instance
      const_set name, instance
   end

   def _dump(limit)
      @n.to_s
   end

   def self._load(s)
      INSTANCES[Integer(s)]
   end

   private_class_method :new, :allocate
   private :dup, :clone
end
```
##### The Singleton Pattern
单例是只有一个实例的类。单例可以用于在面向对象的框架中存储全局程序状态，并且可以作为类方法和类变量的有用替代方法。
正确实现单例需要前面展示的许多技巧。必须将new方法和allocate方法设为私有，必须防止dup和clone复制，等等。幸运的是，我们可以使用标准库中的Singleton模块；只需require “Singleton”，然后将Singleton纳入你的class即可。这定义了一个名为instance的类方法，该方法不带任何参数并返回该类的单个实例。定义一个initialize方法来执行该类的单个实例的初始化。但是请注意，任何参数都不会传递给此方法。
```ruby
class PointStats
   include Singleton

   def initialize
      @n, @totalX, @totalY = 0, 0.0, 0.0
   end

   def record(point)
      @n += 1
      @totalX += point.x
      @totalY += point.y
   end

   def report
      puts "Number of points created: #@n"
      puts "Average X coordinate: #{@totalX/@n}"
      puts "Average Y coordinate: #{@totalY/@n}"
   end
end
```
有了这样的类，我们可以像这样为Point类编写initialize方法：
```ruby
class Point
   def initialize(x, y)
      @x, @y = x, y
      PointStats.instance.record(self)
   end
end
```

Singleton模块会自动为我们创建instance类方法，然后在该Singleton实例上调用常规实例方法记录。类似地，当我们要查询点统计信息时，我们编写：
```ruby
PointStats.instance.report
```
##### Module
像类一样，模块是一组命名的方法，常量和类变量。模块的定义很像类,但是使用module关键字代替了class关键字。与类不同，模块不能被实例化，也不能被子类化。模块独立存在；没有继承的“模块层次结构”。
模块用作名称空间和mix-in。
正如类对象是Class类的实例一样，模块对象也是Module类的实例。类是Module的子类。这意味着所有类都是模块，但并非所有模块都是类。就像模块一样，类可以用作名称空间。但是，类不能用作mixins。
当不需要面向对象的编程时，模块是对相关方法进行分组的好方法。例如，假设你正在编写使用Base64编码在文本之间对二进制数据进行编码和解码的方法。不需要特殊的编码器和解码器对象，因此这里没有理由定义一个类。我们需要的是两种方法：一种进行编码，一种进行解码。我们可以只定义两个全局方法：
def base64_encode

end

def base64_decode

end

为了防止名称空间与其他编码和解码方法冲突，我们为方法指定了base64前缀。此解决方案有效，但是大多数程序员都希望避免在可能的情况下向全局名称空间添加方法。因此，更好的解决方案是在Base64模块中定义两个方法：
```ruby
module Base64
   def self.encode
      
   end
   
   def self.decode
      
   end
end
```
注意，我们用一个self定义我们的方法。前缀，这使它们成为模块的“类方法”。我们还可以显式重用模块名称并定义如下方法：
```ruby
   def Base64.encode

   end

   def Base64.decode

   end
end
```
用这种方法定义方法更具重复性，但是更紧密地反映了这些方法的调用语法：
```ruby
p Base64.encode
p Base64.decode
```
请注意，模块名称必须与类名称一样以大写字母开头。定义模块会创建一个与模块同名的常量。该常数的值是代表模块的Module对象。模块也可以包含常量。
在Base64模块之外，此常量可以称为Base64::DIGITS。在模块内部，我们的编码和解码方法可以通过其简单名称DIGITS来引用它。如果这两种方法需要共享非恒定数据，则可以使用类变量（带有@@前缀），就像在类中定义它们一样。
```ruby
module Base64
   VAR = 100
   @@kobe = 10
   def self.encode
      @@kobe = 20
   end

   def self.decode
      @@kobe = 30
   end

   def self.get
      @@kobe
   end

   def self.get_var
      VAR
   end
end
p Base64::VAR
p Base64.get_var

Base64.encode
p Base64.get

Base64.decode
p Base64.get
```
##### 嵌套的命名空间
包括类在内的模块可以嵌套。这将创建嵌套的名称空间，但没有其他作用：嵌套在另一个中的类或模块对嵌套在其中的类或模块没有特殊的访问权限。为了继续我们的Base64示例，假设我们想定义用于编码和解码的特殊类。由于Encoder和Decoder类仍然彼此相关，因此我们将它们嵌套在模块中：
```ruby
module Base64
   DIGITS = 100

   class Encoder
      def encode
         puts "I am encode"
      end

      def greet
         Base64.utility_helper
      end
   end

   class Decoder
      def decode
         puts "I am decode"
      end

      def greet
         Base64.utility_helper
      end
   end

   def Base64.utility_helper
      puts "Do u want to have a help!"
   end
end

Base64::Encoder.new.encode
Base64::Decoder.new.decode
Base64::Decoder.new.greet
Base64::Encoder.new.greet
```
通过以这种方式构造代码，我们定义了两个新类，即Base64::Encoder和Base64::Decoder。在Base64模块内部，这两个类可以通过不带Base64前缀的非限定名称相互引用。每个类都可以使用没有前缀的DIGITS常量。
另一方面，请考虑Base64.helper实用程序功能。嵌套的Encoder和Decoder类没有对包含模块的方法的特殊访问，它们必须使用其完全限定的名称Base64.helper来引用此帮助器方法。
因为类是模块，所以它们也可以嵌套。将一个类嵌套在另一个类中只会影响内部类的名称空间。它不会为该类提供对外部类的方法或变量的任何特殊访问。如果类的实现需要帮助程序类，代理类或其他不属于公共API的其他类，则您可能需要考虑将内部类嵌套在使用它的类中。这样可以使命名空间保持整洁，但实际上不会以任何方式将嵌套类设为私有。
##### Moudle作为mix-in
第二种使用模块的方式比第一种使用更强大。如果模块定义实例方法而不是类方法，则可以将这些实例方法混入其他类中。 Enumerable和Comparable是mixin模块的知名示例。 Enumerable定义了有用的迭代器，这些迭代器是根据每个迭代器实现的。 Enumerable并没有定义每个方法本身，但是任何定义它的类都可以在Enumerable模块中混合使用，以立即添加许多有用的迭代器。可比类似；它根据通用比较器<=>定义比较运算符。如果您的班级定义了<=>，则可以在Comparable中混合使用<，<=，==>，> =，以及between？。
要将模块混入类中，请使用include。通常将include当作语言关键字使用：
```ruby
class Foo
   include Base64
end
```

实际上，它是Module的私有实例方法，它是在self上隐式调用的，self是包含模块的类。在方法形式中，此代码为：
```ruby
class Foo
   include(Base64)
end
```
因为include是私有方法，所以必须将其作为函数调用，并且我们无法编写self.include（Comparable）。include方法接受任意数量的Module对象进行混合，因此定义each和<=>的类可能包含以下行：
include Comparable, Enumerable
是否包含模块会影响类型检查方法is_a？和开关相等运算符===。例如，字符串混合在Comparable模块中，而在Ruby1.8中也混合在Enumerable模块中：
```ruby
class Foo
   include Comparable, Enumerable
end

p Foo.new.is_a? Comparable # true
p Foo.new.is_a? Enumerable # true
```
注意instance_of？仅检查其接收者的类，而不检查超类或模块，因此以下内容为假：
```ruby
p Foo.new.instance_of? Comparable # false
p Foo.new.instance_of? Enumerable # false
```
尽管每个类都是一个模块，但是include方法不允许将一个类包含在另一个类中。要包含的参数必须是使用模块声明的模块，而不是类。但是，将一个模块包含到另一个模块中是合法的。这样做仅会使包含模块的实例方法成为包含模块的实例方法。
```ruby
module Iterable
   include Enumerable
   def each
      loop { yield self.next }
   end
end
```
混入模块的通常方法是使用Module.include方法。另一种方法是使用Object.extend。此方法使指定模块或多个模块的实例方法成为接收器对象的单例方法.（并且如果接收方对象是Class实例，那么接收方的方法将成为该类的类方法。）下面是一个示例：
```ruby
ountdown = Object.new
def countdown.each
   yield 3
   yield 2
   yield 1
end

countdown.extend(Enumerable)
p countdown.sort
countdown.each {|var| p var}
```
##### Includable Namespace Modules
可以定义定义命名空间的模块，但仍然允许混合使用它们的方法。Math模块的工作方式如下：
```ruby
Math.sin(0) # Math is a namespace
include Math # The Math namespace can be included
p sin(0) # 可以直接访问sin方法
```
kernel模块也这样工作：我们可以通过kernel名称空间调用它的方法，也可以作为对象的私有方法来调用它。
如果要创建像Math或Kernel这样的模块，请将你的方法定义为该模块的实例方法。然后使用module_function将这些方法转换为“模块函数”。 module_function是Module的私有实例方法，非常类似于public，protected和private方法。它接受任意数量的方法名称（作为符号或字符串）作为参数。调用module_function的主要作用是它可以复制指定方法的类方法。第二个效果是，它将实例方法设为私有（稍后我们将对此进行更多说明）。
与public，protected和private方法一样，module_function方法也可以不带任何参数调用。当以这种方式调用时，模块中随后定义的任何实例方法都将是模块函数：它们将成为公共类方法和私有实例方法。一旦调用了不带参数的module_function，它对于其余的模块定义仍然有效。因此，如果要定义不是模块函数的方法，请先定义那些方法。
```ruby
module Dog
   def name
      'lucky'
   end

   def color
      'red'
   end

   module_function :color, :name
end

class TestM
   include Dog
end

p TestM.new.name # 错误 name为私有方法
p TestM.new.color # 错误 color为私有方法
p Dog.name  # 公共类方法
p Dog.color # 公共类方法
```
起初，module_function将模块的实例方法设为私有似乎让人感到惊讶。这样做的原因并非真正用于访问控制，因为显然这些方法也可以通过模块的名称空间公开获得。而是将这些方法设为私有，以将它们限制为函数样式的调用，而无需显式的接收者。(之所以将它们称为模块函数而不是模块方法，是因为它们必须以函数样式调用。)强制在没有接收方的情况下调用包含的模块函数，这样就不太可能将它们误认为是真正的实例方法。假设我们正在定义一个其方法执行许多三角函数的类。为了我们自己的方便，我们包括Math模块。然后，我们可以将sin方法作为函数调用，而不是调用Math.sin。 sin方法是对self隐式调用的，但是我们实际上并不希望它对self起作用。
定义模块函数时，应避免使用self，因为self的值取决于调用它的方式。当然可以定义一个模块函数，其行为取决于调用方式。但是，如果要这样做，那么简单定义一个类方法和一个实例方法就更有意义了。
```ruby
module Dog
   def name
      'lucky'
   end

   module_function
   def color
      'red'
   end


end

class TestM
   include Dog

   def greet
      puts "This is a #{color} dog"
   end
end

p TestM.new.name
TestM.new.greet
```
#### 加载和请求一个模块
Ruby程序可以分解为多个文件，对程序进行分区的最自然的方法是将每个重要的类或模块放入单独的文件中。然后可以使用require或load将这些单独的文件重新组合为一个程序（如果设计合理，则可以被其他程序重用）。这些是在Kernel中定义的全局函数，但与语言关键字一样使用。相同的require方法也用于从标准库加载文件。
load和require用于类似目的，尽管require比load更常用。
这两个函数都可以加载和执行指定的Ruby源代码文件。如果要加载的文件是用绝对路径指定的，或者是相对于〜(用户的主目录)的，则将加载该特定文件。但是，通常，该文件被指定为相对路径，并相对于Ruby的加载路径目录进行加载并要求对其进行搜索(加载路径的详细信息如下所示)。
Ruby 1.9还定义了require_relative方法。它的工作原理与require相同，除了它忽略加载路径并搜索相对于加载调用代码的目录的命名文件。
尽管它们总体上相似，但是load和require之间存在重要的区别：
* 除了加载源代码之外，require还可以加载对Ruby的二进制扩展。二进制扩展名当然取决于实现，但是在基于C的实现中，二进制扩展名通常采用共享库文件的形式，扩展名为.so或.dll
* load需要包含扩展名的完整文件名。通常会为require传递一个没有扩展名的库名，而不是文件名。在这种情况下，它将搜索以库名称为其基本名称以及适当的源或本地库扩展名的文件。如果目录同时包含.rb源文件和二进制扩展名文件，则require将加载源文件而不是二进制文件。
* load可以多次加载同一文件。require尝试阻止相同的文件被多次加载。(但是，如果你使用两个不同但等效的路径指向同一个库文件，require可能会被愚弄。在Ruby 1.9中，require将相对路径扩展为绝对路径，这使它变得有些愚蠢)。require跟踪通过将它们附加到全局数组也称为$"($LOADED_FEATURES)来加载的文件。load并不会这么做。
* load以当前$SAFE级别加载指定的文件。 require加载指定库会将$SAFE设置为0，即使被require调用的代码对该变量具有更高的值。(请注意，如果$SAFE设置为大于0的值，则require将拒绝加载带有污染文件名或外部世界可写目录的文件。因此，从理论上讲，对于require来说，加载降低了$SAFE级别的文件应该是安全的)。

#### 加载路径
Ruby的加载路径是一个数组，你可以使用全局变量$LOAD_PATH或$:进行访问。(此全局符号的助记符是冒号用作类Unix操作系统上的路径分隔符。)数组的每个元素都是Ruby将搜索要加载的文件的目录的名称。在数组末尾的目录之前先搜索数组开头的目录。在Ruby 1.8中，$LOAD_PATH的元素必须是字符串，但是在Ruby 1.9中，它们可以是字符串，也可以是任何具有返回字符串的to_path方法的对象。
$LOAD_PATH的默认值取决于Ruby的实现，它所运行的操作系统，甚至是在文件系统中的安装位置。Ruby 1.9中的一个较小的加载路径更改是在vendor_ruby之后和标准库之前搜索了vendor_ruby目录。这些旨在用于由操作系统供应商提供的自定义。
RubyGems内置在Ruby 1.9中：gem命令随Ruby一起分发，可用于安装新软件包，其安装目录会自动添加到默认加载路径。在Ruby 1.8中，必须单独安装RubyGems（尽管Ruby 1.8的某些发行版可能会自动捆绑它），并且gem安装目录绝不会添加到加载路径中。相反，Ruby 1.8程序需要rubygems模块。这样做会用新版本替换默认的require方法，该新版本可以在哪里查找已安装的gem。
你可以使用Ruby解释器的–I命令行选项将新目录添加到Ruby搜索路径的开头。使用多个–I选项可以指定多个目录，或者使用单个–I并使用冒号（或Windows中的分号）将多个目录彼此分开。 Ruby程序还可以通过更改$ LOAD_PATH数组的内容来修改自己的加载路径。这里有些例子：
```ruby

# Remove the current directory from the load path
$:.pop if $:.last == '.'  

# Add the installation directory for the current program to 
# the beginning of the load path instead of using require_relative.

$LOAD_PATH.unshift File.expand_path($PROGRAM_NAME)

# Add the value of an environment variable to the end of the path
$LOAD_PATH << ENV['MY_LIBRARY_DIRECTORY']


```
最后，请记住，可以通过传递绝对文件名(以/或〜开头)来完全跳过加载路径，以进行load或require。
#### 执行被加载的代码
load和require立即执行指定文件中的代码。但是，调用这些方法并不等同于简单地用文件包含的代码替换对load或require的调用。
加载了load或require的文件在新的顶级作用域内执行，该作用域不同于调用load或require的作用域。加载的文件可以看到在加载时已定义的所有全局变量和常量，但是它无权访问启动加载的本地作用域。其含义包括以下内容：
* 在调用require或load的作用域内定义的局部变量对加载的文件不可见。
* 加载完成后，将丢弃由加载文件创建的任何局部变量；它们在定义它们的文件之外永远不可见。
* 在加载文件的开头，self的值始终是main对象，就像Ruby解释器开始运行时一样。也就是说，在方法调用内调用load或require不会将接收器对象传播到加载的文件。
* 在加载的文件中，当前模块嵌套将被忽略。例如，你不能打开一个类，然后加载一个方法定义文件。该文件将在顶级范围内处理，而不是在任何类或模块内处理。
#### 被包裹的加载
load方法具有我们以前未描述的不常用功能。如果使用非nil或false以外的第二个参数调用，则它将包装指定的文件并将其加载到匿名模块中。这意味着加载的文件不能影响全局名称空间。它定义的任何常量（包括类和模块）都被捕获在匿名模块中。你可以将包装的加载用作安全预防措施(或最小化由名称空间冲突引起的错误的方法)。我们将在安全性中看到，当Ruby在沙盒中运行不受信任的代码时，该代码将无法调用require，并且只能将load用于包装的负载。
当文件加载到匿名模块中时，它仍然可以设置全局变量，并且它所设置的变量对于加载该文件的代码是可见的。假设你编写了一个文件util.rb，该文件是定义了有用的实用程序方法的Util模块。如果你希望即使包装了文件也可以访问这些方法，则可以在文件末尾添加以下行：
$Util = Util   # 将这个模块的引用存储进一个全局变量
现在，将util.rb加载到匿名名称空间中的代码可以通过全局$Util而不是常量Util访问实用程序功能。
在Ruby 1.8中，甚至可以将匿名模块本身传递回正在加载代码。

```ruby
if Module.nesting.size > 0       
  $wrapper = Module.nesting[0]   
end
```
#### 自动加载模块
内核和模块的autoload方法允许根据需要延迟加载文件。全局autoload函数允许你注册未定义常量的名称(通常是类或模块的名称)以及定义该常量的库的名称。首次引用该常量时，将使用require加载命名库。例如：
Module类定义了自己的autoload版本，以与嵌套在另一个模块中的常量一起使用。使用autoload?还是Module.autoload?测试对常量的引用是否会导致文件被加载。该方法需要一个符号参数。如果在引用由符号命名的常量时将加载文件，那么autoload?返回文件名。否则(如果未请求自动加载，或者如果文件已经加载)autoload?返回nil。
#### 单实例方法与Eigenclass
我们在之前了解到可以定义单例方法，仅针对单个对象而不是一个类的多个对象定义的方法。要在对象Point上定义单例方法sum，我们将编写：
```ruby
def Foo.sum
   #
end


```
如前面所述，类的类方法只不过是表示该类的Class实例上的单例方法。
对象的单例方法不是由该对象的类定义的。但是它们是方法，必须与某种类关联。对象的单例方法是与该对象关联的匿名本征类的实例方法。 “本征”是德语单词，大约（大致）表示“自我”，“拥有”，“特定于”或“特征”。本征类也称为单例类或（较不常见）元类。 Ruby社区并未统一接受“本征类”一词，但这是我们在本书中将要使用的术语。
Ruby定义了一种语法，用于打开对象的本征类并向其添加方法。这为定义单例方法提供了一种选择。我们可以改为定义任意数量的特征类实例方法。要打开对象o的本征类，请使用类<< o。例如，我们可以这样定义Foo的类方法：
```ruby
class << Foo
   def class_method1
   ...
   end
   
   def class_method2
   ...
   end
end
```
如果在类本身的定义内打开类对象的本征类，则可以使用self而不是重复类的名称。重复前面的示例：
```ruby
class Foo
   class << self 
      def class_method1
         ###
      end
      
      def class_method2
         ###
      end
   end
   
end
```
请谨慎使用语法。请注意，以下三行之间存在很大差异：
```ruby
class Point            # 创建或打开一个类
class Point3D < Point  # 创建Point的子类
class << Point         # 打开Point对象的Eigenclass

```
通常，将类方法定义为单独的单例方法而无需显式打开本征类会更清楚。当您打开对象的本征类时，self指代本征类对象。因此，获取对象o的本征类的惯用法是：
```ruby
eigenclass = class << Foo ; self; end
p eigenclass #<Class:Foo>
```
我们可以将其形式化为Object的方法，以便我们可以请求任何对象的特征类：
```ruby
class Object
   def eigenclass
      class << self; self; end
   end
end
```
除非你使用Ruby进行复杂的元编程，否则你不太可能真的需要像下面显示的那样一个本征类实用程序函数。但是，值得了解特征类，因为您偶尔会看到它们在现有代码中使用，并且因为它们是Ruby方法名称解析算法的重要组成部分，我们将在下面进行介绍。
#### 方法查询
Ruby在评估方法调用表达式时，必须首先确定要调用的方法。执行此操作的过程称为方法查找或方法名称解析。对于方法调用表达式o.m，Ruby通过以下步骤执行名称解析：
1. 首先，它检查o的特征类以查找名为m的单例方法。
2. 如果在本征类中未找到方法m，则Ruby在o类中搜索名为m的实例方法。
3. 如果在类中未找到方法m，则Ruby会搜索o类所包含的任何模块的实例方法。如果该类包含多个模块，则以与包含它们的顺序相反的顺序搜索它们。即，首先搜索最近包含的模块。
4. 如果在o的类或其模块中未找到实例方法m，则搜索将继承层次结构上移至超类。对继承层次结构中的每个类重复步骤2和3，直到已搜索每个祖先类及其包含的模块。
5. 如果完成搜索后未找到名为m的方法，则将调用名为method_missing的方法。为了找到此方法的适当定义，名称解析算法将从步骤1重新开始。内核模块提供method_missing的默认实现，因此可以保证第二遍名称解析成功。在处理未定义的方法中将更详细地介绍method_missing方法。
让我们考虑该算法的具体示例。假设我们有以下代码：
```ruby
message = "hello"
message.world

```
1. 检查特征类中的单例方法。在这种情况下没有任何东西。
2. 检查String类。没有名为world的实例方法。
3. 检查String类的Comparable和Enumerable模块中是否有一个名为world的实例方法。两个模块均未定义这种方法。
4. 检查String的超类，即Object。 Object类也没有定义名为world的方法。
5. 检查对象包含的内核模块。在这里也找不到world方法，因此我们现在切换到寻找一个名为method_missing的方法。
6. 在上面的每个位置（String对象的特征类，String类，Comparable和Enumerable模块，Object类以及Kernel模块）中查找method_missing。我们发现的method_missing的第一个定义是在Kernel模块中，因此这是我们调用的方法。它的作用是引发异常：NoMethodError："hello"的未定义方法"world":String
这似乎要求Ruby在每次调用方法时都进行详尽的搜索。但是，在典型的实现中，成功的方法查找将被缓存，以使后续的相同名称（无中间方法定义）的查找将非常快。
#### 类方法查询
类方法的名称解析算法与实例方法的名称解析算法完全相同，但是有一个不同之处。让我们从一个简单的案例开始，没有任何曲折。这是一个C类，它没有定义自己的类方法
```ruby
class C

end
```
请记住，在定义了这样的类之后，常量C引用的对象是Class的实例。我们定义的任何类方法都只是对象C的单例方法。
定义了类C之后，我们很可能会编写涉及类方法new的方法调用表达式：c = C.new
为了解析new方法，Ruby首先在对象C的特征类中寻找单例方法。我们的类没有任何类方法，因此在此找不到任何方法。搜索本征类之后，名称解析算法将搜索C的类对象。C的类为Class，因此Ruby接下来在Class中查找方法，并在其中找到一个名为new的实例方法。
你没看错。类方法C.new的方法名称解析算法最终找到了实例方法Class.new。实例方法和类方法之间的区别对于得出面向对象的编程范例非常有用，但是事实是，在Ruby中（类由对象表示），这种区别有些人为。每个方法调用，无论是实例方法还是类方法，都有一个接收者对象和一个方法名称。名称解析算法为该对象找到合适的方法定义。我们的对象C是Class类的实例，因此我们当然可以通过C调用Class的实例方法。此外，Class继承了Module，Object和Kernel的实例方法，因此这些继承的方法也可用作C的方法。我们称之为“类方法”的唯一原因是我们的对象C恰好是一个类。
我们的类方法C.new被发现为Class的实例方法。但是，如果在该处找不到它，则名称解析算法将继续执行，就像实例方法一样。在搜索Class失败之后，我们将查看模块（Class不包含任何模块），然后查看超类Module。接下来，我们将搜索Module（没有模块）的模块，最后是Module，Object及其模块Kernel的超类。
类方法与实例方法一样被继承。让我们定义一个类方法Integer.parse作为示例：
```ruby
def Integer.parse(text)
   text.to_i
end

Fixnum.parse("1")
```
根据我们之前看到的方法名称解析算法的描述，我们知道Ruby首先会在Fixnum的特征类中搜索单例方法。接下来，它将搜索类，模块，对象和内核的实例方法。那么它在哪里找到解析方法呢？ Integer的类方法只是Integer对象的单例方法，这意味着它是由Integer的本征类定义的。那么，该Integer的本征类如何参与名称解析算法？
考虑到这一点，我们现在可以更全面地解释方法名称解析算法，并说当Ruby在对象的本征类中搜索单例方法时，它也会同时搜索本征类的超类(和所有祖先)。因此，在查找Fixnum的类方法时，Ruby首先检查Fixnum，Integer，Numeric和Object的单例方法，然后检查Class，Module，Object和Kernel的实例方法。
#### 常量查询
Ruby首先尝试在引用的词法范围内解析常量引用。这意味着它首先检查包围常量引用的类或模块，以查看该类或模块是否定义了常量。如果不是，它将检查下一个封闭的类或模块。一直持续到没有更多的封闭类或模块为止。请注意，顶级或全局常量不被视为词法范围的一部分，并且在常量查找的这一部分中也未被考虑。类方法Module.nesting以搜索顺序返回在此步骤中搜索的类和模块的列表。
如果在词法范围内未找到常量定义，则Ruby接下来将通过检查引用该常量的类或模块的祖先来尝试解析继承层次结构中的常量。包含的类或模块的ancestors方法返回在此步骤中搜索的类和模块的列表。
如果在继承层次结构中未找到常量定义，则检查顶级常量定义。
如果找不到所需常量的定义，则将调用const_missing方法（如果存在）中的包含类或模块，并有机会为常量提供一个值。
关于此常量查找算法，有几点需要更详细地说明：
* 封闭模块中定义的常量优先于包含模块中定义的常量。
* 在该类的超类之前搜索该类包含的模块。
* Object类是所有类的继承层次结构的一部分。在任何类或模块之外定义的顶级常量类似于顶级方法：它们在Object中隐式定义。因此，当从类内引用顶级常量时，将在搜索继承层次结构时对其进行解析。但是，如果在模块定义中引用了常量，则在搜索模块的祖先后需要对Object进行显式检查。
* 内核模块是Object的祖先。这意味着在内核中定义的常量的行为类似于顶级常量，但是可以被Object中定义的真正的顶级常量覆盖。

#### 反射和元编程
我们已经看到Ruby是一种动态语言。你可以在运行时将新方法插入类，为现有方法创建别名，甚至在单个对象上定义方法。此外，它还具有丰富的API用于反射。反射，也称为自省，仅表示程序可以检查其状态和结构。例如，Ruby程序可以获取Hash类定义的方法的列表，查询指定对象中命名实例变量的值，或者遍历解释器当前定义的所有Regexp对象。反射API实际上更进一步，并允许程序更改其状态和结构。 Ruby程序可以动态设置命名变量，调用命名方法，甚至定义新的类和新的方法。
Ruby的反射API（加上其一般的动态特性，其块和迭代器的控制结构以及其括号可选的语法）使其成为元编程的理想语言。定义松散的元编程是在编写可帮助您编写程序的程序（或框架）。换句话说，元编程是用于以简化编程的方式扩展Ruby语法的一组技术。元编程与编写特定领域语言或DSL的想法紧密相关。 Ruby中的DSL通常使用方法调用和块，就好像它们是该语言的特定于任务的扩展中的关键字一样。
请记住，反射本身并不是元编程。元编程通常以某种方式扩展Ruby的语法或行为，并且经常涉及不止一种反射。
#### 类型，类和模块
最常用的反射方法是用于确定对象类型的那些方法以及它响应什么方法。
```ruby

obj = Array.new
p obj.class               # 对象所属的类
p Array.superclass        # 对象的父类
p obj.instance_of? Array  # 对象属于哪个类
p obj.is_a? Array         # 是否属于数组类
p obj.kind_of? Array      # 与上面是同义词
p Array === obj           # 对象是否属于Array类
p obj.respond_to?'[]'     # 对象是否有该方法
p Array.private_methods   # 数组的私有方法
p obj.respond_to? 'initialize', true  # 加上true表示检查私有方法

module M

end

class A
   include M
end

class B < A; end

class C < B; end

b = B.new
b.is_a? A #=> true
b.is_a? B #=> true
b.is_a? C #=> false
b.is_a? M #=> true 模块也属于父类，因为类B继承了A，而A mix-in了模块M
```
#### 祖先和模块
除了你已经看到的这些方法之外，还有几种相关的反射方法可用于确定类或模块的祖先，以及确定类或模块包括哪些模块。这些方法在演示时很容易理解：
```ruby
module AA; end
module BB; include AA; end
class C; include BB; end

p C < BB  # true
p C < AA  # true
p BB < AA # true

p Fixnum < Integer     # true
p Integer < Comparable # true
p Integer < Fixnum     # false
p String < Numeric     # nil 字符串不是数字

p AA.ancestors
p BB.ancestors
p C.ancestors
p String.ancestors


p AA.included_modules
p BB.included_modules
p C.included_modules

p C.include? BB  #true
p C.include? AA  #true
p AA.include? BB #false
p AA.include? AA # false
```
这段代码演示了include ?，这是Module类定义的公共实例方法。但是它还具有两次调用include方法（不带问号）的功能，这是Module的私有实例方法。作为私有方法，它只能在self上隐式调用，这将其用法限制为类或模块定义的主体。这种方法的使用就好像它是关键字一样，是Ruby核心语法中的元编程示例。
与private include方法有关的方法是public Object.extend。该方法通过使每个指定模块的实例方法成为对象的单例方法来扩展对象：
```ruby
module Greeter; def hi; "hello"; end; end
str = "string object"
str.extend(Greeter)
p str.hi                # 为单个对象添加实例方法 
String.extend(Greeter)  # 为类添加类方法
p String.hi
```
类方法Module.nesting与模块的包含或祖先无关。而是返回一个数组，该数组指定当前位置的模块嵌套。 Module.nesting\[0\]是当前类或模块，Module.nesting\[1\]是包含的类或模块，依此类推：
```ruby
module Kobe
   class Test
      p Module.nesting # [Kobe::Test, Kobe]
   end
   p Module.nesting # Kobe
end
```
#### 定义类和模块
类和模块是类和模块类的实例。这样，您可以动态创建它们：
```ruby
MM = Module.new # 创建一个模块
CC = Class.new  # 创建一个类
DD = Class.new(CC) do
   include MM
end

p DD.include? MM 
p DD.ancestors
```
Ruby的一个不错的功能是，当将动态创建的匿名模块或类分配给常量时，该常量的名称将用作模块或类的名称（并由其名称和to_s方法返回）。
#### 对字符串和块进行求值
Ruby的eval方法是Ruby最强大，最直接的反射功能之一。如果你的Ruby程序可以生成有效的Ruby代码字符串，则Kernel.eval方法可以评估该代码：
```ruby
x = 10
p eval "x + 20"
```
eval是一个非常强大的函数，但是除非您实际上正在编写一个执行用户输入的Ruby代码行的shell程序（如irb），否则您不太可能真正需要它。 （在网络环境中，对从用户收到的文本调用eval几乎是不安全的，因为它可能包含恶意代码。）经验不足的程序员有时最终会使用eval作为拐杖。如果您发现自己在代码中使用了它，请查看是否有避免它的方法。话虽如此，还有一些更有用的方法使用eval和类似eval的方法。
Binding对象表示某个时刻Ruby变量绑定的状态。 Kernel.binding对象返回在调用位置有效的绑定。你可以将Binding对象作为eval的第二个参数传递，并且你指定的字符串将在这些绑定的上下文中进行求值。例如，如果我们定义一个实例方法来返回一个Binding对象，该对象表示一个对象内部的变量绑定，那么我们可以使用这些绑定来查询和设置该对象的实例变量。我们可以这样完成：
```ruby
class Object
   def bindings
      binding
   end
end

class Test
   def initialize(x)
      @x = x
   end
end

t = Test.new(10)
p eval("@x", t.bindings) # 10


```
如Closures和Bindings中所述，Proc对象定义了一个公共绑定方法，该方法返回一个Binding对象，该对象表示对该Proc主体有效的变量绑定。此外，eval方法允许你传递Proc对象而不是Binding对象作为第二个参数。
Ruby 1.9在Binding对象上定义了一个eval方法，因此不必将Binding作为第二个参数传递给全局eval，而可以在Binding上调用eval方法。可以根据需要选择其中一种方式；这两种技术是等效的。

#### instance_eval和class_eval
Object类定义一个名为instance_eval的方法，而Module类定义一个名为class_eval的方法。 （module_eval是class_eval的同义词。）这两个方法都像eval一样计算Ruby代码，但是有两个重要的区别。第一个区别是它们在指定对象的上下文中或在指定模块的上下文中对代码进行评估—对象或模块是评估代码时self的值。这里有些例子
```ruby
t = Test.new(10)
p t.instance_eval "@x" # 返回实例变量的值


String.class_eval "def len; size; end"
p "hello world".len # 字符串长度 11

String.class_eval "alias len2 size"
p "hello world".len2 # 11

String.instance_eval "def empty; ''; end"
p String.empty # ''

```
请注意，当要计算的代码包含方法定义时，instance_eval和class_eval之间的细微但至关重要的区别。 instance_eval定义对象的单例方法（当在类对象上调用它时，将产生类方法）。 class_eval定义常规实例方法。
```ruby
greet = "hello world"
greet.instance_eval "def hi; p 'hi'; end"
greet.hi
```
这两种方法与全局eval之间的第二个重要区别是instance_eval和class_eval可以接受一段代码进行计算。当传递块而不是字符串时，将在适当的上下文中执行块中的代码。因此，这里是先前显示的调用的替代方法：
```ruby
greet.instance_eval do # 为对象定义一个单实例方法
   def hello
      print "hello world\n"
   end
end
greet.hello

String.class_eval do  # 定义新的实例方法
   def good
      print "good\n"
   end
end

String.new.good

t = Test.new(10)
p t.instance_eval { @x }

String.class_eval { def len; size; end }
p "hello world".len # 字符串长度 11

String.class_eval { alias len2 size }
p "hello world".len2 # 11

String.instance_eval { def empty; ''; end }
p String.empty # ''

greet = "hello world"
greet.instance_eval { def hi; p 'hi'; end }


```
#### instance_exec和class_exec
Ruby 1.9定义了另外两种计算方法：instance_exec和class_exec（及其别名module_exec）。这些方法和instance_eval和class_eval那样，在接收器对象的上下文中评估代码块（而不是字符串）。区别在于exec方法接受参数并将其传递给块。因此，代码块是在指定对象的上下文中使用来自对象外部的参数进行计算的。
```ruby
t = Test.new(10)
p t.instance_eval { @x }

back = t.instance_exec(10) { |var|
   @x + var
}

p back

Foos.class_exec do
   def greet2
      puts "hello matz"
   end
end
```
#### 变量和常量
内核，对象和模块定义了反射方法，用于列出所有已定义的全局变量，当前定义的局部变量，对象的所有实例变量，类或模块的所有类变量以及类或模块的所有常量的名称.
```ruby
p global_variables #=> ["$DEBUG", "$SAFE", ...]

a = 10
p local_variables #=> 先前定义了本地变量a，因此结果为[:a]  
```
```ruby
class Foo
   def initialize(x,y); @x, @y = x, y; end
   @@classvar = 1 # 定义类变量
   ORIGIN = Foo.new(1, 2) # 定义常量
end

p Foo::ORIGIN.instance_variables 
p Foo.class_variables
p Foo.constants

output:
# [:@x, :@y]
# [:@@classvar]
# [:ORIGIN]
```
global_variables，local_variables，instance_variables，class_variables和constants方法在Ruby 1.8中返回字符串数组，在Ruby 1.9中返回符号数组。
#### 查询，设置和测试变量
除了列出定义的变量和常量，ruby的Object和Module类也定义了用来查询，设置和移除实例变量，类变量和常量的反射方法。没有特殊的函数用来设置全局变量和局部变量，但是可以使用eval方法来实现这个目的：
```ruby
x = 1
varname = "x"
eval(varname)  # 1
eval('varname = "$g"') #  varname = $g
eval("#{varname} = x") #  $g = 1
eval(varname)          # 1
```
请注意，eval在一个临时范围内评估其代码。 eval可以更改已经存在的局部变量的值。但是，由评估代码定义的任何新局部变量对于eval调用而言都是局部的，并且在返回时不再存在。 （就像所评估的代码在块的主体中​​运行一样，块本地的变量在块外部不存在。）
可以查询，设置和测试任何对象上的实例变量以及任何类或模块上的类变量和常量：
```ruby
o = Object.new
o.instance_variable_set(:@x, 10)
puts o.instance_variable_defined? :@x
puts o.instance_variable_get :@x
Object.class_variable_set :@@x, 11
puts Object.class_variable_defined? :@@x
puts Object.class_variable_get :@@x

```
```ruby
Math.const_set("HIGH_SCHOOL_PI", 22.0/7.0)   #=> 3.14285714285714
Math::HIGH_SCHOOL_PI - Math::PI              #=> 0.00126448926734968

Object.const_set('foobar', 42)               #=> NameError: wrong constant name foobar
```
在Ruby 1.9中，你可以将false作为第二个参数传递给const_get和const_defined?。指定这些方法应仅查看当前类或模块，而不应考虑继承的常量。
对象和模块定义用于undefining实例变量，类变量和常量的私有方法。它们都返回删除的变量或常量的值。由于这些方法是私有的，因此你不能直接在对象，类或模块上调用它们，而必须使用eval方法或send方法
```ruby
puts o.instance_eval { remove_instance_variable :@x } 
puts Object.class_eval { remove_class_variable :@@x } 

puts Object.__send__(:remove_const, :VAR) #也可以使用send方法来实现
```
当引用未定义的常量时，将调用模块的const_missing方法（如果存在）。你可以定义此方法以返回命名常量的值。 （例如，可以使用此功能来实现自动加载功能，在该功能中，可以按需加载类或模块。）下面是一个简单的示例：
```ruby

def Symbol.const_missing(name)
   name
end

p Symbol::Test # :Test 返回常量的符号名称
```
#### 方法
Object和Module类定义了许多用于列出，查询，调用和定义方法的方法。
##### 列出和测试方法
```ruby
o_str = "hello"
p o_str.methods # 打印对象的公有方法
p o_str.public_methods  # 打印对象的公有方法
p o_str.public_methods(false ) # 排除继承而来的方法
p o_str.private_methods # 打印对象的私有方法
p o_str.private_methods(false ) # 排除继承而来的方法
p o_str.protected_methods # 打印对象受保护的方法

def o_str.single; 1; end # 定义一个单实例方法
p o_str.singleton_methods  
```
也可以向类查询其定义的方法，而不是查询类的实例。模块定义了以下方法。像Object方法一样，它们在Ruby 1.8中返回字符串数组，在1.9中返回符号数组：
```ruby

String.instance_methods  # 与下面表达式结果相同
p "s".public_methods

p String.instance_methods(false ) # 与下面表达式的结果相同
p "s".public_methods(false)

p String.public_instance_methods   # 与下面得到的结果相同
p String.instance_methods

p String.protected_instance_methods # []
p String.private_instance_methods(false) # [:initialize, :initialize_copy]
```
回想一下，类或模块的类方法是Class或Module对象的单例方法。因此，要列出类方法，请使用Object.singleton_methods：
```ruby
class Foo2
   def self.test2

   end
end

p Foo2.singleton_methods # [:test2]

p Math.singleton_methods # [:atan2, :cos, :sin, :tan, :acos, :asin,...]
```
除了这些列出方法之外，Module类还定义一些谓词，以测试指定类或模块是否定义了命名实例方法：
```ruby
String.public_method_defined? :reverse    # true
String.protected_method_defined? :reverse  # false
String.private_method_defined? :initialize # true
String.method_defined? :upcase!             # true
```
Module.method_defined？检查命名方法是否定义为公共方法或受保护方法。它的作用与Object.respond_to？基本相同。在Ruby 1.9中，你可以传递false作为第二个参数，以指定不应考虑继承的方法。
#### 获取方法对象
要查询特定的命名方法，请在任何对象上调用method或在模块上调用instance_method。前者返回绑定到接收者的可调用方法对象，后者返回UnboundMethod。在Ruby 1.9中，可以通过调用public_method和public_instance_method将搜索范围限制为公共方法。我们在方法对象中介绍了这些方法及其返回的对象：
```ruby
mth = "as".method :upcase
p mth.call # AS

ubound_mth = String.instance_method :upcase
p ubound_mth.class # UnboundMethod
```
## 调用方法
如前所述，在“方法对象”中，可以使用任何对象的method方法来获取表示该对象的命名方法的“方法”对象。方法对象具有调用方法，就像Proc对象一样。你可以使用它来调用方法。
通常，使用send调用指定对象的命名方法更为简单：
```ruby
p "as".send :upcase # AS
p Math.send :sin, Math::PI/2 # 1.0
```
send在其接收方调用由其第一个参数命名的方法，并将所有剩余参数传递给该方法。名称“发送”源自面向对象的习惯用法，其中调用方法称为“向对象发送消息”。 send可以调用对象的任何命名方法，包括私有方法和受保护的方法。我们看到send曾被用来调用Module对象的私有方法remove_const。因为全局函数实际上是Object的私有方法，所以我们可以使用send在任何对象上调用这些方法（尽管这并不是我们实际上想要做的事情）：
```ruby
"Hello".send :puts, "world" # world
p Object.private_methods.grep /puts/ # [:puts]
```
Ruby 1.9定义public_send作为发送的替代方法。此方法的工作方式类似于send，但只会调用公共方法，而不会调用私有或受保护的方法：
```ruby
"hello".public_send :puts, "world" # error，因为puts是私有方法，无法调用
```
send是Object的一种非常基本的方法，但是它具有一个通用名称，该通用名称可能会在子类中被覆盖。因此，Ruby将__send__定义为同义词，并在尝试删除或重新定义__send__时发出警告。
## 定义，未定义，别名化方法
如果要定义类或模块的新实例方法，请使用define_method。 Module的此实例方法将新方法的名称（作为Symbol）作为其第一个参数。方法的主体由作为第二个参数传递的Method对象或由块提供。重要的是要了解define_method是私有的。你必须在要使用它的类或模块中才能调用它：
```ruby
add_string_mth(String, :kobe, &->{ print "hello world\n"} ) # 通过方法来动态给String类添加一个kobe实例方法
"hello world".kobe # hello world


class A
  def fred
    puts "In Fred"
  end
  def create_method(name, &block)
    self.class.define_method(name, &block)
  end
  define_method(:wilma) { puts "Charge it!" }
  define_method(:flint) {|name| puts "I'm #{name}!"}
end
class B < A
  define_method(:barney, instance_method(:fred))
end
a = B.new
a.barney
a.wilma
a.flint('Dino')
a.create_method(:betty) { p self }
a.betty
```
要使用define_method定义类方法（或任何单例方法），请在eigenclass上调用它：
```ruby
class CC; end

def add_class_method(c, m, &b)
   eigenclass = class << c
         self;
      end
   eigenclass.class_eval do
      define_method(m, &b)
   end
end

add_class_method(CC, :greet) { |name| "hello " + name }
p CC.greet("world")
```
在Ruby 1.9中，您可以更轻松地使用define_singleton_method，它是Object的一种方法：
```ruby
String.define_singleton_method(:greet) {|name| "Hello, " + name }
```
define_method的一个缺点是它不允许你指定需要块的方法主体。如果需要动态创建一个接受块的方法，则需要将def语句与class_eval一起使用。并且，如果要创建的方法具有足够的动态性，则可能无法将代码块传递给class_eval，而必须将方法定义指定为要评估的字符串。
但是，在动态编程时，有时需要使用alias_method代替。像define_method一样，alias_method是Module的私有方法。作为一种方法，它可以接受两个任意表达式作为其参数，而不需要在源代码中硬编码两个标识符。 （作为一种方法，它的参数之间也需要逗号。）alias_method通常用于别名链接现有方法。
```ruby
   n = :"#{prefix}_#{m}"
   c.class_eval do
      alias_method n, m
   end
end

backup(String, :reverse)
p "test".original_reverse
```
你可以使用undef语句来取消定义方法。只有当你可以在程序中将方法名称表示为硬编码标识符时，此方法才有效。如果需要动态删除已由程序计算出的方法的名字，则有两种选择：remove_method或undef_method。两者都是Module的私有方法。 remove_method从当前类中删除方法的定义。如果存在超类定义的版本，则该版本现在将被继承。 undef_method更严重；即使存在该方法的继承版本，它也可以防止通过类的实例调用指定的方法。
如果定义一个类并希望防止对其进行任何动态更改，则只需调用该类的freeze方法。一旦冻结，就不能更改类。
#### 处理未定义的方法
当方法名称解析算法未能找到方法时，它将查找名为method_missing的方法。调用此方法时，第一个参数是一个符号，它命名找不到的方法。该符号后跟将要传递给原始方法的所有参数。如果存在与方法调用关联的块，则该块也将传递给method_missing。
内核模块中method_missing的默认实现只是引发了NoMethodError。如果未捕获此异常，则会导致程序退出并显示错误消息，这是你尝试调用不存在的方法时通常会发生的错误消息。
为类定义自己的method_missing方法可让你有机会处理对类实例的任何类型的调用。 method_missing钩子是Ruby动态功能中最强大的功能之一，也是最常用的元编程技术之一。下面的示例代码向Hash类添加了method_missing方法。它允许我们查询或设置任何命名键的值，就好像该键是方法的名称一样：
```ruby

```