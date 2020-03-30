---
title: 使用docker拉镜像时连接超时
date: 2019-03-15
tags: docker
categories: 问题记录
---

因为在下载官方镜像点的镜像国内访问速度太慢，所以报错，使用加速器就可以解决

这个问题首先打开配置文件daemon.json，centos上安装后有此文件，但是ubuntu上需要自己创建文件：

```shell
vim /etc/docker/daemon.json
```

在文件中加入：

```json
{
"registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]
}
```

然后重启守护进程：

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```



参考：[https://www.cnblogs.com/icebutterfly/p/9489133.html](https://www.cnblogs.com/icebutterfly/p/9489133.html)