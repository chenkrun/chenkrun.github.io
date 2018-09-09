---
layout: post
title: Pyhon Web应用容器化
date: 2018-09-08
categories: DevOps
tag: Docker
excerpt: 本文介绍如何在容器中部署、运行Python Web应用
---

# 前言

之前自己在Python Web开发过程中，借助virtualenv来建立彼此独立的开发环境。例如，程序A希望在Python2.7版本下运行，程序B希望在Python3.5版本下运行。我们可以建立两个virtualenv环境，分别安装两个Python版本。如果把这套开发环境迁移到生产环境中，再部署相同的一套virtualenv环境，有太多重复的劳动。利用Docker，我们可以做到在同一台主机上开发时，建立不同的环境；还可以将建立的Docker镜像迁移到其他不同的主机上，不需要再次安装环境，就能保证开发、测试、生产环境一致。

# CentOS Docker安装

安装Docker前，需要检查系统版本信息：
```
cat /etc/redhat-release
uname -r
```
1. Docker运行在CentOS 7上，要求系统为64位、系统内核版本为3.10以上。
2. Docker运行在CentOS 6.5或更高版本的CentOS上，要求系统为64位、系统内核版本为2.6.32-431或者更高版本。

安装Docker时，可能会遇到"官网脚本执行失败问题"或"yum源找不到包"的问题：
```
[root@host ~]# curl -sSL https://get.docker.com/ | sh
# Executing docker install script, commit: 36b78b2

Either your platform is not easily detectable or is not supported by this
installer script.
Please visit the following URL for more detailed installation instructions:

https://docs.docker.com/engine/installation/

[root@host ~]# yum -y install docker-io
Loaded plugins: fastestmirror
Setting up Install Process
Loading mirror speeds from cached hostfile
 * base: mirror.san.fastserv.com
 * extras: centos.sonn.com
 * remi-safe: mirrors.mediatemple.net
 * updates: mirror.scalabledns.com
 * webtatic: us-east.repo.webtatic.com
No package docker-io available.
```
最后，是通过rpm安装成功的，这条记录是从开源中国的Docker更新记录中找到的：
```
yum install https://get.docker.com/rpm/1.7.1/centos-6/RPMS/x86_64/docker-engine-1.7.1-1.el6.x86_64.rpm
```
启动Docker，并且设置了开机自动启动：
```
[root@host ~]# service docker status
docker is stopped
[root@host ~]# service docker start
Starting cgconfig service:                             [  OK  ]
Starting docker:	                                   [  OK  ]
[root@host ~]# chkconfig docker on
```

# 部署Python Web应用

1. 创建Python Web应用程序目录docker_web_demo。在docker_web_demo中，应用程序源码目录为web_demo，用于构建Docker镜像的Dockerfile文件以及应用说明文件Readme.md。
```
[root@host docker_web_demo]# tree
.
├── Dockerfile
├── Readme.md
└── web_demo
    ├── app
    ├── config.py
    ├── data-dev.sqlite
    ├── manager.py
    ├── LICENSE
    ├── migrations
    ├── README.md
    ├── requirements.txt
```
2. 创建Dockerfile
创建Docker镜像的构建文件Dockerfile，如下：

```
########################################################## 
# Dockerfile to run a flask-based web application
# Based on an centos:7 image 
##########################################################
# Set the base image to use to centos 
FROM centos:7

# Set the file maintainer 
MAINTAINER chenkrun,chenkrun@gmail.com

# Set env varibles used in this Dockerfile (add a unique prefix, such as DOCKYARD) 
# Local directory with project source 
ENV DOCKYARD_SRC=web_demo

# Directory in Container for all project files 
ENV DOCKYARD_SRCHOME=/opt
# Directory in container for project source files 
ENV DOCKYARD_SRCPROJ=/opt/web_demo

# Update the default application repository source list 
RUN yum -y install epel-release
RUN yum -y install python-pip
RUN yum clean all

# Copy application source code to SRCDIR 
COPY $DOCKYARD_SRC $DOCKYARD_SRCPROJ

# Create application subdirectories 
WORKDIR $DOCKYARD_SRCPROJ
RUN mkdir log
VOLUME ['$DOCKYARD_SRCPROJ/log/']

# Install Python dependencies 
RUN pip install --upgrade pip
RUN pip install -r $DOCKYARD_SRCPROJ/requirements.txt

# Port to expose 
EXPOSE 5000

# Copy entrypoint script into the image 
WORKDIR $DOCKYARD_SRCPROJ
```

详细解释参考：https://docs.docker.com/engine/reference/builder/#usage

3. 构建镜像

```
docker build -t chenkrun/web_demo .

... ...
Successfully built 969ff7d73259
```

# 运行Python Web应用

1. 启动镜像

```
docker run -it --name web_demo -p 8000:5000 chenkrun/web_demo /bin/bash

-ti：分配一个终端，并且将STDIN与这个终端建立连接。如此，任何输入的命令都会在该终端内执行。
--name：为启动的容器起个名称。
-p 8000:5000：表示将Docker主机的8000端口与容器的5000端口绑定，访问Docker主机的8000端口时就相当于访问了容器内的5000端口，容器内的5000端口正好是Python Web应用提供的端口。
chenkrun/web_demo：为镜像的名称。


```

2. 在容器中运行Python Web应用

```
python manager.py
```

也可以采用"创建entry-point"启动脚本的方式，来运行Python Web应用，更加方便。