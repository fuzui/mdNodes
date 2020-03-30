---
title: CentOS7 重置 MySQL各版本密码
date: 2019-04-19 23:17:23
tags: MySQL
categories: MySQL
---

<div align='center' ><font size='70'>CentOS7 重置 MySQL各版本密码</font></div>

# 1.查看自动生成的密码

安装完成后 MySQL会给我们自动生成一个随机密码
查看命令如下：`grep 'temporary password' /var/log/mysqld.log`

# 2.配置文件 MySQL 免密码登录
编辑 MySQL 的配置文件

`vim /etc/my.cnf`
在开头的下面一行加入下面这句

`skip-grant-tables`
保存并退出。

# 3.重启 MySQL 服务

# 4.免密码登录到 MySQL 上

`mysql -u root -p`
提示输入密码时直接敲回车。

# 5.选择 mysql 数据库

`use mysql;`

# 6.在 mysql 数据库的 user 表中查看当前 root 用户的相关信息

`select host, user, authentication_string, plugin from user;`
mysql5.6中：

`select host,user,password from user;`

执行完上面的命令后会显示一个表格
表格中有以下信息：
`host`: 允许用户登录的 ip ‘位置’ % 表示可以远程；
`user`: 当前数据库的用户名；
`authentication_string`: 用户密码（在mysql 5.7.6以后废弃了password字段和password()函数）；
`plugin`： 密码加密方式；

# 7.将默认的 root 密码置空

```sql
use mysql;  

update user set authentication_string='' where user='root';
```

mysql5.6中：

```sql
update user set password ='' where User='root';
```



# 8.退出 mysql命令行
# 9.删除` /etc/my.cnf` 文件最后的 `skip-grant-tables`
`vim /etc/my.cnf`
删除 `skip-grant-tables` 并保存退出。

# 10.重启 MySQL 服务

# 11.重新登录到 MySQL 上

`mysql -u root -p`
提示输入密码时直接敲回车，因为我们刚才已经将密码置为空了。

# 12.使用 ALTER 修改 root 用户密码

```sql
ALTER user 'root'@'localhost' IDENTIFIED BY 'Wze@fuzui';
```

其中 `Wze@fuzui`为你设置的新密码，注意这个密码如果设置的比较简单，例如 123456 等等，会设置不成功，它会提示你设置的密码太简单，最好设置成大写字母、数字、符号的组合。
因为 MySQL 5.7.6 以后废弃了 user 表中的 password 字段和 password() 方法，所以使用旧的方法去重置密码对 mysql 8.0 是不行的！而在mysql5.6中无法进入mysql，提示：ERROR 1820 (HY000): You must SET PASSWORD before executing this statement

直接通过以下命令设置密码。`SET PASSWORD = PASSWORD('Wze@fuzui');`



# 13.参考：

* <https://blog.csdn.net/xinpengfei521/article/details/80400142>