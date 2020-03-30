---
title: docker安装
tags: docker
categories: docker
date: 2019-04-07
---

# 安装docke

```shell
yum install -y epel-release

yum install docker-io
```

 配置文件 `/etc/sysconfig/docker`

# 开机启动

```shell
# 加入开机启动
chkconfig docker on
# 启动docker服务
service docker start 
```

# 基本信息查看

* `docker version` 查看docker的版本号，包括客户端、服务端、依赖的Go等
* `docker info` 查看系统(docker)层面信息，包括管理的images, containers数等

* docker pull centos 下载
* docker images [ centos ] 查看
* docker run -i -t centos /bin/bash