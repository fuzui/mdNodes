---
title: Hystrix
tags: [SpringCloud,Hystrix]
categories: SpringCloud
date: 2019-07-28
---

前景：

服务雪崩
多个微服务之间调用的时候，加入微服务A调用微服务B和微服务C，微服务B和微服务C又调其它的微服务，这就是所谓的“扇出”，如果扇出的链路上某个微服务的调用响应时间过长或不可用，对微服务A的调用就会占用越来越多的系统资源，进而引起系统崩溃，就是所谓的雪崩效应。

对于高流量的应用来说，单一的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的屙屎，这些应用程序还可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障。这些都表示需要对故障和延迟进行隔离和管理，以便单个依赖关系的失败，不能取消整个应用程序或系统。

概念Hystrix是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免会调用失败，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统弹性。

“断路器”本身就是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个符合预期的、可处理的被选相应（FallBack），而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统的蔓延，乃至雪崩。

功能：
* 服务降级
* 服务熔断
* 服务限流
* 接近实时的监控


1.服务熔断
熔断机智是应对雪崩效应的一种微服务链路保护机制。
当扇出链路的某个微服务不可用或响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的相应信息。当检测到该节点微服务调用响应正常后恢复调用链路。在springCloud框架里熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到一定的阈值，缺省是5秒内20次调用失败就会启动熔断机智。熔断机制的注解是`@HystrixCommand`

2.服务降级
在客户端配置
在服务不可用时也可获得提示信息而不会挂起耗死服务器。
所谓降级，一般是从整体负荷考虑，就是当某个服务熔断之后，服务端将不再被调用。
此时客户端可以自己准备一个本地的fallback回调，返回一个缺省值。
这样做，虽然服务水平下降，但好歹可用，比直接挂掉要强。