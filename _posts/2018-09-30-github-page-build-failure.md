---
layout: post
title: github.io page build failure
date: 2018-09-30
categories: DevOps
tag: blog
excerpt: 本文介绍如何解决github.io页面构建失败问题
---

## 一、前言

使用Jekyll和github pages搭建个人博客时，可能会遇到这样的问题：

```
The page build failed for the `master` branch with the following error:

The tag `set` on line 62 in `_posts/xxx.md` is not a recognized Liquid tag. For more information, see https://help.github.com/articles/page-build-failed-unknown-tag-error/.

For information on troubleshooting Jekyll see:

  https://help.github.com/articles/troubleshooting-jekyll-builds

If you have any questions you can contact us by replying to this email.
```

## 二、分析

从错误的描述，大致可以判断错误发生原因是：_posts/xxx.md文件中的*set*标签不被Liquid标签识别。

由于_posts/xxx.md中带有jinja2模板的标签，而Jekyll使用Liquid模板，会将jinja2模板中的`set`、`for`、`{{}}`和`{%%}`当作Liquid模板的标签，导致解析错误。

## 三、解决

使用Liquid中的raw标签，临时禁用对markdown文件中jinja2标签处理。不过操作上有点麻烦。

```
{% raw %}
{% raw %}
    ... ...
{% endraw %}
{% endraw %}
```

参考：https://github.com/Shopify/liquid/wiki/Liquid-for-Designers#raw