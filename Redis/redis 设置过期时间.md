---
title: redis 设置过期时间
tags: redis
categories: redis
date: 2019-06-03
---

#### 一、原生

```shell
#以秒计
EXPIRE key seconds
#以毫秒计
PEXPIRE key milliseconds
#秒级时间戳
EXPIREAT key timestamp
#毫秒级时间戳
PEXPIREAT key timestamp

```

在原生redis中，可以使用`TTL key`来检测某个key剩余的存活时间。

移除过期时间
```shell
PERSIST key
```

#### 二、Jedis
```java
Jedis redis = null;
//以秒计
redis.expire(key,seconds);
//时间戳
redis.expire(key,unixTime);

```
在jedis中，可以使用`redis.ttl(key)`来检测某个key剩余的存活时间。
