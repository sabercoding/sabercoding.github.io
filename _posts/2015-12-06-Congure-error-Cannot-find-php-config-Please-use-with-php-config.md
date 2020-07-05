---
layout: article
title: " configure: error: Cannot find php-config. Please use --with-php-config=PATH"
date: 2015-12-06 09:19:00.000000000 +09:00
---

 `configure: error: Cannot find php-config. Please use --with-php-config=PATH`

 一般出现这个错误说明你执行 ./configure 时 --with-php-config 这个参数配置路径错误导致的。

查找:

    find / -name php-config

修改为：

    ./configure --with-php-config=/usr/local/php/bin/php-config

就可以解决问题,上面的 /usr/local/php/ 是你的 php 安装路径
