---
title: java设计模式之工厂方法模式FactoryMethod
tags: [设计模式,FactoryMethod]
categories: 设计模式
date: 2019-04-02
---

<div align='center' ><font size='70'>java设计模式之工厂方法模式FactoryMethod</font></div>

***简介：***

>FactoryMethod是创建型模式之一。也称为多态工厂模式，工厂方法模式的意义是定义一个创建产品对象的工厂接口，将实际创建工作推迟到子类当中。核心工厂类不再负责产品的创建，这样核心类就成为一个抽象工程角色，仅负责具体工厂子类必须实现的接口，这样进一步抽象化的好处是使得工厂方法模式可以使系统在不修改具体工厂角色的情况下引进新的产品。

<!-- more -->

#### 一、角色和职责 ####
* Factory（抽象工厂角色）
	简单工厂模式的核心，任何工厂类都必须实现这个接口。
* ConcreteFactory（具体工厂角色）
	具体工厂类是抽象工厂类的一个实现，负责实例化产品对象。
* Product （抽象角色）
	简单工厂模式所创建的所有对象的父类，它负责所有实例所共有的公共接口。
* ConcreteProduct（具体产品角色）
	简单工厂模式所创建的具体实例对象
#### 二、类图 ####
以手机生成为例：Phone为抽象角色类，Apple和HuaWei为具体产品角色类，PhoneFactory为抽象工厂角色类，AppleFactory和HuaWeiFactory为具体工厂角色类。
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190402231757.png)

>*在简单工厂模式中新增产品会违反开闭原则，而在工厂方法模式中解决了这一问题。*