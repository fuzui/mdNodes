---
title: MongoDB数据管理
tags: MongoDB
categories: MongoDB
date: 2019-04-17
---

#### 一、数据备份 ####

`mongodump`
主要参数
-d 数据库
-p 查询条件
-c 集合
-q "查询条件"  例如- q "{name:'wangze'}"
-o 指定存放路径

```shell
mongodump -d wwze -o /root/mytest/ -uroot -proot --authenticationDatabase=admin
```

#### 二、数据恢复 ####
`mongorestore -d 数据库名 路径`
可选参数： --drop表示已存在则删掉原来的，不加--drop恢复数据时与原数据合并。

#### 三、数据导出 ####
1.json格式
`mongoexport -d 数据库名 -c 集合 -o 路径/文件名.dat`
可选参数 -q

2.csv格式

`mongoexport -d 数据库名 -c 集合 --type=csv -f 字段1,字段2 -o 路径/文件名.csv`

#### 四、数据导入 ####
1.json格式
`mongoimport -d 数据库名 -c 集合名 文件路径`
可选参数
--upsert  如果数据存在，则更新，否则不插入。
--drop 如果数据存在，则删除原数据，否则合并。

2.csv格式

`mongoimport -d 数据库名 -c 集合名 --type=csv --headerline --file 文件路径`

--headerline 表示不导入第一行，因为csv格式文件的第一行为列名。
