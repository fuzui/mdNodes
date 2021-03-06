---
title: java设计模式之原型模式Prototype 
tags: [设计模式,Prototype]
categories: 设计模式
date: 2019-04-02
---
***简介：***

>Prototype是创建模式之一。通过给出一个原型对象来致命所有创建的对象的类型，然后用复制这个原型对象的办法创建出更多同类型的对象。
>原型模式的结构：
>>原型模式要求对象实现一个可以克隆自身的接口，这样就可以通过复制一个实例对象本身来创建一个新的实例。这样一来，通过原型实例创建新的对象，就不再需要关心这个实例本身的类型，只要实现了克隆自身的方法，就可以通过这个方法来获取新的对象，而无须再去通过new来创建。
>>原型模式有两种表现形式：（1）简单形式（2）登记模式

<!-- more -->

#### 一、简单形式  ####
##### 1.角色和职责 #####
* Client（客户）
	客户类提出创建对象的请求
* Prototype（抽象原型）
	由java接口或抽象类实现，给出具体原型类所需要的接口
* ConcretePrototype（具体原型）
	被复制的对象
	
##### 2.类图 #####
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190402132354.png)

#### 二、登记形式  ####
##### 1.角色和职责 #####
* Client（客户）
	客户类提出创建对象的请求
* Prototype（抽象原型）
	由java接口或抽象类实现，给出具体原型类所需要的接口
* ConcretePrototype（具体原型）
	被复制的对象
* PrototypeMananger（原型管理器）
	创建具体原型类的对象，并记录每一个被创建的对象
	
##### 2.类图 #####
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190402141807.png)

#### 三、优缺点  ####
>优点：
>>原型模式允许在运行时动态改变具体的实现类型原型模式可以在运行期间，由客户来注册符合原型接口的实现类型，也可以动态地改变具体的实现类型，看起来接口没有任何变化，但其实运行的已经是另外一个类的实例了，因为克隆一个原型就如同实例化一个类。
>
>缺点：
>
>>需要每个类都不洗配备一个克隆方法。配备克隆方法需要对类的功能进行通盘考虑，这对于全新的类来说不是很难，而对于已经有的类不一定容易，特别是当一个类引用不支持序列化的间接对象，或者引用含有循环结构的时候。