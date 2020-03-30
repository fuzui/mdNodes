---
title:  ArrayList 和 LinkedList 区别 
tags: 容器
categories: javaSE
date: 2019-04-08
---

<div align='center' ><font size='70'>ArrayList 和 LinkedList 区别 </font></div>

1.ArrayList的实现是基于数组，linkedList的实现是基于双向列表；

2.对于随机访问，Array优于LinkedList，Array可以根据下标以O(1)时间复杂度对元素进行随机访问。而LinkedList的每一个元素都依靠地址指针和它后一个元素连接在一起，在这种情况下，查找某个元素的时间复杂度是O(n)；

3.对于插入和删除操作，LinkedList优于ArrayList，因为当元素被添加到LinkedList任意位置的时候，不需要像ArrayList那样中心计算大小或者是更新索引。

4.LinkedList比ArrayList更占内存，因为LinkedList的节点除了存储数据，还存储了两个引用，一个指向前一个元素，一个指向后一个元素。