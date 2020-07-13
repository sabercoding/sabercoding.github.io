---
layout: article
title: "Logstash优化"
date: 2019-08-15 20:39:00.000000000 +09:00
tags: logstash elk
---

>系统大概一天生产一亿条，现在kafka已经堆积了上千万条数据。kafka设置：1个topic，10个partition，logstash配置：五台4核8g。之前没有进行优化，单纯累加机器。。。。

### 优化方向

1. logstash读取kafka日志数据
2. logstash解析日志数据
3. logstash存入es

##### kafka优化
1. 扩展Partition
2. 新增集群

##### logstash优化
1. logstash.conf优化
2. logstash.yml优化
3. jvm配置优化

##### es优化
不可操作略过。

### logstash优化
本文主要涉及logstash、

##### logstash.conf优化

logstash.conf配置文件，含有三部分：input,filter,output。

input部分可配置部分参数优化，如：consumer_threads。
filter过程会含有正则匹配，如果CPU过高，则可以考虑查看下这部分。另外，如果message不在match匹配中，会走logstash默认正则匹配，从而导致`Timeout executing grok `。

##### logstash.yml优化

logstash.yml配置内主要优化的参数：
pipeline.workers: 30 // pipeline 线程数，官方建议是等于CPU内核数 -w 30
pipeline.output.workers: 30 // 实际output 时的线程数
pipeline.batch.size: 20000 // 每次发送的事件数 -b 20000
pipeline.batch.delay: 3 // 发送延时 -u 3

线程数可以比实际内核数大一些，size根据内存大小来配置，最优的配置需要自己去尝试。

##### jvm配置优化

最后记得调整下jvm的配置`-Xms32g`，`-Xmx32g`。

### 实践

```
[2019-08-13T15:54:32,285][INFO ][logstash.pipeline        ] Starting pipeline {"id"=>"main", "pipeline.workers"=>30, "pipeline.batch.size"=>20000, "pipeline.batch.delay"=>3, "pipeline.max_inflight"=>600000}
[2019-08-13T15:54:32,285][WARN ][logstash.pipeline        ] CAUTION: Recommended inflight events max exceeded! Logstash will run with up to 600000 events in memory in your current configuration. If your message sizes are large this may cause instability with the default heap size. Please consider setting a non-standard heap size, changing the batch size (currently 20000), or changing the number of pipeline workers (currently 30)
[2019-08-13T15:54:32,295][INFO ][logstash.pipeline        ] Pipeline main started
[2019-08-13T15:54:32,549][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}
```

1. 启动logstash，查看下`Starting pipeline`是否跟你配置的一致。
2. `ps`查看进程内的`Java`命令，看配置是否跟自己设置的一致。
3. 观察机器情况，如果CPU还有富余，增加线程；内存还有富余，增加Size。
