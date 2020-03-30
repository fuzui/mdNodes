---
title: Collection 和 Collections 区别 
tags: 容器
categories: javaSE
date: 2019-04-08
---

<div align='center' ><font size='70'>Collection 和 Collections 区别 </font></div>

#### Collection ####

java.util.Collection 是一个集合接口（集合类的一个顶级接口）。它提供了对集合对象进行基本操作的通用接口方法。Collection接口在Java 类库中有很多具体的实现。Collection接口的意义是为各种具体的集合提供了最大化的统一操作方式，其直接继承接口有List与Set。

#### Collection ####
Collections则是集合类的一个工具类/帮助类，其中提供了一系列静态方法，用于对集合中元素进行排序、搜索以及线程安全等各种操作。
1.排序（sort）
2.混排（shuffing）
3.翻转（reverse）
4.替换元素（fill）
5.拷贝（copy）
6.返回最小元素（min）
7.返回最大元素（max）
8.返回指定源列表中最后一次出现指定目标列表的起始位置（lastIndexOfSubList）
9.返回指定源列表中第一次出现指定目标列表的起始位置（indexOfSubList）
10.根据指定的距离循环移动指定列表中的元素（rotate）