---
layout: article
title: "记一次nginx 403错误"
date: 2017-02-09 02:21:57.000000000 +09:00
---

```
server {
    charset utf-8;
    client_max_body_size 128M;
    listen       80;

    access_log  /var/log/nginx/xcx.log;
    error_log   /var/log/nginx/xcx_error.log;

    server_name xcx.com www.xcx.com
    root /home/code/xcx/;
    index    index.php;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }
    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \\.php$ {
        include fastcgi.conf;
        fastcgi_pass   127.0.0.1:9000;
    }

    location ~ /\\.(ht|svn|git) {
        deny all;
    }
}
```

```
2017/01/09 11:47:05 [error] 15600#0: *3811575 directory index of "/usr/share/nginx/html/" is forbidden, client: 116.90.80.160, server: xcx.com, request: "GET / HTTP/1.1", host: “xcx.com"
```

`server_name xcx.com www.xcx.com`
缺少;没有提示，所以后面的root并没有生效。。。。。。
