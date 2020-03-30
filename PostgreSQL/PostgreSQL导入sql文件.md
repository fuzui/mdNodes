---
title: PostgreSQL导入sql文件
date: 2019-07-05 15:28:34
tags: PostgreSQL
categories: PostgreSQL
---
 <div align='center' ><font size='70'>PostgreSQL导入sql文件</font></div>

最近新项目中用到PostgreSQL，运行测试导入表结构和数据是必须的，用navicat导入sql文件报错，之后查询了一下资料，得到了正确的导入方式：
<!-- more -->
1.命令行（cmd或powershell）进入PostgreSQL安装目录

```shell
cd \PostgreSQL\11\bin
```
2.执行语句`psql -d ethgateway -U ethgateway -f D:\Desktop\db.sql`
这里我用的powershell，所以我的命令是` .\psql -d test -U fuzui -f D:\Desktop\db.sql`
* -d 数据库名
* -U 数据库用户名
* -f sql文件路径
3.回车之后提示输入用户密码，然后静待完成就可以了。


![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20190705130850.png)