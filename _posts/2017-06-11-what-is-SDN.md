---
layout: post
title: SDN是什么
date: 2017-06-11
categories: Network
tag: SDN
excerpt: 本文介绍SDN的概念、由来和发展
---

# 前言

2017年5月份，Intel公司副总裁兼网络平台组总经理Sandra Rivera表示2017年将会是SDN/NFV发展的转折点，业界将会看到SDN和NFV的商用部署率变得更高，市场上将有更多的可编程解决方案。AT&T宣称到2020年能够实现75%网络的虚拟化，美国其他运营商如Sprint和T-Mobile也对SDN和NFV有些小规划，预计在5G的部署过程中采用SDN/NFV技术。

早在2012年，VMware就斥资12.6亿美元收购创新公司Nicira，不仅包括该公司的技术，还有Nicira的顶级技术人才。2016年底，VMware再次收购SDN创新公司PLUMGrid的技术和员工，加强自身OpenStack软件NSX的定位。VMware如此囤积技术和人才，目的十分明显。希望重新定义网络，取出网络硬件精髓，让它们以软件形式存在，更为轻松地搭建和维护支撑现代企业的网络设备，尝试在业界重新洗牌。

# 什么是SDN

![SDN架构.jpg](E:\Work\SDN\pic\SDN架构.jpg)

SDN官方解释中提出SDN三个特性：
1. 集中式管理，即具有统一的管理接口；
2. 控制与转发（数据计算）分离：控制器集中了全部的网络业务功能。控制器与数据计算节点之间通过南向接口协议通信。OpenFlow正是第一个开放式的南向接口协议。
3. 开放式API：SDN架构中有两种API，一种是控制平面和数据平面的接口API——南向接口；另一种是应用与数据平面的接口API——北向接口。南向接口的可编程能力将直接决定SDN架构的可编程能力。众多网络设备厂商都希望独占南向接口的通信接口标准，进而掌握话语权，因此南向接口也是网络设备厂商竞争的“天王山”。

SDN作为一种网络架构的新理念，凡是满足SDN三大特性，都可以被称为SDN。

# SDN背后的故事

SDN从2009年正式被提出后，一直在发展，但是过了一段时间之后才进入人们的视野。其中原因与另外一个技术的发展相关，那就是云计算技术。云计算就是使计算分布在海量的分布式节点上并且保持弹性，形象一点解释就是使“资源池化”，在需要时按需索取、动态管理。为了实现按需索取、动态管理，才有了虚拟化技术。在虚拟化技术中，计算虚拟化发展最早、最快，网络虚拟化和存储虚拟化相对滞后。当需要寻找解决网络虚拟化的方法和工具时，才想起了SDN。

由于SDN与云关系紧密，往往会让人误解“有SDN必有云”。实际上，存在网络的地方就有SDN发挥的余地，SDN技术应用范围囊括整个信息技术领域。云计算网络（例如，OpenStack Neutron）仅仅是SDN众多北向应用中的某一个。

# 为什么要采用SDN

SDN带来的最大好处在于通过SDN实现的网络功能的成本比传统网络小许多，这些成本包括：开发周期、网络复杂度、架构完整度、网络IO路径长度等等。

# SDN的起源

说到SDN的起源，肯定会提到斯坦福大学教授Nick McKeown。SDN正是起源于Nick McKeown教授带领的团队项目，正式发展的标志应该是2008年的OpenFlow论文。后来也是这位教授和Scott Shenker教授、Martin Casado博士一起在2007年创办了SDN历史上第一个初创公司——Nicira。上文提到，这家公司被VMware斥巨资收购。

Nick McKeown在2009年发表了关于题为“Software-defined Networking”的演讲，这应该是关于SDN理念最早的阐述，他之前还发表了“Why can’t I innovate in my wiring closet?”的演讲，介绍了OpenFlow。他首先提出三个开放性话题，其中一个就是“making routers simpler”，描述了router的现状：

![router.png](E:\Work\SDN\pic\router.png)

Nick Mckeown教授通过类比操作系统、应用以及基础架构如何创新，提出自己的创新想法：

![架构.png](E:\Work\SDN\pic\架构.png)

OpenFlow是一个作用于flow-table的开放式API，FlowVisor是一个虚拟化的东西，用于集中处理OpenFlow协议和策略控制。
为什么Nick McKeown教授会提出OpenFlow协议，这得回到flow-table和flow，进而回到Nick McKeown教授最初的需求：
1. 希望有一个简单、统一的硬件模板；
2. 在硬件模板之上的基础架构能够通过软件方式定义。

但是，上述两者之间，难以有彻底的分离。既然当时的技术没法解决，就自己设计。为了达到硬件模板和开放式编程环境相分离，Nick McKeown让开发人员向一个Controller发送指令，利用datapath分发信息，底层根据接收到的信息进行工作，这就是最初的控制与数据处理相分离思想。

![depatch_1.png](E:\Work\SDN\pic\depatch_1.png)

那么第二个问题，为了有一个简单、统一的硬件模板。Nick McKeown又设计了flow-table，放在每个计算单元（例如，switch、router）中。

![depatch_2.png](E:\Work\SDN\pic\depatch_2.png)

既然是flow-table，那么datapath分发的信息应该是flow，那得对flow进行定义和控制。Nick McKeown提出了“Flowspace”，很像TCP/IP协议的架构吧。

![Flowspace.png](E:\Work\SDN\pic\Flowspace.png)

最后，呈现的架构应该是这样的：

![新架构.png](E:\Work\SDN\pic\新架构.png)

早在2009年，Nick McKeown就已经实现上述设想，发布了Python版本的控制器POX，以及OpenFlow1.0和开源网络虚拟化软件FlowVisor。