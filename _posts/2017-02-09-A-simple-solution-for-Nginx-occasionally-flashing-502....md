---
layout: post
title: "关于Nginx偶尔闪现502的简单解决办法。。"
date: 2017-02-09 02:26:15.000000000 +09:00
---

1. 增加内存
2. 修改nginx配置
```
server_names_hash_bucket_size 256; 
client_header_buffer_size 256k; 
large_client_header_buffers 4 256k; 
#size limits 
client_max_body_size 50m; 
client_body_buffer_size 256k; 
client_header_timeout 3m; 
client_body_timeout 3m; 
send_timeout 3m; 

proxy_buffer_size  128k;
proxy_buffers   32 32k;
proxy_busy_buffers_size 128k;

fastcgi_buffer_size 128k;
fastcgi_buffers 4 256k;
fastcgi_busy_buffers_size 256k;
```
最重要的是去看nginx报错日志为啥会报502。。。。。。这次是header头过大