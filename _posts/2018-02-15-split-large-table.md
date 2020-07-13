---
layout: article
title: "大表拆分"
date: 2018-02-15 20:39:00.000000000 +09:00
tags: mysql 分表
---

>现有一张表，含有113092128条数据。
```
+-----------+---------------+-----------+------------+
| data_size | max_data_size | data_free | index_size |
+-----------+---------------+-----------+------------+
| 6992.00MB | 0.00MB        | 5.00MB    | 10137.00MB |
+-----------+---------------+-----------+------------+
```

随着时间的发展，这个表就会越来越大，越来越不好维护。另外，这个表的唯一索引条件不再唯一，需做两件事。

1. 修改唯一索引为普通索引
2. 分表

第一条很好实现，删除索引，加索引。但是由于需要分表，那就不需要单独操作1了。新建的分表内，不要加唯一即可。

分表，有两种`物理分表`和`分区表`。`分区表`就不需要人工创建多张表，只需创建分区即可。但是`分区表`也有很多限制，分区字段必须在`主键`和`唯一索引`内。

由于框架没有支持分库分表，业务需要紧急修复唯一索引拒绝掉的数据。了解完分区表的坑，我们这次拆表可以使用`分区表`这种方式。

### 分表步骤

#### 创建分区表

```sql
create table wdb_interceptinfo
(
  `eid` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `host` varchar(128) NOT NULL DEFAULT '' COMMENT '域名',
  `area` varchar(128) NOT NULL COMMENT '地区',
  `ctime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`eid`, `ctime`)
)ENGINE=INNODB AUTO_INCREMENT=150000000 DEFAULT CHARSET=utf8
  PARTITION BY RANGE (UNIX_TIMESTAMP(`ctime`))
  (
  PARTITION p2015 VALUES LESS THAN (UNIX_TIMESTAMP('2015-01-01 00:00:00')),
  PARTITION p2016 VALUES LESS THAN (UNIX_TIMESTAMP('2016-01-01 00:00:00')),
  PARTITION p2017 VALUES LESS THAN (UNIX_TIMESTAMP('2017-01-01 00:00:00')),
  PARTITION p2018 VALUES LESS THAN (UNIX_TIMESTAMP('2018-01-01 00:00:00')),
  PARTITION p2019 VALUES LESS THAN (UNIX_TIMESTAMP('2019-01-01 00:00:00')),
  PARTITION p2020 VALUES LESS THAN (UNIX_TIMESTAMP('2020-01-01 00:00:00')),
  PARTITION p2021 VALUES LESS THAN (UNIX_TIMESTAMP('2021-01-01 00:00:00')),
  PARTITION p2022 VALUES LESS THAN (UNIX_TIMESTAMP('2022-01-01 00:00:00')),
  PARTITION p2023 VALUES LESS THAN (UNIX_TIMESTAMP('2023-01-01 00:00:00')),
  PARTITION p2024 VALUES LESS THAN (UNIX_TIMESTAMP('2024-01-01 00:00:00')),
  PARTITION p2025 VALUES LESS THAN (UNIX_TIMESTAMP('2025-01-01 00:00:00')),
  PARTITION p2026 VALUES LESS THAN (MAXVALUE)
  );
```

分区判断条件`UNIX_TIMESTAMP('2015-01-01 00:00:00')`是可以直接计算出来时间戳的，不过也没关系，这只是在建表时它会算一次。

#### 导入数据

线下直接是这么操作的。

```sql
INSERT INTO wdb_interceptinfo SELECT * FROM wdb_interceptinfos where eid > 0 LIMIT 10000000;
```

原本线上也想来一千万一千万的插入，想想问下dba。dba回了一句：“这你会搞死我们的。。。”，乖乖写脚本五千五千导入。。。。。

#### 创建索引

第一步建表时，并没加上索引，为的是减少插入耗时。

```sql
ALTER TABLE wdb_interceptinfo ADD INDEX idx_bankid_host(bankid, host);
```

#### 修改业务代码上线

### 可以优化点

由于时间原因，没有深入理解。如果数据量大的话，可以在做导入的过程中，可以先关掉log，等加完数据在上log。
