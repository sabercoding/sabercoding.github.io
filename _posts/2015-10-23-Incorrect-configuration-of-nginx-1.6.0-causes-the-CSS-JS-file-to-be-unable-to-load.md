---
layout: article
title: "nginx1.6.0配置不对导致css js文件加载不了"
date: 2015-10-23 07:13:00.000000000 +09:00
tags: nginx
---

![QQ截图20151023150912.png](https://shuibo.me/assets/images/201510/ATGBXfCrF0dWXFb1Uzbac8MfGQDYwIBs.png "QQ截图20151023150912.png")

nginx1.6.0的nginx.conf配置不知道为啥没有添加这个文件进去。所以，加载不了成text/css application/javascript 变成了plain。

![QQ截图20151023151239.png](https://shuibo.me/assets/images/201510/LIPJUasnAMK2DIM-k4gPP3RE2e9oLGgi.png "QQ截图20151023151239.png")
把include加上去就可以啦~
