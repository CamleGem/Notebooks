# Ruby学习笔记
***Author： TonyYang*** 

 > ## 换行符与 \
#### 如果想要将输出的字符串后面加上换行符，那么可以通过以下几种方式来达到：

#### 第一种方式：
```ruby

print("hello\n", "tony\n", "yang\n")  #=>
输出结果: hello
         world
         yang
                                    
```

#### 第二种方式：
```ruby

print("hello,
tony,
yang"
)

#=>
输出结果与第一种方式的结果基本一样，只不过逗号也会作为字符串的一部分被打印出来。
输出结果: hello,
         world,
         yang
```

#### 第三种方式:

```ruby

puts("hello", "tony", "yang") #=>
输出结果: hello
         world
         yang
```
#### 这里用到了一个新的函数puts，puts与print的区别在于print不会给字符串自动加上换行符，而puts会给逗号隔开的几个字符串自动加上换行符。

> ## 编码
#### 如果想要在代码中输入中文，需要指定相应平台的编码才能生效。
#### 在脚本的第一行需要添加一个魔符：

```ruby
“#encoding: 编码方式”
```
#### 通常情况下，不需要人为添加，默认会使用utf-8的编码方式
| 平台| 编码
|:---:|:---:|
|Windows| GKB|
|macOS | UTF-8|
|Linux | UTF-8 |    

>### **HASH**
#### Hash 有三种写法 

```ruby

song = {:title => "ParanoidAndroid", :artist => "Radiohead"}

person = {"名字" => "高桥", "拼音" => "gaoqiao"}

person2 = {name:"后藤", pinyin:"houteng"}

```

### 一些常见方法：
```ruby
to_i 这个方法可以将字符串转为整数
````

```ruby
read  一次性将文本内容读取出来
```

```ruby
each_line  一行一行读取文本内容
```

```ruby
Regex.new(...) 创建一个正则表达式文本对象
```

### 方法的定义
```ruby
 def 方法名
     希望执行的处理（代码）
 end
 
 例如 
 def hello
     puts("hello")
 end
 
 调用方法  hello() #=> 输出结果为hello字符串 
```

### 使用require 或者require_relative 来加载一个库文件

```ruby
在grep.rb文件里面定义一个方法：
def simple_grep(pattern, filename)
   file = File.open(filename)
   file.each_line do |line|
      if pattern =~  line
         print line
      end
   end
end

创建一个新的rb文件来加载这个文件：use_grep

require_relative 'grep'

pattern = Regexp.new("hello")
filename = 'hello_world'

simple_grep(pattern, filename)
#=> 输出结果为行里面带有hello字符串的行
```

require 与require_relative的区别在于，require会去加载默认的库文件，而require_relative可以去加载指定路径的文件

```ruby
例如现在去加载一个标准库文件，此时我们用require来加载
require 'date'
days = Date.today - Date.new(1990, 2, 24)
puts days.to_i
#=> 10965
```

```ruby
require 'pp'

books = [
   { title: "name", instance: "tonyyang" },
   { title: "age" , instance: 10 },
   { title: "color", instance: "blue"},
]

p books 
pp books

#=> p 的输出结果 
[{:title=>"name", :instance=>"tonyyang"}, {:title=>"age", :instance=>10},
{:title=>"color", :instance=>"blue"}]

#=> pp的输出结果
[{:title=>"name", :instance=>"tonyyang"},
 {:title=>"age", :instance=>10},
 {:title=>"color", :instance=>"blue"}]
 
 两者之间的区别是pp可以让输出的结果更加人性化，在使用嵌套的数据结构可以使用pp，这样更加直观。
```
> ### 对象 变量 常量 和 类

```ruby
在ruby中表现数据的基本单位称为对象。
```

**常见对象与其所属的类**

| 对象 |类  |
| :---: | :---: |
|  数值|Numeric  |
|  字符串| String |
|  散列| Hash |
|  正则表达式|Regex  |
|  文件| File |
|  符号| Symbol |
|  数组| Array |
**此外还有range和execption对象等**

**变量分为以下几种**：

* 全局变量:  **以$来表示**
* 实例变量:  **以@开头来表示**
* 类变量: **以@@开头来表示**
* 局部变量: **以英文字母或者_来表示**

**除了以上几种变量类型**
**还有伪变量类型（一种在ruby里面预先定义的变量类型）**
### Ruby中，nil、true、false、self等都是伪变量

```ruby
全局变量与局部变量示例
新建两个文件，一个为scopetest.rb, 一个为variable.rb
variable.rb内容
$x = 1
x = 1

scopetest.rb内容
$x = 200
require_relative 'sub'

x =  3
p $x
p x
结果为：1  3
```

```ruby
常量以大写字母命名
且一旦赋值以后就不在可以被更改，如果赋值会产生异常。
```

![241a71d649e6a558822d1991caa5d56b.png](evernotecid://69BB663E-820F-44E3-A5F8-778DC1D85277/appyinxiangcom/27571330/ENResource/p52)


#### 以上这张表格是ruby里面的保留字，所谓保留字就是提前定义在ruby里面的，因此我们在命名变量的时候不能以这些保留字来命名，会产生冲突. ####

### 条件判断表达式
* if
* case
* unless



| 布尔值 |具体类型  |
| :---: | :--- |
| 真 | 除nil和false以外的任何值都是真值 |
|  假|nil, false  |

### 逻辑运算符

**&& 和 || 和取反操作符 ！**

**以上三个操作符还有相对应的低优先级操作符形式 and、or、not**

```ruby
if 条件1 then  
处理1
elsif 条件2 then 
处理2
elsif 条件3 then  
处理3
else  
处理4
end

unless 条件 then 
处理
end

case 比较对象
when 值1 then
处理1
when 值2 then
处理2
when 值3 then 
处理3
else  
处理4
end

# then可以被省略，但是在写一行式的时候比较清楚
```

```ruby
case 示例

tags = %w{ A IMG PRE }
tags.each do |elem|
   case elem
   when "P", "A", "I", "B", "BLOCKQUOTE"
      puts "#{elem}"
   when "IMG", "BR"
      puts "#{elem}"
   else
      puts "#{elem}"
   end
end

其实在when进行比较时，调用了===的操作符来进行表较，除了进行常规的比较以外，还能进行
特殊的比较，例如：
p String === "ok"
p /ok/ === "ok"
p 11 === 11
p (1..3) === 2

#=> 
    true
    true
    true
    true
```

### 对象的同一性

#### 标识是用来表示对象同一性，ruby中所有对象都是唯一的，对象的id可以通过object_id或者__id__方法获得

```ruby
ary1 = []
ary2 = []
p ary1.object_id #=> 67653636
p ary2.__id__ #=> 67650432

同样是两个空数组，但是两个数组对象的id完全不一样，因此是完全不同的两个对象。

对象的值就是对象拥有的信息，只要两个字符串对象的内容相同，ruby就会认为两个对象的内容相等。使用==来判断对象的值是否相等。
ruby还可以使用 equal？方法来判断相等，==与equal？都由Object类提供

==与equal？也有一些不同的地方，示例如下
```
```ruby
p 1.0==1 #=> true
p 1.0.equal?(1) #=> false

实际的比较结果与预期不同，equal通常用在需要更加严谨的比较程序中。
0和0.0如果作为散列key来比较是不同的，底层调用的就是equal方法。
```

### 循环语句
* for 语句
* times 方法
* while 语句
* until 语句
* each 方法
* loop 方法

**..和...都是范围操作符，只不过三个点的范围操作符无法取到最大值边界**

**do end 和 { }都可以用来表示代码块**
```ruby
for 变量 in 开始时的数值..结束时的数值 do
希望循环的处理
end

其中do关键字可以被省略
```

```ruby
示例：
from = 10
to = 20
sum = 0

times的起始值为0，因此需要让它多执行一次循环

(to - from + 1).times do |var|
   sum += var + from
end

puts sum

for 循环不需要考虑让循环多执行一次
sum2 = 0
for i in from..to
   sum2 += i
end
puts sum2

#=> 结果都为165

在Ruby内部，for语句是用each方法来实现的。因此，可以使用each方法的对象，同样也可以指定为for语句的循环对象。

```

| 命令 |用途  |
| :---: | --- |
| break |  终止程序，跳出循环|
| next |  跳到下一次循环|
|  redo|在相同的条件下重复刚才的处理  |


```ruby
break的示例：

pattern = Regexp.new(ARGV[0])
filename = ARGV[1]
max_matches = 10
matches = 0
File.open(filename).each_line do |line|
   break if matches == max_matches
   matches += 1 if line =~ pattern
   puts line
end
```


|  |主要用途  |
| :---: | --- |
| times方法 |  确定了循环次数时使用|
|  for语句|  从对象中取出元素时使用（each语法糖）|
|  while语句| 希望自由指定循环条件时使用 |
|  until语句| 使用while语句使循环条件变得难懂时使用 |
|  each方法|  从对象中去除元素时使用|
|  loop方法|不限制循环次数时使用  |

**语法糖：是一种为了照顾一般人的习惯而产生的特殊语法1。例如，使用常规的语法调用方法，那么加法运算应该写成3.add(2)。但是对于一般人来说，写成3+2会更直观，更简明易懂。语法糖并不会加强编程语言的任何功能，但是对于提高程序的易读性来讲是不可或缺的**


## 方法调用

#### 对象.方法名(参数1,参数2,…,参数n)
#### 对象.方法名(参数,…) do |变量1,变量2,…| 块内容 end
#### 对象.方法名(参数,…) { |变量1,变量2,…| 块内容 }
#### **备注　使用do～end时，可以省略把参数列表括起来的()。使用{～}时，只有在没有参数的时候才可以省略()，有一个以上的参数时就不能省略。**


### 运算符形式的调用
#### 四则运算等二元运算符，-（负号）等一元运算符，指定数组，散列的元素下标的[]等，实际上都是方法调用
* obj + arg1
* obj =~ arg1
* -obj
* !obj
* obj[arg1]
* obj[arg1] = arg2
#### 这些虽然与一般的方法调用不一样，但是其实也是方法调用，有些可以自定义，有些则不能自定义
### 方法种类
* 实例方法 接收者为实例
* 类方法 接收者为类本身
* 函数式方法 没有接收者的方法
#### 类方法也有运算符形式
```ruby
Array[11, 22, 33, 44] # 创建一个数组
调用类方法时，可以使用::代替，这两者的意思都一样
```

```
我们来介绍一下Ruby帮助文档中方法名的标记方法。标记某个类的实例方法时，就像A
rray#each、Array#inject这样，可以采用以下标记方法：

类名#方法名

请注意，这只是写帮助文档或者说明时使用的标记方法，程序中这么写是会出错的。另外，类方法的标记方法，就像Array.new或者Array::new这样，可以采用下面两种写法。

类名.方法名
类名::方法名

这和实际的程序语法是一致的。
``` 

```ruby
对方法指定参数时，可以指定一个默认值，这样在传递参数时，可以选择不传递，也可以选择传递一个新的值
需要注意的是，直接省略左边的参数或者中间的参数是不可以的，希望省略其中两个参数时，可以指定右侧两个参数为默认值

def func (a, b = 1, c = 2)
   |
end

return 可以用return来返回一个函数，如果省略return的参数，则默认返回 nil
```

```ruby
定义一个带块的方法：

def myloop
   while true
      yield
   end
end

num = 1
myloop do
   puts "num is #{num}"
   break if num > 10
   num *= 2
end

本例的程序中没有参数，如果yield部分有参数，程序就会将其当作块变量传到块中。块中最后的表达式的值既是块的执行结果，同时也可以作为yield的返回值在块的外部使用

```
```ruby
1. 参数不确定的方法

def foo(*arg)
  p arg
end

foo(1, 2, 3) #=> [1, 2, 3]

2. 至少需要指定一个参数的方法
def meth(arg, *args)
 p [arg, *arg]
end

meth(1) #=> [1, []]

3. 只确定首个和最后一个参数名，并省略中间参数的情况

def a(a, *b, c)
p [a, b, c]
end

a(1, 2, 3) #=> [1, [2], 3]

3. 关键字参数

def方法名（参数1:参数1的值, 参数2:参数2的值, …)   
希望执行的处理
end
除了参数名外，使用“参数名:值”这样的形式还可以指定参数的默认值。用关键字参数改写计算立方体表面积的area方法的程序如下所示
def area2(x: 1, y: 2, z: 3)
   return x + y +  z
end

p area2
p area2(x: 2, y: 2, z: 3)

def area3(x:, y: 2, z: 3)
   return x + y + z
end

p area3(x: 1) 指定的默认参数可以省略，但是没有默认值的参数不能省略，必须给它赋值！
p are3(y: 3, z: 4) #=> ArgumentError 这里会报错，因为没有指定x的值

4. 关键参数与普通参数搭配使用

def func(a, b: 1, c: 2)
|
end
func(1, b: 2, c: 4)

5. 用散列传递参数
还是调用area3这个方法，但是此时，我们传递的参数是一个散列。
可以把散列作为关键字参数传递给方法，而这时散列的键只能用符号。虽然可以省略定义了默认值的关键字参数，但传递多余的键则会导致程序发生错误。在下面最后一个例子中，省略了关键字参数y。
```
```ruby
test_args = {x: 3, y: 5, z: 8}
p area3(test_args) #=> 16

test_args2 = {x: 4}
p area3(test_args2) #=> 因为area3关键字参数指定了默认值，因此我们可以只指定关键字x的值。✅

test_args3 = {:x => 10}
p area3(test_args3) #=> 这里用散列另外一种形式来作为参数赋值，一样可以正常运行，因为这里的key也是符号。✅

test_args3 = {"x" => 10}
p area3(test_args3) #=> 这里程序会运行错误，因为这里的散列的key是字符串对象，并不是符号对象，因此是错误的。❌

test_args4 = {x: 3, y: 5, z: 8, t: 20}
p area3(test_args4) #=> 错误❌， 这里多了关键字t，但是我们在函数里面并未指定t这个关键字参数，因此函数无法识别，会报错。

6. 把数组分解为参数
将参数传递给方法时，我们也可以先分解数组，然后再将分解后的数组元素作为参数传递给方法。在调用方法时，如果以“*数组”这样的形式指定参数，这时传递给方法的就不是数组本身，而是数组的各元素被依次传递给方法。但需要注意的是，数组的元素个数必须要和方法定义的参数个数一样。

def foo(a, b ,c)
   a + b + c
end

p foo(1, 2, 3)

args1 = [2, 3]
p foo(1, *args1) #=> 如果直接将args1参数传递给函数，而不带*前缀，那么函数无法识别这是一个数组，会报错

