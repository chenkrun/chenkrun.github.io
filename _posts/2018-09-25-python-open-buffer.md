---
layout: post
title: Python open文件缓冲区
date: 2018-09-25
categories: DevOps
tag: python
excerpt: 本文介绍Python内置函数open的buffering参数
---

# 前言

Python内置函数open的buffering参数，用于设置打开文件时的缓冲策略。

Buffer缓冲区是为了提高内存和硬盘间的数据交换速度而设计的，把分散的写操作集中进行，减少磁盘反复寻道时间。

# 缓冲策略

针对不同类型的文件有不同的缓冲策略：

- 二进制文件（Binary）
    1. buffering=-1：使用系统默认buffer大小；
    2. buffering=0：不使用buffer；
    3. buffering=1：buffer大小为1字节；
    4. buffering>1：指定buffer大小。

- 文本文件（Text）
    1. buffering=-1：使用系统默认buffer大小；
    2. buffering=0：不允许设置；
    3. buffering=1：行缓冲；
    4. buffering>1：使用系统默认buffer大小。

**注意**：
1. 打开文件写入数据后，如果close或提前flush，会将缓冲区的数据写入磁盘。
2. 若不显示设置buffering参数，默认为buffering=-1。