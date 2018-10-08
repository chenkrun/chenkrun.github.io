---
layout: post
title: Python中Unicode编码
date: 2018-10-07
categories: DevOps
tag: python
excerpt: 本文介绍Python中关于Unicode编码的内容
---

## 一、前言

字节是计算存储容量的基本单位，也是内存寻址的单位。一个字节包含8个二进制位，即1Byte = 8bit。

Unicode编码是一种编码标准，相应还有ASCII编码、UTF-8编码等标准。编码标准对文字系统进行整理、编码，使得计算机可以用更为简单的方式呈现和处理文字。

- ASCII编码：一个英文字符（不分大小写）占1个字节，一个中文汉字占2个字节。
- UTF-8编码：一个英文字符占1个字节，一个中文占2～4个字节。
- Unicode编码：一个英文字符占2个字节，一个中文占2个字节。
- 英文标点占1个字节，中文标点占2个字节。

## 二、Unicode编码

从上面的介绍可以看到，Unicode编码是一种宽字节编码。通常情况下，我们可以直接在程序中使用Unicode编码，保证通用性（跨平台）。但是，稍微有点浪费。

一般编码转换流程如下：

1. 程序中直接使用Unicode编码。
2. 在需要网络传输时，将Unicode编码encoding为约定编码（eg. UTF-8）再传输。
3. 接收端接收到字节流后，按照约定编码decoding为Unicode编码使用。

在Python中，Unicode类型与普通字符串类型相似，也有许多可供使用的方法。

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-

str = u'国庆节'
print str.count(u'庆')
print str.find(u'节')
```

## 三、关于默认编码方式

需要提醒的是，若在Python中不指定编码标准，那么Python会使用默认的编码标准。可以采用`sys.getdefaultencoding()`来查询默认的编码标准。

一般来说，默认编码标准为ASCII编码。因此，`s.encode("utf-8")`等价于`s.decode(defaultencoding).encode("utf-8")`，`u = unicode(s)`等价于`u = unicode(s, defaultencoding)`。