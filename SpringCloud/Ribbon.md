---
title: Ribbon
tags: [SpringCloud,Ribbon]
categories: SpringCloud
date: 2019-07-28
---

概念：

>Spring Cloud Ribbon是基于Netfix Ribbon实现的一套客户端   负载均衡的工具。
简单来说，Ribbon是Netfix发布的开源项目，主要功能是提供客户端的软件负载均衡算法，将Netfix的中间层服务连接在一起，Ribbon客户端组件提供一系列完善的配置项如连接超时、重试等。简单来说，就是在配置文件中列出Load Balancer（简称LB）后面所有的机器，Ribbon会自动的帮助你基于某种规则（如简单轮询，随机连接等）去连接这些机器。
