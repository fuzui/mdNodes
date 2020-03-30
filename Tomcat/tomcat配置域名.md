---
title: tomcat配置域名 
tags: tomcat
categories: tomcat
date: 2019-03-15
---

<div align='center' ><font size='70'>tomcat配置域名 </font></div>

在server.xml中加入以下：

```xm
<Host name="域名" appBase="/www/server/tomcat/webapps" unpackWARs="true" autoDeploy="true">   <Context path="" docBase="项目绝对路径" debug="0" reloadable="true" />   <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs" prefix="localhost_access_log." suffix=".txt" pattern="%h %l %u %t &quot;%r&quot; %s %b" />   </Host>
```



