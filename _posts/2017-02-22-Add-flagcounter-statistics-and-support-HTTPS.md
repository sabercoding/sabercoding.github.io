---
layout: post
title: "添加flagcounter统计并支持https"
date: 2017-02-22 09:27:00.000000000 +09:00
---


[flagcounter](https://flagcounter.com)是个统计访问人数及地区的工具，能实时展示出来。
##安装步骤
1. 打开首页。
2. 挑选样式，并点击生成。
3. 支持邮箱注册，也可以跳过，获得安装代码。

```
<a href="http://s11.flagcounter.com/more/Lnfv”>
<img src="http://s11.flagcounter.com/count2/Lnfv/bg_FFFFFF/txt_000000/border_CCCCCC/columns_2/maxflags_10/viewers_0/labels_0/pageviews_0/flags_0/percent_0/" alt="Flag Counter" border="0”>
</a>
```
其中的链接是网站更详细的用户分类统计，图片是实时生成的用户统计图。

##但是

flag counter的安装代码中含有http的url。它的图片是支持https，就是跳转链接不能支持。

所以有两个选择：
1. 直接干掉链接。
2. 使用nginx重定向到那个网址。

### 步骤：

1. 获取你要跳转的链接。
2. 修改你的nginx配置文件
```
 location /flagcounter {
     return 301 http://s11.flagcounter.com/more/Lnfv;
 }
```
3. 修改跳转链接。

完美支持https，又可以看到小绿锁啦。
