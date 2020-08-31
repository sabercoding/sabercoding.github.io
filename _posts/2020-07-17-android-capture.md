---
layout: article
title: "安卓抓包实践"
date: 2020-07-17 21:39:00.000000000 +09:00
tags: capture android
---

apk动态分析中，应用发起的请求是最为重要的信息之一。一般我们都是采用代理的方式来做采集这部分数据。实践过程中，首先使用的是安卓模拟器的环境，后再迁移至真机环境。

## 模拟器环境

模拟器：[Mumu](http://mumu.163.com/)
安卓版本：6.0.1
代理：[Mitmproxy](https://mitmproxy.org/)

![020bc1f1220db0f2b2978dbf0c4bfc19.png](https://shuibo.me/assets/images/202007/Pixelbook_3.png)

#### 启动代理
Mitmproxy支持脚本启动，我们可以自定义脚本并在请求的过程中输出指定格式日志到指定位置。
``` json
{
    "t":"2020-06-29 11:05:42",
    "method":"POST",
    "url":"http://cfg.imtt.qq.com/tbs?xxxx"
}
```

官方的文档也很全，可以看到相应的[API](https://mitmproxy.readthedocs.io/en/v2.0.2/scripting/api.html)，选择想要输出的数据，在脚本中打印输出：
```py
from mitmproxy import ctx
import sys
import json
import datetime

def request(flow):
    request = flow.request
    info = ctx.log.info

    t1 = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')
    data = {}
    data["t"] = str(t1)
    data["method"] = str(request.method)
    data["url"] = str(request.pretty_url)
    data = json.dumps(data)
    apkmd5 = str(sys.argv[3])
    filename = "E:\\logs\\%s\\request.txt"%(apkmd5)
    with open(filename, 'a') as file_object:
        file_object.write(data+"\n")
```

启动mimproxy软件：
``` cmd
>mitmdump.exe -s proxy.py
Loading script proxy.py
Proxy server listening at http://*:8080
```

这时候已经启动了代理监听，端口是8080。我们需要在mumu模拟器上配置代理：
设置->WLAN->长按连接WiFi->修改网络->配置代理
![47ec686b4b7d224ec9c1749abd8ade83.png](https://shuibo.me/assets/images/202007/8c19e5c0-4bc7-4d66-97d6-70b29f909b16.png)

#### 证书配置
打开浏览器会发现会弹出安全警告，这是因为访问了https协议的网站，https会有证书校验。
![472f75c8d2f8820207e3197d2c3b0015.png](https://shuibo.me/assets/images/202007/64ea0174-619c-4cf1-87ab-5f4bc5567e22.png)

请求经过mitm代理时，返回的证书是mitm自己颁发的，此时如果系统没有信任这个这个颁发者的话就会提示连接不安全。
![f62cbb56526041b78951e28062a871f3.png](https://shuibo.me/assets/images/202007/1b342cc8-e82c-44e9-b8ed-c987dd5b2938.png)

我们需要去mitm.it网站上下载相应的信任证书。
![3a341e26992099b5871568f760db6ea6.png](https://shuibo.me/assets/images/202007/4244bd79-94de-415a-8c63-aa1b84afb969.png)
![8f928a5b5b6f154f455e63af2ee5298a.png](https://shuibo.me/assets/images/202007/7a7e852e-593c-421b-94cc-e90b52f77374.png)
![3c634312966ca9dea60819cefa704f06.png](https://shuibo.me/assets/images/202007/025ad3f9-87f2-4995-971c-5ef2983eaa4e.png)
安装完毕后，再次访问https网站就不会提示有问题了。
自此，6.0版本模拟器环境配置好了。可以进行安装软件，抓包了。


## 真机环境

模拟器：三星S9
安卓版本：10
usb连接：[Gnirehtet](https://github.com/Genymobile/gnirehtet)
代理：[Mitmproxy](https://mitmproxy.org/)

### 网络
真机环境首先要解决的就是网络连接的问题，在提供网络环境网络连接的同时，还需要能进行网络捕捉。网络连接可以从以下三方面入手：
* WiFi：通过pc创建热点，手机连接热点，实现手机上网。考虑到后续是搬到机房，创建热点的方式，无线连接，不稳定且不易维护。
* 蓝牙：通过蓝牙连接共享网络，实现手机上网。市面上的手机，基本是连接pc，实现PC通过手机上网，实现不了。
* USB：通过USB连接，实现手机上网。手机自带有USB共享网络，不幸的是跟蓝牙网络共享一样，也是Pc共享手机网络，查遍资料，就在放弃的时候找到了[Gnirehtet](https://bbs.pediy.com/thread-251370.htm)。

![b0ff16273179c380eecdc604c2d46d91.png](https://shuibo.me/assets/images/202007/Pixelbook_2.png)

#### Gnirehtet

该项目通过Android的adb提供反向网络连接，可以在不root的情况下设备使用其所连接计算机的Internet连接网络。支持多端运行。

[Gnirehtet](https://github.com/Genymobile/gnirehtet/releases)目前有Rust版和Java版，前者占用的CPU较低而且不需要Java 环境，较为推荐使用该版本。

![38a890ca9f50c022b4236993b907e6da.png](https://shuibo.me/assets/images/202007/1b342cc8-e82c-44e9-b8ed-c987dd5b2938.png)

使用步骤：

1. Gnirehtet依赖adb环境，需要先去下载adb，可看上一篇[adb文章](https://shuibo.me/2020/06/17/adb-commands.html)。

2. 下载对应版本压缩包(以win64为例)，将文件统一放置`platform-tools`。
![dd7559db73d592caf14abed9cbdbc95c.png](https://shuibo.me/assets/images/202007/ad348aa4-9015-4180-9eb8-8def2d2b2aa2.png)

3. 手机开启调试并连接电脑。

4. 在手机上安装`gnirehtet.apk`软件：`adb install gnirehtet.apk`

5. 打开`cmd`，进入到`atform-tools`文件夹，执行命令：
```
>gnirehtet.exe run
2020-07-08 12:26:49.125 INFO Main: Checking gnirehtet client...
2020-07-08 12:26:49.125 INFO Main: Starting relay server on port 31416...
2020-07-08 12:26:49.140 INFO Relay: Relay server started
2020-07-08 12:26:49.510 INFO Main: Starting client...
Starting: Intent { act=com.genymobile.gnirehtet.START cmp=com.genymobile.gnirehtet/.GnirehtetActivity }
2020-07-08 12:26:51.181 INFO TunnelServer: Client #0 connected
```

6. 自此，手机可以上网了。

#### 代理

相同的，真机上也是要配置代理，将请求引向Mitmproxy。刚开始的时候，由于[Gnirehtet不支持](https://github.com/Genymobile/gnirehtet/issues/27)，尝试使用[proxifier](https://www.proxifier.com/)，设置`gnirehtet`走8080端口，`mitmproxy`直连。

这样一来又多了一个软件，增加维护成本，后发现其实手机自己就可以配置代理：
```
>adb shell settings put global http_proxy 本机ip:8080
```

自身实现全局代理，添加证书，启动mimproxy软件，就完美解决了。

### https禁止访问

在浏览器测试的时候，https请求没有毛病，正常解析访问。但直接跑apk的时候遇到问题了，https访问直接挂了，应用显示不了信息。
```
<< Cannot establish TLS with client (sni: app.1hai.cn): TlsException("SSL handshake error: Error([('SSL routines', 'ssl3_read_bytes', 'sslv3 alert certificate unknown')],)",)
```

#### 原因

![b4909f2b1b1e2c26436e97ebcf7cb47f.png](https://shuibo.me/assets/images/202007/dca54231-1ac6-4c7b-9602-46ed00cf164b.png)

https请求过程中，第四步，客户端解析证书时，mitmproxy发送过来的mitm证书由于手机内[没有信任的CA证书](https://developer.android.google.cn/training/articles/security-ssl)就会证书存在问题，请求失败。

明明都已经安装了mitm的证书里，为什么还会证书存在问题呢？

这是因为mitmproxy处理https的时候是解密了数据，自己需要加解密，加解密的化使用的证书是mitmproxy自带的。但Android7.0及以上[netwok配置文件](https://developer.android.google.cn/training/articles/security-config.html)，取消了对用户自己安装的证书信任，所以Android7.0及以上https无法抓包。

Android 6.0 network配置文件
``` xml
<!-- 默认允许所有明文通信 -->
<base-config cleartextTrafficPermitted="true">
    <trust-anchors>
        <!-- 信任系统预装 CA 证书 -->
        <certificates src="system" />
        <!-- 信任用户添加的 CA 证书，Charles 和 Mitmproxy 抓包工具安装的证书属于此类 -->
        <certificates src="user" />
    </trust-anchors>
</base-config>
```

Android 7.0 -8.1 network配置文件

``` xml
<!-- 默认允许所有明文通信 -->
<base-config cleartextTrafficPermitted="true">
    <trust-anchors>
        <!-- 信任系统预装 CA 证书 -->
        <certificates src="system" />
    </trust-anchors>
</base-config>
```

Android 9.0 network配置文件

``` xml
<!-- 默认禁止所有明文通信 -->
<base-config cleartextTrafficPermitted="false">
    <trust-anchors>
        <!-- 信任系统预装 CA 证书 -->
        <certificates src="system" />
    </trust-anchors>
</base-config>
```

#### 解决

##### 低版本Android

Android7以下不会出现此问题，可直接使用低版本系统。

##### Mitmproxy 直通模式

![d96cd5a6736cb933cefe56c970f33abb.png](https://shuibo.me/assets/images/202007/43e831bf-c70e-4591-84e8-f1f323290bd2.png)

直通模式就是，仅传递加密的数据，而不进行解密操作，也就不会出现了证书不信任的情况。
官方提供了[实践脚本](https://github.com/mitmproxy/mitmproxy/blob/ed68e0a1ba090eca5f8b841a9d06303d2b5862f3/examples/contrib/tls_passthrough.py)，执行`mitmdump.exe -s tls_passthrough.py`确实可以访问https了。但自定义request后发现，https请求由于是直通模式，抓不到请求信息，也就没有相应的日志了。

##### 自定义其网络安全设置

详情可以参考[绕过 Android 7.0+ 网络安全配置](https://iacn.me/2018/03/14/network-security-configuration-newer-than-android-n/)

##### 证书迁移至系统目录

手机root后，获得读写挂载的权限后可以将mitm证书复制到`/system/etc/security/cacerts/`位置。

```shell
$ openssl x509 -subject_hash_old -in mitm.pem
c8750f0d
-----BEGIN CERTIFICATE-----

$ openssl x509 -inform DER -text -in mitm.pem -out c8750f0d.0
```

```shell
adb push c8750f0d.0 /system/etc/security/cacerts/
```

##### 安装xposed

如果是使用 [magisk](https://github.com/topjohnwu/Magisk) root 的话，就会出现没权限的问题，也

### 其它

接触的过程中还有其它可以抓取应用网络信息的方法：

1. 安装抓取手机网络信息的黑客工具应用，自动抓取到指定位置，结束应用后提取数据。
