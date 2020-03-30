---
title: MongoDB基本命令
date: 2019-03-18
tags: MongoDB
categories: MongoDB
---

# 1.创建数据库

```shell
use mydb
```

# 2.切换数据库

```shell
db
```

# 3.查看所有数据库

```shell
show dbs
```

# 4.删除数据库

```shell
db.dropDatabase()
```

# 5.创建集合

```shell
db.createCollection(name,{options})
```

options为可选参数，指定有关内存大小及索引的选项。

capped 为true时创建固定集合，达到最大时，自动覆盖最早的文档，必须指定size参数；

autoindexId 为true时，在_id字段上创建索引；

size 为固定集合指定一个最大值（以字节计）；

max  指定固定集合中包含文档的最大数量；

eg:`db.createCollection("student")`

> 插入文档时也可以自动创建集合。

# 6.删除集合

```shell
db.collection.drop()
```

# 7.查看集合

```shell
show collections
```

# 8.插入文档

```shell
db.student.insert({
    name:'wangze',
    age:22,
    profession:'网络工程'
})
```

# 9.插入多条文档

```shell
db.student.insert(
   [
        {
            name:'wwze',
            age:23,
            profession:'网络工程'
        }
        {
            name:'fuzui',
            age:23,
            profession:'网络工程'
        }
    ] 
)
```

# 10.显示集合内容

```shell
db.student.find()
db.student.find().pretty()    //工整显示
```

# 11.插入内嵌文档

```shell
db.student.insert({
    name:'wangze',
    age:22,
    profession:'网络工程'，
    tags:['mongodb','student','huake']
})
```

# 12.插入一条、多条

```shell
db.student.insertOne()
db.student.insertMany()
```

# 13.save()方法：(可用于更新)

> 与insert方法相同，区别在于save时，若主键已存在，则修改，而insert时，会报错。

# 14.更新文档

```shell
db.student.update(
    <query>,
    <update>,
    { 
        upsert:<boolean>,
        multi:<boolean>,
        writeConcern:<document>,
        collation:<document>
    }
)
```

* `query`：查询条件  
* `update`：更新对象文档和更新操作符  
* `upsert`：如果不存在，是否插入  
* `multi`：是否更新多条记录  
* `writeConcern`：出错机制  
* `collation`：指定特定国家语言更新

```shell
db.student.update(
    {
        'name':'wangze'
    },
    {
        $set{'age':24}
    }
)
```

更新操作符

```shell
$set    修改某值
$inc        修改数值，做加法运算
$mul        修改数值，做乘法运算
$rename        重命名键名
$min        将$min给出的值与当前文档字段进行比较，当给定值较小的时候则修改当前文档值为给定值。
$max        与$min相反
$unset        删除一个字段
lasttime:ISODate(2019-03-18 22:40:55)        插入更新时间
```



# 15.删除文档

```shell
db.student.remove(
        <query>,
        {
                justOne:<boolean>
                writeConcern:<document>
        }    
)
```

`query`：删除文档的条件

`justOne` ：true为只删除一个文档，默认为flase，删除全部匹配条件的文档
`remove`方法不会真正释放空间，需要执行`db.repairDatabase()`。

```shell
db.student.deleteMany({})        删除全部文档
db.student.deleteMany({<query>})       删除符合条件的全部文档
db.student.deleteOne({<query>})       删除符合条件的一条文档
```

# 16.查询文档

查询返回值游标操作

```shell
var showCursor=db.student.find()
showCursor.forEach(printjson)        打印游标获取的集合
```

limit和skip方法查询

```shell
db.student.find().limit(1)        返回低一条文档
db.student.find().skip(2)        返回第三条开始的文档记录
```

```shell
等于        {<key>:<value>}
小于        {<key>:{$lt:<value>}}
小于等于    {<key>:{$lte:<value>}}
大于        {<key>:{$gt:<value>}}
小于等于    {<key>:{$gte:<value>}}
不等于      {<key>:{$ne:<value>}}
equal      {<key>:{$eq:<value>}}
```

or条件

```shell
db.student.find(
{
    $or:[
        {key1:value1},{key2:value2}
    ]
}
)
```

and条件

```shell
db.student.find(
{
        {key1:value1},{key2:value2}
    
}
)
```

projection参数

```shell
db.collection.find(query,projection)
```

> projection指定返回的参数

> 0不显示，1显示，只能全0或全1.只有在全1条件下可以指定_id为0，不显示id

模糊查询

```shell
db.student.find(
{key:{$regex:/pattern/<options>}}
)
```

> pattern：
>
> /任意部分/
>
> /^前面部分/
>
> /后面部分$/

> options：
>
> i：忽略大小写
>
> x：忽略空格、hash值、#
>
> m：多行匹配模式，会更改^和$的默认行为，分别是行的开头与结尾
>
> s：单行匹配模式会忽略.的默认行为，会匹配所有字符。

# 17.$type操作符

> 基于BSON类型来检索集合中匹配的数据类型，并返回结果

# 18.排序方法sort()

```shell
db.student.find().sort("age":1)        按年龄升序
db.student.find().sort("age":-1)        按年龄降序
```

# 19.索引

创建索引

```shell
db.student.createIndex(keys,options)
```

key为创建的索引字段，1为升序创建索引，-1为降序创建索引

```shell
db.student.createIndex({"name":1})
```

> options:
>
> background     建索引过程会阻塞其它数据库操作，true可指定后台方式创建，默认为flase。
>
> unique  建立索引是否唯一，默认为flase
>
> name     索引名称。

查看索引

`db.student.getIndexes()`

查看索引大小

`db.student.totalIndexSize()`

删除所有索引

`db.student.dropIndexes()`

删除指定索引

`db.student.dropIndex("索引名")`

重构索引（会消耗大量资源，性能下降）

`db.student.reIndex()`

# 20.聚合

```shell
db.student.aggregate(aggergate_operation)
```

> 聚合表达式：
>
> $sum     计算总和
>
> $avg     计算平均值
>
> $min     获取集合中所有文档对应值的最小值
>
> $max     获取集合中所有文档对应值的最大值