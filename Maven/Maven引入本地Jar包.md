---
title: Maven引入本地Jar包
tags: maven
categories: maven
date: 2019-12-24
---

#### 问题简述

最近使用SpringBoot做项目，需要引入其他工程的POJO包，由于第一次接触Maven，所以在网上随便找了一则例子将jar包放到工程下的lib（根目录新建）文件夹中，然后pom中引入，工程开发测试都OK，但是打包发布时，目录lib下的jar包总是打不进去，网上搜寻好久才找到答案，在此分享给大家。
#### 问题环境

使用SpringBoot 2.0.1；Maven 4.0.0；JDK 1.8
#### 问题解决

1.首先在工程根目录下新建文件夹lib，之后在pom.xml中引入本地jar包，配置如下：
```xml
<dependency>
 <groupId>coupon-pojo</groupId>
  <artifactId>coupon-pojo</artifactId>
  <version>1.0</version>
  <scope>system</scope>
  <systemPath>${project.basedir}/lib/coupon-pojo-1.0.jar</systemPath>
</dependency>
```

其中groupId和artifactId自定义取名，version为当前jar包版本（自定义），scope固定用system，systemPath中${project.basedir}为项目根目录（建议使用此方式），coupon-pojo-1.0.jar为jar包全名称。

2.如项目打包成war，则在`<build>`标签下的`<plugins>`中新增如下配置：
```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-war-plugin</artifactId>
  <configuration>
    <warName>${project.artifactId}</warName>
    <webResources>
      <resource>
        <directory>lib/</directory>
        <targetPath>WEB-INF/lib</targetPath>
        <includes>
          <include>**/*.jar</include>
        </includes>
      </resource>
    </webResources>
  </configuration>
</plugin>
```

其中：
groupId和artifactId固定；
warName为打包war名称（${project.artifactId}取的是pom中设置的值）；
directory为工程中jar包所在路径；
targetPath为打包后jar包放置的路径，同上；
include为lib目录中的jar包，可配置*全部导入；

3.如果工程打包成jar，需要在pom.xml中添加如下配置：
```xml
 <resources>  
     <resource>  
         <directory>lib</directory>  
         <targetPath>BOOT-INF/lib/</targetPath>  
         <includes>  
             <include>**/*.jar</include>  
         </includes>  
     </resource>
 </resources>
```
此配置方式能否在war包中适用，暂未尝试，有待各位尝试，本文只是解决本项目中所遇到的问题，仅做参考。



作者：暗香抚动
链接：<a href='https://www.jianshu.com/p/2896cba00ce1'>https://www.jianshu.com/p/2896cba00ce1</a>