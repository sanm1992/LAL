```ActiveSupport```中提供的一个叫做```extract_options!```的牛逼方法。它的主要作用是提取给出的参数中的选项（options）。如果没有选项（options）它会返回一个空的Hash。

秀一个列子
```ruby
# Use args with the splat operation to allow
# an unlimited number of parameters
def my_method(*args)
  options = args.extract_options!
  puts "Arguments:  #{args.inspect}"
  puts "Options:    #{options.inspect}"
end

my_method(1, 2)
# Arguments:  [1, 2]
# Options:    {}

my_method(1, 2, :a => :b)
# Arguments:  [1, 2]
# Options:    {:a=>:b}
```
```extract_options!```可以让你从一个数组的参数中方便的提取出选项列（a list of options），这通常发生在方法调用时。 它不是Ruby的标准方法，但是它是Rails的核心扩展，并且你需要引用```ActiveSupport```才能使用。请注意它是一个“bang”方法，因此它会修改调用它的对象。
