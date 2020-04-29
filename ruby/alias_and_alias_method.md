### ruby中的alias和alias_method
* 1.alias 是 Ruby 的一个关键字，因此使用的时候是 ```alias :new name :oldname```，而 alias_method 是 module 类的一个方法，因此使用的时候是 ```alias_method(:newname, :oldname)```有一个逗号;

* 2.alias_method 可以重定义，alias不能

* 3.方法调用时的参数不同：alias_method要求参数是字符串或者符号变量，alias使用变量名或者符合变量

* 4.作用域不同：强调在顶级作用域main中，只能通过alias进行别名定义，而不能使用alias_mehtod是因为在main中调不到Module#alias_method方法，而alias是关键字的缘故，所以能正常在main中使用。

* 5.通过alias定义方法别名，其变量self的内容是固定不变的，即self所代表的是调用的对象，而alias_method方法中的self却是在运行时确定的,如下：

 **alias实例代码**
```ruby
class User
  def full_name
    puts "Johnnie Walker"
  end

  def self.add_rename
    alias :name :full_name
  end
end

class Developer < User
  def full_name
    puts "Geeky geek"
  end
  add_rename  #执行类方法，但alias是关键字，在定义时，已经绑定了self为User
end

Developer.new.name #=> ‘Johnnie Walker‘
```
**alias_method示例代码**
```ruby
class User
  def full_name
    puts "Johnnie Walker"
  end

  def self.add_rename
    alias_method :name, :full_name
  end
end

class Developer < User
  def full_name
    puts "Geeky geek"
  end
  add_rename   #执行类方法。self是Developer。
end

Developer.new.name #=> ‘Gekky geek‘
```