#### 利用logrotate分割日志
- logrotate是linux自带的日志分割工具
- logrotate配置：
```bash
    cd /etc/logrotate.d # 进入配置文件夹
    vim myproject       # 新建并打开配置文件
```
- 在文件中填入一下内容
```bash
/var/projects/sanmblog/log/production.log { # 项目日志文件路径
        weekly # 按周，也可以 daily 按日，monthly 按月
        missingok # 如果文件不存在，忽略错误信息
        rotate 8 # 保留8个拆分文件，超过时会删除最久远的文件
        compress # 压缩
        dateext # 增加日期作为后缀
        delaycompress #延迟压缩，也就是本次拆分的文件在下次执行时再压缩
        notifempty # 忽略空白文件
        olddir /var/projects/sanmblog/log/production # 日志转存到其他文件
        lastaction # 所有命令执行完执行以下命令
                /etc/init.d/nginx reload > /dev/null
        endscript
}

```
- 保存配置文件，执行以下命令检测配置文件确保无错误
```bash
/usr/sbin/logrotate /etc/logrotate.d/myproject -v
```
- 执行以下命令，会看到拆分后的日志文件
```bash
/usr/sbin/logrotate -f /etc/logrotate.conf
```
