---
title: github搭建blog
date: 2019-03-17
tags: Hexo
categories: Hexo
---

1.前往github新建一个项目

​	项目名称为：`fuzui.github.io`

2.设置

​	选择项目settings

​	可以看到`GitHub Pages`已经成功生成，显示`fuzui.github.io`

​	可以通过`choose a theme`选择github的默认主题

3.简单编辑

* clone博客项目到本地，复制git或https链接，打开git bash

  ```shell
  git clone https://github.com/XXX/XXX.github.io.git
  ```

* 编辑简单的博客

  ```shell
  echo "Hello World!" > index.html
  git add index.html
  git commit -m "Init commit"
  git push origin master
  ```

* 打开链接后会发现hello world页面