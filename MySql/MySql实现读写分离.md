---
title: MySql实现读写分离
tags: [读写分离,MySql]
categories: MySql
date: 2019-03-17
---

<div align='center' ><font size='70'>MySql实现读写分离</font></div>

> 一个是主库，负责写入数据，成为写库；
>
> 其他都是从库，负责读取数据，成为读库。

对于集群的要求：

* 读库和写库的数据一致
* 写数据必须写到写库

* 读数据必须读库读

应用层解决  

select——读库 

 insert/update/delete——写库 

**优点**：

* 多数据切换方便，由程序自动完成
* 不需要引入中间件
* 理论上支持任何数据库  

**缺点**

* 由程序员完成，运维参与不到
* 不能做到动态增加数据源

技术实现：

* 驱动实现 `com.mysql.jdbc.ReplicationDriver`
* `mybatis puugin(selType:select,update,insert)`,可拦截sql类型
* springAOP+mybatis+注解
* spring动态数据源+mybatis plugin

中间件解决 

`select/insert/update/delete`——中间件, 再由中间件指向读库和写库。

**优点**：

* 源程序不需要做任何改动就可以实现读写分离
* 动态添加数据源不需要重启程序 

**缺点**：

* 程序依赖于中间件，会导致切换数据库变的困难
* 由中间件做了中转代理，性能有所下降  

**产品**：  

  MySQL-Proxy  Amoeba for MySQL   MyCat

**实现读写分离**  

后台结构`spring+mybatis`,可以通过`spring`的`AbstractRoutingDataSource`和`mybatis Plugin`拦截器可以实现非常友好的读写分离。