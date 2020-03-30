---
title: (已解决)command serverStatus requires authentication 
tags: [问题记录,mongodb]
categories: 问题记录
date: 2019-04-17
---

详情：<font color="red">2019-04-17T20:00:31.142+0800    Failed: command serverStatus requires authentication</font>
今天在学习mongodb监控时，使用mongostat命令出现如上提示，是说需要验证，因为我设置了用户密码。

查看了一下帮助 `mongostat --help`
发现了几个参数
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417201720.png)

觉得和mysql好像！
于是：
输入`mongostat -uroot -proot`
结果![enter description here](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/1555503513971.png)
想起来还需要指定数据库，在内部登录时也是先`use admin`,才能`db.auth("root","root")`
在帮助的-p参数下面也发现了--authenticationDatabase
最后成功解决
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190417202035.png)