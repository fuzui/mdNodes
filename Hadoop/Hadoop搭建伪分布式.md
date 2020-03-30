---
title: Hadoop搭建伪分布式
date: 2019-03-25 19:35:18
tags: [linux,hadoop]
categories: hadoop
---
<div align='center' ><font size='70'>Hadoop搭建伪分布式</font></div>

### ==概念==  ###

>海量、高增长、多样化的信息数据
>有三大发行版本
>
>>Apache、CDH、HDP

<!-- more -->
#### 一、优势 ####

**1、高可靠性：**
Hadoop假设计算元素和存储会出现故障，因为它维护多个工作数据副本，在出现故障时可以对失败节点重新分布处理。
**2.高扩展性：**
在集群间分配任务数据，可方便扩展数以千计的节点。
**3.高效性：**
在MapReduce的思想下，Hadoop是并行工作的，以加快任务处理速度。
**4.高容错性：**
自动保存多份副本数据，并且能够自动将失败的任务重新分配。

#### 二、组成 ####

 * Hadoop HDFS：一个高可靠、高吞吐的分布式文件系统。
 * Hadoop MapReduce：一个分布式的离线并行计算框架。
 * Hadoop YARN：一个资源调度平台，负责为运算程序提供服务器运算资源，相当于一个分布式的操作系统，而MaoReduce等运算程序则相当于运算于操作系统之上的应用程序。
 * ResourceManager：一个中心服务，用来调度、启动每一个Job所属的ApplicationMaster。
 * NodeManager：是每台机器框架的代理，是执行应用程序的容器，监控应用程序的资源使用情况，并向调度器汇报。
 * Hadoop Common：Hadoop体系最底层的一个模块，为Hadoop各个子项目提供各种工具，如：配置文件和日志操作等。

 #### 三、运行模式 ####
 * 本地模式（默认模式）：不需要启用单独进程，直接可以在运行、测试和开发时使用。
 * 伪分布式模式：等同于完全分布式，只有一个节点。
 * 完全分布式模式：多个节点一起运行。



 #### 四、伪分布式 ####

  ###### 1. 配置环境变量   ######
  * 安装JDK，配置环境变量
  * 安装HADOOP，配置环境变量


``` linux
vi /etc/profile

export JAVA_HOME=JDK路径
export HADOOP_HOME=hadoop路径
PATH=$PATH:$JAVA_HOME/bin
PATH=$PATH:$HADOOP_HOME/bin
PATH=$PATH:$HADOOP_HOME/sbin
```
&emsp;&emsp;保存后刷新

``` linux
source /etc/profile
```
  ###### 2. 修改配置文件   ######
&emsp;&emsp;（1）hadoop-env.sh
``` linux
export JAVA_HOME=JDK路路径
```
  &emsp;&emsp;（2）core-site.xml
  ``` linux
<!-- 指定HDFS中的NameNode的地址 -->
<property>
		<name>fs.defaultFS</name>
		<!-- 使用主机名或ip -->
		<value>hdfs://主机名:9000</value>
</property>
<!-- 指定hadoop运行时产生文件的存储目录 -->
<property>
		<name>hadoop.tmp.dir</name>
		<!-- 默认路径/tmp/hadoop-${user.name} -->
		<value>/hadoop安装目录/data/tmp</value>
</property>
  ```
  &emsp;&emsp;（3）hdfs-site.xml
   ``` linux
<!-- 指定HDFS副本的数量 -->
<property>
		<name>dfs.replication</name>
		<!-- 默认为3，伪分布式为单机模式，设置成1 -->
		<value>1</value>
</property>

   ```
  ###### 3. 启动集群   ######
 &emsp;&emsp;（1）格式化namenode


``` linux
hdfs namenade -format
```
 &emsp;&emsp;只需要在修改配置后格式化一次，格式化后会生成data和logs文件夹。
  &emsp;&emsp;（2）启动namenode

``` linux
hadoop-daemon.sh start namenode
```
  &emsp;&emsp;（3）启动datanode

