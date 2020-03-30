---
title: Hadoop从零开始搭建完全分布式
date: 2019-03-27 23:34:48
tags: [linux,hadoop]
categories: hadoop
---
**简介：**

>由三个或以上实体机或虚拟机组成的集群。

**准备：**
>1.hadoop2.7.2版本
>2.jdk8版本
>3.vmware

<!-- more -->
#### 一、创建虚拟机及相关配置 ####
##### 1.创建虚拟机 #####
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1553754905885.png)
一路下一步，选择已有centos7镜像
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328143641.png)
这里我取名为hadoop01
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328143753.png)
选择NAT网络模式
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328143852.png)
设置相关硬件
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328144135.png)
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328144100.png)
然后等待创建（中间空的步骤均使用默认）
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328144756.png)
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328145249.png)
开始安装：
设置root密码和创建一个用户
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328145401.png)
等待安装完成

##### 2.查看ip并修改主机名以及配置映射 #####

（1）查看并修改ip
我将这一台的ip设为192.168.17.134
``` linux
cd /etc/sysconfig/network-scripts/
vim vi ifcfg-ens33
#这个配置每个人都可能不一样，进去修改
```
将原来的完全替换（灵活修改name和device）
``` linux
TYPE=Ethernet
BOOTPROTO=static
NAME=ens33
DEVICE=ens33
ONBOOT=yes
IPADDR=192.168.17.134
NETMASK=255.255.255.0
GATEWAY=192.168.17.2
DNS1=8.8.8.8
```
重启reboot
ping www.baidu.com查看网络是否通

永久关闭防火墙：

``` linux
systemctl stop firewalld
systemctl disable firewalld
```
这里我使用的secureCRT连接我的虚拟机。
（2）修改主机名
``` linux
vi /etc/hostname
修改为hadoop01
```

（3）配置ip映射
预计后面两台虚拟机的ip分别是192.168.17.135和192.168.17.136
有以下关系

| 主机     | ip             |
| -------- | -------------- |
| hadoop01 | 192.168.17.134 |
| hadoop02 | 192.168.17.135 |
| hadoop03 | 192.168.17.134 |

``` linux
vi /etc/hosts
#
127.0.0.1   localhost
192.168.17.134   hadoop01
192.168.17.135   hadoop02
192.168.17.136   hadoop03

```
重启reboot

##### 3.上传hadoop和jdk并配置环境变量 #####
（1）安装ftp

安装ftp
``` linux
yum -y install vsftpd lftp ftp
```
（2）上传
这里我使用的是xftp6工具
上传相关包
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328150814.png)
然后创建相关目录并解压
我在根目录下创建software
``` linux
mkdir /software
```
将hadoop和jdk解压并重命名放在其下。（解压移动重命名基础过程省略）

（3）配置环境变量
``` linux
vi /etc/profile
```
在后面添加
``` linux
export JAVA_HOME=/software/jdk1.8
export HADOOP_HOME=/software/hadoop2.7.2
PATH=$PATH:$JAVA_HOME/bin
PATH=$PATH:$HADOOP_HOME/bin
PATH=$PATH:$HADOOP_HOME/sbin
```
刷新生效
``` linux
source /etc/profile
```
检测
``` linux
echo $PATH
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328162516.png)
##### 3.修改hadoop配置文件 #####
``` linux
cd /software/hadoop2.7.2/etc/hadoop/
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328162713.png)

vi修改，以下xml文件修改在configuration标签内
1.core-site.xml
``` linux
<property>
	<name>fs.defaultFS</name>
	<value>hdfs://hadoop01:9000</value>
</property>
<property>
	<name>hadoop.tmp.dir</name>
	<value>/software/hadoop2.7.2/data/tmp</value>
</property>
```
2.hadoop-env.sh
``` linux
export JAVA_HOME=/software/jdk1.8
```
3.hdfs-site.xml
``` linux
<property>
	<name>dfs.replication</name>
	<value>3</balue>
</property>
<property>
	<name>dfs.namenode.secondary.http-address</name>
	<value>hadoop03:50090</value>
</property>
```
4.slavs
<font color="red">配置datanode节点的ip，每个占一行，不能出现多余的空格和换行行</font>
``` linux
192.168.17.134
192.168.17.135
192.168.17.136
```
5.yarn-env.sh
``` linux
export JAVA_HOME=/software/jdk1.8
```
6.yarn-site.xml
``` linux
<property>
	<name>yarn.nodemanager.aux-services</name>
	<value>mapreduce_shuffle</value>
</property>
<property>
	<name>yarn.resourcemanager.hostname</name>
	<value>hadoop02</value>
</property>
```
7.mapre-env.sh
``` linux
export $JAVA_HOME=/software/jdk1.8
```
8.mapred-site.xml
将mapred-site.xml.template重命名
``` linux
<property>
	<name>mapreduce.framework.name</name>
	<value>yarn</value>
</property>
```
至此，一台配置完成，紧接着克隆出两台一模一样的
#### 二、克隆并配置####
##### 1.克隆 #####
首先关机刚刚虚拟机
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328163857.png)
下一步，创建完整克隆
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328163944.png)
更名为hadoop02。
以此方法克隆出hadoop03。
##### 2.修改配置 #####
（1）分别修改主机名和ip
hadoop02    192.168.17.135
hadoop03    192.168.17.136
（2）重启reboot
##### 3.创建免密登录 #####
三台机器都执行ssh-keygen -t rsa,一路回车
cd ~/.ssh   查看

再分别执行
``` linux
ssh-copy-id hadoop01
ssh-copy-id hadoop02
ssh-copy-id hadoop03
```
检测
``` linux
ssh hadoop02
ssh hadoop03
```

#### 二、启动集群####

##### 1.启动 #####
（1）格式化namenode
在hadoop01上
``` linux
hdfs namenode -format
```
（2）启动NameNode和DataNode(在hadoop01上)
``` linux
start-dfs.sh
```
（3）启动ResourceManager和NodeMananger(在hadoop02上)
``` linux
start-yarn.sh
```
##### 2.检测 #####
（1）jps检测
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328182607.png)

（2）登录web 192.168.17.134:50070
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328182651.png)

#### 三、测试 ####
##### 1.创建并上传hdfs测试文本 #####
（1）本地通过vi创建了一个wz.txt，并编辑文本
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328183139.png)
（2）上传到hdfs上
创建输入文件夹
``` linux
hadoop fs -mkdir -p /data/input
```
将wz.txt上传
``` linux
hadoop fs -put /root/wz.txt /data/input
```
查看
``` linux
hadoop fs -cat /data/input/wz.txt
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328183609.png)

##### 2.执行官网测试wordcount #####
<font color="red">输出文件夹事先不能有！</font>
``` linux
hadoop jar /software/hadoop2.7.2/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount /data/input /data/output

```
中间部分过程
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328184038.png)
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328184127.png)
执行完成
查看输出文件夹
``` linux
hadoop fs -ls /data/output/
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328184236.png)
其中part-r-00000便是输出结果
查看：
``` linux
hadoop fs -cat /data/output/part-r-00000
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328184355.png)

也可以通过192.168.17.134:50070查看
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328184619.png)
点进data——output
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328184722.png)
点击part-r-00000
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328184751.png)

下载这块，如果想点击下载，需要在你本机上配置ip映射（或者点击下载后吧url上的hadoop02换成192.168.17.135也是可以的=-=）
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328184940.png)

win10上ip映射文件位置
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190328185235.png)

<br>
<br>

***==至此，完全分布式搭建成功！==***