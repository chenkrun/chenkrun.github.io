---
layout: post
title: Nginx学习笔记
date: 2018-09-17
categories: DevOps
tag: nginx
excerpt: 本文介绍Nginx基本指令和常用方法
---

# 0. 前言

Nginx

# 1. 高层配置（main块）

```
worker_processes auto;
```

worker_processes：Nginx开启的worker角色的进程个数，worker角色负责处理http或https请求。进程个数最优值取决于许多因素，包括CPU核心数量、负载模式等。作为最初的配置，可以将其设置为可用的CPU核心数目，“auto”能够自动检测CPU核心数目。

## 1.1 events块

events块的作用是配置Nginx“处理连接”的部分，允许微调配置来驱动事件循环。Nginx作为事件驱动服务器，每个worker进程都是单线程的，执行非阻塞事件循环快速处理请求。

```
events {
    worker_connections 512;
}
```

worker_connections：设置每个worker进程能够打开的同时连接的最大数目。

Linux中，每个连接被算作一个打开的文件，操作系统会限制每个进程所能打开的最大文件数量。因此，如果想要增加worker_connections，也要相应地增加操作系统限制的最大文件数量。

## 1.2 http块

http块的作用是定义HTTP服务器和其他适用的指令。http块和server块联系密切，一个http块中可以包含一个或多个server块。

```
http {
    include mime.types;
    default_type application/octet-stream;
    sendfile on;
    client_max_body_size 6000m;
    keepalive_timeout 65;
    
    server {
        ... ...
    }
}
```

include：在当前文件中包含mime.type文件内容，用于加载稍后将会使用的MIME类型。
default_type：当HTTP请求的文件没有在服务器中定义MIME类型映射时，使用default_type。
sendfile：启动**sendfile()**系统调用功能。
client_max_body_size：设置最大上传文件容量。
keepalive_timeout：设置HTTP请求结束后保持连接的超时时间。

### 1.2.1 server块

```
server {
    listen *:80;
    server_name "";
    root /usr/share/nginx/html;
    error_page 500 502 503 504 /50x.html;
}
```

listen：设置HTTP服务器监听的网络接口和端口。
server_name：匹配HTTP请求中的HTTP Host头部，进而选择server块。若为空，表明对HTTP Host头部没做要求。
root：设置静态文件存放的目录路径。root目录下的文件都能通过HTTP访问。
error_page：设置自定义HTML错误页面。

#### 1.2.1.1 配置SSL

Nginx提供了非常健壮且高性能的SSL（Secure Sockets Layer）实现。

为了设置SSL，需要：
1. SSL certificate（SSL证书）
2. SSL key（SSL私钥）

```
server {
    listen 80;
    listen 443 ssl; # 用443端口处理SSL请求
    server_name foobar;
    ssl_certificate ssl/nginx.crt; # Nginx默认相对路径为/etc/nginx/ssl/nginx.crt
    ssl_certificate_key ssl/nginx.key;
}
```

ssl_certificate：设置SSL证书。
ssl_certificate_key：设置SSL证书的私钥。

#### 1.2.1.2 location块

location块的作用是为HTTP请求的URI提供自定义配置，用前缀字符串或正则表达式来匹配URI。

```
location optional_modifier uri_matcher {
    ... ...
}
```

optional_modifier：可选的修饰符。例如，“=”。
uri_matcher：匹配的URI。例如，“/”、“/foo/”。

### 1.2.2 upstream块

upstream块的作用是定义upstream服务器，常用于定义负载均衡器。

```
http {
    upstream backend {
        server 192.0.2.10:4801;
        server 192.0.2.11:4801;
    }

    server {
        listen 80;
        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host:$server_port; # 用$host:$server_port代替HTTP请求中的的Host字段
            proxy_set_header X-Real-IP $remote_addr; # 将客户端IP地址存放在X-Real-IP内
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; # 添加$proxy_add_x_forwarded_for到X-Forwarded-For，记录经过的代理，格式为client，proxy1，proxy2，...
            proxy_read_timeout 6000;
        }
    }
}
```

proxy_pass：设置通过Nginx代理转发HTTP请求。
proxy_set_header：向后端主机新增HTTP头部或者修改已有的HTTP头部。
proxy_read_timeout：设置Nginx等候后端服务器响应时间。

# 2. Nginx命令参考表

| 选项 | 描述 |
|--------|--------|
|   -h   | 显示帮助 |
|   -v   | 显示版本 |
|   -V   | 实现版本和配置选项 |
|   -t   | 测试配置 |
|   -q   | 不显示非错误信息 |
|   -s signal   | 发送信号给主进程：stop, quit, reopen, reload |
|   -p prefix   | 设置前缀路径（默认为/usr/share/nginx） |
|   -c filename | 设置配置文件（默认为/etc/nginx/nginx.conf） |
|   -g directives | 在配置文件外设置全局指令 |
|   -T   | dumps整个配置文件 |