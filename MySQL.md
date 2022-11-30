# MySQL

1. create database <database_name>	创建数据库

   1. create database <database_name> IF NOT EXISTS

2. drop database <database_name>

3. 数据类型 <https://www.runoob.com/mysql/mysql-data-types.html>

   数值类型	ints	float	double	decimal

   日期和时间	date	time	year	datetime	timestamp

   字符串	varchar	char

   日志	blog

   文本	text

   **Attention：char(n) 和 varchar(n) 中括号中n代表字符的个数，并不代表字节数**

4. create table <table_name>

```mysql
CREATE TABLE `ums_user`
(
    `id`              bigint(20)  NOT NULL AUTO_INCREMENT,
    `name`            varchar(36) NULL,
    `age`             int(11)     NULL,
    `created_at`      datetime    NULL,
    `updated_at`      datetime    NULL,
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

不想字段为 **NULL** 可以设置为 **NOT NULL**，当操作数据库输入字段的数据为 **NULL** 时，会报错

**AUTO_INCREMENT** 定义为自增的属性，设置主键自增

**PRIMARY KEY** 定义主键

**ENGINE**

5. drop table <table_name>
6. insert into <table_name> (field1, field2, field3) values (77, 'user77', 7)

```mysql
INSERT INTO 'ums_user' ('id','name','age')
VALUES (777,'user777',7);
```

7. select * from <table_name>
8. select * from <table_name> where ...
9. update <table_name> 

``` mysql
update <table_name> 

set <field_name> = ? 

where id = ? 
```

10. delete from <table_name> where id = ?
11. select * from <table_name> where name like 'kobe%';
12. UNION

``` mysql
select field1,field2,field3

from <table_name1>

where conditions

UNION [ ALL | DISTINCT ]

select field1,field2,field3

from <table_name2>

where conditions
```

**UNION ALL**	返回所有的结果集，包含重复

**UNION DISTINCT**	删除结果中重复的数据，默认情况下 UNION 操作符删除重复的数据

13. order by [ ASC | DESC ]	升序|降序

``` mysql
select * 

from <table_name> 

where conditions 

order by date ASC
```

14. group by	分组

``` mysql
select * 
from <table_name> 
where conditions 
group by name
```

15. JOIN	连接

``` mysql
select * 
from <table_name1> as t1 left join <table_name2> as t2 
on t1.name = t2.name
```

![image-20221111141423084](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221111141423084.png)

![image-20221111141650058](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221111141650058.png)

![image-20221111141717290](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221111141717290.png)

![image-20221111142340798](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221111142340798.png)

**全外连接**	 left join UNION [DISTINCT] right join

​					cross join

16. self join

``` mysql
select * 
from <table_name1> as t1 left join <table_name1> as t2
where t1.id = t2.id
```

17. NULL 值

**IS NULL**

**IS NOT NULL**

数据表查询时，判断字段是否为空

``` mysql
select * 
from <table_name> 
where count IS NOT NULL
```

18. 正则表达式	REGEXP

    ^	开始

    $	结束

    .	 匹配单个字符

    [...] 匹配包含的任意一个字符

    p1|p2|p3	匹配 p1 或 p2 或 p3

    \*	匹配前面的表达式 0 次或者多次

![image-20221111171109481](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221111171109481.png)

``` mysql
select * 
from <table_name> 
where name REGEXP '^st'
```

19. 事务 Transaction
    1. Atomicity	事务在执行过程中发生错误，会回滚 Rollback 到事务执行之前的状态
    2. Consistency
    3. Isolation     数据库允许多个事务并发执行，对数据库进行读写和修改。隔离性可以防止多个事务并发执行时，由于交叉执行导致的数据不一致
       1. Read uncommitted
       2. Read committed
       3. Repeatable read
       4. Serializable
    4. Durability

![image-20221111173337604](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221111173337604.png)

20. ALTER <table_name>

``` mysql
alter table <table_name> drop field;
alter table <table_name> add field INT;
alter table <table_name> modify field CHAR(10);
alter table <table_name> change field1 field2 BIGINT;
```

![image-20221112180019201](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221112180019201.png)

``` mysql
alter table <table_name> RENAME TO <new_table_name>
```

21. 索引 Index

索引提高 MySQL 的检索效率

类型：

	1. 主键索引    PRIMARY KEY
	1. 唯一索引    UNIQUE
	1. 普通索引    NORMAL
	1. 全文索引    FULL TEXT

``` mysql
//创建索引
CREATE INDEX <index_name> ON <table_name> (column_name)
//修改表结构（添加索引）
ALTER TABLE <table_name> ADD INDEX <index_name> (column_name)
//创建表时指定
create table <table_name> (INDEX [index_name])
//删除索引
DROP INDEX <index_name> ON <table_name>
```

```mysql
ALTER TABLE tbl_name ADD PRIMARY KEY (column_list)
	: 该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。
ALTER TABLE tbl_name ADD UNIQUE index_name (column_list)
	: 这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）。
ALTER TABLE tbl_name ADD INDEX index_name (column_list)
	: 添加普通索引，索引值可出现多次。
ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list)
	:该语句指定了索引为 FULLTEXT ，用于全文索引。
```

22. 运算符

![image-20221114093618396](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221114093618396.png)

![image-20221114093700164](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221114093700164.png)

![image-20221114093803140](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221114093803140.png)

![image-20221114093933923](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221114093933923.png)

23. 标准 sql

``` mysql
SELECT *,<column_name>
FROM <table_name>
WHERE conditions
GROUP BY <colomn_name>
HAVING condition
GROUP BY <column_name>
LIMIT 1,5
```

Attention：WHERE 过滤行，HAVING 过滤分组

##### 执行顺序：where>group by>having>order by



## 数据库系统原理

1. 事务 Transaction：ACID	Rollback

2. 并发一致性问题

3. 封锁：粒度（行级锁、表级锁、页级锁）& 类型-读写锁（互斥锁、共享锁）

4. 隔离级别（Read Uncommitted，Read Committed，Repeatable Read，Serializable）

5. 范式

6. 索引 Index（B树、B+树、哈希索引）

   ![image-20221114112218976](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221114112218976.png)

7. 引擎（Inn哦DB & MyISAM）
8. 拆分 Sharding（垂直 & 水平）





### Attention

1. from，where，group by，having，order by 执行顺序

   from>where>group by>having>order by

2. PostgreSQL 分区 partition by（比较和 MySQL group by）

``` mysql
SELECT name,type,price,max(price),
			rank() OVER (PARTITION BY type)
FROM <table_name>
```

![image-20221114142910590](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221114142910590.png)

![image-20221114143317607](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221114143317607.png)

partition by 和 group by 对比：

* group by 是分组函数

* partition by 应用在关键字（from，where，group by，having，order by）之后

  实际在执行完 select 之后，在所得结果集上进行 partition

##### partition by example:

* https://www.postgresql.org/docs/current/tutorial-window.html
* https://linuxhint.com/postgresql-partition-by-example/



### Reference:

​	<https://www.w3schools.com/mysql/>

​	<http://www.cyc2018.xyz/>
