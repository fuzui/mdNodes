---
title: git项目到多个远程仓库
tags: git
categories: git
date: 2019-07-01
---

<div align='center' ><font size='70'>git项目到多个远程仓库</font></div>

#### 一、关联多个仓库

主要命令 `git remote add <refs> <addr>`
refs不能一样，addr为自己远程仓库的地址，可以是github、coding、码云等，但是多个的话refs要唯一，例如设置github的refs为origin，coding的refs为cod，码云的refs为gitee等，可以自定义名字。

设置完成可通过`git remote -v`查看已有的。

#### 二、推送到仓库

逐一推送：
`git push -u origin master`
`git push -u coding master`
`git push -u gitee master`
等。