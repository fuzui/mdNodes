---
title: linux安装MongoDB4.0（centos7）
date: 2019-04-17 17:08:07
tags: [linux，MongoDB]
categories: MongoDB
---

<div align='center' ><font size='70'>linux安装MongoDB4.0（centos7）</font></div>

<!-- more -->

#### 1. 配置包管理系统（yum） ####
```shell
[mongodb-org-4.0] 
name = MongoDB Repository 
baseurl = https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/ 
gpgcheck = 1 
enabled = 1 
gpgkey = https：// www.mongodb.org/static/pgp/server-4.0.asc
```
#### 2. 安装MongoDB ####
```shell
sudo yum install -y mongodb-org
```

#### 3.启动MongoDB ####
启动：`systemctl start mongod`
停止：`systemctl stop mongod`
重启：`systemctl restart mongod`
开机启动：`systemctl enable mongod.service`

#### 4.进入MongoDB ####
```shell
mongo
```

#### 5.设置验证（账户密码） ####
(1) 进入MongoDB
```shell
mongo
```
(2) 进入admin数据库
```shell
use admin
```
(3) 创建
```shell
db.createUser({user:"root",pwd:"root",roles:["root"]})
```
(4) 退出
```shell
exit
```
(5) 修改配置文件
`vim /etc/mongod.conf`
修改：
```shell
security：
    authorization: enabled
```
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417163444.png)

<font color="red">原本sercurity是被注释掉的，需要把前面#删除，而authorization也是不存在的，需要手动添加。</font>

(6) 重启
```shell
systemctl restart mongod
```
(7)验证
使用mongo进入MongoDB之后，使用show dbs是没有反应的，需要先进入admin数据库，持行`db.auth("root","root")`后，才可以使用。
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417164057.png)

#### 6.远程连接 ####
(1) 关闭防火墙
`systemctl stop firewalld`
可以使其开机关闭：`systemctl disable firewalld`
(2) 修改配置文件
`vim /etc/mongod.conf`
修改：
```shell
 bindIp: 0.0.0.0
```
 原来bindIp是127.0.0.1，需要改为0.0.0.0
 ![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417164501.png)

(3) 重启MongoDB
```shell
systemctl restart mongod
```
(4)	连接
我这里用的 Navicat 12 for MongoDB
工具都差不多
* 新建：
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417164725.png)

* 输入信息：这里填写centos7的ip和MongoDB的admin数据库和刚刚设置过的账号密码
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/$PE@Q7]7T~E]PPG%W`%6G59.png)

* 建立ssh：这里填写centos7的ip和其登录的用户（我直接用的root）
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/GFA@R3BFTAV[O_0[$OV~S$G.png)


* 测试连接
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417165508.png)

最后就可以查看了，默认的数据库是看不到的，所以我这里创了个新数据库
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417165631.png)
linux中：
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417170126.png)
再通过图形界面查看：
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417170229.png)

<br>


参考：
* [MongoDB官方安装](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/)
* [MongoDB4.0.0 远程连接及用户名密码认证登陆配置——windows](https://blog.csdn.net/qq_26896281/article/details/81206492)


