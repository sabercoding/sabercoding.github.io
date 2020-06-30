---
layout: article
title: "mongo 数据类型错误查询"
date: 2015-08-29 08:51:00.000000000 +09:00
---

今天弄简单的mongo条件查询，一定时间内platform的显示。。。。。。条件查询只有时间是对的。platform死活就是不行= =
就在刚才发现了问题，数据库里面的是string，查询条件里面是int。

。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。

o(︶︿︶)o 唉。。。。。php类型啊。。。。
![1.png](https://o8ekw8sx0.qnssl.com/upload/201508/VIqJiQ5eqq0lfMhtTDViutNv4HT9mFC0.png "1.png")![2.png](https://o8ekw8sx0.qnssl.com/upload/201508/h6PdnZJDi3_vEEYRba3MsfGWzSiuwNs3.png "2.png")![3.png](https://o8ekw8sx0.qnssl.com/upload/201508/PARjPlUmUqwbp3sz3A4V3kxhtSjSsbBK.png "3.png")
