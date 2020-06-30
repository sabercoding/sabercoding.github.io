---
layout: article
title: "网站迁移工作"
date: 2016-04-02 16:05:00.000000000 +09:00
---

一直用着虚拟机，因为没啥访问量，对空间也没有什么要求，所以一直也就没有管它。最近像搞下https所以打算迁移到云服务器上面来。
首先是部署代码，配置好nginx后，使用ip+端口访问是成功的！
其次要去修改解析，修改域名的解析指定到我的云服务器～然后就发现了，它是指到了80端口了。那该怎么办？查了资料发现还是很好解决的，就用nginx做个简单的域名分发～

    location / {
     if ($host ~ ''www.saber91.com'') {
     proxy_pass http://127.0.0.1:9044;
     break;
     }
     if ($host ~ ''saber91.com'') {
     proxy_pass http://127.0.0.1:9044;
     break;
     }
    }

我就仅仅做了分发功能，具体的配置没有详细配置，之后有计划要去深入解读nginx。
输入域名，即可访问了。
但是问题来了。访问速度慢的要死。。。。。
找了个网站测试了下<a href="http://ce.cloud.360.cn/task">http://ce.cloud.360.cn/task</a>
全线飘红。。。。。。。。网站评分下，就80.因为没有缓存跟gzip。这台云主机是新开的，所以就没有配置好。
开启gzip从两方面来，首先修改php.ini

    zlib.output_compression = On
    zlib.output_compression_level = 4
    ;zlib.output_handler

修改nginx.conf  在http{}里面添加


    gzip on;
     gzip_buffers 4 16k;
     #gzip_http_version 1.1;
     gzip_types text/plain application/x-javascript text/css application/xml text/javascript application/javascript;
     gzip_comp_level 6;
     gzip_vary on;
     gzip_disable "MSIE";
     gzip_proxied any;

网站评分85，缓存有空搞搞，打开速度1.34s。嗯，可以了。

参考：[gzip][1]


  [1]: https://www.fancycoding.com/enable-css-js-compress-in-nginx/
