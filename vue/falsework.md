### 一、环境搭建

#### 1. 安装 nvm（Node Version Manager）
在终端中执行命令：
```
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash
```
然后执行 `source ~/.bashrc && source ~/.bash_profile` 加载环境

查看是否安装成功
```
$ nvm

Node Version Manager

Note: <version> refers to any version-like string nvm understands. This includes:

```

> `nvm help`可以查看nvm命令

#### 2. 使用 nvm 安装 node

运行 `nvm install v8.4.0` 安装node

```
$ nvm install v8.4.0
Downloading https://nodejs.org/dist/v8.4.0/node-v8.4.0-darwin-x64.tar.gz...
################################################################## 100.0%
```
>使用`nvm use <version>`命令切换版本 

>使用`nvm alias default <version>`命令来指定一个默认的node版本

#### 3. 安装vue相关

设置淘宝源
`npm config set registry https://registry.npm.taobao.org`

`npm install -g cnpm`

安装webpack
`cnpm install webpack -g`

安装vue手脚架
`npm install vue-cli -g`

> 报权限相关就运行`sudo chmod -R 777 /usr/local/lib/node_modules/`

### 二、其他

##### 创建vue项目


```
$ vue init webpack-simple 工程名字
$ cd 工程名字
$ npm install
$ npm run dev
```

##### 新版运营系统相关

```
$ git clone ssh://git@stash.sz.optilink.local:7999/pacific/webpc.git
$ cd webpc
$ npm install
$ npm run dev
```

##### 遇到如下问题时：
```
Module build failed: ENOENT: no such file or directory, scandir '/home/xxx/webpc/node_modules/node-sass/vendor'
```
执行`npm rebuild node-sass`

##### 启动webpack-dev-server只能本机访问的解决办法

* 修改package.json的dev启动设置，增加--host 0.0.0.0 启动后localhost更换为本机IP即可访问

> 登录授权问题。。。

### 三、vue资料相关

- [vue官网](https://cn.vuejs.org/)
- [菜鸟教程](http://www.runoob.com/vue2/vue-tutorial.html)
- [vue-router](https://router.vuejs.org/zh-cn/)
- [vux-基于WeUI和Vue(2.x)开发的移动端UI组件库，主要服务于微信页面](https://vux.li/)