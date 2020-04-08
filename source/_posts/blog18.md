---
data:
title: EOJ搭建教程
update:
categories:
- 技术
tags:
- tec
---
# ECNU Online Judge 搭建
<!--more-->
## 一、系统介绍
    eoj是基于python框架Django开发的一款在线程序评测系统。
## 二、EOJ环境配置
### 1、 安装Django:
默认情况下，Ubuntu 18.04附带Python 3.6。 您可以通过键入以下内容来验证系统上是否安装了Python 3：

```cpp
python3 -V
```
安装Django：
```cpp
pip3 install django
```

### 2、eoj前端配置

克隆eoj:

```cpp
git clone https://github.com/ultmaster/eoj3.git
```
进入eoj3目录安装依赖:

```cpp
pip3 install -r "requirements.txt"
```

如果中途出错，执行:

```cpp
sudo apt-get install libmysqld-dev
```

执行完之后再重新获取依赖即可

接下来我们需要配置一下JS环境
进入static文件夹:

```
cd static 
```
安装npm和js:

```cpp
apt install npm
npm install
```
此时前端还差一个css文件，我们从ECNUOJ官网就可以获取到。

把这个名为app.min.css的文件保存到eoj3/static/css下
这样我们的前端就算是搭建完毕了。

### 3、Django配置
我们需要导入数据库,执行

```cpp
python manage.py migrate
```
创建超级管理员：

```
python manage.py createsuperuser
```
安装redis-server:

```
apt-get install redis-server
```
这样我们运行80端口：

```
python3 manage.py runserver 0.0.0.0:80
```
输入服务器ip即可访问.但是现在还是无法判题,因为我们没有安装ejudge,

ejudge可以看作是一台server,用来进行判题.

### 4、ejudge配置

从github上把ejudge克隆下来
```cpp
git clone https://github.com/ultmaster/ejudge.git
```

安装docker:

```cpp
apt install docker.io
```
执行：

```
systemctl start docker
systemctl enable docker
```
查看是否成功：

```
docker -v
```

进入ejudge执行：

```
ssh init.sh
```

在ejudge下执行：

```cpp
docker build .
```

等待...等待...(如果失败了多试几次)
检查镜像是否创建成功:

``` cpp
docker images
```
运行容器：

```
sudo docker run -d -p 5000:5000 -v /path/to/your/run/:/ejudge/run <imageName>
```

### 5、后台配置：
用超级管理员登陆账户，进入后台，点击server
将server命名为ejudge
ip设置为:127.0.0.1
enable server.
最后回到eoj3目录下执行：

```cpp
python3 manage.py runserver 0.0.0.0:80 & python3 manage.py qcluster
```

这样EOJ就搭建完成了。
如果普通用户显示eoj is closed,进入util目录，将site_settings.py的

```
def force_closed():
    return {"force_closed": True}
```
改为

```
def force_closed():
    return {"force_closed": False}
```

大功告成！！
