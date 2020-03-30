---
title: docker hub
tags: docker
categories: docker
date: 2019-03-16
---

<div align='center' ><font size='70'>docker hub</font></div>



# 1.拥有一个镜像
`docker pull      `

# 2.给镜像打标签

`docker tag IMAGE ID`

 新`REPOSITORY:tag新``REPOSITORY`命名：` [仓库名]/[镜像名]或[仓库名]/[镜像名]:[版本号]`，仓库名即注册的账号名称， 如果不指定版本号则默认的版本号为`latest`。
# 3.登录

`docker login`

# 4.推送

 `docker push 新REPOSITORY:tag`

docker hub官网：https://cloud.docker.com/repository/list