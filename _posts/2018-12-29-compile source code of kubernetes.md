---
layout: post
title: Go编译Kubernetes源码
date: 2018-12-29
categories: DevOps
tag: kubernetes
excerpt: 本文介绍直接使用Go编译Kubernetes源码，产生二进制文件
---

## 一、前言

Go编译分为两种方式，一种是全编译，另一种是单独模块编译。

## 二、安装Go编译环境

准备编译Kubernetes源码前，需要安装Go编译环境。以编译Kubernetes 1.9源码为例子，与之对应需要安装go 1.9.1编译环境。

- 从官网`https://golang.org/dl/`下载Go安装包`go1.9.1.linux-amd64.tar.gz`。
- 将Go安装包解压到`/usr/local`目录。

```
tar -zxvf go1.9.1.linux-amd64.tar.gz -C /usr/local
```

- 将环境变量添加到`/etc/profile`文件结尾。

```
export GOROOT=/usr/local/go
export GOPATH=/home/chenkrun/gopath
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

1. GOROOT：go安装路径。
2. GOPATH：项目工作目录，go命令和go工具都会使用到该环境变量。GOPATH目录主要包含三个目录bin，pkg，src。bin存放可执行文件，pkg存放编译好的库文件，主要是*.a文件；src存放go的源代码，之后要编译的Kubernetes源码即存放其中。
3. go命令go get下载的第三方工具, 一般都会下载到bin目录里，所以需要把GOPATH中bin目录也配置到环境变量中, 否则将无法使用下载的第三方工具。

- 重新加载`/etc/profile`文件，加载环境变量。

```
source /etc/profile
```

## 三、Go编译Kubernetes源码

- 下载Kubernetes源码

常用`git clone https://github.com/kubernetes/kubernetes.git -b release-1.9`选择需要下载的分支，下载到`$GOPATH/src/k8s.io/kubernetes/`目录下。还可以通过`go get -d k8s.io/kubernetes`。通过`go get`来下载源码时，有时会因为Go版本兼容性问题，报错`no Go source files in xxx`。

- 第一种：全编译方式

这种方式比较简单，直接进入kubernetes目录先`make clean`，然后直接`make`就行。最后，会在`kubernetes/_output`目录中得到二进制文件。全编译虽然简单，但是对编译机器的硬件条件有要求，尽量多CPU大内存。不然会报一些奇怪错误，如`./hack/run-in-gopath.sh: line 33: 12374 Killed  "${@}"`，`kernel:BUG: soft lookup - CPU#0 stuck for 22s! [...]`。

- 第二种：单独模块编译方式

可以通过制定相关参数`make WHAT=cmd/kubelet`或`make kubelet`。还可以进入到`cmd/kubelet`目录中，执行`go build`命令在当前目录下生成二进制文件。
