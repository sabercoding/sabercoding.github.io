---
layout: article
title: "解决PHP使用普通账号连接mongodb报错问题"
date: 2015-10-17 05:06:00.000000000 +09:00
---

>起先使用mongodb来开发，未加上账号密码验证功能. mongodb一直正常，准备放到线上之时加上账号密码功能，整个过程如下:
### mongo配置

1. 增加mongodb账号
2. 增加用户a_ttlsa_com对a_ttlsa_com这个库有读写权限,密码为ttlsa.com.passwd

```
# mongo
 MongoDB shell version: 2.4.5
 connecting to: test
 > use a_ttlsa_com
 switched to db a _ttlsa_com
 > db.addUser("a_ttlsa_com","ttlsa.com.passwd")
 {
 "user" : "a_ttlsa_com",
 "readOnly" : false,
 "pwd" : "48f7704260bf8c966fcdec40915f5c22",
 "_id" : ObjectId("52327181611f841827ccae9d")
 }
 >
```
### php配置
我这边是yii框架,连接方式大同小异

```
    "mongodb" => array(
        "class"            => "EMongoDB",
        "connectionString" => "mongodb://a_ttlsa_com:ttlsa.com.passwd@192.168.50.101",
        "dbName"           => "a_ttlsa_com",
        "fsyncFlag"        => true,
        "safeFlag"         => true,
        "useCursor"        => false
    ),
```

打开页面,出现如下报错:
```
    EMongoDB failed to open connection: Failed to connect to: 192.168.50.101:27017:Authentication failed on database "admin" with username "a_ttlsa_com": auth fails
```

用户密码密码都是正确，为什么会去连接admin数据库，是不是有点莫名其妙，其实这边犯了一个错误。mongodb的连接字符串后面要加上库名，否则他默认会去连接admin库，所以配置文件后面的dbName实际上和连接完全没有关系.

配置文件修改为如下:
```
    "mongodb" => array(
        "class"            => "EMongoDB",
        "connectionString" => "mongodb://a_ttlsa_com:ttlsa.com.passwd@192.168.50.101/a_ttlsa_com",
        "dbName"           => "a_ttlsa_com",
        "fsyncFlag"        => true,
        "safeFlag"         => true,
        "useCursor"        => false
    ),
```
恢复正常.
