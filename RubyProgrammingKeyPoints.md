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