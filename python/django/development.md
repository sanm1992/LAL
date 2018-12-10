##### 1. 当用django-admin startproject projectName创建对应的django项目时出现command not found: django-admin情况的解决方案：
* 原因是找不到对应的django-admin.py的路径，python加载django错误
* 解决办法：
    - 找到django的绝对路径：在终端输入```pip3 install Django``` 尝试安装django,终端会提示已经安装，并且给出已经安装的django的绝对路径
    - 切换到对应的目录下可以看到'django-admin.py'文件
    - 建立软连接：```sudo ln -s /usr/local/python3/lib/python3.7/site-packages/django/bin/django-admin.py  /usr/local/bin/django-admin.py```
    - 然后切换到你想创建项目的目录下，运行命令： ```django-admin startproject testdjango```,即可看到testdjango项目创建成功
