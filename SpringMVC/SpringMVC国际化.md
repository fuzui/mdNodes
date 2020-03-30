---
title: SpringMVC国际化 
tags: SpringMVC
categories: SpringMVC
date: 2019-03-07
---

<div align='center' ><font size='70'>SpringMVC国际化 </font></div>

写对应的资源文件` .properties`

资源文件名称_语言编码_国家编码.properties

`abc_zh_CN.properties`

`abc_en_US.properties`

key=value

1、国际化资源文件

2、告知`springMVC`所使用的资源文件

```xml
<bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource"><property name="basename" value="abc"></property></bean>
```

id必须为messageSource

3.JSTL

​		Core

​		`fmt:message` 通过一个`key`来实现国际化