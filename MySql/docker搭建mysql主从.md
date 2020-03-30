---
title: docker搭建mysql主从
date: 2019-04-19 23:17:23
tags: MySql
categories: MySql
---

<div align='center' ><font size='70'>docker搭建mysql主从</font></div>

# 1.获取镜像

```shell
docker pull fuzui/mysql-master:5.7
docker pull fuzui/mysql-slave:5.7
```



# 2.查看镜像

```shell
docker images
```



# 3.宿主机创建目录用来挂载（放置容器意外删除或宕机）

```shell
mkdir -p /home/mysql/master-data
mkdir -p /home/mysql/slave-data
```



# 4.启动主从mysql容器

* 启动mysql-master

  ```shell
  docker run \
  --name mysql-master \
  --privileged=true \
  -v /home/mysql/master-data:/var/lib/mysql \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=root \
  -d fuzui/mysql-master:5.7 tail -f /dev/null
  ```

* 启动mysql-slave

  ```shell
  docker run \
  --name mysql-slave \
  --privileged=true \
  -v /home/mysql/slave-data:/var/lib/mysql \
  -p 3307:3306 --link mysql-master:master \
  -e MYSQL-ROOT_PASSWORD=root \
  -d fuzui/mysql-slave:5.7 tail -f /dev/null
  ```

  

# 5.登录master容器授权

```shell
docker exec -it mysql-master /bin/bash 
mysql -uroot -proot 
grant replication slave on *.* to 'test'@'%' identified by '123456'; 
flush privileges;

8.0
CREATE USER 'slave'@'%' IDENTIFIED BY '123456';
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'root'@'%';
```

如需指定ip，通过以下命令查询ip

```shell
docker inspect --format='{{.NetworkSettings.IPAddress}}' mysql-master
docker inspect --format='{{.NetworkSettings.IPAddress}}' mysql-slave
```

在master容器查看主机状态，查询对应的file和position

```shell
show master status;
```

# 6.登录slave容器并启动
```shell
docker exec -it mysql-slave /bin/bash 
mysql -uroot -proot 
change master to master_host='master', master_user='test', master_password='123456', \
master_port=3306, master_log_file='file', master_log_pos=position, master_connect_retry=30; start slave; 
// 查看从的状态 
show slave status\G
```



# 7.验证是否同步。



# 8.参考：

* [Docker搭建mysql主从](https://blog.csdn.net/supermao1013/article/details/83065682)