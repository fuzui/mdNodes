---
title: MySql利用存储过程批量插入一千万数据（半小时）
date: 2019-07-01 16:49:43
tags: MySql
categories: MySql
---
<div align='center' ><font size='70'>MySql利用存储过程批量插入一千万数据</font></div>

示例：
向`user`表中插入一千万条数据，其中`user_id`11位手机号随机，可以重复；`age`为18-27之间；`count`随机金额，八位小数；`status`二百万数据为1，八百万数据为0。

```sql
-- 如果该名字存储过程已存在，则删除
DROP PROCEDURE IF EXISTS proc_initData1;
-- 创建
CREATE PROCEDURE proc_initData1 ( ) BEGIN
	DECLARE
		i INT DEFAULT 0;
	DECLARE
		uid VARCHAR ( 255 ) DEFAULT 0;
	DECLARE
		count DECIMAL(20,8) DEFAULT 0;
	DECLARE
		op INT DEFAULT 0;
	DECLARE
		age INT DEFAULT 0;
	START TRANSACTION;
	WHILE
			i <= 10000000 DO
		SELECT
			CONCAT( '1', CEILING( RAND( ) * 9000000000+1000000000 ) ) INTO uid;
		SELECT
			FLOOR( 18 + ( RAND( ) * 9 ) ) INTO age;
		SELECT
			ROUND( ( 10 + ( RAND( ) * 1001 ) ), 8 ) INTO count;
		IF
			i % 5 = 0 THEN
				INSERT INTO user ( user_id, age, count, status )
			VALUES
				( uid, age, count, 1);
			ELSE INSERT INTO ryw_intelligent_dog ( user_id, age, count, status)
			VALUES
				( uid, age, count, 0 );
		END IF;
		
		SET i = i + 1;
		
	END WHILE;
	COMMIT;
	
END

-- 调用
CALL proc_initData1 ( );


-- 删除表数据
truncate table user;
```

亲测需要半小时左右，前提删除索引，引擎innodb。
总结：在开始编写时并未使用事物手动提交，导致数据量插入非常慢。在批量插入大量数据时，要删除索引，并开启事物手动提交。