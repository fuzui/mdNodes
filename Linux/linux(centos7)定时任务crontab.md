---
title: linux(centos7)定时任务crontab
tags: [linux,crontab]
categories: linux
date: 2019-04-06
---
>百度百科：crontab命令常见于Unix和类Unix的操作系统之中，用于设置周期性被执行的指令。该命令从标准输入设备读取指令，并将其存放于“crontab”文件中，以供之后读取和执行。该词来源于希腊语 chronos(χρνο)，原意是时间。通常，crontab储存的指令被守护进程激活， crond常常在后台运行，每一分钟检查是否有预定的作业需要执行。这类作业一般称为cron jobs。

*在centos7中：*
**启动**
`systemctl start crond`

**停止**
`systemctl stop crond`

**重启**
`systemctl restart crond`

**编辑**
`crontab -e`

**删除**
`crontab -r`

**添加任务**

`* * * * * command`
*表示分钟1-59  每分钟用 */1 表示
*表示小时1-23
*表示日期1-31
*表示月份1-12
*表示星期0-6