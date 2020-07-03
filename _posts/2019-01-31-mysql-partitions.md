---
layout: article
title: "MySQL分区表"
date: 2019-01-31 18:39:00.000000000 +09:00
category: MySQL
---

#### 0x01 水平切分

随着数据量的增加，数据库单表不堪重负，访问速度降低。这时候我们可以采用水平切分跟垂直切分进行数据优化，优化性能。一般我们用的切分是水平切分，将数据拆分到多个地方，而字段不进行变化。

水平切分常用的的分库分表和分区表。

##### 分库分表

把一个很大的库（表）的数据分到几个库（表）中，每个库（表）的结构都相同，但他们可能分布在不同的mysql实例，甚至不同的物理机器上，以达到降低单库（表）数据量，提高访问性能的目的。

##### 分区表

所有数据还在一个表中，但物理存储根据一定的规则放在不同的文件中。这个是mysql支持的功能，业务rd代码无需改动。

优势：

* 分区表，分区键设计不太灵活，如果不走分区键，很容易出现全表锁

劣势：

* 分区表，分区键设计不太灵活，如果不走分区键，很容易出现全表锁。
* 一旦数据量并发量上来，如果在分区表实施关联，就是一个灾难自己分库分表，自己掌控业务场景与访问模式，可控。
* 运维坑

#### 0x02 分区表设计

##### 分区键

分区表需要一个分区键来水平拆分表，如何给分区键加索引？怎么加方式比较好？

**索引方式：**

性能依次降低

**1.主键分区**

主键分区即字段是主键同时也是分区字段，性能最好

**2. 部分主键+分区索引**

使用组合主键里面的部分字段作为分区字段，同时将分区字段建索引。注：不允许反过来创建！！

**3.分区索引**

没有主键，只有分区字段且分区字段建索引

**4.分区+分区字段没有索引**

只建了分区，但是分区字段没有建索引

正常场景下，分区键都不会唯一。所以，我们可以选择第二种方式来创建索引提高性能，部分主键就可以使用自增的id。

对于其它的索引也是有影响的：

**对于唯一性索引键，至少有一个字段不包含在分区表达式里**

分区键的创建是重中之重，参考分区键的业务场景选择不同的分区类型。

举个按日期分区的例子：

很多统计类的业务，查询条件里必备的就是时间范围搜索，因此，我们选择的分区键就是那个时间字段。

```sql
CREATE TABLE `list` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `ctime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`,`ctime`)
) ENGINE=InnoDB AUTO_INCREMENT=175496881 DEFAULT CHARSET=utf8 ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=8
 PARTITION BY RANGE (UNIX_TIMESTAMP(ctime))
(PARTITION p201501 VALUES LESS THAN (1420041600) ENGINE = InnoDB,
 PARTITION p201601 VALUES LESS THAN (1451577600) ENGINE = InnoDB,
 PARTITION p201701 VALUES LESS THAN (1483200000) ENGINE = InnoDB,
 PARTITION p201801 VALUES LESS THAN (1517414400) ENGINE = InnoDB,
 PARTITION p201901 VALUES LESS THAN (1548950400) ENGINE = InnoDB);
```

这样的分表也是极为常见的，但是如果业务上都是以天为维度的，类似的执行sql：

```sql
select DATE_FORMAT(ctime, "%Y-%m-%d"),count(1) as total from list where ctime >= '2018-12-16' group by DATE_FORMAT(ctime, "%Y-%m-%d");
```

这样包含函数的sql语句执行的效率并不会很高，因此，我们可以这样设计表格：

```sql
CREATE TABLE `list` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `ctime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `in_date` date NOT NULL DEFAULT '0000-00-00' COMMENT '冗余的入库时间',
  PRIMARY KEY (`eid`,`in_date`)
) ENGINE=InnoDB AUTO_INCREMENT=176000000 DEFAULT CHARSET=utf8 ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=8
  PARTITION BY RANGE (to_days(in_date))
  ( PARTITION p201501 VALUES LESS THAN (to_days('2015-01-01')) ENGINE = InnoDB,
  PARTITION p201601 VALUES LESS THAN (to_days('2016-01-01')) ENGINE = InnoDB,
  PARTITION p201701 VALUES LESS THAN (to_days('2017-01-01')) ENGINE = InnoDB,
  PARTITION p201801 VALUES LESS THAN (to_days('2018-01-01')) ENGINE = InnoDB,
  PARTITION p201901 VALUES LESS THAN (to_days('2019-01-01')) ENGINE = InnoDB);
```

sql修改为：

```sql
select in_date,count(1) as total from list where in_date >= '2018-12-16' group by in_date;
```

这种方式执行的效率比第一种来的快将近**3**倍!!!

##### 分区类型

1. RANGE：基于一个给定连续区间范围，把数据分配到不同的分区

```sql
create table list (
    id int unsigned not null AUTO_INCREMENT,
    age int,
    PRIMARY KEY(id, age)
)engine = innodb
    partition by range(age) (
    partition p1 values less than (10),
    partition p2 values less than (20),
    partition p3 values less than MAXVALUE
);
```

2. LIST：类似 RANGE 分区，区别在 LIST 分区是基于枚举出的值列表分区，RANGE 是局域给定的连续区间范围分区。

```sql
create table list (
    id int unsigned not null AUTO_INCREMENT,
    area int,
    PRIMARY KEY(id, area)
)engine innodb
    partition by list(area) (
    partition bj values in (1),
    partition ah values in (2),
    partition xb values in (4,5,6)
);
```

3. HASH：基于给定的分区个数，把数据分配到不同的分区。

```sql
create table list (
    id int unsigned not null AUTO_INCREMENT,
    birthday date,
    PRIMARY KEY(id, birthday)
)engine innodb
    partition by hash (month(birthday)) patitions 12;
```

4. KEY：按照某个字段取余

```sql
create table list (
    id int unsigned not null AUTO_INCREMENT,
    limit int,
    PRIMARY KEY(id, limit)
)engine = innodb
    partition by key (limit) partitions 5;
```

MySQL不禁止在分区键值上使用Null，分区键可能是一个字段或者一个用户定义的额表达式。一般情况下:

1. MySQL的分区把 Null当作零值，或者一个最小值进行处理；
2. range 分区中，Null值会被当作最小值来处理；
3. list分区中，Null值必须出现在枚举列表中，否则不被接受；
4. hash/key 分区中，Null值会被当作零值来处理。

#### 0x03 分区表的使用

导出：

```shell
$ mysql -h 127.0.0.1 -uroot -proot test --default-character-set=utf8 -e"select * from list order by id desc" > ./out
```

导入：

```sql
mysql> LOAD DATA LOCAL INFILE 'out' INTO TABLE list1;
```



#### 0x04 分区表的总结

1. 多做测试，找出最优的建表语句。
2. 注意分区维护。如果没有`maxvalue`，在快要超分区的时候，及时添加。



参考：

1. https://www.w3cschool.cn/architectroad/architectroad-mysql-partition-table.html
2. https://segmentfault.com/a/1190000009724052
