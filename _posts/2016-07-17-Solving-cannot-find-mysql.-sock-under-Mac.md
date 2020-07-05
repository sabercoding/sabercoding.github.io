---
layout: article
title: "Mac 下解决cannot find mysql.sock"
date: 2016-07-17 16:00:00.000000000 +09:00
---

之前数据库都是用的公司的虚拟机，但是没网的时候就不方便了。所以还是在自己的Mac下搭建一个MySQL。
使用的是下面命令：

    brew install mysql
然后安装完了。

    We"ve installed your MySQL database without a root password. To secure it run:
        mysql_secure_installation

    To connect run:
        mysql -uroot

    A "/etc/my.cnf" from another install may interfere with a Homebrew-built
    server starting up correctly.

    To have launchd start mysql now and restart at login:
      brew services start mysql
    Or, if you don"t want/need a background service you can just run:
      mysql.server start
看了前面的就开始操作了。。
执行：

    mysql_secure_installation
报错：

    ERROR 2002 (HY000): Can"t connect to local MySQL server through socket "/tmp/mysql.sock"
然后就开始谷歌了。。。。。。。
这个问题是普遍出现的，谷歌上面说了一个通用的方法。
创建my.cnf文件后执行：

    $ unset TMPDIR
    $ mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp
执行下面语句后，报错：

    mysql_install_db: [ERROR] unknown variable "tmpdir=/tmp"
瞬间无语了，估计是版本不适应，继续找答案。
最后的解决方法其实就在安装完提示信息里的最后。
执行：

    $ mysql.server start                                                                                                                                                   [23:24:47]
    Starting MySQL
    . SUCCESS!
然后再执行：

    $ mysql_secure_installation                                                                                                                                            [23:26:08]
配置完成后即可。
