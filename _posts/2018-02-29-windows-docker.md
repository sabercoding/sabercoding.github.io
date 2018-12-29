---
layout: post
title: "跟我在Windows下玩docker吧!"
date: 2018-02-29 10:38:00.000000000 +09:00
tags: 系统
---

> 迫于公司电脑是个台式机，而且网上的云也挺贵的，大一点的配置就得好多钱。所以就在windows下先搞定docker，再去云上实践。嘿嘿嘿！

## 安装
> 需要个执行命令行的工具，需先安装gitbash

### 步骤
1. 访问安装[文件地址](https://github.com/boot2docker/windows-installer/releases);
2. 下载并按照步骤进行安装。

### 配置

#### 配置账号密码
1. 使用`boot2docker ssh`登录到docker服务器
2. 使用`sudo su - root`来切换到root下
3. 使用`passwd`设置root密码

#### 配置加速器
> 因为官网访问相对来说比较慢，可以选择国内的镜像站点。

1. 加速器有多个可选，例如[阿里云](https://cr.console.aliyun.com/?spm=5176.100239.blogcont29941.12.pf3kXv#/accelerator)、[网易蜂巢](https://c.163.com/wiki/index.php?title=DockerHub%E9%95%9C%E5%83%8F%E5%8A%A0%E9%80%9F)。我们以阿里云的镜像站点为例，访问网址。
2. 获取加速器地址`https://xxxxxx.mirror.aliyuncs.com`
3. 进入到root用户下执行命令
```
sh -c "echo EXTRA_ARGS=\'--registry-mirror=https://xxxxxx.mirror.aliyuncs.com\' >>/var/lib/boot2docker/profile"
```

### 使用
1.进入boot2docker，下载镜像

```
boot2docker ssh
docker pull imagine10255/centos6-lnmp-php56
```

2.创建容器

```
 docker run -d -it -p 800:80 -p 8088:8080 --name test imagine10255/centos6-lnmp-php56
```

3.退出boot2docker，执行以下命令，查找ip

```
exit
$ boot2docker ip
192.168.59.103
```

4.访问页面http://192.168.59.103:800，正常显示即环境搭建成功。

## 相关资料
1. [docker安装](http://www.cnblogs.com/bjfuouyang/p/3798198.html)
2. [docker镜像库](https://hub.docker.com)
3. [docker入门到实践](https://www.gitbook.com/book/yeasy/docker_practice/details)
4. [docker部署](http://bbs.wuyou.net/forum.php?mod=viewthread&tid=378224)