``` linux
hadoop-daemon.sh start datanode
```

  ###### 5. 查看集群   ######
   &emsp;&emsp;（1）jps（类似ps命令，只列出java相关程序进程）
   &emsp;&emsp;（2）查看日志，hadoop安装目录下的logs文件夹
   &emsp;&emsp;（3）使用hadoop系统自带的web程序
   &emsp;&emsp;&emsp;  http://主机名或ip:50070 访问HDFS系统
  ###### 6. 操作集群   ######
  >hadoop提供三套操作集群的命令，命令格式相同，①hdfs dfs，②hadoop dfs，③hadoop fs，使用hadoop fs，可操作任何类型的文件系统，其他两种只能操作HDFS.


  &emsp;&emsp;（1）HDFS上创建文件夹
  ``` linux
hadoop fs -mkdir -p /hdfs上文件夹路径
  ```
  &emsp;&emsp;（2）从linux上传文件到HDFS
 ``` linux
hadoop fs -put linux上文件路径 /hdfs上文件夹路径
 ```
&emsp;&emsp;（3）查看HDFS目录结构和文件内容
  ``` linux
hadoop fs -ls /hdfs上文件夹路径
hadoop fs -cat /hdfs上文件路径
  ```
&emsp;&emsp;（4）在HDFS上运行官方示例
  ``` linux
hadoop jar hadoop安装目录/share/hadoop/mapreduce/hadoop-mapreduce-examples-版本号.jar 示例程序名称（wordcount） /HDFS输入文件夹 /HDFS输出文件夹

#输出文件夹不可事先拥有。
  ```

&emsp;&emsp;（5）从HDFS上下载文件到linux
  ``` linux
hadoop fs -get /hdfs上文件路径 /linux文件夹
  ```
  &emsp;&emsp;（6）HDFS删除
``` linux
hadoop fs -rmr /hdfs上文件或文件夹路径
```
  ###### 7. 配置YARN   ######
&emsp;&emsp;（1）在yarn-env.sh和mapred-env.sh中配置JAVA_HOME
&emsp;&emsp;（2）yarn-site.sml
``` linux
<!-- NodeManager上运行的附属服务，许配置mapreduce_shuffle，才可运行MapReduce -->
<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
</property>
<!-- 指定YARN的ResourceManager的地址 -->
<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>主机名</value>
</property>
```
&emsp;&emsp;（3）mapred-site.sml
``` linux
<!-- 指定MapReduce运行在Yarn上 -->
<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
</property>
```
>在yarn集群上提交任务，等任务执行完成后，就不能查看log文件了，为了解决这个问题，可以开启historyserver，实现yarn集群上历史任务的保存，yarn的web程序中每一个job都对应一个history的链接。

``` linux
<!-- 历史服务器地址 -->
<property>
		<name>mapreduce.jobhistory.address</name>
		<value>主机名:10020</value>
</property>
<!-- 历史服务器的web程序地址 -->
<property>
		<name>mapreduce.jobhistory.webapp.address</name>
		<value>主机名:19888</value>
</property>
```

  ###### 8. 启动集群   ######
  ***必须先启动HDFS，再启动YARN。停止时先停止YARN，再停止HDFS***

  &emsp;&emsp;（1）格式化namenode(需先删除data和logs文件夹)
  ``` linux
hdfs namenode -format
  ```
  &emsp;&emsp;（2）启动NameNode和DataNode
  ``` linux
hadoop-daemon.sh start namenode
hadoop-daemon.sh start datanode
  ```
  &emsp;&emsp;（3）启动ResourceManager和NodeManager
  ``` linux
yarn-daemon.sh start resourcemanager
yarn-daemon.sh start nodemanager
  ```
  ###### 8. 集群测试  ######
&emsp;&emsp;（1）查看yarn的web程序：http://主机名:8088
&emsp;&emsp;（2）命令行操作HDFS：hadoop fis -xxx
&emsp;&emsp;（3）查看HDFS的web程序：http://主机名:50070
&emsp;&emsp;（4）执行官方测试程序dataword
``` linux
hadoop jar hadoop安装目录/share/hadoop/mapreduce/hadoop-mapreduce-examples-版本号.jar wordcount /HDFS输入文件夹 /HDFS输出文件夹

```
>输出文件夹不可事先拥有。输入文件为统计每个单词个数，实现常见一个文本，编写若干个单词，空格或换行分割。然后上传到HDFS上，事先创建输入文件夹，将该文档上传于输入文件夹，最后运行上面命令。执行完成可查看输出文件夹内文件内容，便为“单词 个数”数行形式展现。
