---
layout: post
title: "mysql进入安全模式修改密码。"
date: 2015-11-05 10:27:00.000000000 +09:00
tags: ""
---
### 步骤:
#### 首先sudo /etc/init.d/mysql stop 停止服务器
#### 执行 sudo mysqld_safe --skip-grant-tables &  确定服务器已停止，
[1] 5239

```
melina@melina-laptop:~$ 110320 21:13:14 mysqld_safe Logging to syslog.
110320 21:13:14 mysqld_safe Starting mysqld daemon with databases from /var/lib/mysql
```
#### 看到上面的结果后，输入mysql -u melina  出现下面结果：
```
Welcome to the MySQL monitor.  Commands end with ; or \\g.
Your MySQL connection id is 1
Server version: 5.1.37-1ubuntu5.5 (Ubuntu)
Type ''help;'' or ''\\h'' for help. Type ''\\c'' to clear the current input statement.
mysql>
```
#### 输入use mysql
```
    mysql> use mysql
    Reading table information for completion of table and column names
    You can turn off this feature to get a quicker startup with -A
    Database changed
```
#### 执行更新密码
```
    mysql>update  user set password=password("551111") where user="root";
    Query OK, 3 rows affected (0.08 sec)
    Rows matched: 3  Changed: 3  Warnings: 0
    
    mysql> commit;
    Query OK, 0 rows affected (0.00 sec)
    mysql> exit;
    Bye
    melina@melina-laptop:~$ mysql -u root
    Welcome to the MySQL monitor.  Commands end with ; or \\g.
    Your MySQL connection id is 3
    Server version: 5.1.37-1ubuntu5.5 (Ubuntu)
    
    Type ''help;'' or ''\\h'' for help. Type ''\\c'' to clear the current input statement.
    
    mysql>
```