args2  = [1, 2, 3]
p foo(*args2) #=> 6 可以正确运行

其实为了更好的理解，可以转换一下，其实在进行数组赋值时，默认其实是执行了这样的操作：
a, b, c = 1，*args1 #=> 结果是a=1, b=2, c=1
换句话说，其实*号的作用其实是把args1这个数组的值展开，然后分别赋值给函数的参数。
这里有一个需要注意的地方：如果数组元素个数超过了函数定义的参数个数，函数会报错，无法正常执行。

7. 把散列作为参数传递

def test_hash_arg(arg)
   p arg
end

arg_hs = {"x" => 1, "y" => 2}
test_hash_arg(arg_hs) #=> {"x" => 1, "y" => 2}

def test_hash_arg2(arg1, arg2)
   p [arg1, arg2]
end

test_hash_arg2(1, arg_hs) #=> [1, {"x" => 1, "y" => 2}]
test_hash_arg2(1, x: 2, y: 3) #=> [1, {:x => 1, :y => 2}]
test_hash_arg2(1, :x=> 2, :y => 3) #=> [1, {:x => 1, :y => 2}]
test_hash_arg2(1, "x" => 2, "y" => 3) #=> [1, {"x" => 1, "y" => 2}]
test_hash_arg2(1, {"x" => 2, "y" => 3}) #=> [1, {"x" => 1, "y" => 2}]
在这里{}可以省略，第四种与第五种结果是一样的。

使用关键字参数定义方法，既可以对键进行限制，又可以定义参数的默认值。因此建议大家在实际编写程序时多尝试使用关键字参数。


```
## 类和模块

```ruby
当想知道某个对象属于哪个类时，我们可以使用class方法

ary = []
str = "hello world"
p ary.class #=> Array
p str.class #=> String

当想知道某个对象是否属于某个类时，我们可以使用instance_of?方法
ary=[]
str="Helloworld."
p ary.instance_of?(Array)  #=>true
p str.instance_of?(String) #=>true
p ary.instance_of?(String) #=>false
p str.instance_of?(Array)  #=>false

```
#### 继承
通过扩展已定义的类来创建新类称为继承。
继承后创建的新类称为子类（subclass），被继承的类称为父类2（superclass）。通过继承可以实现以下操作。

* 在不影响原有功能的前提下追加新功能
* 重新定义原有功能， 使名称相同的方法产生不同的效果
* 在已有功能的基础上追加处理，扩展已有功能
![371dc3a8c833053286ec95b1efa383cb.png](evernotecid://69BB663E-820F-44E3-A5F8-778DC1D85277/appyinxiangcom/27571330/ENResource/p53)

```ruby
BasicObject类是Ruby中所有类的父类，它定义了作为Ruby对象的最基本功能。备注BasicObject类是最最基础的类，甚至连一般对象需要的功能都没有定义。因此普通对象所需要的类一般都被定义为Object类。字符串、数组等都是Object类的子类。

子类与父类的关系称为“isa关系”3。例如，String类与它的父类Object就是isa关系。
str = "hello" ;
p str.is_a?(String) #=> true
p str.is_a?(Object) #=> true
p str.is_a?(BasicObject) #=> true
由于instance_of?方法与is_a?方法都已经在Object类中定义过了，因此普通的对象都可以使用这两个方法。

示例：创建一个类
class HelloWorld                   # class 语句

   def initialize(myname = "Ruby") # 类方法
      @name = myname               # 初始化实例变量
   end

   def hello                       # 实例方法
      puts "hello, world. I am #{@name}"
   end
end

bob = HelloWorld.new("Bob")
alice = HelloWorld.new("Alice")
ruby = HelloWorld.new("Ruby")
bob.hello
alice.hello
ruby.hello

class 类名  
类的定义
end

类名的首字母必须大写。

名为initialize的方法比较特别。使用new方法生成新的对象时，initialize方法会被调用，同时new方法的参数也会被原封不动地传给initialize方法。因此初始化对象时需要的处理一般都写在这个方法中。

而只要在同一个实例中，程序就可以超越方法定义，任意引用、修改实例变量的值。另外，引用未初始化的实例变量时的返回值为nil。
```
#### 存取器
```ruby
在Ruby中，从对象外部不能直接访问实例变量或对实例变量赋值，需要通过方法来访问对象的内部。
对类进行扩充，增加存取方法：

class HelloWorld                   # class 语句

   def initialize(myname = "Ruby") # 类方法
      @name = myname               # 初始化实例变量
   end

   def hello                       # 实例方法
      puts "hello, world. I am #{@name}"
   end

   def name=(name) # 存
      @name = name
   end
 
   def name       # 取
      return @name
   end
end

bob = HelloWorld.new("Bob")
bob.hello

bob.name =("kobe")
p bob.name
bob.name =("kobe") #=> 实例变量@name的值此时为kobe
p bob.name
```
#### 当对象越来越多时，需要定义的存取器越来越多，导致代码越来越复杂。因此ruby提供了存取器的快捷方式

| 定义 | 意义 |
| :---| --- |
|  attr_reader :name|  只读（定义name方法）|
|  attr_writer :name|  只写（定义 name=方法）|
|  attr_accessor :name|读写（定义以上两个方法）  |

```ruby
在实例方法中，可以用self这个特殊的变量来引用方法的接收者

class HelloWorld                   # class 语句

   attr_accessor :name
   def initialize(myname = "Ruby") # 类方法
      @name = myname               # 初始化实例变量
   end

   def greet
      puts "Hi, I am #{self.name}"
   end

   def greet_again
      puts "Hi, I am #{name}"
   end

bob = HelloWorld.new("Bob")
bob.greet #=> Hi, I am Bob
bob.greet_again #=> Hi, I am Bob
调用方法时，如果省略了接收者，Ruby就会默认把self作为该方法的接收者。因此，即使省略了self，也还是可以调用name方法

但是有一点需要注意：
在调用像name=方法这样的以=结束的方法时，即使实例方法中已经有了name="Ruby"这样的定义，但如果仅在方法内部定义名为name的局部变量，也不能以缺省接收者的方式调用name=方法。这种情况下，我们需要用self.name="Ruby"的形式来显式调用name方法。

备注　虽然self本身与局部变量形式相同，但由于它是引用对象本身时的保留字，因此即使对它进行赋值，也不会对其本身的值有任何影响。像这样，已经被系统使用且不能被我们自定义的变量名还有nil、true、false、__FILE__、__LINE__、__ENCODING__等。

```
#### 如何在已经定义的类中追加新的类方法？
```ruby
方法的接收者就是类本身（类对象）的方法称为类方法。

第一种：
class << 类名 ~ end 这个特殊的类定义中，以定义实例方法的形式来定义类方法。

class << HelloWorld
   def hello(name)
      puts "#{name} said hello"
   end
end

HelloWorld.hello("Ruby")

第二种：
在class语句中使用self时，引用的对象是该类本身，因此，我们可以使用class << self ~ end 这样的形式，在class语句中定义类方法。这种方式很常见

class HelloWorld
   class << self
      def greet(name)
         puts "#{name} said Hello"
      end
   end

end

HelloWorld.greet("Perl")

第三种：
我们还可以使用 def 类名.方法名 ~ end 这样的形式来定义类方法。
class HelloWorld
   def self.speak(name)
      puts "#{name} said hello"
   end
end

HelloWorld.speak("Matz")

备注 class << 类名 ~ end 这种写法的类定义称为单例类定义，单例类定义中定义的方法称为单例方法。

常量
常量就是指拥有固定值，且值一旦被定义就无法更改的变量，通常用大写字母来命名。
对于在类中定义的常量，我们可以像下面这样使用::通过类名来实现外部访问。

类变量
以@@开头的变量称为类变量。类变量是该类所有实例的共享变量，这一点与常量类似，不同的是我们可以多次修改类变量的值。另外，与实例变量一样，从类的外部访问类变量时也需要存取器。不过，由于attr_accessor等存取器都不能使用，因此需要直接定义。

class HelloCount
   @@count = 0

   def self.hello
      @@count += 1
      puts "hello #{@@count}"
   end

   def self.counts
      @@count
   end
end

HelloCount.hello
HelloCount.hello
HelloCount.hello
p HelloCount.counts

```

#### 限制方法的调用
##### 到目前为止，我们定义的方法都能作为实例方法被任意调用，但是有时候我们可能并不希望这样。例如，只是为了汇总多个方法的共同处理而定义的方法，一般不会公开给外部使用。

**Ruby提供了3种方法的访问级别，我们可以按照需要来灵活调整。**
* public...以实例方法的形式向外部公开该方法
* private...在指定接收者的情况下不能调用该方法（只能使用默认接收者的形式调用）
* protected...在同一个类中时可将该方法作为实例方法调用

```ruby
public 和 private 的例子

class AccTest
   def pub
      puts "pub is a public meth!"
   end

   public :pub

   def priv
      puts "priv is a private meth"
   end

   private :priv

end

acc = AccTest.new
acc.pub #=> 正常调用
acc.priv #=> 报错

希望统一定义多个方法的访问级别时，可以使用下面的语法。
class AccTest
   public
   
   def pub
      puts "pub is a public meth!"
   end
   
   private
   def priv
      puts "priv is a private meth"
   end
end

没有指定访问级别的放马默认为public，但initialize方法是列外，它通常被定义为private。

定义为protected的方法，在同一个类（及其子类）中可作为实例方法使用，而在除此以外的地方则无法使用。
示例：
class Point
   attr_accessor :x, :y
   protected :x=, :y=

   def initialize(x = 0.0, y = 0.0)
      @x, @y = x, y
   end

   def swap(other)
      tmp_x, tmp_y = @x, @y
      self.x, self.y = other.x, other.y
      other.x, other.y = tmp_x, tmp_y

      return self
   end
end

obj = Point.new
obj2 = Point.new(30, 40)

obj.swap(obj2)
p [obj.x, obj.y]
p [obj2.x, obj2.y]
p obj2.x=20 #=> 错误❌，因为方法不是public方法，无法在类外部调用
```
#### 给原有的类添加方法
```ruby
Ruby允许我们在已经定义好的类中添加方法。
class String
   def count_str
      ary = self.split(/\s+/)
      return ary.size
   end
end

str = "Just another ruby newbie"
p str.count_str #=> 4
```
#### 继承 
```ruby
class 类名 < 父类名
类定义
end

示例：重新定义[]运算符
其中super来调用父类中的[]方法
class RingArray < Array

   def [](i)
      idx = i % self.size
      super (idx)
   end
end

obj = RingArray[1, 2, 3, 4]
p obj[4]

定义类时没有指定父类的情况下，Ruby会默认该父类为Object类。
Object类提供了很多便于实际编程的方法，但在某些情况下希望使用更加轻量的类，
而这时就可以使用BasicObject类。
BasicObject 类只提供了作为Ruby对象的最低限度的方法。类对象调用 instance_methods 方法后，就会以符号的形式返回该类的实例方法列表。下面我们就用这个方法来对比一下Object类和BasicObject类的实例方法。
定义BasicObject的子类时，与Object类不同，需要明确指定BasicObject类为父类。

```
#### alias与undef
```ruby
有时我们会希望给已经存在的方法设置别名，这种情况下就需要使用alias方法。alias方法的参数为方法名或者符号名。

alias 别名 原名　　　# 直接使用方法名
alias :别名 :原名　　# 使用符号名

class C1

   def hello
      puts "hello"
   end
end

class C2 < C1
   alias old_hello hello
   def hello
      puts "hello again"
   end
end

class C2 < C1
   alias :old_hello :hello
   def hello
      puts "hello again"
   end
end

C2.new.hello
C2.new.old_hello

undef  方法名　　 # 直接使用方法名
undef  :方法名　　# 使用符号名例如，在子类中希望删除父类定义的方法时，可以使用undef。

```
#### 单例类

```ruby
在下面的例子中，我们分别将"Ruby"赋值给str1对象和str2对象，然后只对str1对象添加hello方法。这样一来，两个对象分别调用hello方法时，str1对象可以正常调用，但str2对象调用时程序就会发生错误。

class << str
   def hello
      puts self.size
   end
end

str2 = ""
str.hello #=> 5
str2.hello #=>  No method error

到目前为止，我们已经多次只在特定的某个类中添加类方法。Ruby中所有的类都是Class类的对象，因此，Class类的实例方法以及类对象中所添加的单例方法都是类方法。

```
#### 模块
模块是Ruby的特色功能之一。如果说类表现的是事物的实体（数据）及其行为（处理），那么模块表现的就只是事物的行为部分。模块与类有以下两点不同。

* 模块不能拥有实例
* 模块无法被继承、

**Mixin就是将模块混合到类中。在定义类时使用include，模块中的方法、常量就都能被类使用。**
* 虽然两个类拥有相似的功能，但是不希望把它们作为相同的种类（Class）来考虑
* Ruby不支持父类的多重继承，因此无法对已经继承的类添加共通的功能

```ruby
module MyMoudle
   def hello
      puts "hello I am Moudle"
   end
end

class MyClass1
   include MyMoudle
end

class MyClass2
   include MyMoudle
end

MyClass1.new.hello
MyClass2.new.hello

模块可以提供独立的命名空间
所谓命名空间（namespace），就是对方法、常量、类等名称进行区分及管理的单位。由于模块提供各自独立的命名空间，因此A模块中的foo方法与B模块中的foo方法就会被程序认为是两个不同的方法。同样，A模块中的FOO常量与B模块的FOO常量也是两个不同的常量。
例如，在FileTest模块中存在与获取文件信息相关的方法。我们使用“模块名.方法名”的形式来调用在模块中定义的方法，这样的方法称为模块函数。

p FileTest.exist?('case.rb')
p FileTest.size("case.rb")
p Math::PI
p Math.sqrt(2)

如果没有定义与模块内的方法、常量等同名的名称，那么引用时就可以省略模块名。通过include可以把模块内的方法名、常量名合并到当前的命名空间。下面是刚才提到的Math模块的例子。

我们使用module语句来创建模块。语法与创建类时几乎相同，模块名的首字母必须大写。
module 模块名  
    模块定义
end

module HelloModule
   Version = "1.0"

   def hello(name)
      puts "Hello, #{name}."
   end

   module_function :hello
end

p HelloModule::Version
HelloModule.hello("Ruby")
HelloModule.__send__(:hello, "Perl")
备注：如果只是定义了方法，但是并没有通过 module_function symbol 方法将模块函数公开给外部使用的话，无法通过模块名.方法名的形式调用。 一旦通过module_function symbol 公开方法以后，就能以这种方法调用方法。

