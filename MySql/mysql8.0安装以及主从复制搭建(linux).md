---
title: mysql8.0安装以及主从复制搭建(linux)
date: 2019-04-19 23:17:23
tags: MySql
categories: MySql
---

<div align='center' ><font size='70'>mysql8.0安装以及主从复制搭建</font></div>

**一主一从：**
前提：

* 两台centos7（虚拟机或云服务器）
* 防火墙已关闭

主master：192.168.17.201 			msql8.0.12
从slave：192.168.17.202		msql8.0.12

<!-- more -->

#### 1.mysql8.0安装 ####
##### (1) 卸载Mariadb #####
由于CentOS7自带的是 Mariadb, 所以需要先删除Mariadb
1>. 查找版本
`rpm -qa|grep mariadb`
2>. 删除
`rpm -e --nodeps 上面查出来的文件名`
3>. 删除配置文件
` rm /etc/my.cnf `

##### (2) 下载安装 #####
1>. 下载
官网下载：[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190415171239.png)
2>. 解压
`tar -xvf 包名`
解压完会出现几个不同的包，通过ll查看即可
3>. 安装依赖
```linux
#以下六条，具体作用我还没去了解过
yum -y install numactl
yum -y install libaio.so.1 libgcc_s.so.1 libstdc++.so.6
yum update libstdc++-4.4.7-4.el6.x86_64
yum search libaio
yum install libaio
yum install net-tools
````
4>. 安装
```
#以下具体名字看通过ll命令查看，按顺序安装
rpm -ivh mysql-community-common-8.0.15-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-8.0.15-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-8.0.15-1.el7.x86_64.rpm
rpm -ivh mysql-community-devel-8.0.15-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-8.0.15-1.el7.x86_64.rpm
````
5>. 初始化
`mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql`

6>. 修改密码
①编辑配置文件
`vim /etc/my.cnf`
在[mysqld]下加`skip-grant-tables`
②重启mysql服务
`systemctl start mysql`
③免密登录
`mysql -u root -p`
提示输入密码时直接敲回车。
④选择 mysql 数据库
`use mysql;`
⑤置空默认密码
`update user set authentication_string='' where user='root';`
⑥退出mysql，修改配置文件my.cnf,把kip-grant-tables删掉
`vim /etc/my.cnf`
⑦重启服务
`systemctl start mysql`
⑧登录
`mysql -u root -p`
提示输入密码时直接敲回车。
⑨修改root密码
`ALTER user 'root'@'localhost' IDENTIFIED BY '密码';`
⑩退出重启服务通过自己设置的密码登录
`mysql -u root -p密码`
7>. 设置远程登录
设置权限 %表示允许远程
`grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;`
或者
`update user set host='%' where user='root';`
刷新数据库，使权限立刻生效
`flush privileges;`

<font color="red">注意所有标点均为英文符号！</font>

到此mysql8.0安装完成。


*配置主从复制需要两台虚拟机或云服务器都安装。*


#### 2.配置修改 ####
* 主master数据库中：在配置文件my.cnf新增：
```linux
server-id = 100
log-bin = mysql-bin
innodb-file-per-table =ON
skip_name_resolve=ON

#以下原有
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```
* 从slave数据库中：在配置文件my.cnf新增：
```linux
relay-log=relay-log1
relay-log-index=relay-log.index
server-id = 101
innodb_file_per_table=ON
skip_name_resolve=ON
#以下原有
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

配置完成重启数据库

#### 3.主库master操作 ####
（1）创建用于同步的用户并授权
```linux
#创建用户
CREATE USER 'wwze'@'192.168.17.201' IDENTIFIED BY 'wish';
#授权
grant replication slave on *.* to 'wwze'@'192.168.17.201' identified by 'wish';
#刷新权限
flush privileges;
```
（2）查看master状态，记录二进制文件名（File）和位置（Position）

```linux
SHOW MASTER STATUS;
```

#### 4.从库slave操作 ####

（1）同步
```linux
change master to master_host='94.191.88.237', master_user='wwze', master_password='wish', \
master_log_file='mysql-bin.000002', master_log_pos=885, master_connect_retry=30;
```
*其中mysql-bin.000002为在主库从查询到的二进制文件名，885为位置。*

（2）启动同步
```linux
start slave;
```
（3）查看同步状态
```linux
show slave status\G
```
*这里结尾不加分号。*

当 Slave_IO_Running: Yes 和Slave_SQL_Running: Yes时，便主从搭建成功。


#### 5.额外配置 ####
（1）选择性同步数据库
```linux
# 不同步哪些数据库  
binlog-ignore-db = mysql  
binlog-ignore-db = test  
binlog-ignore-db = information_schema  
  
# 只同步哪些数据库，除此之外，其他不同步  
binlog-do-db = game
```
（2）从库设置为只读，不能写
在从库slave的配置文件my.cnf中加：
```linux
read_only=1
```
可以限定普通用户的对数据的修改操作，<font color="red">但不会限制root用户（具有super权限的用户）。</font>如若要限制root用户，需要对所有表加读锁：`flush tables with read lock;`

#### 6.其他 ####
停止同步：
`stop slave;`
清理slave：
`reset slave all; `

参考：

[https://blog.csdn.net/xinpengfei521/article/details/80400142](https://blog.csdn.net/xinpengfei521/article/details/80400142)
[https://www.cnblogs.com/gl-developer/p/6170423.html](https://www.cnblogs.com/gl-developer/p/6170423.html)
[https://blog.csdn.net/wuapeng/article/details/80632446](https://blog.csdn.net/wuapeng/article/details/80632446)