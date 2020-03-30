---
title: HashMap 和 Hashtable  区别 
tags: 容器
categories: javaSE
date: 2019-04-08
---

<div align='center' ><font size='70'>HashMap 和 Hashtable  区别</font></div>

**1.是否线程安全**

*	Hashtable是线程安全的。
*	HashMap非线程安全，但可以使用ConcurrentHashMap（java 5提出）替代多线程环境下。

**2.父类继承**
* Hashtable 继承 Dictionary类。
* HashMap 继承 AbstractMap类。

（但两者都实现了Map接口）

**3.key和value是否允许null值**
* Hashtable中都不允许出现控制。会抛出空指针异常。
* HashMap中允许出现一个key为null，允许出现多个value为null。所以HashMap中不能用get()判断是否存在某个键，而应该用containsKey方法。

**4.contains**
* Hashtable 中有contains、containsKey、containsValue方法，其中contains与containsValue方法相似。
* HashMap中没有contains方法。


**5.遍历方式**
* Hashtable 使用Iterator，但还使用了Enumeration。
* HashMap 中只使用了Iterator。

**6.hash**
* Hashtable 直接使用对象的hashCode。
* HashMap 重新计算hash值。

**7.扩容方式**
* Hashtable 容器默认容量为11，扩容时将原来的2倍加1。
* HashMap 容器默认容量为16，扩容是原来的二倍。
（两者增长因子都是0.75）

参考：https://www.cnblogs.com/cathyqq/p/5279859.html