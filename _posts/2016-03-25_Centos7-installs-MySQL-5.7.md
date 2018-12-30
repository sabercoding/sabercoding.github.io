---
layout: post
title: "centos7 安装mysql5.7"
date: 2016-03-25 11:04:00.000000000 +09:00
tags: ""
---
    # mysql -V
     mysql Ver 14.14 Distrib 5.7.11, for Linux (x86_64) using EditLine wrapper

**Step1: 检测系统是否自带安装mysql**

    #yum list installed | grep mysql

**Step2: 删除系统自带的mysql及其依赖**

    # yum -y remove mysql-libs

**Step3: 给CentOS添加rpm源，并且选择较新的源**

    # wget dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm
    # yum localinstall mysql-community-release-el6-5.noarch.rpm
    # yum repolist all | grep mysql
    # yum-config-manager --disable mysql55-community
    # yum-config-manager --disable mysql56-community
    # yum-config-manager --enable mysql57-community-dmr
    # yum repolist enabled | grep mysql
**Step4:安装mysql 服务器**

    #yum install mysql-community-server

**Step5: 启动mysql**

    #service mysqld start

**Step6: 查看mysql是否自启动,并且设置开启自启动**

    # chkconfig --list | grep mysqld 
    # chkconfig mysqld on

**Step7: mysql root密码查找**
密码保存在临时文件里：/var/log/mysqld.log命令：

    # grep ''temporary password'' /var/log/mysqld.log
     2016-03-25T08:28:24.920752Z 1 [Note] A temporary password is generated for root@localhost: &lt;5%vn9JFxmUz

**Step8:重设root 创建新用户远程**
命令：

    # mysql -uroot -p
     Enter password:
     mysql: use mysql; //不重新设置密码会报错。
     ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
     mysql: SET PASSWORD = PASSWORD(''Your-password''); //设置密码
     Query OK, 0 rows affected, 1 warning (0.00 sec)
     mysql: grant all privileges on *.* to username@localhost identified by "password" ; //创建新用户
     Query OK, 0 rows affected, 1 warning (0.00 sec)
     mysql: grant all privileges on *.* to username@"%" identified by "password" ; //设置远程权限
     Query OK, 0 rows affected, 1 warning (0.00 sec)

参考相关文档地址: 
<a href="https://segmentfault.com/a/1190000003049498" target="_blank">CentOS 6.5/6.6 安装mysql 5.7 最完整版教程</a>






