---
title: java设计模式之单例模式Singleton 
tags: Singleton
categories: 设计模式
date: 2019-03-30
---

***简介：***

>单例模式是创建型模式之一。作为对象的创建模式，单例模式确保某一个类之一一个实例，而且自行实例化并向整个系统提供这个实例。

<!-- more -->

#### 一、特点 ####
* 单例类只能有一个实例
* 单例类必须创建自己的唯一实例
* 单例类必须给所有其他对象提供这一实例

#### 二、饿汉式单例类 ####
>空间换时间，当类装载的时候就会创建类的实例，不管用不用，先创建出来，每次调用的时候就不需要再去判断，节省了运行时间。

```java
public class EagerSingleton {
	
	//初始化
	private static EagerSingleton instance = new EagerSingleton();
	
	/**
	 * 	私有默认构造方法
	 */
	private EagerSingleton() {
		
	}
	
	/**
	 * 	静态工厂方法
	 * @return
	 */
	public static EagerSingleton getInstance() {
		return instance;
	}
	
}
```
>类被加载时，静态变量instance会被初始化，此时类的私有构造子会被调用。这时候，单例类的唯一实例就被创建出来了。


#### 三、懒汉式单例类 ####

>时间换空间。每次获取实例都会进行判断，看是否需要创建实例，浪费判断的时间，一直不用的话就不会创建实例，节省内存空间。
>懒汉式是线程安全的，会降低访问速度。

```java
public class LazySingleton {
	
	private static LazySingleton instance = null;
	
	/**
	 * 	私有默认构造方法
	 */
	private LazySingleton() {
		
	}
	
	/**
	 * 	静态工厂方法，使用同步化，以适应多线程环境
	 * @return
	 */
	public static synchronized LazySingleton getInstance() {
		
		if(instance == null) {
			instance = new LazySingleton();
		}
		return instance;
		
	}
}
```
#### 三、双重检查加锁 ####
>并不是每次进入getInstance方法都需要同步，而是先不同步，进入方法后，先检查实例是否存在，如果不存在才进行下面的同步块，这是一重检查，进入同步块过后，再次检查实例是否存在，如果不存在，就在同步的情况下创建一个实例，这是第二重检查，这样就只需要同步一次，从而减少了多次在同步情况下进行判断所浪费的实际。
>会使用volatile关键字，被volatile修饰的变量的值，将不会被本地线程缓存，所有对该变量的读写都是直接操作共享内存，从而确保了多个线程能够正确处理该变量。

```java
public class Singleton {
	
	//使用volatile修饰，保证不会被本地线程缓存
	private static volatile Singleton instance = null;
	
	/**
	 * 	私有默认构造器
	 */
	private Singleton() {
		
	}
	
	/**	
	 * 	静态工厂方法
	 * @return
	 */
	public static Singleton getInstance() {
		
		//检查是否存在实例，不存在进入下面同步块
		if(instance == null) {
			// 同步块，保证创建实例线程安全
			synchronized(Singleton.class) {
				//第二重检查
				if(instance == null) {
					instance = new Singleton();
				}
			}
		}
		return instance;
	}

}
```

#### 四、Lazy initialization holder class模式 ####

> 使用了Java的类级内部类和多线程缺省同步锁的知识，很巧妙地同时实现了延迟加载和线程安全。
> * 什么是类级内部类？
简单点说，类级内部类指的是，有static修饰的成员式内部类。如果没有static修饰的成员式内部类被称为对象级内部类。
类级内部类相当于其外部类的static成分，它的对象与外部类对象间不存在依赖关系，因此可直接创建。而对象级内部类的实例，是绑定在外部对象实例中的。
类级内部类中，可以定义静态的方法。在静态方法中只能够引用外部类中的静态成员方法或者成员变量。
类级内部类相当于其外部类的成员，只有在第一次被使用的时候才被会装载。
>* 多线程缺省同步锁的知识
在多线程开发中，为了解决并发问题，主要是通过使用synchronized来加互斥锁进行同步控制。但是在某些情况中，JVM已经隐含地为您执行了同步，这些情况下就不用自己再来进行同步控制了。这些情况包括：
1.由静态初始化器（在静态字段上或static{}块中的初始化器）初始化数据时
2.访问final字段时
3.在创建线程之前创建对象时
4.线程可以看见它将要处理的对象时

```java
public class HolderSingleton {

	/**
	 * 私有默认构造方法
	 */
	private HolderSingleton() {
		
	}
	
	/**
	 * 	类级内部类，即静态成员式内部类，该内部类与外部类的实例没有绑定关系，只有调用时才会装载，从而实现了延迟加载。
	 *
	 */
	private static class Singleton{
		/*
		 * 静态初始化器，由JVM来保证线程安全
		 * */
		private static HolderSingleton instance = new HolderSingleton();
	}
	
	/**
	 * 	静态工厂方法
	 * @return
	 */
	private static HolderSingleton getInstance() {
		return Singleton.instance;
	}
	
}
```
当getInstance方法第一次被调用的时候，它第一次读取Singleton.instance，导致Singleton类得到初始化；而这个类在装载并被初始化的时候，会初始化它的静态域，从而创建Singleton的实例，由于是静态的域，因此只会在虚拟机装载类的时候初始化一次，并由虚拟机来保证它的线程安全性。

#### 五、枚举 ####
>单元素的枚举类型已经成为实现Singleton的最佳方法。用枚举来实现单例非常简单，只需要编写一个包含单个元素的枚举类型即可

```java
public enum EnumSingleton {
	/**
	 * 	定义一个枚举元素，就代表EnumSingleton实例
	 * */
	uniqueInstance;
	
	/**
	 * 单例自己的操作
	 * */
	public void singletonOperration() {
		//功能处理
	}
	
}
```


完全参考：[java_my_life博主的《JAVA与模式》之单态模式](http://www.cnblogs.com/java-my-life/archive/2012/03/31/2425631.html)