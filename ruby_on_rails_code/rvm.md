RVM是Ruby的版本管理器工具。
### 1、安装RVM
```
sudo apt install curl
curl -L https://get.rvm.io | bash -s stable
source ~/.rvm/scripts/rvm
```
### 2、安装RVM的环境依赖
```
rvm requirements
```
### 3、安装Ruby
* 列出所有可安装ruby版本
```
rvm list known
```
* 列出当前安装的ruby版本
```
rvm list
```
* 安装ruby
```
rvm install 2.4.1
```
* 查看当前安装ruby版本
```
ruby -v
```
* 切换ruby2.4.1
```
rvm 2.4.1
```
### 4.安装rails
```
gem install rails --version 5.2.0
```

### 5.gemset运用
* 创建gemset
```
rvm use 1.8.7
rvm gemset create rails23
```
* 切换gemset
```
rvm use 1.8.7
rvm use 1.8.7@rails23
```
* 查看当前gemset list
```
rvm use 1.8.7
rvm use 1.8.7@rails23
```
* 清空gemset中的gem
```
rvm gemset empty 1.8.7@rails23
```
* 删除gemset
```
rvm gemset delete rails2-3
```
* 项目自动加载gemset
```
项目根目录，建立一个 .rvmrc 文件，加入以下语句
rvm use 1.9.3@rails313
```