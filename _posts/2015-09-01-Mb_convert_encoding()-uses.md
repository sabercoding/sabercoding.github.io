---
layout: article
title: "mb_convert_encoding()使用"
date: 2015-09-01 07:51:00.000000000 +09:00
---

![QQ截图20150901153912.png](https://shuibo.me/assets/images/201509/ayK7apJ_B883loLHcSDv0Av5d9OHUgSM.png "QQ截图20150901153912.png")

今天遇到一个奇葩问题。。。。。。wedgit传值编码错误，我参照着做一个wedgit。view传值给wedgit的时候就出现编码问题，想要显示图表就必须要utf8，可是我参照弄得这个就偏偏是gbk。。      + +

找问题就花了一个多小时，然后用了一个函数转换了成utf8。。。。。。可是我还是没发现这是为啥。
