---
title: HIVE的搭建配置及关联MySQL
date: 2019-04-03 15:37:58
tags: [linux,hadoop,hive]
categories: hadoop
---
**概念：**

>Hive是由Facebook实现并开源。是基于Hadoop的一个数据仓库工具，可以将结构化的数据映射为一张数据库表，并提供HQL（Hive SQL）查询功能。其底层数据时存储在HDFS上。Hive的本质是将SQL语句转换为MapReduce任务运行，使部署需MapReduce的用户很方便地利用HQL处理和计算HDFS上的结构化数据，使用于离线的批量数据计算。

<!-- more -->

#### 一、下载 ####
**前提：** 配置好hadoop分布式环境，我在上一篇博文写到[Hadoop从零开始搭建完全分布式](https://fuzui.net/2019/03/27/hadoop-fully-distributed/)。

官网下载hive，这里我用hive-1.2.2版本：官网下载地址：[http://mirrors.shu.edu.cn/apache/hive/hive-1.2.2/](http://mirrors.shu.edu.cn/apache/hive/hive-1.2.2/)

我这里选择下载到本地然后通过xftp上传到虚拟机上。
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403160608.png)

解压：
```linux
tar -xzvf apache-hive-1.2.2-bin.tar.gz
```
重命名
```linux
mv apache-hive-1.2.2-bin hive-1.2.2
```
#### 二、配置 ####
（1）配置环境变量
```linux
vi /etc/profile
```
将以下添加在末尾
```linux
export HIVE_HOME=/software/hive-1.2.2
PAHT=$PATH:$HIVE_HOME/bin
```
刷新
```linux
source /etc/profile
```
查看
```linux
source /etc/profile
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403162230.png)
（2）配置文件
修改software/hive-1.2.2/conf/hive-env.sh.template为hive-env.sh。
```linux
cd /software/hive-1.2.2/conf/
cp hive-env.sh.template hive-env.sh
```
* 配置HADOOP_HOME=/Hadoop所在路径

```linux
export HADOOP_HOME=/software/hadoop2.7.2
```
* 配置HIVE_CONF_DIR=/Hive中conf文件夹路径
```linux
export HIVE_CONF_DIR=/software/hive-1.2.2/conf
```
#### 三、启动 ####
（1）启动HDFS
在hadoop01机上
```linux
start-dfs.sh
```
（2）启动YARN
在hadoop02机上
```linux
start-yarn.sh
```
通过jps检测启动情况
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403163744.png)
（3）在HDFS上创建/tmp和/user/hive/warehouse
```linux
hadoop fs -mkdir /tmp
hadoop fs -mkdir -p /user/hive/warehouse
```
（4）修改文件夹权限，同组可写
```linux
hadoop fs -chmod 775 /tmp
hadoop fs -chmod 775 /user/hive/warehouse
```
（5）启动Hive
```linux
hive
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403164248.png)

#### 四、hive基本操作 ####
1.基本操作(语法类似于MySQL)
2.显示所有数据库：show databases;
3.使用数据库(默认数据库default):use 数据库名;
4.显示使用数据库下所有表:show tables;
5.创建表:create table 表名(字段1 类型,字段2 类型…)
6.插入数据(map reduce):insert into 表名 (字段1,字段2…) values (数据1,数据2…)
7.查询数据:select 字段 from 表名;
8.删除表:drop table 表名;
9.退出:exit

