---
layout: article
title: "siege 工具使用"
date: 2017-02-09 02:31:31.000000000 +09:00
tags: siege
---

# 安装
Mac 下直接brew安装：

```
brew install siege
```

# 参数详解：

```
siege --help

SIEGE 3.0.6
Usage: siege [options]
       siege [options] URL
       siege -g URL
Options:
  -V, --version             VERSION, prints the version number.
  -h, --help                HELP, prints this section.
  -C, --config              CONFIGURATION, show the current config.
                            #在屏幕上打印显示出当前的配置,配置是包括在他的配置文件$HOME/.siegerc中,
                            #可以编辑里面的参数,这样每次siege 都会按照它运行.
  -v, --verbose             VERBOSE, prints notification to screen.
                            #运行时能看到详细的运行信息
  -q, --quiet               QUIET turns verbose off and suppresses output.
  -g, --get                 GET, pull down HTTP headers and display the
                            transaction. Great for application debugging.
  -c, --concurrent=NUM      CONCURRENT users, default is 10
                            #模拟有n个用户在同时访问,n不要设得太大,因为越大,siege 消耗本地机器的资源越多
  -i, --internet            INTERNET user simulation, hits URLs randomly.
                            #随机访问urls.txt中的url列表项,以此模拟真实的访问情况(随机性)
  -b, --benchmark           BENCHMARK: no delays between requests.
  -t, --time=NUMm           TIMED testing where "m" is modifier S, M, or H
                            ex: --time=1H, one hour test.
                            #持续运行siege ‘n’秒(如10S),分钟(10M),小时(10H)
  -r, --reps=NUM            REPS, number of times to run the test.
                            #重复运行测试n次,不能与 -t同时存在
  -f, --file=FILE           FILE, select a specific URLS FILE.
                            #指定用urls文件,默认为siege安装目录下的etc/urls.txt
                            #urls.txt文件：是很多行待测试URL的列表以换行符断开,格式为:
                            #[protocol://]host.domain.com[:port][path/to/file]
  -R, --rc=FILE             RC, specify an siegerc file
                            #指定用特定的siege配置文件来运行,默认的为$HOME/.siegerc
  -l, --log[=FILE]          LOG to FILE. If FILE is not specified, the
                            default is used: PREFIX/var/siege.log
                            #运行结束,将统计数据保存到日志文件siege.log中,可在.siegerc中自定义日志文件
  -m,; --mark="text"         MARK, mark the log file with a string.
  -d, --delay=NUM           Time DELAY, random delay before each requst
                            between 1 and NUM. (NOT COUNTED IN STATS)
                            #hit每个url之间的延迟,在0-n之间
  -H, --header="text"       Add a header to request (can be many)
  -A, --user-agent="text"   Sets User-Agent in request
  -T, --content-type="text" Sets Content-Type in request

Copyright (C) 2014 by Jeffrey Fulmer, et al.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS
FOR A PARTICULAR PURPOSE.
```

# 操作命令：
## post请求：

```
siege -c 200 -r 50 "http://user.com/api/user/query?time=1481613695.8274 POST uid_list=[\\"UstiurDyhSoz8\\"]&app_id=58" -b  //命令执行
siege -c100 -r100 -f siege.txt -b  //文件数据执行
```
### siege.txt内容：

```
http://user.com/api/credit/grant?time=1481613695.8274 POST app_id=58&uid=UstUJAj5PFseN&buss_id=xxxxxsdsdsf&=&money=2000000
http://user.com/api/credit/grant?time=1481613695.8274 POST app_id=58&uid=UstUJAjR61Ric&buss_id=xxxxxsdsdsf&=&money=2000000
...
```
## get请求

```
siege -c 200 -r 50 "http://user.com/api/user/query?time=1481613695.8274" -b  //命令执行
siege -c100 -r100 -f siege.txt -b  //文件数据执行
```
### siege.txt内容：

```
http://user.com/api/credit/grant?time=1481613695.8274
http://user.com/api/credit/grant?time=1481613695.8274
...
```

# 结果说明：
```
** SIEGE 2.72
** Preparing 300 concurrent users for battle.
The server is now under siege.. done.

Transactions:             30000 hits      #完成30000次处理
Availability:            100.00 %         #成功率
Elapsed time:             68.59 secs      #总共使用时间
Data transferred:        817.76 MB        #共数据传输 817.76 MB
Response time:             0.04 secs      #响应时间，显示网络连接的速度
Transaction rate:        437.38 trans/sec #平均每秒完成 437.38 次处理
Throughput:               11.92 MB/sec    #平均每秒传送数据
Concurrency:              17.53           #实际最高并发连接数
Successful transactions:  30000           #成功处理次数
Failed transactions:          0           #失败处理次数
Longest transaction:       3.12           #每次传输所花最长时间
Shortest transaction:      0.00           #每次传输所花最短时间
```


# 参考文档：
     [压力测试和评测工具Siege](http://xstarcd.github.io/wiki/shell/siege.htm)


