## rails中的ActiveSupport::Concern
#### ActiveSupport::Concern的作用以及由来
在rails项目中，如果model的代码太多，可读性和维护性则会大大下降。一个好的解决办法是把相关的逻辑代码放到对应的模块(Module)里，然后把这个模块Mixin到对应的model中，以提高代码的高内聚，低耦合。而ActiveSupport::Concern为此提供了一种规范(约定),代替原本的Mixin。
#### ActiveSupport::Concern的运用
举一个简单的例子，比如一个项目中有很多model都需要有有判断这个 model 是否是 active 的。我们一般会给这个model加上一个is_active字段，然后再相应的model里面会有如下的方法。
```
class Post < ActiveRecord::Base

  # scopes
  scope :active, -> {where(is_active: true)}

  ...

  # instances methods
  def active?
    is_active
  end

  ...
end

class Advertisement < ActiveRecord::Base

  # scopes
  scope :active, -> {where(is_active: true)}

  ...

  # instances methods
  def active?
    is_active
  end

  ...
end
```
Post 和 Advertisement 都需要判断这个model 是不是 active 状态的，是否active这个逻辑属性在各个model里面的表现又是一致的，这样我们就可以通过 ActiveSupport::Concern 来重构.

首先把相同的逻辑放到一个 model 里面去，称为 act_as_activable。首先定义scope，然后定义 ClassMethods 和 instanceMethods。

```
# app/models/act_as_activable.rb or app/modeles/concerns/act_as_activable.rb
# for models with field :is_active

module ActAsActivable
  extend ActiveSupport::Concern

  included do |base|
    scope :active, -> {where(is_active: true)}
  end

  module ClassMethods
    def all_active(reload = false)
      @all_active = nil if reload
      @all_active ||= active.all
    end
  end

  # instance methods
  def active?
    is_active
  end
end
```
然后我们把这个model indclude 到需要的model 里面去。
```
class Post < ActiveRecord::Base

  # concerns
  include ActAsActivable
  ...
end


class Advertisement < ActiveRecord::Base

  # concerns
  include ActAsActivable
  ...
end
```
这里我们将 act_as_activable 这个可重用的功能抽出来，然后多个model可以共用.

那如果不用 concern, ActAsActivable 这个model会写成怎么样呢？
```
# app/models/act_as_activable.rb or app/modeles/concerns/act_as_activable.rb
# for models with field :is_active

module ActAsActivable
  def self.included(base)
    base.send(:include, InstanceMethods)
    base.extend ClassMethods
    base.class_eval do
      scope :active, where(is_active: true)
    end
  end

  module InstanceMethods
    def active?
      is_active
    end
  end

  module ClassMethods
    def all_active(reload = false)
      @all_active = nil if reload
      @all_active ||= active.all
    end
  end
end
```

使用这个钩子 self.included让里面的代码在被include的时候调用.base.send(:include,InstanceMethods), 用 send 方法 include InstanceMethods, 然后 extend ClassMethods, 就是把这些方法放到 base 的 eigenclass 里面。最后用 class_eval 打开这个类写 scope.

参考文献:
* [rails 4.1 ActiveSupport::Concern](https://api.rubyonrails.org/classes/ActiveSupport/Concern.html)
* [ruby-mixins-activesupportconcern/](http://engineering.appfolio.com/2013/06/17/ruby-mixins-activesupportconcern/)
* [rubychaina:lex (David.fu)](https://ruby-china.org/topics/19812)