以“模块名.方法名”的形式调用时，如果在方法中调用self（接收者），就会获得该模块的对象。
def myself
      self
   end
p HelloModule.myself #=> HelloModule 模块名

Mix-in
Note: 关于Mix-in
当我们想要知道一个类的继承关系时可以使用xxx.ancestors 来获取
p Regexp.ancestors #=> [Regexp, Object, Kernel, BasicObject]

当我们想要知道一个类的父类可以用 Regexp.superclass方法来获取


假设有个类C, 类C的实例在调用方法时，Ruby会按类C、模块M、类C的父类Object这个顺序查找该方法，并执行第一个找到的方法。被包含的模块的作用就类似于虚拟的父类。
虽然Ruby采用的是不允许具有多个父类的单一继承模型，但是通过利用Mixin，就既可以保持单一继承的关系，又可以同时让多个类共享功能。
单一继承的优点就是简单，不会因为过多的继承而导致类之间的关系变得复杂。但是另一方面，有时我们又会希望更加积极地重用已有的类，或者把多个类的特性合并为更高级的类，在那样的情况下，灵活使用单一继承和Mixin，既能使类结构简单易懂，又能灵活地应对各种需求。

查找方法的规则
1.同继承关系一样，原类中已经定义了同名的方法时，优先使用该方法。
2.在同一个类中包含多个模块时，优先使用最后一个包含的模块。
3.嵌套include时，查找顺序也是线性的.
4.相同的模块被包含两次以上时，第二次以后的会被省略。


extend方法
extend方法可以使单例类包含模块，并把模块的功能扩展到对象中。
module A
   def name
      puts "I am A"
   end
end

str = "Hello"
str.extend(A)
str.name

class V

end

V.extend(A)
V.name
extend既可以用来扩展实例方法，也可以扩展类方法。见上面的示例。



module A
   def name
      puts "I am A"
   end
end

module B
   def name
      puts "I am B"
   end
end

module C
   include B
   def name
      puts "I am C"
   end
end

class TestModule
   include C
   include A
end

p TestModule.ancestors #=> [TestModule, A, C, B, Object, Kernel, BasicObject]

class TestModule
   include C, A
end

p TestModule.ancestors #=> [TestModule, C, B, A, Object, Kernel, BasicObject]
注意这里导入模块时，第一种方式是分行，第二种是用逗号隔开，先后顺序有区别，参考结果。
逗号之前的模块会被优先查找，而分行形式，最后倒入的模块会被优先查找。



如果类 Mix-in 了模块，就相当于为该类添加了实例方法，这种情况下self代表的就是被mix-in的类的对象。

即使是相同的方法，在不同的上下文调用时，其含义也会不一样，因此对于Mixin的模块，我们要注意根据实际情况判断是否使用模块函数功能。一般不建议在定义为模块函数的方法中使用self。

除了之前介绍的定义类方法的语法外，使用 extend 方法也同样能为类对象追加类方法。下面是使用 extend 方法追加类方法，并使用 include 方法追加实例方法的一个例子。

module ClassMethod
   def cmethod
      "class method"
   end
end

module InstanceMethods
   def imethod
      "instance method"
   end
end

class MyClass
   extend ClassMethod
   include InstanceMethods
end


p MyClass.cmethod
p MyClass.new.imethod

在这里，extend 与 include的区别在于 extend 用于扩展类方法，而include是扩展实例方法，这个不能搞错。
其实不管是类也好，实例也好，本质上都是对象，因为对于Class类来说，String等类，都是它的实例，因此类本质上也是对象，因此在调用方法时，只是为了区分接收者的对象类型，才有类方法和实例方法之区别。

```
#### 面向对象的语言中的“对象”就是指数据（或者说数据的集合）及操作该数据的方法的组合。

#### 操作符
##### 操作符每个语言基本上都差不多，因此不做过多叙述。只记录一些比较特别的操作符。
```ruby

我们可以使用 “对象&.方法” 的写法进一步简化代码，这种写法又叫作安全运算符（safenavigationoperato）或nil检查方法调用。仅当ary的值不为nil时才调用first方法，ary为nil时表达式则返回nil。

item = ary&.first
item = ary && ary.first

以上两种形式都是等价的，意思是当ary不是nil值得时候，继续执行它的方法first来获得它的第一个值，如果ary为假，那么不会继续执行后面的表达式。
```

#### 范围运算符
```ruby
在Ruby中有表示数值范围的范围（range）对象。例如，我们可以像下面这样生成表示1到10的范围对象。

Range.new(1, 10)
1..10
以上两种形式是等价的

范围操作符有两种 ..  ... ，这个在之前已经提到过，前者能取到值边界，而后者取不到最大值边界。
to_a 方法可以将范围对象转为数组对象

p (1..5).to_a #=> [1, 2, 3, 4, 5]
p ("a".."c").to_a #=> [a, b, c]
p(1...5).to_a #=> [1, 2, 3, 4]

在Range对象内部，可以使用succ方法根据起点值逐个生成接下来的值。具体来说就是，对succ方法的返回值调用succ方法，然后对该返回值再调用succ方法……直到得到的值比终点值大时才结束处理。
str = "a"
p str.succ #=> "b"


Ruby的运算符大多都是作为实例方法提供给我们使用的，因此我们可以很方便地定义或者重定义运算符，改变其原有的含义

::  && || .. ...  ?: not = and or 这些操作符无法被重新定义。


class Points

   attr_accessor :x, :y

   def initialize(x = 0, y = 0)
      @x, @y = x, y
   end

   def inspect
      "(#{x}, #{y})"
   end

   def +(other)
      self.class.new(x + other.x, y + other.y)
   end

   def -(other)
      self.class.new(x - other.x, y - other.y)
   end
end

points0 = Points.new(3, 6)
points1 = Points.new(1, 8)

p points0 - points1
p points0 + points1
p points1.inspect

String#to_s 的返回结果与原字符串相同，但  String#inspect 的返回结果中却包含了\"。这是因为p方法在输出字符串时，为了让我们更明确地知道输出的结果就是字符串而进行了相应的处理。这两个方法的区别在于，作为程序运行结果输出时用to_s方法，给程序员确认程序状态、调查对象内部信息等时用inspect方法。除了puts方法、print方法外，to_s方法还被广泛应用在Array#join方法等内部需要进行字符串处理的方法中。inspect方法可以说是主要使用p方法进行输出的方法

一元运算符
可定义的一元运算符有+、、~、!四个。它们分别以+@、@、~@、!@为方法名进行方法的定义。下面就让我们试试在Point类中定义这几个运算符。这里需要注意的是，一元运算符都是没有参数的。

def +@
      dup
   end
   
   def -@
      self.class.new(-x, -y)
   end
   
   def ~@
      self.class.new(-y, x)
      end
在刚才的类中加入以上这三个方法
obj = Points.new(3, 6)
p +obj  #=> (3, 6)
p -obj  #=> (-3, -6)
p ~obj  #=> (-6, 3)

下标方法

数组、散列中的obj[i]以及obj[i]=x这样的方法，称为下标方法。定义下标方法时的方法名分别为[]和[]=。在代码清单9.3中，我们将会定义Point类的实例point的下标方法，实现以point[0]的形式访问point.x，以point[1]的形式访问point.y。

class Pointing

   attr_accessor :x, :y
   def initialize(x = 0, y = 0)
      @x, @y = x, y
   end

   def [](index)
      case index
      when 0
         x
      when 1
         y
      else
         raise ArgumentError, "out of range `#{index}'"
      end
   end

   def []=(index, val)
      case index
      when 0
         self.x = val
      when 1
         self.y = val
      else
         raise ArgumentError, "out of range `#{index}'"
      end
   end
end

point = Pointing.new(3, 6)
p point[0]
p point[1]
p point[2] #=> 会引发异常

p point[1] = 10
p point[0] = 20
```
#### 错误与异常处理

##### 常见的错误有以下几种：
* 数据错误
* 系统错误
* 程序错误
#### 对错误的处理有以下几种：
* 排除错误的原因
* 忽略错误
* 回复错误发生前的状态
* 重试一次
* 终止程序
##### 根据实际情况来继续选择何种的错误处理。
#### 异常处理
##### 程序在发生错误时，会暂时停止运行，并寻找是否有队友的异常处理程序。如果有则执行，如果没有，程序就会显示默认的错误信息。
#### 异常处理的写法
```ruby
Ruby中使用 begin～rescue～end 语句描述异常处理。

在Ruby中，异常及其相关信息都是被作为对象来处理的。在rescue后指定变量名，可以获得异常对象。

begin
可能会发生异常的处理
rescue => 引用异常对象的变量
发送异常时的处理
end
即使不指定变量名，ruby也会把异常对象赋值给$!。不过，变量名明确写出来会使程序更加易懂。
```


| 变量 |意义  |
| --- | --- |
| $! |  最后发生的异常（异常对象）|
|  $@|最后发生的异常的位置信息  |


| 方法名 |  意义|
| --- | --- |
| class |异常的种类  |
|  message|异常信息  |
|  backtrace|异常发生的位置信息（$@与$!.backtrace是等价的）  |


```ruby
#模拟wc程序

lines_count = 0
word_count = 0
char_count = 0

ARGV.each do |filename|
   l = 0
   w = 0
   c = 0
   begin
      input = File.open(filename)
      input.each_line do |line|
         l += 1
         c += line.size
         line.sub!(/^\s+/, "")
         w += line.split(/\s+/).size
      end
      input.close
      printf("%8d %8d 8%d %s\n", l, w, c, filename)
      lines_count += l
      word_count += w
      char_count += c
   rescue => ex
      puts ex.message
   end
   printf("%8d %8d 8%d %s\n", lines_count, word_count, char_count, "total_count")
end

按照正常的情况，如果不加异常处理，程序一旦在碰到异常以后，就会终止程序，如果有异常处理，那么程序会跳转到异常处理部分进行处理，然后继续执行后面的程序。

不管是否发生异常都希望执行的处理，在Ruby中可以用ensure关键字来定义。

#copy一个文件的内容到另外一个文件
这里用了ensure来收尾工作，如果程序

def copy (from, to)
   input = File.open(from)
   begin
   content = input.read
   output = File.open(to, "w")
   output.write(content)
   outout.close
   ensure 
      input.close
   end 
end

在rescue中使用retry后，begin以下的处理会再重做一遍.

rescue 修饰符 
与if修饰符、unless修饰符一样，rescue也有对应的修饰符。表达式1 rescue 表达式2 如果表达式1中发生异常，表达式2的值就会成为整体表达式的值。也就是说，上面的写法与下面的写法是等价的。

back = Integer("agc") rescue 0
p back
当前面一个表达式出现异常时，则执行表达式2，此时会返回表达式2的值0

```
#### 异常补充语法
```ruby
如果异常处理的范围是方法的整个处理，也就是说整个方法内的程序都用begin～end括起来的话，就可以省略begin以及end，直接书写rescue与ensure部分的程序。

def foo
正常处理
rescue
异常处理
ensure
后处理
end

同样，我们在类定义中也可以使用rescue以及ensure。但是，如果类定义途中发生异常，那么异常发生位置之后的方法定义就不会再执行了，因此一般我们不会在类定义中使用它们。

class Foo
类定义
rescue
异常处理
ensure
后处理
end

指定需要捕捉的异常
begin
可能发生异常的处理
rescue Exception1， Exception2 => 变量
对E1, E2 的处理
rescue Exception3 => 变量
对E3的处理
rescue
对上述异常以外的异常的处理
end

通过直接指定异常类，可以只捕捉我们希望处理的异常。

file = ARGV[0]
file2 = ARGV[1]

begin
   fl1 = File.open(file)
rescue Errno::ENOENT, Errno::EACCES
   fl2 = File.open(file2)
   puts fl2.read
end

Exception类的子类，并根据程序错误的种类来定义相应的异常。

在rescue中指定的异常的种类实际上就是异常类的类名。rescue中不指定异常类时，程序会默认捕捉StandardError类及其子类的异常。

MyError = Class.new(StandardError)
MyErro1 = Class.new(MyError)
MyErro2 = Class.new(MyError)
以上这种写法是，创建一个继承StandError的子类，然后继续创建MyError的子类

class MyError < StandError
.....
end      #=> 等价于上面的写法，但是上面的写法更加简洁


使用class语句可以进行定义方法等操作，但在本例中，由于我们只需要生成继承StandardError类的新类就可以了，所以就向大家介绍了这个只需1行代码就能实现类的定义的简洁写法。
```
#### 抛出异常
```ruby
使用raise方法可以使程序主动抛出异常。在基于自己判定的条件抛出异常，或者把刚捕捉到的异常再次抛出并通知调用者等情况下，我们会使用raise方法。

```
#### 块

```ruby
块（block）就是在调用方法时能与参数一起传递的多个处理的集合。之前在介绍each方法、time方法等与循环有关的部分时，我们就已经接触过块。接收块的方法会执行必要次数的块。块的执行次数由方法本身决定，因此无需事前指定，甚至有可能一次都不执行。

对象.方法名(参数列表) do|块变量| 
希望循环的处理
end
或者 对象.方法名(参数列表){ |块变量|   希望循环的处理 }

块变量就是在执行块时，从方法传进来的参数。不同方法的块变量个数也不相同。例如，在Array.each方法中，数组的元素会作为块变量被逐个传递到块中。而在Array.each_with_index方法中，则是[元素,索引]两个值被传递到块中。

sum = 0
outcome = {
   "参加费" => 1000,
   "挂件费用" => 1000,
   "联欢会费用" => 4000,
}

outcome.each do |pair|
   sum += pair[1]
end

sum2 = 0
outcome2 = {
   "参加费" => 1000,
   "挂件费用" => 1000,
   "联欢会费用" => 4000,
}

outcome2.each do |key, value|
   sum2 += value
end

p sum
p sum2

以上两种方式结果都一样，第一种方法指定了一个块变量，那么每次换递给块变量的值是以键值对数组。


除了逐行循环读取数据的 each_line 方法外，File对象中还有以字符为单位来循环读取数据的 each_char 方法，以及以字节为单位进行循环读取的 each_byte 方法等。而其他对象也有很多以each_××命名的循环读取数据的方法。

隐藏常规处理：
file = File.open('files/hello_world')
file.each_line do |line|
   print line
end
file.close

File.open('files/hello_world') do |file2|
   file2.each_line do |line|
      print line
   end
end

