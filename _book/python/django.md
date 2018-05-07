## Django教程
### 1. 第一个Django应用
查看Django的版本
```
$ python
>>> import django
>>> django.__version__
```
示例：
```
falcon@ubuntu:/opt/code/django$ python
Python 2.7.12 (default, Nov 20 2017, 18:23:56) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import django
>>> django.__version__
'1.8.7'
```
#### 1.1 新建项目
在项目保存目录执行如下命令：
```
$ django-admin startproject mysite
```
这将在目录下生成一个mysite目录，也就是你的这个Django项目的根目录。它包含了一系列自动生成的目录和文件，具备各自专有的用途。
```
falcon@ubuntu:/opt/code/django$ django-admin startproject mysite
falcon@ubuntu:/opt/code/django$ ls
mysite
falcon@ubuntu:/opt/code/django$ tree
.
└── mysite
    ├── manage.py
    └── mysite
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
```
各文件和目录解释：
- mysite/：外层的mysite/目录与Django无关，只是你项目的容器，可以任意命名。
- manage.py：一个命令行工具，用于与Django进行不同方式的交互脚本，非常重要！
- 内层的mysite/：内层的mysite/目录是真正的项目文件包裹目录，它的名字是你引用内部文件的包名，例如：mysite.urls。
- mysite/__init__.py:一个定义包的空文件。
- mysite/settings.py:项目的主配置文件，非常重要！
- mysite/urls.py:路由文件，所有的任务都是从这里开始分配，相当于Django驱动站点的内容表格，非常重要！
- mysite/wsgi.py:一个基于WSGI的web服务器进入点，提供底层的网络通信功能，通常不用关心。
![image](images/django组织结构.jpg)
#### 1.2 启动开发服务器
进入mystie项目的根目录，输入下面的命令：
```
$ python manage.py runserver
```
Django的开发服务器（以后简称服务器）默认运行在内部的8000端口，如果你想指定端口，请在命令中显示给出：
```
$ python manage.py runserver 8080
```
如果想修改服务器的ip地址，请按下面的方式运行命令：
```
$ python manage.py runserver 0.0.0.0:8000
```
这时，Django将运行在8000端口，整个局域网内都将可以访问站点，而不只是是本机。

注意： Django的开发服务器具有自动重载功能，当你的代码有修改，每隔一段时间服务器将自动更新。但是，有一些例如增加文件的动作，不会触发服务器重载，这时就需要你自己手动重启。