---
title: sql优化
tags: MySql
categories: MySql
date: 2019-06-09
---

#### 一、原生优化 ####

1.适当添加索引
最左原则

2.不适用`!=`，使用`<>`替换，减少模糊查询的使用。
3.where中左侧不参与运算
4.使用`inner join`替换`not exit`或`exit`

#### 二、JDBC优化 ####
1.使用联表查询代替嵌套循环
2.在需要循环使用时，插一条更一条然后commit，避免存放在list中，减少new出对象的空间。
