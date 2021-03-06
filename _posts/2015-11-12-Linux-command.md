---
layout: article
title: "linux命令"
date: 2015-11-12 10:34:00.000000000 +09:00
tags: linux
---

今天发现服务器上Tomcat 8080端口起不来，老提示端口已经被占用。

使用命令：
```
ps -aux | grep tomcat
```

发现并没有8080端口的Tomcat进程。

使用命令：`netstat –apn`

方法二：直接使用 `netstat -anp | grep portno`
即：`netstat –apn | grep 8080`

查看所有的进程和端口使用情况。发现下面的进程列表，其中最后一栏是PID/Program name

2. ps 命令用于查看当前正在运行的进程。
grep 是搜索
例如：`ps -ef | grep java`
表示查看所有进程里 CMD 是 java 的进程信息
`ps -aux | grep java`
-aux 显示所有状态
ps

3. kill 命令用于终止进程
例如： kill -9 [PID]

scp -r /home/space/music/ root@www.cumt.edu.cn:/home/root/others/

chmod命令详解
```
　　使用权限：所有使用者
　　使用方式：chmod [-cfvR] [--help] [--version] mode file...
　　说明：
　　Linux/Unix 的档案存取权限分为三级 : 档案拥有者、群组、其他。利用 chmod 可以藉以控制档案如何被他人所存取。
　　mode ：权限设定字串，格式如下 ：[ugoa...][[+-=][rwxX]...][,...]，其中u 表示该档案的拥有者，g 表示与该档案的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示这三者皆是。

+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
r 表示可读取，w 表示可写入，x 表示可执行，X 表示只有当该档案是个子目录或者该档案已经被设定过为可执行。

-c : 若该档案权限确实已经更改，才显示其更改动作
-f : 若该档案权限无法被更改也不要显示错误讯息
-v : 显示权限变更的详细资料
-R : 对目前目录下的所有档案与子目录进行相同的权限变更(即以递回的方式逐个变更)
--help : 显示辅助说明
--version : 显示版本
```
　　范例：

　　将档案 file1.txt 设为所有人皆可读取
chmod ugo+r file1.txt

　　将档案 file1.txt 设为所有人皆可读取

chmod a+r file1.txt

　　将档案 file1.txt 与 file2.txt 设为该档案拥有者，与其所属同一个群体者可写入，但其他以外的人则不可写入

chmod ug+w,o-w file1.txt file2.txt

　　将 ex1.py 设定为只有该档案拥有者可以执行

chmod u+x ex1.py

　　将目前目录下的所有档案与子目录皆设为任何人可读取

chmod -R a+r *

　　此外chmod也可以用数字来表示权限如 chmod 777 file

　　语法为：chmod abc file

　　其中a,b,c各为一个数字，分别表示User、Group、及Other的权限。

　　r=4，w=2，x=1

　　若要rwx属性则4+2+1=7；

　　若要rw-属性则4+2=6；

　　若要r-x属性则4+1=7。

范例：
```
　　chmod a=rwx file 和 chmod 777 file 效果相同
　　chmod ug=rwx,o=x file 和 chmod 771 file 效果相同
　　#若用chmod 4755 filename可使此程式具有root的权限
```
chown命令详解

使用权限：root

使用方式：chown [-cfhvR] [--help] [--version] user[:group] file...

说明：
　
Linux/Unix 是多人多工作业系统，所有的档案皆有拥有者。利用 chown 可以将档案的拥有者加以改变。一般来说，这个指令只有是由系统管理者(root)所使用，一般使用者没有权限可以改变别人的档案拥有者，也没有权限可以自己的档案拥有者改设为别人。只有系统管理者(root)才有这样的权限。

```
user : 新的档案拥有者的使用者
IDgroup : 新的档案拥有者的使用者群体(group)
-c : 若该档案拥有者确实已经更改，才显示其更改动作
-f : 若该档案拥有者无法被更改也不要显示错误讯息
-h : 只对于连结(link)进行变更，而非该 link 真正指向的档案
-v : 显示拥有者变更的详细资料
-R : 对目前目录下的所有档案与子目录进行相同的拥有者变更(即以递回的方式逐个变更)
--help : 显示辅助说明
--version : 显示版本
```

范例：

 将档案 file1.txt 的拥有者设为 users 群体的使用者 jessie


`chown jessie:users file1.txt`

将目前目录下的所有档案与子目录的拥有者皆设为 users 群体的使用者 lamport

`chown -R lamport:users *`

* -rw------- (600) -- 只有属主有读写权限。
* -rw-r--r-- (644) -- 只有属主有读写权限；而属组用户和其他用户只有读权限。
* -rwx------ (700) -- 只有属主有读、写、执行权限。
* -rwxr-xr-x (755) -- 属主有读、写、执行权限；而属组用户和其他用户只有读、执行权限。
* -rwx--x--x (711) -- 属主有读、写、执行权限；而属组用户和其他用户只有执行权限。
* -rw-rw-rw- (666) -- 所有用户都有文件读、写权限。这种做法不可取。
* -rwxrwxrwx (777) -- 所有用户都有读、写、执行权限。更不可取的做法。

以下是对目录的两个普通设定：

* drwx------ (700) - 只有属主可在目录中读、写。
* drwxr-xr-x (755) - 所有用户可读该目录，但只有属主才能改变目录中的内容

suid的代表数字是4，比如4755的结果是-rwsr-xr-x

sgid的代表数字是2，比如6755的结果是-rwsr-sr-x

sticky位代表数字是1，比如7755的结果是-rwsr-sr-t
