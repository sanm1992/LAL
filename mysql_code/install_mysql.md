## Linux下安装mysql
* **采用默认命令行安装，不会提醒输入用户密码，之后会很坑，所以采用如下方式：**
    - 用虚拟机浏览器https://dev.mysql.com/downloads/file/?id=477124 ，点击下方==No thanks, just start my download.==,手动下载安装包
    - 解析安装包
    ```
    sudo dpkg -i mysql-apt-config_0.8.6-1_all.deb
    # 这里需要注意下载的安装包版本，修改解析命名的版本与之对应
    ```
    - 安装mysql
    ```
    sudo apt-get update
    sudo apt-get install mysql-server
    sudo apt-get install libmysqlclient-dev
    ```
    - 在安装过程中根据提示输入密码
    - 启动和关闭musql
    ```
    service mysql start
    service mysql stop
    ```
    - 查看是否启动成功
    ```
    sudo netstat -tap | grep mysql
    ```
    - 卸载mysql
    ```
    sudo apt-get autoremove --purge mysql-server-5.0
    sudo apt-get remove mysql-server
    sudo apt-get autoremove mysql-server
    sudo apt-get remove mysql-common 
    ```
    - 清理残留数据
    ```
    dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
    ```
    
* **设置远程连接**

    具体思路是修改root的权限，让其可以被其他用户远程访问，网上大多方案是通过语句
    ```
    GRANT ALL ON *.* TO 'root'@'%';
    ```
    为root用户（或者是其他用户）设置权限，但我试了几次都是失败的，都报错了，
    ```
    ERROR 1410 (42000): You are not allowed to create a user with GRANT
    ```
    鼓捣了许久，看到别人的博客，一语惊醒梦中人。其实上面我们已经知道，就是修改root或者某个用户的访问权限，其次，我们知道mysql数据库安装完成后有默认的mysql数据库，里面有个user表，数据就是默认的系统用户的一些信息，包括权限。现在来查看user表到底有些什么：
    ```
    select host,user,authentication_string,plugin from user;
    
    ```
    我们看host和user两列，host和user中root对应的值为localhost，即root用户的访问权限为localhost，想把该用户的访问权限设置可远程连接，我们只需要把localhost更改为通 的%就可以了。上面的权限操作语句没有走通，这里就直接使用update语句来更改吧。
    ```
    update user set host = "%" where user = "root";
    ```
    修改完之后刷新数据库权限就ok了
    ```
    flush privileges;
    ```
    但当我测试连接的时候，navicat for mysql 连接报错1251，然后网上找到解决方法：原来是8.0的版本，因为比较新的mysql采用新的保密方式所以旧的似乎不能用，改密码方式
    ```
    use mysql;
    ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '你的密码';
    flush privileges;
    ```
    到此，连接成功;
    
