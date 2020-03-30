---
title: MySQL 性能优化神器 Explain 使用分析
tags: MySql
categories: MySql
date: 2019-05-30
---

> 简介：
> MySQL 提供了一个 EXPLAIN 命令, 它可以对 SELECT 语句进行分析, 并输出 SELECT 执行的详细信息, 以供开发人员针对性优化

字段含义：
##### 1.id
* select 的查询符，是select的的查询序列号
##### 2.select_type
* SIMPLE： 简单的查询（不使用UNION或子查询）
* PRIMARY： 最外面的SELECT
* UNION： UNION中的第二个或者后面的SELECT语句
* DEPENDENT UNION：UNION中的第二个或者后面的SELECT语句，取决于外面的查询
* UNION RESULT：UNION的结果
* SUBQUERY：子查询中第一个SELECT
* DENPENDENT SUBBQUERY：子查询中第一个SELECT,取决于外面的查询
* DERIVED：导出表的SELECT（FROM子句的子查询）

##### 3.table
* 输出行所引用的表

##### 4.type
联接类型。下面给出各种联接类型,按照从最佳类型到最坏类型进行排序:
* system：表中只有一条数据，这个类型是特殊的const类型
* const：针对主键或唯一索引的等值查询扫描，最多只返回一条数据。const查询速度非常快，因为它仅仅读取一次即可。
* eq_ref：此类型通常出现在多表的join查询，表示对于前表的每一个结果，都只能匹配到后表的一行结果，并且查询比较的操作通常是=，查询效果较高。
* ref：多表的join查询，针对非唯一索引或非主键索引，或是使用了最左前缀规则索引的查询。
* range：表示使用索引范围查询，通过索引字段获取表中部分数据记录， 这个类型通常出现在 =, <>, >, >=, <, <=, IS NULL, <=>, BETWEEN, IN() 操作中。
* index：表示全索引扫描，和ALL类型相似。
* ALL：全表扫描

##### 5.possible_keys
表示 MySQL 在查询时, 能够使用到的索引，并不代表被真正使用

##### 6.key

表示当前查询真正使用到的索引

##### 7.key_len
表示查询优化器使用了索引的字节数. 这个字段可以评估组合索引是否完全被使用, 或只有最左部分字段被使用到.

##### 8.rows
rows 也是一个重要的字段. MySQL 查询优化器根据统计信息, 估算 SQL 要查找到结果集需要扫描读取的数据行数.
这个值非常直观显示 SQL 的效率好坏, 原则上 rows 越少越好.

##### 9.filtered
显示了通过条件过滤出的行数的百分比估计值。
##### 10.Extra

* Distinct:MySQL发现第1个匹配行后,停止为当前的行组合搜索更多的行。
*    Not exists:MySQL能够对查询进行LEFT JOIN优化,发现1个匹配LEFT JOIN标准的行后,不再为前面的的行组合在该表内检查更多的行。
*    range checked for each record (index map: #):MySQL没有发现好的可以使用的索引,但发现如果来自前面的表的列值已知,可能部分索引可以使用。
 *   Using filesort:MySQL需要额外的一次传递,以找出如何按排序顺序检索行。
 *   Using index:从只使用索引树中的信息而不需要进一步搜索读取实际的行来检索表中的列信息。
*    Using temporary:为了解决查询,MySQL需要创建一个临时表来容纳结果。
*    Using where:WHERE 子句用于限制哪一个行匹配下一个表或发送到客户。
 *   Using sort_union(...), Using union(...), Using intersect(...):这些函数说明如何为index_merge联接类型合并索引扫描。
*    Using index for group-by:类似于访问表的Using index方式,Using index for group-by表示MySQL发现了一个索引,可以用来查 询GROUP BY或DISTINCT查询的所有列,而不要额外搜索硬盘访问实际的表。
