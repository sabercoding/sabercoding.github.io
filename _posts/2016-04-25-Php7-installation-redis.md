---
layout: post
title: "php7安装redis"
date: 2016-04-25 08:57:31.000000000 +09:00
---

①、下载phpredis：

    wget https://github.com/edtechd/phpredis/archive/php7.zip

②、解压并编译安装phpredis：

    unzip php7redis.zip
    cd phpredis-php7
    /usr/local/php7/bin/phpize
    ./configure --with-php-config=/usr/local/php7/bin/php-config
    make && make install
    make test

③、修改php.ini添加php的redis扩展：

    vi /etc/php.ini

找到最后一行添加extension=redis.so；
重启nginx 即可生效。 
