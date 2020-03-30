---
title: (已解决)The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more than one time zone.
tags: mysql
categories: 问题记录
date: 2019-05-12
---


详情：
<font color="red">SQLSTATE = 01S00
The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.</font>

今天在使用powerdesigner工具连接mysql构建逆向工程图时，在连接过程中出现如上提示。
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190512173949.png)解决办法:
在连接url上加：`serverTimezone=GMT%2B8`
即：`jdbc:mysql://localhost/数据库名?serverTimezone=GMT%2B8`