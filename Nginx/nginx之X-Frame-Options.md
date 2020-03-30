---
title: nginx之X-Frame-Options 
tags: Nginx
categories: Nginx
date: 2020-02-01
---
 `X-Frame-Options`头主要是为了防止站点被别人劫持、iframe引入

nginx配置形式:

```xml
add_header X-Frame-Options ALLOWALL; #允许所有域名iframe

add_header X-Frame-Options DENY; #不允许任何域名iframe,包括相同的域名

add_header X-Frame-Options SANEORIGIN; #允许相同域名iframe,如a.test.com允许b.test.com

add_header X-Frame-Options ALLOW-FROM uri; #允许指定域名iframe
```

