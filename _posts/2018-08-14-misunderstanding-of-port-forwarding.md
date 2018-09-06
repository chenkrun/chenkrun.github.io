---
layout: post
title: 对端口转发的误解
date: 2018-08-14
categories: network
tag: iptables
excerpt: 本文介绍端口转发中对DNAT与SNAT或MASQUERADE组合的误解
---

# 前言

端口转发，就是将数据包从源服务器的端口转发到目标服务器的端口，代理就是最简单的端口转发。有许多途径能够实现端口转发，比如常见的iptables、socat、rinetd等，但是转发的数据包可能有所不同，有些是IP包，有些是TCP包。

# 实践中遇到的问题

拿iptables举例，经常将DNAT与SNAT组合或DNAT与MASQUERADE组成来实现：
```
iptables -t nat -A PREROUTING -p tcp --dport <server_first_port> -d <server_first_ip> -j DNAT --to-destination <server_second_ip>:<server_second_port>
iptables -t nat -A POSTROUTING -j MASQUERADE
```
无论是采用SNAT或MASQUERADE都会改变发送到第一台服务器的源IP地址，然后第二台服务器将数据包又发回到改变后的源IP地址。这时，会很自然地想到将SNAT或MASQUERADE删掉，但这样做会带来另一个后果---第二台服务器的应答数据包将回不到第一台服务器。

# 解决方法

在端口转发时，既要保留源IP地址的属性，又想应答数据包正常返回给第一台服务器，进而返回给发送端。有一种巧妙的方法可以解决，不过可能只适用于某些场景：
1. 保留DNAT转发规则；
2. 在第二台服务器设置路由规则，若目的主机IP地址为源IP地址，就通过第一台服务器作为网关，将数据包从第二台服务器路由出去。
3. 最好能自定义路由表，以免产生冲突。