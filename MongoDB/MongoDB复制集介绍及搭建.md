---
title: MongoDB复制集介绍及搭建
date: 2019-04-19 23:17:23
tags: MongoDB
categories: MongoDB
---

<div align='center' ><font size='70'>MongoDB复制集介绍及搭建</font></div>

**简介**

>为了防止数据错误和丢失，采用复制集可以数据同步到多个服务器，提供了数据的冗余备份，在多个服务器上存储数据副本，提高了数据的可用性，保证了数据的安全性。

<font color="blue">参考《MongoDB游记之轻松入门到进阶》</font>

<!-- more -->
#### 一、主从复制和副本集 ####
MongoDB提供了两种复制部署方案：主从复制和副本集
1.主从复制
一个主节点，多个从节点，所有从节点会去主节点获取最新数据，做到主从数据保持一致。
缺点:
当主节点出现宕机，那么集群将不能正常运作，需要先人工将其中一个从节点作为主节点，需要停机操作，使对外服务会有一段空白时间。

2.副本集

为了解决主从复制的容灾性问题。
没有固定的主节点，集群会自己选举出一个主节点，当这个主节点不能正常工作时，又会另外选举出其他节点作为主节点。副本集中总会有一个活跃节点和一个（多个）备份节点，当当前活跃节点出问题时，备份节点会提升为活跃节点。
在副本集中会有一个只参与投票选举、不复制数据的仲裁节点，用于当票数出现一致时来判决。官方推荐集群节点为奇数。

#### 二、副本集工作原理 ####

##### 1.oplog（操作日志） #####
>记录数据改变操作（更新插入），oplog是一个固定集合，喂鱼每个复制节点的local的数据库里。新操作会替换旧的操作，以保证oplog不会超过预设的大小，oplog中的每个文档都代表主节点上执行的一个才做。

##### 2.数据同步 #####
>每个oplog都有时间戳，所有从节点都使用这个时间戳来追踪它们最后执行写操作的记录。当某个从节点准备更新自己时，会做三件事：首先，查看自己oplog里的最后一条时间戳；其次，查询主节点oplog里所有大于此时间戳的文档；最后，把那些文档应用到自己库里，并添加写操作文档到自己的oplog里。

##### 3.复制状态和本地数据库 #####
>复制状态的文档记录在本地数据库local中。主节点的local数据库的内容是不会被从节点复制的。如果有不想被从节点复制的文档，可以将它放在本地数据库local中。

##### 4.阻塞复制 #####
>当主节点写入操作太快时，从节点的更新状态有可能跟不上。
>为避免这种情况：
>* 使主节点的oplog足够大
>* 阻塞复制：在主节点使用getLastError命令加参数“w”来确保数据的同步性。w越大会导致写操作越慢。

##### 5.心跳机制 #####
>心跳检测有助于发现故障进行自动选举和故障转移。默认情况下副本集成员每两分钟ping一下其他成员，来弄清自己的健康状态。
>如果是某一从节点出现故障只会等待从节点重新上线，而如果主节点出现故障，则副本集会开始选举，重新选出新的主节点，原主节点会降级为从节点。

##### 6.选举机制 #####
>根据优先级和Bully算法（评判睡的数据最新）选举出主节点，在选举出主节点之前，整个集群服务是只读的，不能执行写操作。
>非仲裁节点都会有优先级配置，范围为0~100，越大值越优先成为主节点，默认为1，如果是0则不能成为主节点。
>拥有资格的从节点会向其他节点发出请求，而其他节点在收到选举提议后会判断三个条件：
>* 副本集中是否有其他节点已经是主节点了
>* 自己的数据是否比请求成为主节点的的那个节点上数据更新
>* 副本集中其他节点的数据是否比请求成为主节点的那个节点数据更新
>
>只要有一个条件城里，都会认为对方提议不可行。请求者只要收到任何一个节点返回不合适，都会退出选举。
>选举机制会让优先级高的节点成为主节点，即使先选举出了优先级低的节点，也至少会短暂作为主节点运行一段时间。副本集还会再之后继续发出选举，直到优先级最高的节点成为主节点。（后面会有例子）

##### 7.数据回滚 #####
>在从节点成为主节点后，会认为其是副本集中的最新数据，对其他节点的操作都会回滚，即所有节点连接新的主节点重新同步。这些节点会查看自己的oplog，找出新主节点中没有执行过的操作，然后请求操作文档，替换掉自己的异常样本。

#### 三、主从复制搭建 ####

我在mongod4.0运行时提示：<font color="red">[main] Master/slave replication is no longer supported</font>
也就是mongodb4.0后不再支持主从复制！

遵从技术发展方向=-=，不再阐述主从复制搭建。

#### 四、副本集搭建 ####

我在虚拟机上测试centos7

（1）安装完MongoDB，参考：

