### 路由文件拆分和重构
* 当项目路由越来越多，routes文件会变得拥挤，难以管理，这时候就需要我们对路由文件进行拆分

```ruby
# 重构前
namespace :a do
  resources :users
  resources :orders
end

namespace :b do
  resources :users
  resources :orders
end

# 重构后
concern :base do
  resources :users
  resources :orders
end

namespace :a do
  concerns :base
end

namespace :b do
  concerns :base
end
```
* 拆分重构

1.初始化一个实例
```ruby
# vim config/initializers/routing_draw.rb
# Adds draw method into Rails routing
# It allows us to keep routing splitted into files
class ActionDispatch::Routing::Mapper
  def draw(routes_name)
    instance_eval(File.read(Rails.root.join("config/routes/#{routes_name}.rb")))
  end
end
```
2.在routes文件里使用
```ruby
Rails.application.routes.draw do
  draw :admin
end
```
3.创建拆分之后对应的文件(为了方便管理，我们可以以功能分块来拆分路由，放到对应的文件)
```ruby
#vim config/routes/user.rb
namespace :user do
  resources :users
end
```
* 其他方法
```ruby
Dir["#{Rails.root}/config/routes/*.rb"].each { |f| require f }
```
