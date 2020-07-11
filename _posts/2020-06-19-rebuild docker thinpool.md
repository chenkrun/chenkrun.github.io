---
layout: post
title: 重建docker thinpool
date: 2020-06-19
categories: DevOps
tag: tool
excerpt: 本文介绍当docker相关文件目录丢失了，如何进行环境恢复
---

## 一、前言

某些情况下，应用升级或者虚拟机重启可能会导致docker相关文件目录丢失，导致docker服务启动失败，需要进行环境恢复。

## 二、重建docker thinpool

1. 进入`/var/lib/docker`目录，与正常环境比较，确认是否是由于文件丢失导致docker启动失败。

2. 若确认是，则进行如下恢复操作。
3. 关闭`systemctl`重启docker服务。

```
cd /usr/lib/systemd/system
mv docker.service docker.service.bak
```

4. 删除`/var/lib/docker`目录下除了`hooks`文件夹之外的所有文件。

```
rm -rf !(hooks)
```

5. 删除残留的thinpool卷。

```
lvremove /dev/VG_vdb/thinpool
```

6. 重建thinpool卷。

```
lvcreate --wipesignatures y -n thinpool VG_vdb -L 18.1g, 18.1g为thinpool_size
lvcreate --wipesignatures y -n thinpoolmeta VG_vdb -L 0.38g, 0.38g为metadata_size
lvconvert -y --zero n -c 512k --thinpool VG_vdb/thinpool --poolmetadata VG_vdb/thinpoolmeta, 把thinpool数据卷和thinpoolmeta元数据卷换为一个精简池
```

7. 查询VG_vdb-thinpool.profile是否生成。

```
cd /etc/lvm/profile/
ls -l
```

8. 激活lvm profile

```
lvchange --metadataprofile VG_vdb-thinpool VG_vdb/thinpool
```

9. 开启`systemctl`重启docker服务。

```
cd /usr/lib/systemd/system
mv docker.service.bak docker.service
systemctl enable docker.service
```

10. 启动docker服务并查询服务是否启动完毕

```
systemctl start docker
systemctl status docker
```