#### 五、安装mysql####
##### 1. 卸载Mariadb #####
CentOS7自带的是 Mariadb
（1）查找
```linux
rpm -qa|grep mariadb
```
（2）删除
```linux
rpm -e --nodeps 上面查出来的文件名
```
（3）删除配置文件
```linux
rm /etc/my.cnf
```
##### 2.下载mysql 8.0 #####
这里我通过rpm安装
官网下载：[mysql8.0.15](https://dev.mysql.com/downloads/file/?id=484537)
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403165951.png)
（1）解压
```linux
tar -xvf mysql-8.0.15-1.el7.x86_64.rpm-bundle.tar 
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403170217.png)
（2）安装
解决一些依赖
```linux
yum -y install numactl    
yum -y install libaio.so.1 libgcc_s.so.1 libstdc++.so.6
yum update libstdc++-4.4.7-4.el6.x86_64
yum search libaio
yum install libaio
yum install net-tools
yum install openssl-devel
```
按次序安装
```linux
rpm -ivh MySQL-client-5.6.43-1.el7.x86_64.rpmrpm -ivh mysql-community-common-8.0.15-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-8.0.15-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-8.0.15-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-8.0.15-1.el7.x86_64.rpm
```
（3）初始化
```linux
mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```
（4）启动
```linux
systemctl start mysqld
```
（5）修改密码
* 编辑配置文件
vim /etc/my.cnf
在[mysqld]下添加skip-grant-tables
保存退出
* 重启mysql服务
systemctl restart mysqld
* 通过mysql -uroot -p
登录，密码直接回车即可
* 选择mysql数据库
use mysql;
* 将默认密码清空
update user set authentication_string='' where user='root';
exit命令退出
* 删除 /etc/my.cnf 文件刚刚添加的 skip-grant-tables
vim /etc/my.cnf
选中skip-grant-tables所在行按两下d键即可删除
保存退出
* 重启mysql服务
systemctl restart mysqld
* 再次登录
mysql -uroot -p
登录，密码直接回车即可
* 设置密码（我这里将密码设为root）
选择mysql数据库
use mysql;
ALTER user 'root'@'localhost' IDENTIFIED BY 'root';
* 设置远程访问
update user set host='%' where user='root';
* 重启
exit退出
重启服务systemctl restart mysqld
重新通过mysql -uroot -proot登录
* 远程连接
我通过navicat远程连接，
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403182935.png)
* 设置开机启动
systemctl enable mysqld

至此，mysql安装完成。

#### 六、hive关联mysql####
##### 1.下载驱动jar #####
我安装的是mysql8.0.15版本，我找对应版本的jar
平时个人比较喜欢到mvnrepository去找jar，附上地址：[https://mvnrepository.com/artifact/mysql/mysql-connector-java/8.0.15](https://mvnrepository.com/artifact/mysql/mysql-connector-java/8.0.15)

将下载好的jar放到hadoop01虚拟机中hive安装目录的lib文件夹下
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403183816.png)

##### 2.配置hive-site.xml #####
创建hive-site.xml
```linux
cd /software/hive-1.2.2/conf
vim hive-site.xml
```
将以下添加进去（灵活应变）
```linux
<configuration>
        <property>
                <name>javax.jdo.option.ConnectionURL</name>
                <value>jdbc:mysql://192.168.17.134:3306/myhive?createDatabaseIfNotExist=true</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionDriverName</name>
                <value>com.mysql.cj.jdbc.Driver</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionUserName</name>
                <value>root</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionPassword</name>
                <value>root</value>
 </property>
</configuration>

```
##### 3.重启整个集群 #####
（1）在Hadoop02上
```linux
stop-yarn.sh
```
（2）在Hadoop01上
```linux
stop-dfs.sh
```
（3）在Hadoop01上
```linux
start-dfs.sh
```
（4）在Hadoop02上
```linux
start-yarn.sh
```
（5）在Hadoop01上重启mysql
```linux
systemctl restart mysqld
```
##### 4.启动 #####
```linux
hive
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403190805.png)
启动完成后用navicat查看发现已经自动创建了myhive数据库。
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403190031.png)
也可以通过命令行查看
```linux
mysql -uroot -proot
show databases;
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403190923.png)

##### 5.其他配置（看需求） #####
hive-site.xml中
```linux
#数据库存放位置
<property>
<name>hive.metastore.warehouse.dir</name> 
<value>/user/hive/warehouse</value>
<description>location of default database for the warehouse</description> 
</property> 
#查询时显示字段名称和数据库名
<property> 
<name>hive.cli.print.header</name>
<value>true</value> 
</property> 
<property> 
<name>hive.cli.print.current.db</name>
 <value>true</value> 
</property> 

```

#### 七、测试 ####
（1）进入数据库
```linux
create database fuzui;
use fuzui;
```
（2）创建表

```linux
create table student(
    id int, 
    name string, 
    age int, 
    sex string)
row format delimited fields terminated by ',';
```

在mysql中会看到创建这个表的字段信息
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403200116.png)
（3）退出hive
```linux
exit
```
（4）创建本地文件上传到HDFS
<1>我直接用的root用户，所以我在root目录下创建
```linux
cd /root
vim fz.txt
```
添加如下内容
```linux
201501,wangze,22,man
201502,wangchen,20,man
201503,liyu,20,woman
201504,zhangna,21,woman
201505,songxin,19,woman
201506,changyi,18,man
201507,liuni,20,woman
201508,zhaoming,22,man
```
保存退出
<2>上传hdfs
* 创建文件夹
`hadoop fs -mkdir /myhive`
* 上传
`hadoop fs -put /root/fz.txt /myhive`
* 查看
`hadoop fs -cat /myhive/fz.txt`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403193537.png)
（5）进入hive
```linux
hive
```
（6）导入hive表
* 进入数据库
	`use fuzui`
* 导入
`load data inpath '/myhive/fz.txt' into table student;`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403194307.png)

* 查看数据
`select * from student;`
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403194417.png)

* 稍微复杂点的查询
查询最小的女生多少岁
`select min(age) from student where sex = 'woman';`
这时，就开始一系列运行了，速度会慢。

![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403195629.png)

***==至此，HIVE搭建成功！==***