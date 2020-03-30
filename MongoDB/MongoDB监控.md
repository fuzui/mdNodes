---
title: MongoDB监控
tags: MongoDB
categories: MongoDB
date: 2019-04-17
---

#### 一、原生管理接口监控 ####

* REST接口：
	用户配置监控、告警脚本和一些管理任务
* HTTP接口：
	在web界面显示MongoDB服务的情况
	
#### 二、serverStatus ####
* 查看全部 `db.serverStatus()`
* 主机名 `db.serverStatus().host`
* 锁信息 `db.serverStatus().locks`
* 全局锁信息 `db.serverStatus().globalLock`
* 内存信息 `db.serverStatus().mem`
* 连接数信息 `db.serverStatus().connections`
* 额外信息 `db.serverStatus().extra_info`
* 索引统计信息 `db.serverStatus().indexCounters`
* 后台刷新信息 `db.serverStatus().backgroundFlushing`
* 游标信息 `db.serverStatus().cursors`
* 网络信息 `db.serverStatus().network`
* 副本集信息 `db.serverStatus().repl`
* 操作计数器`db.serverStatus().opcounters`
* 副本集的操作计数器`db.serverStatus().opcountersRepl`
* 断言信息 `db.serverStatus().asserts`
·····

#### 三、mongostat ####
`mongostat`
身份验证：
`mongostat -uroot -proot --authenticationDatabase=admin`

每秒输出一次
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417200910.png)

各参数含义：
 insert:     一秒内的插入数。
query :     一秒内的查询数。
update:     一秒内的更新数。
delete:     一秒内的删除数。
getmore:    查询时游标(cursor)的getmore操作。
command:    一秒内执行的命令数。
dirty：无效数据所占的百分比.。
used：正在使用的缓存百分比。
flushes:    一秒内flush的次数 。
vsize：最后一次调用mongostat时，进程中虚拟内存使用的大小。
res：最后一次调用mongostat时，进程中常驻存储器内存大小。
qrw：客户端队列中，等待获取读写操作的实例数。
arw：执行读写操作的活动客户数。
netIn：分别代表mongo在网络流通中接收到数据量，单位是字节。
netOut：分别代表mongo在网络流通中发出到数据量，单位是字节。
conn：当前mongodb打开的连接数。
time：当前时间