```

**与改写之前的程序相比，File对象读取数据的部分一样，不同点在于没有了最后的close方法的调用。如果使用完打开的文件后没有将文件关闭，就可能会产生其他程序无法打开该文件，或者到达一次性可打开的文件数的上限时无法再打开新文件等问题。
在File.open方法中使用块时，在块内部的处理完毕并跳出方法前，文件会被自动关闭，因此就不需要像代码清单11.3那样使用File#close方法。文件使用完毕后，由方法执行关闭操作，而我们只需将必要的处理记述在块中即可。这样一来可以减少程序的代码量，二来可以防止忘记关闭文件等错误的发生。**

```ruby
def myloop
   while true
      yield
   end
end

num = 1
myloop do
   puts "num is #{num}"
   break if num > 10
   num *= 2
end

yield关键字的作用就是执行方法的块，在块中调用break就可以随时终止方法执行。
def total (from, to)
   total = 0
   from.upto(to) do |num|
      if  block_given?
         total += yield(num)
      else
         total += num
      end
   end
   return total
end

p total(1, 20)
p total(1, 20) { |num| num ** 2 }

对yield传递1个参数，就有1个块变量接收。

def block_args_test
   yield
   yield(1)
   yield(1, 2, 3)
end
block_args_test do |var1, var2, var3|
   p [var1, var2, var3]
end

当yield的参数与块变量的个数不一致时，多出来的变量会被赋值为nil，而缺少的变量则不会被赋值，
可以使用*arg的形式来满足任意数量的yield参数个数。

控制块的执行

break
见示例：
def foo(*args)
   sum = 0
   args.each do |num|
      sum += yield(num)
   end
   return sum
en

p foo(1, 2, 3, 4, 5, 6) { |num| break 0 if num == 5; num ** 2 }
在块中使用break，程序会马上返回到调用块的地方，因此foo方法中返回计算结果的处理等都会被忽略掉。
方法实际返回的是break方法的参数，当没有指定参数时，值为nil，如果指定了值，则方法返回的值就是指定的值。

next
见示例
def total (from, to)
   total = 0
   from.upto(to) do |num|
      if  block_given?
         total += yield(num)
      else
         total += num
      end
   end
   return total
end

result = total(1, 10) do |num|
   if num % 2 != 0
      next 0
   end
   num
end
p result

next 方法的返回值会作为块的返回值返回给函数，做进一步处理，该方法会将奇数过滤掉，而只计算偶数的和，
这里next方法的参数为0，因此奇数部分每次都返回0，实际计算的过程是 0 + 2 + 0 + 4 + 0 + 6 + 0 + 8 + 0 + 10 = 30， 如果next的值不是0，则实际计算值又会不一样。

redo

如果在块中使用了redo，程序就会返回到块的开头，并以相同的块变量再次执行处理。这种情况下，块的处理结果不会返回给外部，因此需要十分小心redo的用法，注意不要使程序陷入死循环。

块对象
Ruby还能把块当作对象处理。把块当作对象处理后，就可以在接收块的方法之外的其他地方执行块，或者把块交给其他方法执行。把块当作对象操作时，我们需要用到Proc对象。定义Proc对象的典型的方法是，调用Proc.new方法这个带块的方法。在调用Proc对象的call方法之前，块中定义的程序不会被执行。
见示例
block = Proc.new do |name|
   puts "Hello. #{name}"
end

block.call("World")
block.call("Ruby")
通过Proc.new来得到一个块对象，然后在调用call方法来执行亏对象的代码。


把块从一个方法传给另一个方法时，首先会通过变量将块作为Proc对象接收，然后再传给另一个方法。在方法定义时，如果末尾的参数使用“&参数名”的形式，Ruby就会自动把调用方法时传进来的块封装为Proc对象。

def total(from, to, &block)
   sum = 0
   from.upto(to) do |num|
      if block
         sum += block.call(num)
      else
         sum += num
      end
   end
   return sum
end

p total(1, 10) { |num| num }
p (1..10).sum #=> 做个类比
在首行的方法定义中定义了&block参数。像这样，在变量名前添加&的参数称为Proc参数。如果在调用方法时没有传递块，Proc参数的值就为nil，因此通过这个值就可以判断出是否有块被传入方法中。另外，执行块的语句不是yield，而是block.call(num)，这一点与之前的例子也不一样。

将块封装为Proc对象后，我们就可以根据需要随时调用块，甚至还可以将其赋值给实例变量，让别的实例方法去任意调用。
我们也能将Proc对象作为块传给其他方法处理。只要在调用方法时，用&Proc对象的形式定义参数就可以了。

def call_each(ary, &block)
   ary.each(&block)
end

call_each([1, 2, 3, 4, 5]) do |item|
   p item
end

局部变量与块变量

块内部的命名空间与块外部是共享的。在块外部定义的局部变量，在块中也可以继续使用。而被作为块变量使用的变量，即使与块外部的变量同名，Ruby也会认为它们是两个不同的变量。

x = 10
y = 0
(1..6).each do |x|
   y = x
end

p [x, y]
局部变量x的值依然是原来的值，而y因为在块内部也是可见的，所以值不再是0而是6.

另外块中的变量在外部是无法访问的。
块变量是只能在块内部使用的变量（块局部变量），不能覆盖外部的局部变量，但Ruby为我们提供了定义块变量以外的块局部变量的语法，即通过在块变量后加上;来定义。

```
#### 排序
##### 将数组等中的元素按照一定的顺序重新排列的过程，我们称为“排序”。使用<=>运算符比较字符串时，会按照字符编码的值的大小进行比较。比较字母时，会按先大写字母后小写字母的顺序排列。

```ruby
ary = Array[22, 12, 3, 4, 5]
p ary.sort

p ary.sort { |a, b| a <=> b }
p ary.sort { |a, b| b <=> a }


strings = %w{
Ruby is a open source programming language. It is
created by matz.It is so graceful and flexible. I love
it very much!
}

p strings.sort
p strings.sort{ |a, b| a <=> b }

p strings.sort{ |a, b| a.length <=> b.length }
p strings.sort_by{ |string| string.length }
sort 与sort_by比较的话，后者的排序效率更高，因为调用length的次数跟字符串个数一样多，而前者每次都要调用两次，因此效率不及后者。
```
##### 在这里，我们需要注意块中最后一个表达式的值就是块的执行结果，因此<=>运算符必须在最后一行使用。备注　块的最后一个表达式不是指块的最后一行表达式，而是指在块中最后执行的表达式。


#### 回看数组和哈希
##### 数组
```ruby
创建数组的方法
直接创建 [ ]
ary = [1, 2, 3, 4]
ary2 = Array.new(5) #=> 创建一个元素个数为5的数组，且元素为nil, [nil, nil, nil, nil, nil]
ary3 = Array.new #=> 创建个数为0的数组，也就是空数组
ary4 = Array.new(5,0) #=> 创建元素为0，长度为5的数组 [0, 0, 0, 0, 0]
ary5 = Array[1, 2, 3] #=> [1, 2, 3]

创建字符串数组与符号数组
%w 和 %i
lang = %w(ruby perl python scheme pike rebol)
p lang #=> ["Ruby", "Perl", "Python", "Scheme", "Pike", "rebol"]

lang = %i(ruby perl python scheme pike rebol)
p lang #=> [:ruby, :perl, :python, :scheme, :pike, :rebol]

to_a 方法
color_table = {black: "#000000", white: "FFFFFF"}
p color_table.to_a #=> [[:black, "#000000"],
                      [:white, "#FFFFFF"]]
hash = Hash("one" => 1,"two" => 2)
p hash
p hash.to_a #=> [["one", 1], ["two", 2]]
对散列对象使用to_a方法，就会得到相应的数组的数组。具体来说，将散列的各jian，值作为一个数组，然后再把这样的数组放到一个大数组中。  

split方法
用split来分割一长串字符串
p "hello world".split(" ") #=> [hello, world]

数组索引
1.  a[n]
2. a[n..m]
3. a[n, len]

如果指定的索引值大于元素个数，则返回nil。
用法（2）的a[n..m]表示获取从a[n]到a[m]的元素，重新排列后创建新数组并返回。a[n...m]表示获取从a[n]到a[m1]的元素，重新排列后创建新数组并返回。虽然下面的例子中只讨论[n..m]的形式，但能用[n..m]的地方同样能用[n...m]。
如果m的值比数组长度大，则返回的结果与指定数组最后一个元素时是一样的

array = [33, 4, 5]
p array[0..2]
p array[0...2]
p array[0, 2]
p array[-1]
p array[-2..-1]

a.at(n)……与a[n]等价
a.slice(n)……与a[n]等价
a.slice(n..m)……与a[n..m]等价
a.slice(n,len)……与a[n,len]等价
不过一般情况下我们很少会使用上述方法。

以上的方法只能获取连续的元素，想要获取独立的元素需要使用别的方法：
a.values_at(n1, n2, ...)
p [1, 2, 3, 4, 5, 6].values_at(0, 2)

集合 
例如，我们可以把数组当成集合，这样一来，Array类中的各元素就变了集合中的元素。然而，由于集合没有顺序的概念，因此["a","b","c"]、["b","c","a"]、["c","b","a"]就都可以被认为是同一个集合。
集合的基本运算分为交集和并集。
1. 取出同时属于两个集合的元素，并创建新的集合
2. 取出两个集合中的所有元素，并创建新的集合
我们把第1种集合称为交集，第2种集合称为并集。

ary = ary1 & ary2 #=> 交集
ary = ary1 | ary2 #=> 并集

集合还有另外一种运算——补集，即获取某个集合中不属于另外一个集合的元素。但是Array类的情况下，由于没有全集的概念，因此也就没有补集。不过Array类有把某个集合中属于另外一个集合的元素删除的差运算

ary1 = ["a", "b", "c", "d"]
ary2 = ["a", "b", "e", "f"]
p ary1 | ary2 # 并集 %w{a b c d e f}
p ary1 & ary2 # 交集 %w{a b}
p ary1 - ary2 #=> %w{c d}
p ary1 + ary2 #=> %w{a b c d a b e f}

+ 与 | 的区别在于 +会把两个数组的元素合并到一起，不会去重，而|会把两个数组的元素合并到一起，并且会去重。
减号的作用是会把一起属于数组1和数组2的元素删掉，留下属于数组1的元素。

```
#### 队列与栈
简单地说，按A、B、C的顺序保存数据时，按照A、B、C的顺序获取数据的数据结构就是队列，按照C、B、A的顺序获取数据的数据结构就是栈。队列与栈都是比较复杂的数据结构，同时也是提高程序运行效率所不可欠缺的工具。
在数组的开头或末尾插入元素，或者从数组的开头或末尾获取元素等操作，是实现队列、栈等数据结构的前提条件。


|  |  对数组开头的元素操作| 对数组末尾的元素操作 |
| --- | --- | --- |
| 追加元素 |  unshift|  push|
|  删除元素|  shift| pop  |
|  引用元素| first |last  |
```ruby
arrays = Array[1, 2, 3, 4]
p arrays.first
p arrays.last
p arrays #=> 数组没有改变
p arrays.push(6)
p arrays.unshift(11)
p arrays #=> 数组发生了改变
除了push，也可以使用 << 效果与push完全一样，在数组末尾添加元素。

a.concat(b)
a + b
这两种方法的区别在于，前者是破坏性的，也就是说它会改变数组a的实际值，而后者是返回一个新的数组，
不会对数组a和数组b值造成影响。
见示例
ary1 = [1, 2, 3]
ary2 = [4, 5]

#第一种情况，数组的值没有被改变
p ary1 + ary2
p ary1
p ary2

#第二种情况数组的值发生了改变
p ary1.concat(ary2)
p ary1
p ary2

输出结果：
[1, 2, 3, 4, 5]
[1, 2, 3]
[4, 5]
[1, 2, 3, 4, 5]
[1, 2, 3, 4, 5]
[4, 5]

ary3 = [1, 2, 3]
ary3[0..2] = 0
p ary3 #=> [0]
这里会将索引为0，1，2的元素的值替换为一个0值，而不是将每个值替换为0值，这里需要注意。

```
#### 破坏性方法
具有破坏性的方法像pop方法、shift方法那样，会改变作为接收者的对象的值的方法称为具有破坏性的方法。在使用具有破坏性的方法时需要特别小心，因为当有变量也引用了接收者对象时，如果接收者对象值发生了改变，变量值也会随之发生变化。我们来看看下面的例子。
```ruby
a = [1, 2, 3]
b = a
p a.pop #=> [1, 2]
p b #=> [1, 2]
这里b的值也会发生改变，因为在ruby里面一切皆对象，任何值都是值对象引用，因此将一个变量的值赋值给另一个新的变量，那么这两个变量都指向这个值的地址空间，因此一旦一个变量变了，另外一个变量也会跟着改变。

我们可以使 Object 的 freeze 方法禁止具有破坏性的方法修改对象内容。调用这个方法后，对象就会被冻结，如果其内容被变更了，就会抛出错误。虽然冻结后对象无法解冻，但可以使用dup方法，得到未冻结的对象副本。

ary1 = [1, 2, 3]
ary1.freeze
ary2 = ary1.dup
ary2[0] = 11
p ary2
p ary1

从数组中删除元素
注意点：在ruby里面带！的方法都为破坏性方法，会改变原来接受者对象的值，而有些不带！的也具有破坏性，
例如pop，push等
a.compact
a.compact! #=> 删除数组中元素为nil的值
从数组a中删除所有nil元素。compact方法会返回新的数组，compact!则直接替换原来的数组。compact!方法返回的是删除nil元素后的a，但是如果什么都没有删除的话就会返回nil。

a.delete(x)
a = [1, 2, 2, 3]
a.delete(2)
p a #=> [1, 3] 删除所有值为2的元素

a.delete_at(n)
a = [1,2,3,4,5]
a.delete_at(2)
p a #=>[1,2,4,5] 删除索引为2的元素

a.delete_if{|item| ...}
ary2 = [11, 2, 2, 3]
p ary2.delete_if{|var| var < 3}
p ary2 #=> [11, 3]
delete_if和reject!方法都是具有破坏性的方法。

a.reject{|item| ...}
a.reject!{|item| ...}
a.slice!(n)
a.slice!(n..m)
a.slice!(n,len)
a.uniq
a.uniq!

替换数组元素
a.collect{|item|…}
a.collect!{|item|…}
a.map{|item|…}
a.map!{|item|…}

a.fill(value)
a.fill(value, begin)
a.fill(value, begin, len)
a.fill(value, n..m)

