---
layout: article
title: "执行shell_exec()方法返回为NULL"
date: 2016-05-16 06:54:00.000000000 +09:00
---

>我想让php的www用户能执行命令，用shell_exec()命令执行了phpmd，我把这个文件夹权限改成了777为啥还是不能执行？

首先shell_exec()方法相比于exec()方法来说，它可以输出每行的输出信息，而后者仅仅只能输出最后一行的结果。

phpmd是使用composer执行安装的。我发现这些composer的工具用起来挺好用的，直接安装下然后执行bin里面的执行文件即可执行。更新安装都非常方便~

在虚拟机上面执行下命令：

``` shell
$ /data1/htdocs/vendor/vendor/phpmd/phpmd/src/bin/phpmd /data1/htdocs/ text codesize
```

在这个用户下是可以输出结果的，担心www用户不能执行，我就把整个phpmd文件夹设置成权限777。

为了确保文件能找到，我也是使用了绝对路径的。在php里面执行下面代码：

``` shell
$ var_dump(shell_exec("/data1/htdocs/pecker/security /data1/htdocs/rd3.i.beebank.com"));
```

但是输出的是个NULL。。。。。。。。。确实郁闷了，我也感觉是没有执行就报错了，感觉是shell_exec()这个方法仅仅只是输出标准输出流，错误流它是没有输出的。

然后就不懂了。就去百度了下，权限问题。。。。。。。。。。。。。。。。。。
但还是没有找到相关的，就去问人。。。。。。。。给了个网址，瞬间就觉得我找问题解决的能力还是太差了。T T
`http://stackoverflow.com/questions/18241305/shell-exec-returning-null-on-ls`

这么明显的问题我都没有去搜。。。

解决这个问题的方法也很简单，因为错误流没有输出，就可以把错误流输出即可

``` php
// add  "2>&1" to the end of your shell command to have STDERR returned as well as STDOUT.
$shell_return = shell_exec($shell_command." 2>&1");
```

发现问题是：

`string(45) "/usr/bin/env: php: No such file or directory "`
我把原本是php脚本的文件改成执行文件了。。。

``` bash
    #!/usr/bin/env php
```

虚拟机的php是软连接，所以就会报php没找到，要添加绝对路径去执行就好了。

### 总结
搜索问题原本自己觉得还是不错的。。。这次的问题发现自己还是没有达到一定的解决问题的能力。首先，php的函数可以去好文档函数详细的查找，之后再去google搜索，[stackoverflow](https://stackoverflow.com/)搜索。千万不要嫌英语烦！！！！！！！！！！

安利个[提问的智慧](http://www.dianbo.org/9238/stone/tiwendezhihui.htm)。
