---
layout: post
title: 使用audit监控文件
date: 2020-01-09
categories: DevOps
tag: tool
excerpt: 本文介绍使用audit监控文件
---

## 一、前言

当Linux系统内文件的内容被修改后，想抓住“罪犯”，可以使用audit。

## 二、安装audit服务包，启动audit服务

1. 准备对应Linux系统的audit服务包并安装。

- audit-2.8.1-3.el7_5.1.x86_64.rpm。
- audit-libs-2.8.1-3.el7_5.1.x86_64.rpm。

2. 装好之后，可以通过rpm命令查询是否安装完成。

```
rpm -qa | grep audit
```

3. 使能audit服务。

```
systemctl enable auditd.service
```

4. 启动audit服务。

```
systemctl start auditd.service
```

## 三、举例：配置监控/etc/sudoers

1. 针对/etc/sudoers进行监控配置。

```
auditctl -w /etc/sudoers -k zuifan
```

因为auditd不支持restart、stop，只支持reload、start。所以，当用auditctl修改了监控配置后，只能用reload重新加载auditd规则。

```
systemctl reload auditd
```

2. 查询生效后的auditd规则。

```
auditctl -l
```

3. 查询auditd日志，确认是哪位“罪犯”修改了。

```
ausearch -f /etc/sudoers
```

4. 查询/var/log/audit/audit.log中指定关键字“zuifan”的日志，监控/etc/sudoers被修改的动作。

5. 知道“罪犯”后，去除audit，防止影响正常环境运行。

audit服务默认不能手工停止，需要修改/usr/lib/systemd/system/auditd.service中的RefuseManualStop=yes为RefuseManualStop=no。再执行systemctl stop auditd。