将数组a的元素替换为value。只有1个参数时，数组a的所有元素值都会变为value。有2个参数时，从begin到数组末尾的元素值都会变为value。有3个参数时，从begin开始的len个元素的值会变为value。另外，当第2个参数指定为[n..m]时，则指定范围内的元素值都会变为value。

a.flatten
a.flatten!
扁平化数组，将嵌套的数组合并为一个数组。

a.reverse
a.reverse!

a.sort
a.sort!
a.sort{|i, j| ...}
a.sort!{|i, j| ...}
a.sort_by{|i| ...}

数组与迭代器
对数组的各元素进行相同的操作时使用的each方法就是典型的迭代器。该方法会遍历数组的所有元素，并对其进行特定的处理。
此外，接收者不是数组时，为了让迭代器的执行结果能作为某个对象返回，也会用到数组。collect方法就是其中一个具有代表性的方法。collect方法会收集某种处理的结果，并将其合并为一个数组后返回。
a = 1..6
b = a.collect { |var| var + 2 }
p b #=>[3, 4, 5, 6, 7, 8] 返回一个数组对象


处理数组中的元素的方法
1.循环与索引处理
list = %w{a b c d e f}
for i in 0..list.length - 1
   p "第#{i + 1}个元素：#{list[i]}"
end

values = [1, 2, 3, 4, 5, 6]
sum = 0
for var in values
   sum += var
end
p sum

2. 使用each来处理
list2 = [1, 2, 3, 4, 5]
list2.each_with_index do |elem, i|
   p "第#{i + 1}个元素：#{elem}"
end

sum2 = 0
list2.each { |var|  sum2 += var  }
p sum2

创建元素为数组的数组
a = Array.new(3, [1, 2, 3])
p a

a = Array.new(3) do
   [1, 2, 3]
end
p a
第一种方法与第二种方法都能创建元素为数组的数组，但是第一种的情况，a[1], a[2], a[3]都指向一个对象，一旦其中一个改变了，另外两个也会跟着改变。
第二种就不会出现这样的情况

同时访问多个元素
ary1 = [1, 2, 3, 4, 5]
ary2 = [10, 20, 30, 40, 50]
ary3 = [100, 200, 300, 400, 500]
result = []
ary1.zip(ary2, ary3) do |a, b ,c|
   result << a + b + c
end
p result

arr1 = [1, 2, 3, 4, 5]
arr2 = [10, 20, 30, 40, 50]
arr3 = [100, 200, 300, 400, 500]

i = 0
result = []
while i < arr1.length
   result << arr1[i] + arr2[i] + arr3[i]
   i += 1
end

p result

两种方法都能实现，但是第一种方法更加便捷

Enumerable模块

Enumerable模块介绍完Comparable模块后，我们再来介绍一下经常被用于Mixin的Enumerable模块。Enumerable的意思是“可数的”“可列举的”。在本书介绍过的类中，Array、Dir、File、Hash、IO、Range、Enumerator等类中都包含了Enumerable模块。


```


| 方法 | 意义 |
| --- | --- |
| all? | 所有元素为真时返回true， 否则返回false |
| any? | 一个或一个以上的元素为真时返回true， 否则返回false |
|  collect|把各元素的块执行结果以数组的形式返回|
|count  | 不指定参数时返回所有元素的数量，指定参数时则返回与参数值一样的元素数量 |
|  cycle|  对各个元素循环（最后一个执行完后返回首个元素再）执行块处理）|
|  detect|  对元素执行块处理，当结果为真时返回首个元素|
|  each_slice|指定参数n, n个元素为1组，把各组元素传给块执行  |
| each_with_index |  把元素及其索引传给块执行|
|  find|  与detect等价|
|  find_all| 把所有元素传给块，以数组的形式返回结果为真的元素 |
|  first|  获取首个元素|
|  grep| 匹配参数指定的模式，得到的元素以数组形式返回 |
| include? | 如果包含参数指定的值返回真 |
|  inject| 对元素进行块的叠加运算并返回结果 |
|map     | 与collect等价|
|member？ | 与include？等价|
|none?   |所有元素为假时返回true，否则返回false|
|one?     |有且只有一个元素为真时返回true，否则返回false|
|partition|把块的执行结果为真的放在一个数组里，为假的放在另外一个数组里，并返回这两个数组|
|reduce|与inject等价|
|reject|把所有元素传给块执行，以数组的形式返回结果为假的元素|
|reverse_each|倒序执行各元素的块运算|
|select|与find_all等价|
|sort|对块的执行结果排序后以数组形式返回|
|sort_by|对块的执行结果用<=>比较后进行排序，然后以数组的形式返回|
|to_a|把所有元素以数组的形式返回|
|zip|把接收者和参数对应的各元素合并为1个元素后以数组的形式返回|



```ruby
array = (1..99).to_a
p array

p array.collect { |var|  var * 100 }
p array.collect! { |var| var * 100 }

p array.find_all {|var| var % 3 == 0}
p array.reject! { |var| var % 3 == 0 }

p array.reverse
p array.reduce(:+)
p array.inject{ |sum, n|  sum + n }


从元素为1到100的整数数组中，取出10个分别包含10个元素的数组，如1～10，11～20，21～30。再将取出的全部数组按顺序保存到数组result中，请思考
解答如下：
ars =  (1..100).to_a
result = Array.new
10.times do |i|
   result << ars[10 * i, 10]
end
p result

定义方法sum_array，计算数值数组nums1和nums2中相对应的各个元素的合计值，并将结果作为数组返回。
解答如下：
def sum_array(ar1, ar2)
   result = []
   ar1.zip(ar2) do |a, b|
      result << a + b
   end
   return result
end

p sum_array([1, 2, 3], [4, 6, 8])


```
#### 字符串
```ruby
%Q 代表双引号
%q 代表单引号
desc = %Q{Ruby 的字符串中也可以使用''和""}
str = %q|Ruby said, 'Hello world'|
p desc
p str

Here Document
<<"结束标识符" 
字符串内容结束标识符

<< 后面的结束标识符可以用""括起来的字符串或者用''括起来的字符串来定义。用""括起来的字符串中可以使用转义字符和内嵌表达式，而用''括起来的字符串则不会进行任何特殊处理，只会原封不动地显示。另外，使用既没有""也没有''的字符串时，则会被认为是用""创建的字符串。

我们一般将EOF或者EOB作为结束标识符使用。EOF是“EndofFile”的缩写，EOB是“EndofBlock”的缩写。
HereDocument的结束标识符一定要在行首。因此，在程序缩进比较深的地方使用。
HereDocument的话，有时会出现整个缩进乱掉的情况：

10.times do |i|
   print <<EOF 
#{i}
EOF
end

若希望缩进整齐，可以像下面这样用<<代替<<，这样程序就会忽略结束标识符前的空格和制表符，结束标识符也就没有必要一定要写在行首了。下面例子中的print和EOB对齐了，代码也因此变得更清晰了。
10.times do |i|
   print <<-EOF
      #{i}
   EOF
end

而且，我们可以使用<<~消除行首的空白，这样一来，i:#{i}的缩进也变得整齐了。
10.times do |i|
   print <<~EOF
      #{i}
   EOF
end

第二种方法与第三种方法的区别在于，第二种方法无法忽略行首的空白等字符，因此在打印的时候会将字符一起打印出来，
而第三种方法会忽略空白等字符，因此即使代码缩进了，也不会将空白字符打印出来，输出结果和第一种结果一样。

Here Document也可以赋值给变量
str = <<~EOB
hello!
hello!
EOB

``反引号可以回去linux命令的结果。
puts `cat /etc/passwd`

printf与sprintf
printf(format[,arg1[,...]])
sprintf(format[,arg1[,...]])
第一个参数format是字符串，它以“%字符”的形式指定了我们希望输出的格式。format后面的参数是与“%字符”对应的数值。printf方法会将格式化后的字符串向控制台输出，而sprintf方法则会将格式化后的字符串以对象的形式返回。

sprintf方法有一个别名叫format，与“字符串%数组”这个形式是等价的。

