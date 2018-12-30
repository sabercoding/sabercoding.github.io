---
layout: post
title: "记一次webpack资源路径问题"
date: 2017-09-03 12:26:27.000000000 +09:00
tags: ""
---
>webpack执行npm run build 之后传到远程服务器后，路由不能正常显示。本地环境能正常访问。
## 问题
访问/dashboard正常跳转到/dashboard/index能正常显示，而在页面/dashboard/index刷新访问的时候就不能访问报了以下的错误。
![问题][1]
## 解决过程
看了下router文件后，并没有啥问题。
![路由][2]
又去看了下nginx正常配置：
```
location / {
    expires -1; add_header Pragma "no-cache";
    add_header Cache-Control "no-store, no-cache, must-revalidate, post-check=0, pre-check=0";
    root /home/code/qingtingtui/dist;
    try_files $uri $uri/ /index.html =404;
}
```
后来发现是，资源路径的问题=。=刚开始用webpack这套，不知道。。。那三个错误，因为引入不对报的错。
<script type=text/javascript src=./static/js/app.508ef9f77b875a8ecd8b.js></script>
使用的是./相对路径，而正确的应该是绝对路径。手动修改了上传，果然好了。总不能每次都手动改各个资源吧。。。。应该有个配置的地方，搜索了下资料，解决方法如下。
## 解决
1. 打开config/index.js文件。
![文件地址][3]
2. 修改assetsPublicPath。果然dev的是`/`,build的是`./`。修改成`/`，build之后上传，完事！
![配置文件][4]


  [1]: https://o8ekw8sx0.qnssl.com/B9CF5B26-7F4C-430A-AB68-0D430250580F.png
  [2]: https://o8ekw8sx0.qnssl.com/0BCAB6E5-C21A-453E-A71A-E86543F5D866.png
  [3]: https://o8ekw8sx0.qnssl.com/C1498B8B-5797-46A7-B7BF-F28178FAD03A.png
  [4]: https://o8ekw8sx0.qnssl.com/500C1B23-6339-465A-AB70-43601954DF8C.png