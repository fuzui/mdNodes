---
title: git添加私钥 
tags: git
categories: git
date: 2019-07-01
---

1.添加到bash
`ssh-agent bash`

>ssh-agent是一种控制用来保存公钥身份验证所使用的私钥的程序。
ssh-agent是一个密钥管理器，运行ssh-agent以后，使用ssh-add将私钥交给ssh-agent保管，其他程序需要身份验证的时候可以将验证申请交给ssh-agent来完成整个认证过程


2.添加私钥
`ssh-add ~/.ssh/id_rsa`

其中id_rsa便是私钥，而id_rsa.pub是公钥，名字在创建的时候可以自定义。
