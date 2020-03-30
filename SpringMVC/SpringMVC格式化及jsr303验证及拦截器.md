---
title: SpringMVC格式化及jsr303验证及拦截器 
tags: SpringMVC
categories: SpringMVC
date: 2019-03-07
---

# 一、数字格式化

* `<mvc:annotation-driven>`

* `@NumberFormat`标注在pojo属性上面。

  `-pattern`：自定义样式，#来代表任意数字

  -`style`：`NumberFormat.style`

* 3.必须让从`versionService`采用的树`FormattingConversionService`

  ①、默认`spring`加载的`FormattingConversionService`

  ②、自定义类型转换器，`FormattingConversionService`

# 二、日期格式化

* `@DateTimeFormat`

* `-pattern`

* `-iso` 四种 `ISO.NONE`(不使用)默认，`ISO.DATE(yyyy-MM-dd)、ISO.TIME(hh:mm:ss.SSSZ)、ISO.DATE_TIME(yyyy-MM-dd hh:mm:ss.SSSZ)`

* `-style`属性：字符串类型。通过样式指定日期时间的格式，由两位字符组成，第一位镖师日期的格 式，第二位表示时间的格式：S：短日期/时间格式、M：中日期/时间格式、L：长日期/时间格式、F：完成日期/时间格式、-：忽略日期或时间格式。

# 三、JSR303

在实体类属性上加需要验证的。如`@Notnull`

在springmvc配置文件中

```XML
<bean id="validator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean"></bean>
```

并在`<mvc:annotation-driven>`加属性`validator="validator"`（可以自动创建bean）在控制器类中需要用到参数的前面加`@Valid`。

# 四、拦截器

接口 `implement HandlerInterceptor`

抽象方法：

`- preHandle()`:在业务初期器处理请求之前被调用，在该方法中对用户请求request进行处理`- postHandle()`:这个方法在业务处理器处理完请求后，但是DispatcherServlet向客户端返回响应前被调用。`- afterCompletion()`:这个方法DispatcherServlet完全处理完请求后被调用，可以在该方法中进行一些资源清理的操作。在springmvc配置文件中

```xml
<mvc:interceptors><mvc:interceptor><mvc:mapping path="/toFormat"/> 
    <!--toFormat通过映射访问业务方法-->
    <!--定义一个bean-->
    </mvc:interceptors>
</mvc:interceptors>
```



