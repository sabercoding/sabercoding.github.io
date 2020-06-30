---
layout: article
title: "nginx 让users有权限启动"
date: 2015-11-18 09:17:00.000000000 +09:00
---

普通用户在restart和reload nginx时，会报错：the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /opt/nginx/conf/nginx.conf:1

我又不能给开发人员root权限，没办法，只好这么做。

原因是:默认情况下linux的1024以下端口是只有root用户才有权限占用
方法一：
所有用户都可以运行（因为是755权限，文件所有者：root，组所有者：root）
chown root:root nginx
chmod 755 nginx
chmod u+s nginx

方法二：
仅 root 用户和 reistlin 用户可以运行（因为是750权限，文件所有者：root，组所有者：www）
chown root:www nginx
chmod 750 nginx
chmod u+s nginx
