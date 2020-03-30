---
title: linux下配置ftp（centos7）
date: 2019-03-25 14:49:51
tags: [linux,ftp]
categories: linux
---
<div align='center' ><font size='70'>linux下配置ftp（centos7）</font></div>

#### 1.安装 ####

##### &emsp;1.1 准备工作 #####
&emsp;&emsp;关闭防火墙

``` linux
systemctl stop firewalld
#在centos6中使用命令 service iptables stop
```
&emsp;&emsp;拥有固定ip

##### &emsp;1.2 yum安装 #####

``` linux
yum -y install vsftpd lftp ftp
```
##### &emsp;1.3 关闭开机启动 #####

``` linux
systemctl disable firewalld
#在centos6中使用命令 chkconfig vsftpd off
```
#### 2.简介 ####
>ftp：文件传输协议
>协议名：ftp
>软件包：vsftpd
>ftp端口：控制端口 21/tcp   数据端口 20/tcp
>配置文件：/etc/vsftpd/vsftpd.conf

&emsp;&emsp;(1)控制端口是用来建立连接的，不能传输数据。
&emsp;&emsp;(2)主动模式是服务器主动送达，客户端需提供地址，不安全。
&emsp;&emsp;(3)被动模式是客户端去服务器获取。
&emsp;&emsp;(4)主动和被动是由客户端决定。
&emsp;&emsp;`vi /etc/lftp.conf`
&emsp;&emsp;主动模式
&emsp;&emsp;`set ftp:passive-mode off`
&emsp;&emsp;被动模式
&emsp;&emsp;`set ftp:passive-mode on`
&emsp;&emsp;服务器关闭被动模式
&emsp;&emsp;`vi /etc/vsftpd/vsftpd.conf`
&emsp;&emsp;`pasv_enable=no`


#### 3.配置文件 ####
&emsp;&emsp;==vsftpd.conf==

``` 
#是否允许用户匿名登录
anonymous_enable=YES
#是否允许本地用户登录
local_enable=YES
#是否允许写
write_enable=YES
#控制本地用户上传文件的默认权限
local_umask=022
#控制匿名用户上传文件的默认权限
anon_umask=022
#匿名用户限速
anon_max_rate=500000
#本地用户限速
local_max_rate=80000
#ftp用户最大连接数（匿名用户不受限制）
max_clients=500
#单个ip最大连接数、线程数
max_per_ip=2
#指定本地用户访问root的目录
local_root=/ftproot
#指定匿名用户访问root的目录
anon_root=/anonroot
#处于安全考虑，应把所有用户锁定在自己的家目录
chroot_local_user=YES
```

```
#限制用户登录ftp
#黑名单
#开启黑名单功能
userlist_enable=YES
#确定黑名单（默认）
userlist_deny=YES
#名单文件 默认存在
userlist_file=/etc/vsftpd/user_list
#白名单
#确认白名单
userlist_deny=NO
```