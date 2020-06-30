---
layout: article
title: "logrotate使用教程"
date: 2019-01-23 12:19:00.000000000 +09:00
category: Liunx
---
#### 0x01 前言

日志文件随着系统的运行，会越积越多。如何来解决这个问题呢？

手动删除->自动删除->压缩后，自动删除

1. 手动删除:磁盘报警就直接删掉历史的
2. 自动删除:写脚本定期执行删除
3. 压缩后，自动删除：定期删除的话，如果单周期内的日志文件很大也会导致磁盘过高。所以需要先进行压缩。

刚想上手写个脚本来实现的，就被安利了logrotate，一直捣鼓日志这块，竟然没有发现这个便捷的工具。之前傻傻的执行gzip。。。

#### 0x02 简介

**logrotate**可以简化对大量日志的管理。它可以对日志文件进行自动旋转，压缩，删除和移动。每个日志文件可以每天，每周，每月或当它变得太大时处理。

**logrotate**默认使用cron任务来执行。可以编写多个配置文件，根据相应配置来执行。

#### 0x03 配置

默认文件地址：

1. /etc/logrotate.conf
2. /etc/logrotate.d/

自定义的配置文件需要存储到/etc/logrotate.d/内。

配置文件：

```shell
/home/saber/code/xxx/logs/* {
    daily
    missingok
    compress
    nocreate
    rotate 10
    notifempty
    olddir archive
    sharedscripts
    postrotate     #在所有其它指令完成后，postrotate和endscript里面指定的命令将被执行。在这种情况下，rsyslogd进程将立即再次读取其配置并继续运行
        /bin/kill -USR1 $(cat /var/run/nginx.pid 2>/dev/null) 2>/dev/null || :
    endscript
}
```

日志文件格式：

1. /home/saber/code/xxx/logs/info.log
2. /home/saber/code/xxx/logs/*
3. /data/nginx/logs/\*/app/*



常用参数：

* daily：按天处理
* weekly：按周处理
* monthly：按月处理
* nocompress：不需要压缩时，用这个参数
* copytruncate：用于还在打开中的日志文件，把当前日志备份并截断
* nocopytruncate：备份日志文件但是不截断
* create mode owner group：转储文件，使用指定的文件模式创建新的日志文件
* nocreate：不建立新的日志文件
* delaycompress 和 compress 一起使用时，转储的日志文件到下一次转储时才压缩
* nodelaycompress 覆盖 delaycompress 选项，转储同时压缩。
* size size 当日志文件到达指定的大小时才转储，Size 可以指定 bytes (缺省)以及KB (sizek)或者MB (sizem).
* dateext：定义日志文件后缀是日期格式,也就是切割后文件是:xxx.log-20160402.gz这样的格式。如果该参数被注释掉,切割出来是按数字递增,即前面说的 xxx.log-1这种格式
* delaycompress：总是与compress选项一起用，delaycompress选项指示logrotate不要将最近的归档压缩，压缩将在下一次轮循周期进行。这在你或任何软件仍然需要读取最新归档时很有用
* create 640 nginx adm：以指定的权限和用书属性，创建全新的日志文件，同时logrotate也会重命名原始日志文件。
* rotate count 指定日志文件删除之前转储的次数，0 指没有备份，5 指保留5 个备份

注意：

​	linux系统中，内核是根据文件描述符来找文件的，所以logrotate执行后需要重启相应程序，重新打开日志文件，否则会继续往转存的文件写数据的。

```shell
kill -USR1 "ps axu | grep "nginx: master process" | grep -v grep | awk '{print $2}' "
```

##### 压缩

logrotate默认使用的压缩是gzip，参考下图结果，如果你要是想更小的话，可以选择xz的压缩。

| file type            | .jpg | .mp3 | .mp4 | .odt | .png | .txt |
| -------------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| number of files      | 2163 | 45   | 279  | 2990 | 2072 | 4397 |
| space on disk        | 98M  | 99M  | 99M  | 98M  | 98M  | 98M  |
| tar                  | 94M  | 99M  | 98M  | 93M  | 92M  | 89M  |
| zip (no compression) | 92M  | 99M  | 98M  | 91M  | 91M  | 86M  |
| zip (deflate)        | 87M  | 98M  | 93M  | 85M  | 77M  | 28M  |
| tar + gzip           | 86M  | 98M  | 93M  | 82M  | 77M  | 27M  |
| tar + bz2            | 87M  | 98M  | 93M  | 42M  | 71M  | 22M  |
| tar + xz             | 70M  | 98M  | 22M  | 348K | 51M  | 19M  |

来源：https://itsfoss.com/tar-vs-zip-vs-gz/

```
/home/saber/code/xxx/logs/* {
    size 40G
    dateext
    dateformat -%Y%m%d-%s
    rotate 15
    compress
    compresscmd /usr/local/bin/xz
    uncompresscmd /usr/local/bin/unxz
    compressoptions "--threads=0"
    compressext .xz
    missingok
    notifempty
    sharedscripts
}
```

##### 场景

配置文件要根据现有日志输出格式和后续日志需要如何处理来配置。

1. 单文件输出
2. 现有日志已经按日期输出

单文件输出的话，就比较好处理，直接切割日志数据配置日期后缀，配上rotate就可以只留下配置的天数。

现有文件按日期输出的话，就跟我的配置差不多了。转存，压缩即可，有一点不好就是没法自动删除。

#### 0x03 执行

logrotate在系统里已经是自动配置了的，会定期跑的。

```shell
$ sudo cat /etc/cron.daily/logrotate
#!/bin/sh

/usr/sbin/logrotate /etc/logrotate.conf
EXITVALUE=$?
if [ $EXITVALUE != 0 ]; then
    /usr/bin/logger -t logrotate "ALERT exited abnormally with [$EXITVALUE]"
fi
exit 0
```

除了用的cron自动执行的话，你还可以自定义执行。

```
59 23 * * * /usr/sbin/logrotate -f /etc/logrotate.d/syslog >/dev/null 2>&1
```

##### 操作（先在线下测试，再到生产环境执行）

1. 检查日志文件位置是否正确。
2. 检查转存的文件夹是否已创建，有权限写入。
3. 执行一遍`/usr/sbin/logrotate -f /etc/logrotate.d/syslog`



参考：

1. https://linux.die.net/man/8/logrotate



