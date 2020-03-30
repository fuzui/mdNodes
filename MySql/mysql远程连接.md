---
title: MySQL远程连接
date: 2019-03-25
tags: MySQL
categories: MySQL
---

设置权限 %表示允许远程

```sql
grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
```

或者

```sql
update user set host='%' where user='root';
```

刷新数据库，使权限立刻生效

```sql
flush privileges;
```

