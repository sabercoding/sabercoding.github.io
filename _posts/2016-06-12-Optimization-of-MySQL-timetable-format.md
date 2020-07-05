---
layout: article
title: "mysql建表时间格式优化"
date: 2016-06-12 13:41:00.000000000 +09:00
tags: mysql
---

创建时间，更新时间设置成下面这样就可以自动设置时间跟更新时间。

``` sql
`ctime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
`mtime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
```

以前对于时间都是存时间戳或者datetime的格式，新get到技能喔~。

DATETIME和TIMESTAMP都是精确到秒，优先选择TIMESTAMP，因为TIMESTAMP只有4个字节，而DATETIME8个字节。同时TIMESTAMP具有自动赋值以及自动更新的特性。

如何使用TIMESTAMP的自动赋值属性？
* 将当前时间作为ts的默认值：ts TIMESTAMP DEFAULT CURRENT_TIMESTAMP。
* 当行更新时，更新ts的值：ts TIMESTAMP DEFAULT 0 ON UPDATE CURRENT_TIMESTAMP。
* 可以将1和2结合起来：ts TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP。

但是TIMESTAMP值不能早于1970或晚于2037。
