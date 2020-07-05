---
layout: article
title: "PHP数组合并去重统计"
date: 2015-10-21 02:51:00.000000000 +09:00
tags: php
---

```
    $a = array("1001","1002");
    $b = array("1002","1003","1004");
    $c = array("1003","1004","1005");
    $d = count(array_flip($a) + array_flip($b) + array_flip($c));
```