p format("Hello", "%s", "Ruby)
p "%d %d" % [1, 2] #=> "1 2"

```

| 标记 | 意义 |
| --- | --- |
| # | 对于%b、%B、%o、%x、%X，输出加上前缀后的结果(0b、0B、0、0x、0X)|
| - | 指定了宽度时，使输出结果左对齐 |
| + | 输出符号+或- |
| 空格 |  仅当负数时输出符号-|
|  0|  指定最小宽度时，多出的位数不预埋空格而是预埋0|

#### 获取字符串长度
```ruby
length
size  #=>两个方法都一样，看习惯选择
puts "hello".length
puts "hello".size
若是中文字符串则返回字符数
p'面向对象编程语言'.length#=>8
如果想获取的不是字符数，而是字节数，可以用bytesize方法
p'面向对象编程语言'.bytesize#=>24

想知道字符串的长度是否为0时，可以使用empty?方法，该方法常被用于在循环等处理中判断字符串是否为空。

字符串索引
与数组一样
str="全新的String类对象"
p str[0] #=>"全"
p str[3] #=>"S"
p str[9] #=>"类"
p str[2,8] #=>"的String类"
p str[4] #=>"t"

字符串连接
+
<<
concat
三种方法来连接字符串
str = "Hello"
p str.empty?
str << " world"
p str
str = str + "!"
p str
p str.concat("!!")

str = "Hello"
str2 = str
str2 = str2 + "world"
p str2
p str

str3 = "Hello"
str4 = str3
str4 << "world"
p str3
p str4

+, concat, <<的区别在于，后两者会改变原来的对象，而前者会创建新的对象，不会改变原来赋值的变量，
参考上面的例子

字符串的比较
我们可以使用==或者!=来判断字符串是否相同。例如在表达式str1==str2中，str1与str2为相同字符串时返回true，为不同字符串时则返回false。!=与==表示的意思正好相反。

虽然判断字符串是否相同时使用==或者!=会很方便，但判断是否为相似的字符串时，使用正则表达式则会简单得多。

字符串的大小由字符编码的顺序决定。在排列英语或日语的字符串时，英文字母按照“ABC”的顺序排列，日语的平假名与片假名按照“あいうえお”的顺序排列1。不过，Ruby中日语的排序规则与字典中的顺序是不同的。
中文字符也同样是由字符编码的顺序决定的。例如，在UTF8字符编码表中，“一”的编码为U+4E00，“丁”的编码为U+4E01。

ASCII编码是计算机的基础。ASCII编码把英文字母、数值、其他符号，以及换行符、制表符这样的特殊字符集合起来，为它们分配1到127之间的数值，并使之占用1个字节的空间（1个字节可以表示0到255）。另外，在欧美，一种名为ISO88591的编码曾经被广泛使用，该编码包含了欧洲常用的基本字符（拼写符号、原音变音等），并为它们分配128到255之间的数值。也就是说，大部分的字符都是占用1个字节的空间。

字符串的替换
sub 和 gsub
字符串的检索
index 和 rindex
str="ABBBBBB"
p str.index("BB") #=>1
p str.rindex("BB") #=>5
如果只是想知道字符串中是否有参数指定的字符串，可以使用include?方法。

删除换行符
chop 和 chomp
chop删除最后一个字符， chomp删除换行符
带感叹号为破坏性操作

字符串分割
split来分割字符串

换行符的种类所谓换行符就是指进行换行的符号。就像在专栏“字符编码”中介绍的那样，计算机中使用的字符都被分配了一个与之对应的数值，同理，换行符也有一个与之对应的数值。不过麻烦的是，不同的OS对换行符的处理也不同。

Ruby中的标准换行符为LF，一般在IO.each_line 等方法中使用。也就是说，Ruby在处理MaxOS9以前版本的文本时，可能会出现不能正确换行的情况。我们可以通过参数指定each_line方法使用的换行符，默认为each_line("\n")。

字符串与数组的共同方法
字符串与数组的共同方法字符串中有很多与数组通用的方法。

继承了Object类的实例的方法，在字符串以及数组中也能使用。除此以外，下面的方法也都能使用。
（a）与索引操作相关的方法
（b）与Enumerable模块相关的方法
（c）与连接、反转（reverse）
我们提到过字符串也能像数组那样操作索引。适用于数组的索引操作方法，同样也适用于字符串。
s[n]=str
s[n..m]=str
s[n,len]=str
s.slice(n)
s.slice(n..m)s.slice(n,len)
s.concat(s2)
s + s2
s.delete(str)
s.delete!(str)
s.reverse
s.reverse!
s.strip
s.strip!
s.upcases.upcase!
s.downcase
s.downcase!
s.swapcase swapcase方法会将大写字母转换为小写，将小写字母转换为大写。
s.trs.tr! 该方法与gsub方法有点相似，不同点在于tr方法可以像s.tr("az","A-Z")这样一次替换多个字符。
p"ABCDE".tr("B","b")#=>"AbCDE"
p"ABCDE".tr("BD","bd")#=>"AbCdE"
p"ABCDE".tr("AE","ae")#=>"abcde"
tr方法不能用正则表达式替换字符，因为它只是用一个字符替换另外一个字符而已。


s.swapcase!
s.capitalize
s.capitalize!


除了以字符为单位指定索引外，还可以以字节为单位指定索引，这时需要用byteslice方法。
p "你好Ruby".byteslice(6,4) #=>"Ruby"


返回Enumerator对象的方法
在处理字符串的方法中，有以行为单位进行循环处理的each_line方法、以字节为单位进行循环处理的each_byte方法，以及以字符为单位进行循环处理的each_char方法。调用这些方法时若不带块，则会直接返回Enumerator对象，因此，通过使用这些方法，就可以像下面的例子这样使用Enumerable模块的方法了。

Enumerator类
虽然Enumerable模块非常方便，但它将遍历元素的方法限定为each方法，这一点有些不太灵活。String对象有each_byte、each_line、each_char等用于循环的方法，如果这些方法都能使用each_with_index、collect等Enumerable模块的方法的话，那就方便多了。而Enumerator类就是为了解决这个问题而诞生的。Enumerator类能以each方法以外的方法为基础，执行Enumerable模块定义的方法。使用Enumerator类后，我们就可以用String.each_line方法 替代each方法，执行Enumerable模块的方法了。另外，不带块的情况下，大部分Ruby原生的迭代器在调用时都会返回Enumerator对象。因此，我们就可以对each_line、each_byte等方法的返回结果继续使用map等方法。

字符串字面量与freeze作为字面量的字符串一般都不会被冻结（freeze），可以被任意更改。str="Ruby"pstr.upcase!#=>"RUBY"

true，这样脚本中的所有字符串字面量都会被冻结（代码清单14.1），这与Object.freeze方法的效果一样。
#frozenstringliteral:true　
str="Ruby"
p str.upcase!执行上述代码后程序会抛出错误。

另外，我们还可以在执行脚本时指定选项enable=frozenstringliteral，这样即使没有魔法注释，脚本内的字符串字面量也都会被冻结。


```
#### 散列

```ruby
散列的创建

{ 键 : 值 }
Hash.new
Hash.new 是用来创建新的散列的方法。若指定参数，则该参数值为散列的默认值，也就是指定不存在的键时所返回的值。未指定参数时，散列的默认值为nil。
散列的键可以使用各种对象，不过一般建议使用下面的对象作为散列的键。
1.字符串（String）
2.数值（Numeric）
3.符号（Symbol）
4.日期（Date）

值得获取与设定
与数组一样，散列也用[]来获取或设定与键相对应的值。
另外，还可以用store方法设定值，用fetch方法获取值。下面的例子的执行结果
使用fetch方法时，有一点与[]不一样，就是如果散列中不存在指定的键，程序就会发生异常。

h = Hash.new
p h.fetch("N")#=>错误（KeyError）
如果对fetch方法指定第2个参数，那么该参数值就会作为键不存在时的返回值。
此外，fetch方法还可以使用块，此时块的执行结果为键不存在时的返回值。

```
一次性获取所有键值

|  |数组形式  |迭代器形式  |
| --- | --- | --- |
| 获取键 | keys |each_key { \|key\| ... }  |
|获取值  | values | each_value { \|value\|... } |
| 获取键值对[key, value] |to_a  |  each { \|key,value\|... } 和 each { \|arrry\| ... }|

```ruby
散列的默认值
在获取散列值时，即使指定了不存在的键，程序也不会发生错误，而是会返回某个值。我们有3种方法来指定这种情况下的返回值。

1.创建散列时指定默认值Hash.new的参数值即为散列的默认值（什么都不指定时默认值为nil）。
h = Hash.new(1)
h["a"]=10
p h["a"] #=>10
p h["x"] #=>1
p h["y"] #=>1
这个方法与初始化数组一样，所有的键都共享这个默认值。

2.指定生成散列默认值的块当希望不同的键返回不同的值时，或者不希望所有的键共享一个值时，可以在Hash.new方法中指定块。

h2 = Hash.new do |hash, key|
   hash[key] = key.upcase
end
h2["a"] = 100
p h2["a"] #=> 100
p h2["b"] #=> B
p h2["d"] #=> D

3.用fetch方法指定最后就是刚才已经介绍过的fetch方法。在Hash.new方法中指定了默认值或块时，会优先返回fetch方法的第2个参数指定的值。

h2 = Hash.new do |hash, key|
   hash[key] = key.upcase
end

p h2.fetch("a", "undef")

查看指定对象是否为散列的键或值

h.key?(key)
h.has_key?(key)
h.include?(key)
h.member?(key)
上面4个方法都是查看指定对象是否为散列的键的方法，它们的用法和效果都是一样的。大家可以统一只用某一个，也可以根据不同的情况区分使用。

h.value?(value)
h.has_value?(value)
查看散列的值中是否存在指定对象的方法。这两个方法只是把key?、has_key?方法中代表键的key部分换成了值value，用法是完全一样的。散列h中包含值value时返回true，否则返回false。

查看散列的大小
h.size
h.length
我们可以用length方法或者size方法来查看散列的大小，也就是键的数量。

h.empty?
我们可以用empty?方法来查看散列的大小是否为0，也就是散列中是否不存在任何键。

删除键值
h.delete(key)删除指定的键可以用delete方法。

delete方法也能在参数中指定块。指定块后，如果不存在键，则返回块的执行结果。
h.delete_if{|key,val|…}
h.reject!{|key,val|…}希望只删除符合某种条件的键值时，可以使用delete_if方法。
另外，虽然reject!方法的用法与delete_if方法相同，但当不符合删除条件时，两者的返回值却各异。delete_if方法返回的是原来的散列，而reject!方法返回的则是nil。
hash = Hash.new
hash["a"] = 1
hash["b"] = 2
hash["c"] = 3
p hash

p hash.fetch("a")
p hash.delete_if { |key, var| key == "a" }

p hash.delete_if { |key, var| key == "e" }
p hash.reject! { |key, var| key == "e" } #=> nil

初始化散列
a = {"a" => 1}
a.clear
p a #=> {}

a2 = {"a" => 2}
a3 = a2
a2.clear
p a3 #=> {}

a2 = {"a" => 2}
a3 = a2
a2 = Hash.new
p a3 #=> {a => 2}

处理有两个键的散列散列的值也可以是散列，也就是所谓的“散列的散列”，这与数组中的“数组的数组”的用法是一样的。table = {"A"=>{"a"=>"x","b"=>"y"},"B"=>{"a"=>"v","b"=>"w"} }
p table["A"]["a"] #=>"x"
p table["B"]["a"] #=>"v"

合并两个散列使用Hash.merge方法可以合并两个散列，创建一个新的散列。
p ({"a"=>"x"}.merge({"b"=>"y"})) #=> {"a"=>"x","b"=>"y"}

统计单词个数
count = Hash.new(0)
File.open("files/hello_world") do |file|
   file.each_line do |line|
      line.chomp.split(" ").each do |word|
         count[word] += 1
      end
   end
end
count.keys.sort {|a, b| count[a] <=> count[b] }.each do |key|
   puts "#{key}: #{count[key]}"
end
puts "#" * 90
p count.sort { |a, b| a[1] <=> b[1] }
     .each do |key, value|
   print "#{key}: #{value}\n"
end

在散列内部，程序会将散列获取值时指定的键，与将值保存到散列时指定的键进行比较，判断两者是否一致。这时，判断键是否一致与键本身有着莫大的关系。具体来说，对于两个键key1、key2，当key1.hash与key2.hash返回相同的主值，且key1.eql?(key2)为true时，就会认为这两个键是一致的。像本例那样，虽然使用==比较时得到的结果是一致的，但是，当两个键分别属于Fixnum类和Float类的对象时，由于不同类的对象不能判断为相同的键，因此就会产生与期待不同的结果。

```
#### Pro类
```ruby
所谓Proc，就是使块对象化的类。

Proc.new(...)
proc{...}
创建Proc对象的典型方法是使用Proc.new方法，或者对proc方法指定块。

通过调用Proc#call方法执行块。调用Proc#call方法时的参数会作为块变量，块中最后一个表达式的值则为Proc#call的返回值。Proc#call还有一个名称叫Proc#[]。

leap = Proc.new do |var|
   var + 2
end

p leap[2]
p leap.call(2)
以上两种方法结果是一样的，根据实际情况选择
将块变量设置为|*数组|的形式后，就可以像方法参数一样，以数组的形式接收可变数量的参数。

double = proc do |*vars|
   vars.map do |var|
      var ** 2
   end
end

p double.call(1, 2, 3, 4)
p double[1, 2, 3, 4]

lambda

Proc.new、proc等有另外一种写法叫lambda方法。与Proc.new、proc方法一样，lambda也可以创建Proc对象，但通过lambda方法创建的Proc对象的行为会更接近方法。

备注　下文中我们把用lambda方法创建的Proc对象统一称为lambda表达式。

第一个不同点是，lambda表达式的参数数量的检查更加严密。对用Proc.new创建的Proc对象调用call方法时，call方法的参数数量与块变量的数量可以不同。但通过lambda表达式创建Proc对象时，如果参数数量不正确，程序就会产生错误。
prc1 = Proc.new do |a, b|
   p [a, b]
end

prc1.call(1)

lam = lambda do |a, b|
   p [a, b]
end

lam.call(1) #=> wrong

第二个不同点是，lambda表达式可以使用return将值从块中返回。
见示例power_of方法会利用参数n返回“计算x的n次幂的Proc对象”。请注意，返回值并不是数值，而是进行运算的Proc对象。调用power_of(3)后，结果就会得到call方法参数值的3次幂的Proc对象。返回值时使用了return，这个return就是Proc对象调用call方法时的返回值。

def power_of(n)
   lambda do |x|
      return x ** n
   end
end

meth = power_of(2)
p meth.call(3)


def power_of_two(n)
   Proc.new do |x|
      return x ** n
   end
end

meth2 = power_of_two(2)
p meth2.call(3) #=>wrong
使用Proc.new方法时，在块中使用return后，程序就会跳过当前执行块，直接从创建这个块的方法调用中返回。在本例中，虽然块内的return应该从power_of方法返回，但由于程序运行时power_of方法的上下文会消失，因此程序就会出现错误。

普通块中的return，会从正在执行循环的方法返回。示例当中prefix方法会比较参数ary中的元素是否与obj相等，相等就返回在此之前的所有元素，不相等则返回空数组。第6行中的return并不会从块返回，而是跳过块，并作为prefix方法整体的返回值返回。

def prefix(ary, obj)
   result = []
   ary.each do |var|
      result << var
      if obj == var
         return result
      end
   end
   return result
end

p prefix([1, 2, 3, 4], 3)

被用于控制迭代器的break的行为也和平常不一样。这个命令会向接收块的方法的调用者返回结果值。如下所示，break[]会马上终止Array#map方法，并将空数组作为map方法的整体的返回值返回。

result = [1, 2, 3, 4].map do |var|
   var ** 2
   break []
end

p result

用Proc.new方法或者proc方法创建Proc对象时，由于break试图结束这些方法，在调用Proc#call方法时并没有适当的返回对象，因此就会发生错误。而lambda表达式的情况则与return一样，将值返回给Proc#call方法。另一方面，由于next方法的作用在于中断块的执行，因此无论如何创建Proc对象，都可以将值返回给call方法。lambda表达式有另外一种写法——“>(块变量){处理}”。块变量在{～}之前，看上去有点像函数。使用->时，一般会使用{～}，而不是do～end。

square = ->(n){returnn**2}
p square[5] #=>25

备注：关于Proc与lambda的进一步讨论可以参考《programming ruby》


通过Proc参数接受块
def total3(from, to, &block)
   result = 0
   from.upto(to) do |var|
      if block
         result += block.call(var)
      else
         result += var
      end
   end
   return result
end


result = total3(1, 10) do |var|
   var ** 2
end

p total3(1, 10)
p result

to_proc方法
有些对象有to_proc方法。在方法中指定块时，如果以“&对象”的形式传递参数，对象.to_proc就会被自动调用，进而生成Proc对象。其中，Symbol#to_proc方法是比较典型的，并且经常被用到。例如，对符号:to_i使用Symbol#to_proc方法，就会生成下面这样的Proc对象。
虽然Proc对象可以作为无名函数或方法使用，但它并不只是单纯将处理对象化。

block = Proc.new do |var|
   var + 10
end
p [1, 2, 3].map(&block)

Proc类的实例方法
prc.call(args,...)
prc[args,...]
prc.yield(args,...)
prc.(args,...)
prc === arg

由于受到语法的限制，通过===指定的参数只能为1个。大家一定要牢记这个方法会在Proc对象作为case语句的条件时使用。因此，在创建这样的Proc对象时，比较恰当的做法是，只接收1个参数，并返回true或者false。

prc = Proc.new do |a, b|
   a + b
end

p prc.call(1, 2)
p prc[1, 2]
p prc.yield(5, 6)
p prc.(7, 8)
p prc === [9, 100]
以上几种方法结果都是一样的

fizz = proc{ |n| n % 3 == 0 }
buzz = proc{ |n| n % 5 == 0 }

fizzbuzz = proc{ |a| fizz[a] && buzz[a] }
p fizzbuzz.call(15)

prc.arity #=> 返回作为call方法的参数的块变量的个数。以“|*args|”的形式指定块变量时，返回1。

prc1 = Proc.new { |a| a }
prc2 = Proc.new { |a, b| a + b }
prc3 = Proc.new { |a, b, c| a + b + c }
prc4 = Proc.new { |*arg| args }

p prc1.arity
p prc2.arity
p prc3.arity
p prc4.arity


prc.parameters #=> 返回关于块变量的详细信息。返回值为“[种类,变量名]”形式的数组的列表。

prc0 = proc{nil}
prc1 = proc{|a|a}
prc2 = lambda{|a,b|[a,b]}
prc3 = lambda{|a,b=1,*c|[a,b,c]}
prc4 = lambda{|a,&block|[a,block]}
prc5 = lambda{|a:1,**b|[a,b]}　
p prc0.parameters #=>[]
p prc1.parameters #=>[[:opt,:a]]
p prc2.parameters #=>[[:req,:a],[:req,:b]]
p prc3.parameters #=>[[:req,:a],[:opt,:b],[:rest,:c]]
p prc4.parameters #=>[[:req,:a],[:block,:block]]
p prc5.parameters #=>[[:key,:a],[:keyrest,:b]]



prc.lambda? #=> 判断prc是否为通过lambda表达式定义的方法。是则返回true，否则返回false。
prc5 = Proc.new { |a, b| a + b }
p prc5.lambda? #=> false

prc6 = lambda { |a, b| a + b }
p prc6.lambda? #=> true

prc.source_location #=> 返回定义prc的程序代码的位置。返回值为“[代码文件名,行编号]”形式的数组。prc由扩展库等生成，当Ruby脚本不存在时返回nil。
p prc6.source_location
#=> ["/Users/liuyang/RubymineProjects/BeginRuby/total3.rb", 61]
```

Proc#parameters方法返回的变量种类：

| 符号 |意义  |
| --- | --- |
|  :opt|  可省略的变量|
|  :req|  必须的变量|
|  :rest|  以\*args形式表示的变量|
|  :key|  关键字参数形式的变量|
|  :keyrest| 以\*\*args形式表示的变量 |
|  :block| 块 |

#### IO类
IO类的主要作用就是让程序与外部进行数据的输入（input）/输出（output）操作。
程序在启动后会预先分配3个IO对象。

* 标准输入
标准输入是用于接收数据的IO对象。可以通过预定义常量STDIN调用IO对象，也可以用全局变量$stdin引用IO对象。不指定接收者的gets方法等都会默认从$stdin中获取数据。标准输入最初与控制台关联，接收从键盘输入的内容。

* 标准输出
标准输出是用于输出数据的IO对象。可以通过预定义常量STDOUT调用IO对象，也可以用全局变量$stdout引用IO对象。不指定接收者的puts、print、printf等方法会默认将数据输出到$stdout。标准输出最初与控制台关联。


* 标准错误输出
标准错误输出是用于输出警告、错误的IO对象。可以通过预定义常量STDERR调用IO对象，也可以用全局变量$stderr引用IO对象。用于显示警告信息的warn方法会将信息输出到$stderr。标准错误输出最初与控制台关联。

虽然标准输出与标准错误输出都是向控制台输出内容，但我们可以根据输出的内容区别使用，程序本身的输出内容使用标准输出，错误、警告或者单纯的消息通知则使用标准错误输出
```ruby
3.times do |i|
   STDOUT.puts("hello world:#{i + 1}次")
   STDERR.puts("ERROR")
end
```
通常标准输入、标准输出、标准错误输出都是与控制台关联的，但是将命令的输出重定向到文件，或者使用管道（|）将结果传递给其他程序时则与控制台没有关系。根据实际的使用情况，程序根据输入/输出状态所进行的处理也各异。IO对象是否与控制台关联，可以通过tty?方法判断。

```ruby
p $stderr.tty?
p $stdin.tty?
p $stdout.tty?
p file.close?
```
文件输入/输出
通过IO类的子类File类可以进行文件的输入/输出处理。File类中封装了文件创建、删除等与文件系统相关的操作，而一些基本的输入/输出处理则使用继承自IO类的方法。
```ruby
io = File.open(file[,mode[,perm]][,opt])
io = open(file[,mode[,perm]][,opt])

模式（mode）会指定以何种目的打开文件。缺省模式为只读模式（"r"）。在Windows环境下，在各模式后加上b，通过"rb"、"rb+"等形式即可表示二进制模式（后述）。Windows以外的平台会忽略b模式，不过为了便于辨别目标文件的类型，建议操作二进制文件时还是使用b模式。像"rt"这样，在各模式末尾加上t，会将读写时的换行符统一为\n。
```
| 模式 |意义  |
| --- | --- |
| "r" |  用只读模式打开文件|
| "r+" |  用读写模式打开文件|
| "w" | 用只写模式打开文件。文件不存在则创建新的文件；文件已存在则清空文件，即将文件大小设置为0|
| "w+" | 读写模式，其余同"w" |
|  "a"| 用追加模式打开文件。文件不存在则创建新的文件|
|  "a+"| 用读取/追加模式打开文件。文件不存在则创建新的文件|

权限模式（perm）用于指定新创建的文件的访问权限。

需要注意的地方：系统能够打开的文件描述符有限的，当使用完一个文件以后，需要将文件句柄关闭，来释放资源。
可以用file.close来关闭文件，可以使用open的块形式来得到File对象，这样会自动关闭file句柄，

```ruby
File.read(file [, length [, offset]]) #=>不创建文件对象，而是直接读取文件内容
File.binread(file [, length [, offset]]) #=>  以二进制模式读取数据
File.write(file [, data [, offset]]) #=> 写入数据到文件中
File.binwrite(file [, data [, offset]]) #=> 以二进制模式写入数据
p File.read("files/hello_world")
p File.write("files/hello_world", "good!")

```
#### 基本的输入输出操作
输入/输出操作的对象数据为字符串，也就是所谓的String对象。执行输入操作后，会从头到尾按顺序读取数据，执行输出操作后，则会按写入顺序不断追加数据。接下来介绍的大部分内容都是IO对象的方法，但IO类的子类File类也同样能使用这些方法。

```ruby
io.gets(rs)
io.each(rs)
io.each_line(rs)
io.readlines(rs)
io.lineno
io.lineno=(number)
io.each_char
File.open("files/hello_world", "r") do |file|
   file.each_char do |ch|
      p ch
   end
end
io.each_byte
File.open("files/hello_world", "r") do |file|
   file.each_byte do |byte|
      p byte
   end
end
io.getc
File.open("files/hello_world") do |file|
   p file.getc
end

io.ungetc(ch)
将参数ch指定的字符退回到io的输入缓冲区中。
File.open("files/hello_world", "r") do |file|
   p file.getc
   file.ungetc("HELLO")
   p file.gets
end

io.getbyte
返回一个字节，以整数对象返回与得到的字节相对应的ASCII码

 
io.ungetbyte(byte)
将参数byte指定的一个字节退回到输入缓冲区中。参数为整数时，将该整数除以256后的余数作为ASCII码返回一个字节；参数为字符串时，只返回字符串的第一个字节。

io.read(size)
读取参数size中指定的大小的数据。不指定大小时，则一次性读取全部数据并返回。


io.puts(str0, str1, ....)
输出参数指定的字符串。参数可指定多个字符串。参数为String以外的对象时会自动将其转换为字符串。
给每个字符串加上换行符。
io.putc(ch)
如果参数为字符串则打印字符串首个字符
io.print(str0, st1, ...)
输出参数指定的字符串。参数可指定多个字符串。参数为String以外的对象时会自动将其转换为字符串。

io.printf(fmt, arg0, arg1, ...)
io.write(str)
size = $stdout.write("hello") #=>打印hello并且返回写入的字符数
p size 
io << str
size = $stdout << "hello" << "world" #=> 输出字符串并且返回接收者本身
p size  #=> #<IO:<STDOUT>>
从IO类的对象io中读取一行数据。用参数rs中指定的字符串分行，省略rs时则使用预定义变量$/（默认值为"\n"）。

File.open("files/hello_world", "r") do |file|
   while line = file.gets
      line.chomp!
      puts line
   end
end

puts "file is end!" if file.eof?

File.open("files/hello_world", "r") do |file|
   line = file.readlines
   p line
end   #=> readlines 返回行元素构成的数组

File.open("files/hello_world", "r") do |file|
    file.each do |line|
       print "#{file.lineno}: #{line}"
    end
end
```
##### 文件指针
为了提高读取效率，可以将文件分成固定长度的文件块，来直接访问某个位置的数据（虽然据此可以访问任意位置的数据，但却不能处理超过指定长度的数据）。我们用文件指针（filepointer）或者当前文件偏移量（currentfileoffset）来表示IO对象指向的文件的位置。每当读写文件时，文件指针都会自动移动，而我们也可以使文件指针指向任意位置来读写数据。

```ruby
通过pos方法可以获得文件指针现在的位置。改变文件指针的位置用pos=方法。
io.pos
io.pos=(position)
File.open("files/testing") do |file|
   p  file.pos
   p file.readline
   p file.pos
   p file.readline
   p file.pos
   file.pos=(0)
   p file.readline
   p file.pos
end

移动文件指针的方法。参数offset为用于指定位置的整数，参数whence用于指定offset如何移动
io.seek(offset, whence)
```

| whence | 意义 |
| --- | --- |
| IO::SEEK_SET |将文件指针移动到offset指定的位置|
|  IO::SEEK_CUR| 将offset视为相对于当前位置的偏移位置来移动文件指针|
|  IO::SEEK_END| 将offset指定为相对于文件末尾的偏移位置|

```ruby
将文件指针返回到文件的开头。lineno方法返回的行编号为0。
io.rewind
File.open("files/testing") do |io|
   p io.gets
   io.rewind
   p io.gets
end

io.truncate(size)
按照参数size指定的大小截断文件。
io.truncate(0)
io.truncate(io.pos) #=> 删除当前文件指针以后的数据
File.open("files/testing", "a+") do |io|
   io.truncate(55)
   p io.gets
   p io.pos
   io.truncate(11)
end

io.binmode
新的IO对象默认是文本模式，使用binmode方法可将其变更为二进制模式。
转换为二进制模式的IO对象无法再次转换为文本模式。

缓冲区
即使对IO对象进行写入，结果也并不一定马上就会反映在控制台或者文件中。在使用write、print等方法操作IO对象时，程序内部会开辟出一定的空间来保存临时生成的数据副本（图17.2）。这部分空间就称为缓冲区（buffer）。缓冲区里累积一定量的数据后，就会进行输出处理，然后清空缓冲区。

filename = 'files/testings'
File.open(filename, "w") do |io|
   3.times do |i|
      io.write("a" * 5)
      puts "第#{i + 1}次：#{File.size(filename)}"
   end
end
puts "结束后：#{File.size(filename)}"
通过这个脚本我们可以知道，程序并不是在调用write方法时写入文件，而是在完成所有写入操作并关闭文件后才最终将数据写入文件。

io.flush
io.sync=(state) #=> sync与flush效果一样，当state为true时，每一次输入都会同步写入操作

filename = 'files/testings'
File.open(filename, "w") do |io|
   io.sync=(true )
   3.times do |i|
      io.write("a" * 5)
      puts "第#{i + 1}次：#{File.size(filename)}"
   end
end
puts "结束后：#{File.size(filename)}"

filename = 'files/testings'
File.open(filename, "w") do |io|
   io.sync=(true )
   3.times do |i|
      io.write("a" * 5)
      io.flush
      puts "第#{i + 1}次：#{File.size(filename)}"
   end
end
puts "结束后：#{File.size(filename)}"
```

##### 与命令进行交互
```ruby
IO.popen(command, mode)
参数mode的使用方法与File.open方法是一样的，参数缺省时默认为"r"模式。用IO.popen方法生成的IO对象的输入/输出，会关联启动的命令command的标准输入/输出。也就是说，IO对象的输出会作为命令的输入，命令的输出则会作为IO对象的输入。

IO.popen("ls", "r") do |io|
   p io.readlines.each { |line| line.chomp! }
end

IO.popen("wc -l", "w") do |io|
   io.print <<-EOF
      hello world
      hello larry
      hello wall
      hello matz
    EOF
end

r 模式表示从命令读取结果
w 模式表示将输出作为命令的输入

open("|command",mode)
filename = ARGV[0]
open("|cat #{filename}") do |io|
   io.each_line do |line|
      print line
   end
end


open-uri模块
require 'open-uri'

open("http://www.baidu.com") do |io|
   puts io.read
end
除了控制台、文件以外，进程间通信时使用的管道、网络间通信时使用的套接字也都可以作为IO对象使用。管道、套接字的使用方法超出了本书的范围，因此不详细说明，这里我们简单地介绍一下利用HTTP、FTP从网络获取数据的方法。通过require引用openuri库后，我们就可以像打开普通的文件一样打开HTTP、FTP的URL。使用openuri库的功能时，不要使用File.open方法，只使用open方法即可。

stringio库
在测试程序时，虽然我们希望知道向文件或控制台输出了什么，但程序实际执行的结果却往往很难知道。为此，可以通过向模拟IO对象的对象进行输出来确认执行结果。StringIO就是用于模拟IO对象的对象。通过require引用stringio库后，就可以使用StringIO对象了

require 'stringio'
io = StringIO.new("hello\nworld")
p io.gets
p io.gets

ios = StringIO.new
ios.puts("hello")
ios.puts("world")
ios.rewind
p ios.read
```
#### File与Dir类
```ruby
File.rename(before, after)
由于文件复制是常用的操作，如果每次使用时都需要自己重新定义一次的话就非常麻烦。因此，我们可以通过引用fileutils库，使用其中的 FileUtils.cp（文件复制）、FileUtils.mv（文件移动）等方法来操作文件。

注 File.rename方法无法跨文件系统或者驱动器移动文件。

由于文件复制是常用的操作，如果每次使用时都需要自己重新定义一次的话就非常麻烦。因此，我们可以通过引用fileutils库，使用其中的 FileUtils.cp（文件复制）、FileUtils.mv（文件移动）等方法来操作文件。

require 'fileutils'
FileUtils.cp("Fuck.rb", "fucking.rb")
FileUtils.mv("blocl.rb", "Files")

File.delete(file)
File.unlink(file)
我们可以使用 File.delete方法或File.unlink方法删除文件。
File.delete("data.txt")

目录的操作
Dir类中实现了目录相关的操作方法
Dir.pwd
Dir.chdir(dir)

p Dir.pwd
io = File.new("files.rb")
io.close

Dir.chdir("./Files")
p Dir.pwd

读取目录内容
1.打开目录
2.读取内容
3.关闭

Dir.open(path)
Dir.close
dir = Dir.open(".")
while name = dir.read
   p name
end

dir.close

Dir.open("Files") do |dir|
   dir.each do |file|
      p file
   end
end

与File类一样，Dir类也有read方法。
执行Dir#read后，程序会逐个读取最先打开的目录下的内容。这里读取的内容可分为以下4类。
1. 表示当前目录的.
2. 表示上级目录的..
3. 其他目录名
4. 文件名

请注意 /usr/bin 与 /usr/bin/. 表示同一个目录。

def traverse(path)
   if File.directory?(path)
      Dir.open(path) do |dir|
         while name = dir.read
            next if name == "." or name == ".."
            traverse(path + "/" + name)
         end
      end
   else
      process_file(path)
   end
end

def process_file(path)
   puts path
end


创建与删除目录
Dir.mkdir(path)
Dir.rmdir(path)

文件与目录的属性
File.stat(path)
文件与目录都有所有者、最后更新时间等属性

File.stat方法返回的是File::Stat类的实例。实例方法如下表：
```

| 方法 |返回值的含义  |
| :---: | :---: |
| dev | 文件系统的编号 |
| ino | i-node编号 |
|  nlink|  链接数|
| uid | 文件所有者的用户ID |
| gid | 文件所属组的组ID |
| rdev | 文件系统的驱动器种类 |
| size | 文件大小 |
| blksize | 文件系统的块大小 |
| blocks | 文件占用的块数量 |
|  atime|  文件的最后访问时间|
|  mtime| 文件的最后修改时间 |
|  ctime| 文件状态的最后更改时间 |

```ruby
其中，除了atime方法、mtime方法、ctime方法返回Time对象外，其他方法都返回整数值。通过uid方法与gid方法获取对应的用户ID与组ID时，需要用到Etc模块。我们可以通过require'etc'来引用Etc模块。Unix的密码文件（/etc/passwd）包含用户ID、用户名、组ID、主目录等信息，Etc.getpwuid 方法可以返回对应用户ID的密码文件内容。同样地，组文件（/etc/group）包含组ID、组名等信息，Etc.getgrgid 方法可以返回对应组ID的组文件内容

require 'etc'

st = File.stat("count.rb")
pw = Etc.getpwuid(st.uid)
p pw.class
p pw.name

gr = Etc.getgrgid(st.gid)
p gr.class
p gr.name


File.ctime(path)
File.mtime(path)
File.atime(path)

这三个方法的执行结果与实例方法File::Stat#ctime、File::Stat#mtime、File::Stat#atime是一样的。需要同时使用其中两个以上的方法时，选择实例方法会更加有效率。

File.utime(atime, mtime, path)
改变文件属性中的最后访问时间atime、最后修改时间mtime。时间可以用整数值或者Time对象指定。另外，还可以同时指定多个路径。下面是修改文件foo的最后访问时间以及最后修改时间的程序。通过 Time.now方法创建表示当前时间的Time对象，然后将时间设为当前时间减去100秒。

 filename = "kinbon.txt"
File.open(filename, "w").close

st = File.stat(filename)
p st.class
p st.atime
p st.mtime
p st.ctime

puts ""
File.utime(Time.now - 100, Time.now - 100, filename)
sts = File.stat(filename)
p File.atime(filename)
p File.mtime(filename)

File.chmod(mode, path)
修改文件path的访问权限（permission）。mode的值为整数值，表示新的访问权限值。可以同时指定多个路径。

例如，设定“所有者为读写权限，其他用户为只读权限”时的权限位（permissionbit）为110100100。由于八进制刚好是用1个数字表示3位，因此一般会用八进制表示权限位。刚才的110100100用八进制表示则为0644。关于八进制请大家参考第12章专栏“位与字节”。

File.chmod(0755, "test.txt")
File.chown(owner, group, path)
改变文件path的所有者。owner表示新的所有者的用户ID，group表示新的所属组ID。同时也可以指定多个路径。执行这个命令需要有系统管理员的权限。

在追加特定位时，例如在现在的访问权限中追加执行权限时，需要对从 File.stat方法得到的访问权限位进行追加位的位运算，然后再用计算后的新值重新设定。使用按位或运算追加权限位。

rb_file = "test.rb"
st = File。stat(rb_file）
File.chmod(st.mode | 0111, rb_file)

```
##### FileTest模块

| 方法  | 返回值 |
| :---: | :---:|
|  exist?(path)| path若存在则返回true |
| file?(path) |  path若是文件则返回true|
|  directory?(path)|path若是目录则返回true  |
|  owned(path)|  path的所有者与执行用户一样则返回true |
|  grpowned?(path)|  path的所属组与执行用户的所属组一样则返回true|
| readable?（path) | path可读则返回true |
| writeable?(path) | path可写则返回true |
| executable?(path) | path可执行则返回true |
| size(path) |  返回path的大小|
|size?(path)  |path的大小比0大则返回true，大小为0或者不存在则返回nil  |
|     zero?(path)      |path的大小为0则返回true   |

##### 文件名的操作

```ruby
File.basename(path [, suffix])
返回路径名path中最后一个"/"以后的部分。如果指定了扩展名suffix，则会去除返回值中扩展名的部分。在从路径名中获取文件名时会使用本方法。

File.dirname(path)
返回路径名path中最后一个"/"之前的内容。路径不包含"/"时则返回"."。在从路径名中获取目录名时会使用本方法。

File.extname(path)
返回路径名path中basename方法返回结果中的扩展名。没有扩展名或者文件名以"."开头时则返回空字符

File.split(path)
将路径名path分割为目录名与文件名两部分，并以数组形式返回。在知道返回值的数量时，使用多重赋值会方便得多。

File.join(name1[, name2, ...])
用File::SEPARATOR连接参数指定的字符串。File::SEPARATOR的默认值设为"/"。

File.expand_path(path[, default_dir])
根据目录名default_dir，将相对路径名path转换为绝对路径名。不指定default_dir时，则根据当前目录进行转换。

templie库

require 'tempfile'

file = Tempfile.new("temp_file")
p file.path
file.write("hello world")
file.rewind
p file.read
file.rewind
p file.read
file.close
file.unlink

require 'tempfile'
begin
   file2 = Tempfile.new("jordan")
   file2.write("good")
   file2.rewind
   p file2.read
ensure
   file2.close
   file2.unlink
end
```

##### Time和date类
Time类用于表示时间。时间除了表示年月日时分秒的信息以外，还包含表示地域时差的时区（timezone）信息。例如我们可以计算中国当前时间是国际协调时间的几点。Date类只用于表示年月日，是以天为单位进行计算的。Date类还可以求下个月的同一天、本月末等日期。

Time类与Date类表示时间、日期时并没有什么特别限制（前提是现在的日历能一直用下去，甚至“西历100亿年”这样的时间、日期也都可以表示）。但实际上文件的时间戳、程序的运行时间等系统内的时间，以及数据库中的时间类型数据等，有时会受到执行环境的限制。
```ruby
获取时间

Time.now

p Time.now
sleep 10
p Time.now

Time.mktime(
year[,month[,day[,hour[,min[,sec[,usec]]]]]])
Time.at(epoch)
通过Time.at方法可以获取自Unix系统的标准时间1970年1月1日凌晨0时0分0秒（UTC）之后经过的秒数所对应的Time对象。


计算日期
Date对象之间的计算以天为单位。因此，Date对象之间进行减法运算时，返回的是两者之间的天数。日期减法运算的结果不是整数，而是Rational对象。此外，还可以将Date对象与整数进行加法、减法等运算，这时会返回该对象前后的日期。

require 'date'
d1 = Date.new(2015, 1, 2)
d2 = Date.new(2016, 1, 2)
puts d1
puts d2
puts d2 - d1 
不能将对象与对象相加，可以将对象与整数进行加减运算

Date类有一个特点：可以对月末的日期做1处理（2表示月末的前一天）。当然也可以应对闰年。
require 'date'
d1 = Date.new(2015, 1, 2)
d2 = Date.new(2016, 1, 2)
puts d1
puts d2

d3 = Date.new(2015, 1, -1)
puts d3

d4 = Date.new(2015, 2, -1)
puts d4

d5 = Date.new(2015, 2, -2)
puts d5

通过使用>>运算符，就可以获取表示下一个月相同日期的Date对象。同理，使用<<运算符得到的是表示前一个月相同日期的Date对象。如果该月中没有相同的日期（例如2月31日），则会返回月末的日期。
d = Date.today
puts d >> 1
puts d >> 2
puts d << 3


Time与Date相互转换
require 'date'
t1 = Time.now
p t1
d = t1.to_date
puts d

t2 = d.to_time
puts t2

可以使用to_time方法和to_date方法互相转换Time对象和Date对象。由于Date对象没有时间部分，因此转换后时间部分会显示系统时区的0点0分0秒
```

##### 正则表达式
在程序中，通过用//将表示正则表达式模式的字符串括起来，就可以非常简单地创建出正则表达式。另外，我们也可以使用类方法Regexp.new(str)来创建对象。当程序中已经定义了字符串对象str，且希望根据这个字符串来创建正则表达式时，就可以使用这个方法。

除上述两种方法外，与数组、字符串一样，我们也可以通过使用%的特殊语法来创建正则表达式。正则表达式的情况下使用的是%r，如果正则表达式中包含/，用这种方法会比较方便。%r的语法如下所示。
```ruby
%r(pattern)
%r<pattern>
%r |pattern|<br>%r! pattern !

正则表达式=~字符串无法匹配时返回nil，匹配成功则返回该字符串起始字符的位置。

if 正则表达式 =~ 字符串
   匹配时的处理
else
   不匹配时的处理
end 

Note: 正则表达式=~字符串无法匹配时返回nil，匹配成功则返回该字符串起始字符的位置。


我们还可以使用!~来颠倒“真”与“假”。

行首与行尾
^, $ 分别匹配“行首”行尾“，而匹配字符串的开头用 \A，匹配字符串的结尾用 \z.
这两种情况有什么不同呢？Ruby的字符串，也就是String对象中，所谓的“行”就是用换行符(\n)隔开的字符串，因此模式/^ABC/也可以匹配字符串"012\nABC"。也就是说，像下面这种跨两行的字符串，由于第2行是以ABC开头的，因此也可以匹配。

另外，还有一个与\z类似的元字符，就是\Z，不过两者的作用有点不一样。\Z虽然也是匹配字符串末尾的元字符，但它有一个特点，就是如果字符串末尾是换行符，则匹配换行符前一个字符。

p 'ab\n' =~ /ab\z/ #=> nil
p "ab\n" =~ /ab\n\z/ #=> 0
p "ab\n" =~ /ab\Z/ #=> 0

Note:我们一般常用\z，而很少使用\Z.

如果是[]中首个或者最后1个字符，那么就只是单纯地表示字符。反过来说，如果表示的不是字符类，而是单纯的字符，那么就必须写在模式的开头或者末

在[]的开头使用^时，^表示指定的字符以外的字符。

\s 匹配空白字符
\d 匹配 0-9的数字
\w 匹配数字字母和下划线

我们还可以用\对元字符进行转义。在\后添加^、$、[等非字母数字的元字符后，该元字符就不再发挥元字符的功能，而是直接被作为字符本身用于匹配

在正则表达式中使用{}可以更详细地指定重复的次数。{3}表示刚好重复3次，{3,}表示重复3次以上，{,3}表示重复3次以下，{3,5}则表示重复3~5次。

最短匹配

表示重复0次以上的*以及表示重复1次以上的+会匹配尽可能多的字符1。相反，匹配尽可能少的字符2时，可以用以下元字符。
1. 尽可能匹配也成为贪婪匹配
2. 尽可能少的匹配称为懒惰匹配
例如：
*? ……0次以上的重复中最短的部分
+? ……1次以上的重复中最短的部分

以上说的都是以一个字符为单位就行匹配，如果想要以一个字符串为单位来匹配，则可以使用()来进行。

quote
quote可以用来转义所有的元字符，如果想要创建的模式里面包含元字符，但是只是想元字符的字面值，则可以用quote来转义。

patten = Regexp.new("abc*d")
p "abd" =~ patten

patten2 = Regexp.new(Regexp.quote("abc*d"))
p "abc*d" =~ patten2

正则表达式中的选项
pattern = Regexp.new("abc.def", Regexp::MULTILINE)
p "abc\ndef" =~ pattern
```

| 选项 |选项常量  |意义  |
|:---: | :--- | :--- |
|  i|  Regexp::IGNORECASE | 忽略大小写|
|  x| Regexp::EXTENED | 忽略模式中的空白字符或者#后面的内容 |
|  m| Regexp::MULTILINE | 匹配多行 |
| o |无  | 只使用一次内嵌表达式 |

```ruby
捕获
除了检查字符是否匹配外，正则表达式还有另外一个常用功能，甚至可以说是比匹配更加重要的功能——捕获（后向引用）。所谓捕获，就是从正则表达式的匹配部分中提取其中的某部分。通过$1、$2这种“$数字”形式的变量，就可以获取匹配了正则表达式中用()括起来的部分的字符串。
Note: 可以使用(?:)过滤不需要捕获的模式。
示例1：不关闭捕获
pattern2 = Regexp.new("(.)(.)(.)")
"abc" =~ pattern2
first = $1
second = $2
third = $3
p [first, second, third]

示例2：关闭捕获
pattern3 =  Regexp.new("abc(\\w)*d(?:.)f")
p "abcddsf" =~ pattern3
p $2 #=> nil 此时第二个括号并没有开启捕获

除了“$数字”这种形式以外，保存匹配结果的变量还有$`、$&、$'，分别代表匹配部分前面的字符串、匹配部分的字符串、匹配部分后面的字符串

pattern4 = /
    (\w+) # 匹配一个以上的单词数字下划线
    \s+   # 匹配一个以上的空白字符
    (\d+) # 匹配一个以上的数字
/ix

"dsa abc 123dsa" =~ pattern4
p $` # 匹配前的内容 
p $& # 匹配到的内容
p $' # 匹配后的内容

备注　匹配后的结果可以通过$~或者 Regexp.last_match获取。得到的返回值是MatchData类的对象，可以使用$~[1] 代替$1，$~.pre_match代替$`。

pattern4 = /
    (?<final>
      (?<first>\w+) # 匹配一个以上的单词数字下划线
      \s+           # 匹配一个以上的空白字符
      (?<third>\d+) # 匹配一个以上的数字
    )
/ix

"dsa abc 123dsa" =~ pattern4
p $` # 匹配前的内容
p $& # 匹配到的内容
p $' # 匹配后的内容

# Note $~ 和 Regexp.last_match是一样的，本质是MatchData的实例对象
# 因此它可以调用自己的实例方法

p $~[:first]
p $~[:third]
p $~[:final]

# $` $& $'效果是一样的
puts "#" * 9
p $~.post_match
p $~.pre_match
p $~.to_s
puts "#" * 9

#同上
puts "#" * 9
p Regexp.last_match.pre_match
p Regexp.last_match.post_match
p Regexp.last_match.to_s
puts "#" * 9
# 命名捕获可以加入标签，然后根据标签来获取捕获的内容
p Regexp.last_match(:first)
p Regexp.last_match(:third)
p Regexp.last_match(:final)

# 判断$~与Regexp.last_match是否为同一个对象
# 以下两种方法都能用来判断
p $~.__id__ == Regexp.last_match.__id__ 
p $~.eql?(Regexp.last_match)

pattern2 = Regexp.new("(.)(.)(.)")
"abc" =~ pattern2
first = $1
second = $2
third = $3
p $~[1] #=> $1
p $~[2] #=> $2
p $~[3] #=> $3

```
##### 使用正则表达式方法
```ruby

sub 方法
gsub 方法
sub方法与gsub方法的作用是用指定的字符串替换字符串中的某部分字符。sub方法与gsub方法都有两个参数。第1个参数用于指定希望匹配的正则表达式的模式，第2个参数用于指定与匹配的部分替换的字符串。sub方法只替换首次匹配的部分，而gsub方法则会替换所有匹配的部分。
str = "ab   cd ed      af"
p str.sub(/\s+/, " ")

str2 = "ab   cd ed      af"
p str2.gsub(/\s+/, " ")

sub方法与gsub方法还可以使用块。这时，程序会将字符串中匹配的部分传递给块，并在块中使用该字符串进行处理。这样一来，块中返回的字符串就会替换字符串中匹配的部分。

str = "ab   cd ed      af"
p str.sub(/\s+/, " ")

str2 = "ab   cd ed      af"
p str2.gsub(/\s+/, " ")


str3 = "ab   cd ed      af"
p str3.sub(/\s+/) {" "} #结果与不带块的形式一样
result = str3.gsub(/\s+/) do
   " "
end                     

p result


scan 方法
scan方法能像gsub方法那样获取匹配部分的字符，但不能进行替换操作。因此，当需要对匹配部分进行某种处理时，可以使用该方法

str4 = "arbcbdalr"
pattern = %r|.b|
str4.scan(pattern) do |matched|
   puts matched
end

str5 = "ardsgsdwsdas"
pattern2 = %r/(dsg)/  #带括号的会返回数组结果，并将结果赋值给块变量
                      #在括号里面加上?:，会让括号失去作用，返回的不再是数组而是字符串，与不加括号的效果一样
str5.scan(pattern2) do |matched|
   raise "find dsg" if matched[0] == "dsg"
end


```