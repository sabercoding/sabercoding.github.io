---
layout: post
title: " rsync: read error: Connection reset by peer解决办法"
date: 2015-11-18 09:18:00.000000000 +09:00
---

    [marsaber@server1 ~]$ rsync --list-only marsaber@10.10.10.3::
    rsync: read error: Connection reset by peer (104)
    rsync error: error in rsync protocol data stream (code 12) at io.c(614) [receiver=2.6.8]

我在服务器上查看日志，看到有这么一行：

    rsync: unable to open configuration file "/etc/rsyncd.conf": No such file or directory

于是我：

    ln -s /etc/rsyncd/rsyncd.conf /etc/rsyncd.conf

 
解决！
