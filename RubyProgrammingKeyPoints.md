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
