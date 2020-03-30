---
title: Mon Apr 08 14:02:29 CST 2019 WARN: Establishing SSL connection without server's
date: 2019-04-08
tags: MySql
categories: 问题记录
---

Mon Apr 08 14:02:29 CST 2019 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.

有道翻译结果：

> 警告:不建议在没有服务器身份验证的情况下建立SSL连接。根据MySQL 5.5.45+、5.6.26+和5.7.6+的要求，如果没有设置显式选项，则默认情况下必须建立SSL连接。您需要通过设置useSSL=false显式禁用SSL，或者设置useSSL=true并为服务器证书验证提供信任存储。

解决办法：

在数据库连接上加：`useSSL=false`