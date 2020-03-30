---
title: form标签库 
tags: SpringMVC
categories: SpringMVC
date: 2019-03-07
---

<div align='center' ><font size='70'>form标签库 </font></div>

# 1.引入form标签库

# 2.常用标签

1) `<form:form>`:可以完成回显操作。`modelAttribute:`完成回显，`key`，自动到请求范围中，寻找指定`key`对应的`value`值，如果没有指定`modelAttribute`属性，`springmvc`自动到请求范围当中寻找一个叫`command`这样的`key`值，如果`command`也没有，出错.

2）`<form:input path="">``.path`是`name`属性值