（2）三台虚拟机（安好一台克隆）
* 修改ip和hostname：
MongoDB-master：192.168.17.138
MongoDB-slave：192.168.17.139
MongoDB-arbiter：192.168.17.140
hostname修改：`vim /etc/hostname`
ip修改：`vim /etc/sysconfig/network-scripts/ifcfg-ens33`其中ifcfg-ens33每个机器可能不一样。
```shell
TYPE=Ethernet
BOOTPROTO=static
NAME=ens33
DEVICE=ens33
ONBOOT=yes
IPADDR=192.168.17.139
NETMASK=255.255.255.0
GATEWAY=192.168.17.2
DNS1=8.8.8.8
```
* 配置ip映射
`vim /etc/hosts`
```shell
192.168.17.138   mongodb-master
192.168.17.139   mongodb-slave
192.168.17.140   mongodb-arbiter
```
配置完成后重启

（3）创建key
在mongodb-master中：
生成key：
	```shell
	openssl rand -base64 90 > /var/lib/mongo/mongodb-keyfile
	```
将这个mongodb-keyfile复制到其他两台机器上（我用的xftp6传过去的，也可以使用scp传）

（4）权限
在三台机器上分别执行
```shell
	chmod 700 /var/lib/mongo/mongodb-keyfile
```
在实现时不知为何我的700在master机器上一直无法生效！我采用另一种写法（u+x）
然后切换用户:
<font color="red">很重要，我在配置时因为直接用的root，所以mongodb-keyfile文件也是root用户，导致我后面修改配置文件后一直无法启动mongod！即需要在三台机器上使用chown切换为mongod。</font>
```shell
	chown mongod /var/lib/mongo/mongodb-keyfile
```

（5）修改配置文件
在三台机器上都修改，keyFile和replSetName都一样。
`vi /etc/mongod.conf`
有两处修改
```shell
#上面省略
security:
 authorization: enabled
 keyFile: /var/lib/mongo/mongodb-keyfile
#operationProfiling:

replication:
 replSetName: wangze
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419214000.png)

（6） 启动

在三台机器上分别持续`systemctl restart mongod`
启动成功什么信息都没有，如若启动失败，前往查看日志`cat /var/log/mongodb/mongod.log`


（7）配置
进入mongodb-master的mongo
`mongo`
* 初始化
`rs.initiate()`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419205102.png)

* 查看配置
`rs.conf()`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419205018.png)
会发现只有一个节点，因为从节点和仲裁者节点还未添加。

* 添加从节点
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419205119.png)
`rs.add("mongodb-slave:27017")`
* 添加仲裁者节点
 `rs.addArb("mongodb-arbiter:27017")`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419205207.png)
* 重新查看配置
`rs.conf()`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419205319.png)
* 查看状态
`rs.status()`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419205412.png)
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419205459.png)
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419205520.png)
.


#### 五、数据同步测试 ####

在安装时我曾创了一个wwze库，里面插入一条数据如
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419205809.png)
现在在主节点中再写入一条新数据
`db.user.insert({name:"张三",age:21,address:"上海"})`
* 查看
`db.user.find().pretty()`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419210220.png)

* 进入从节点
 在mongodb-slave中执行
 `mongo`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419210353.png)
设置slave为true
`rs.slaveOk()`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419210701.png)
这里我查看dbs发现没有数据库，是因为我还没有登录！
`use admin`
`db.auth("root","root")`
再次查看
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419210853.png)
最后进入wwze库查看一些刚在主节点写的数据是否同步
`use wwze`
`db.user.find().pretty()`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419212002.png)

* 而进入仲裁者节点时，会发现不能读写，因为他只负责投票选举。
 在mongodb-arbiter机器中
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419212222.png)


<font color="blue">至此，副本集搭建完成</font>

#### 六、故障测试 ####
默认情况下主节点和从节点的优先级都为1，仲裁者为0，因为它不可参加选举。
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419215955.png)

我将现有主节点的优先级提高为5
```shell
 var rscfg=rs.conf()
rscfg.members[0].priority = 5
 rs.reconfig(rscfg)
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419220207.png)

查看一下状态
`rs.status()`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419220346.png)


现在将主节点停掉
```shell
use admin
db.shutdownServer()
```
这时可以查看状态
`rs.status()`
这里我没截图，自测~


然后重启进入mongo，会发现，原主节点变为从节点了
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419220639.png)
而从节点，自然变为主节点：
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419220819.png)

因为设置了优先级，所以在重启mongodb-master机器的mongo后，很快（几秒或者十几秒）mongodb-master又会被重新选举为主节点，而mongodb-slave下位，我没来得及截状态图他就已经回来了。
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419220924.png)

<br><br>
<font color="blue">最后远程连接或用java连接操作即可，因为我电脑配置有限![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190419221727.png)，所以只开了三台虚拟机，只配置了一个从节点，如需更多以相同方法添加即可</font>



