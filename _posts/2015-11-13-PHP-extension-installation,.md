---
layout: article
title: "php扩展安装"
date: 2015-11-13 02:26:00.000000000 +09:00
tags: php
---

安装 mongo 扩展，先在php.ini里面添加

    extension=mongo.so

重启php-fpm报错!
![QQ截图20151113102354.png](https://shuibo.me/assets/images/201511/b6vpfJR8CAZGgENYNisdatkEXyAO4Ah_.png "QQ截图20151113102354.png")
缺少.so 直接把其它机器相关的的.so copy过来在重启一下即可！
