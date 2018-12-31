---
layout: post
title: "logstash-elasticsearch-kibana时间问题"
date: 2017-11-09 02:33:05.000000000 +09:00
---

> 基于elk搭建一套日志系统，在搭建的同时发现一个关于时间的问题。

## 发现问题

### 0x01

设置索引mapping，写入日志，filebeat收集传送到logstash上。使用kibana查看数据，此时会发现，过去的十五分钟无数据。这是为啥？明明看到logstash写进去了。去看下文档数，确实写进去了。

### 0x02

十五分钟没有，往前半小时也是没有，一整天都没有（现在时间16点多）。将时间范围调整到整个月的时候，神奇的事发生了。。。在往后的八小时出现了数据了。这就是timezone的问题了。

### 0x03

使用api调用的时候同样也是时间范围需要添加八小时才能读取出来数据，确定是es的问题。es系统的时区是utc的导致写日期数据的时候是写死应用服务器上的时间，并不是utc。使用utc查询的时候就会导致时间相差八小时。

## 解决问题

因为我们并不是在utc时区，应用服务器需设置成当前时区的时间，es又是第三方提供的，所以我们就只能在kibana上做操作了。

修改kibana的时区即可。

1. kibana的Management菜单内，Advanced Settings选项。这里面有很多关于kibana的设置，dateFormat:tz就是时区的设置了。
2. 设置成utc即可。

由于kibana的时间格式看着比较变扭，也可以修改Advanced Settings的dateFormat成YYYY-MM-DD HH:mm:ss.SSS，这样就看着顺眼多了。