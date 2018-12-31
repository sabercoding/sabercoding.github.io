---
layout: post
title: "mysql开启远程连接"
date: 2015-11-14 09:35:00.000000000 +09:00
---

    mysql> use mysql； 
    mysql> update user set host=''%'' where user = ''root''; 
    mysql> flush privileges; 