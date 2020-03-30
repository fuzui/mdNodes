---
title: centos系统及内核升级
tags: linux
categories: linux
date: 2019-04-07
---

<div align='center' ><font size='70'>centos系统及内核升级</font></div>

#### 一、检测版本

  ####

检测系统：
`cat /etc/redhat-release`
检测内核
`uname -sr`

#### 二、升级系统  ####

使用update命令
`yum update`

重启
`reboot`

检测系统：
`cat /etc/redhat-release`

#### 三、升级内核  ####


CentOS 允许使用 ELRepo,这是一个第三方仓库,可以将内核升级到最新主线稳定内核.
要在 CentOS 7.× 上启用 ELRepo 仓库,请运行:
`rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org`

`rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm`

仓库启用后，可以使用下面的命令列出可用的系统内核相关包:
`yum --disablerepo="*" --enablerepo="elrepo-kernel" list available`

接下来，安装最新的主线稳定内核:
`yum --enablerepo=elrepo-kernel install kernel-ml`

为了让新安装的内核成为默认启动选项，需要如下修改 GRUB 配置,打开并编辑 /etc/default/grub 并设置 GRUB_DEFAULT=0.意思是 GRUB 初始化页面的第一个内核将作为默认内核.
```
# vi /etc/default/grub

> GRUB_TIMEOUT=5
> GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
> GRUB_DEFAULT=0
> GRUB_DISABLE_SUBMENU=true
> GRUB_TERMINAL_OUTPUT="console"
> GRUB_CMDLINE_LINUX="crashkernel=auto console=ttyS0 console=tty0 panic=5"
> GRUB_DISABLE_RECOVERY="true"
> GRUB_TERMINAL="serial console"
> GRUB_TERMINAL_OUTPUT="serial console"
> GRUB_SERIAL_COMMAND="serial --speed=9600 --unit=0 --word=8 --parity=no --stop=1"
```
接下来运行下面的命令来重新创建内核配置.
`grub2-mkconfig -o /boot/grub2/grub.cfg`

重启机器，查看系统当前内核版本,验证最新的内核已作为默认内核
`uname -a`


删除 CentOS 更新后的旧内核
查看系统中全部的内核 RPM 包:
`rpm -qa | grep kernel`
```
kernel-tools-3.10.0-514.26.2.el7.x86_64
kernel-devel-3.10.0-514.10.2.el7.x86_64
kernel-3.10.0-514.26.2.el7.x86_64
kernel-3.10.0-327.el7.x86_64
kernel-ml-4.12.0-1.el7.elrepo.x86_64
kernel-headers-3.10.0-514.26.2.el7.x86_64
kernel-devel-3.10.0-514.26.2.el7.x86_64
kernel-tools-libs-3.10.0-514.26.2.el7.x86_64
```

删除旧内核的 RPM 包
`yum remove ...`

重启系统
`reboot`
转载:
作者：My_Oh_My
链接：https://www.jianshu.com/p/fdf6bb6c5b9c
