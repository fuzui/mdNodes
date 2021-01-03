---
title: Rest风格及接口
tags: elasticsearch
categories: elasticsearch
date: 2020-11-28
---



```
|  表头   | 表头  |
|  ----  | ----  |
| 单元格  | 单元格 |
| 单元格  | 单元格 |
```





# 索引



| method | url                                             | 描述                 |
| :----: | ----------------------------------------------- | -------------------- |
|  PUT   | localhost:9200/索引名称/类型名称/文档id         | 创建文档(指定文档id) |
|  POST  | localhost:9200/索引名称/类型名称                | 创建文档(指定文档id) |
|  POST  | localhost:9200/索引名称/类型名称/文档id/_update | 修改文档             |
| DELETE | localhost:9200/索引名称/类型名称/文档id         | 删除文档             |
|  GET   | localhost:9200/索引名称/类型名称/文档id         | 查询文档通过文档id   |
|  POST  | localhost:9200/索引名称/类型名称/_search        | 查询所有数据         |



关于文档的基本操作

# 基本操作



1.添加数据

```json
PUT /student/_doc/2
{
  "name": "张三",
  "age": 24,
  "desc": "生活何止眼前的苟且",
  "tags": ["php","react"]
}

```



2.查询数据

```json
GET /student/_doc/1
```







3.更新数据

* 全部更新(其他不填属性将会被置空)

```json
POST student/_doc/2
{
  "doc": {
    "name": "李四a",
    "age": 25,
    "desc": "学而不思",
    "tags": ["node","css"]
  }
}
```



* 指定更新(只修改已填数据)

```
POST student/_doc/2/_update
{
  "doc": {
    "name": "李四a",
    "age": 25,
    "desc": "学而不思",
    "tags": ["node","css"]
  }
}
```



4.查询

```json
GET student/user/_search
{
  "query": {
    "match": {
      "name": "李四a"
    }
  }
}
```

