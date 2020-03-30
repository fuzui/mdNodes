---
title: java设计模式之抽象工厂模式AbstractFactory
tags: [设计模式,AbstractFactory]
categories: 设计模式
date: 2019-04-08
---

<div align='center' ><font size='70'>java设计模式之抽象工厂模式AbstractFactory</font></div>

***简介：***

>AbstractFactory是创建型模式之一。是所有工厂模式中最为抽象和最其一般性的。抽象工程模式可以向客户端提供一个接口，使得客户端在不必指定产品的具体类型的情况下，能够创建多个产品族的产品对象。

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
以手机生成为例：Phone为抽象角色类，Apple和HuaWei为抽象产品，实现Phone接口，ChinaApple、ChinaHuaWei、OtherApple、OtherHuaWei为具体产品角色
PhoneFactory为抽象工厂角色类，ChinaFactory和OtherFactory为具体工厂角色类。

![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190403120439.png)

>抽象工厂模式中分为产品族和产品登记，其中ChinaFactory和OtherFactory为产品族，ChinaApple、ChinaHuaWei和OtherApple、OtherHuaWei分别为ChinaFactory和OtherFactory的产品等级。

#### 三、优缺点 ####
##### 1.优点 #####
* 分离接口和实现
客户端使用抽象工厂来创建需要的对象，而客户端根本就不知道具体的实现是谁，客户端只是面向产品的接口编程而已。也就是说，客户端从具体的产品实现中解耦。
* 使切换产品族变得容易
因为一个具体的工厂实现代表的是一个产品族。
##### 2.缺点 #####
不容易扩展新的产品。如果需要给整个产品族添加一个新的产品，那么就需要修改抽象工厂，这样就会导致修改所有的工厂实现类。