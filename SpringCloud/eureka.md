---
title: eureka
tags: [SpringCloud,eureka]
categories: SpringCloud
date: 2019-07-28
---

概念：

>Netflix在设计Eureka时遵循AP原则

>Eureka是Netfilix的一个子模块，也是核心模块，主管服务的发现与注册。

基本架构：
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190727173927.png)



Eureka包含两个组件：Eureka Server和Eureka Client
Eureka Server 提供服务注册服务
各个节点启动后，会在EurekaServer中进行注册这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中的直观的看到

Eureka Client 是一个java客户端，用户简化Eureka Server 的交互，客户端同时也具备一个内置的、使用轮询（round-robin）负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳（默认周期30s）。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务中注册表中把这个服务节点移除（默认90s）。


自我保护

服务发现
对于注册进eureka里面的微服务，可以通过服务发现来或得该服务的信息
