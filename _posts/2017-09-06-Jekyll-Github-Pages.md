---
layout: post
title: Jekyll+Github Pages搭建个人博客
date: 2017-09-06
categories: DevOps
tag: blog
excerpt: 本文介绍如何用Jekyll和Github Pages搭建个人的技术博客
---

# 前言

Jekyll是一款开源的静态网站搭建工具，不需要数据库支持，拥有丰富的插件和主题，还有前人已经完成的可供参考的模板。尤其是能够免费部署在Github，成为托管在Github上的静态网站。这种方式可以作为搭建“小型”博客的选择之一。

# 搭建

1. 安装RVM
RVM是一款Ruby版本管理工具，提供多版本Ruby环境的管理与切换。其中，Ruby环境包括Ruby本身、依赖的第三方插件等。
```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
curl -sSL https://get.rvm.io | bash -s stable
```
载入RVM环境
```
source /usr/local/rvm/scripts/rvm
```
2. 使用RVM安装Ruby
```
rvm requirements # 下载依赖包
rvm list known # 列出已知的Ruby版本
rvm install 2.3.0
rvm use 2.3.0 --default # 将指定该Ruby版本为系统默认版本
rvm list # 查询已经安装好的Ruby版本
```
3. 使用gem安装Jekyll
RubyGems是Ruby的一个包管理器，类似于pip、yum和apt-get，提供了分发Ruby程序和库的标准格式“gem”，为了方便地管理gem安装的工具以及用于分发gem的服务器。
```
gem install jekyll
```
4. 安装bundler
bundler相当于运行多个RubyGems批处理，配置文件Gemfile中记录着应用依赖的第三方包，bundler自动下载安装多个包，包括依赖包。
```
gem install bundler
```
5. 测试Jekyll
```
jekyll new my_blog
cd my_blog
jekyll server --host=0.0.0.0
```

# 在Github上托管博客
github.io是完全基于Github创建的，本质是Github账户下创建的特殊repo。
1. 创建一个新的repo，repo名称格式为username.github.io，username为Github帐户名。
2. 克隆其他人的主题（也可以自己从0实现），并且添加到自己的github.io中
```
git clone https://github.com/Huxpro/huxpro.github.io.git # 其他人的
git remote add origin https://github.com/<username>/<username>.github.io
```
3. 修改_config.yml配置
由于是其他人的博客主题，所以有其他人的信息，需要修改。
4. 发布博客
Jekyll对于博客文章，要求放在_posts目录下，并且对博客文章的名称也有严格的规定，必须保持“年-月-日-题目.md”格式。
```
git add _posts/2017-09-06-Jekyll-Github-Pages.md
git commit -m "A add a new blog to my github.io"
git push origin master
```
等待10秒左右，就可以打开博客网站查看博客文章了。

- 参考资料：https://segmentfault.com/a/1190000012560959#articleHeader0