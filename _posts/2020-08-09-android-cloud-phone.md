---
layout: article
title: "Android云真机解决方案"
date: 2020-08-09 20:24:00.000000000 +09:00
tags: 云真机 android
---

为了接入现有业务需求，方便运营人员测试，需搭建一个符合自身的云真机平台。

### 背景
自动化应用操作并不能解决全部应用场景，需人工介入操作，最大化的获取应用全部信息内容。便于对更多的信息识别。

### 现状

在平台搭建过程中，对比调研了现有一些比较知名的云真机服务平台，如下：
* [360开测](http://opentest.360.cn/)
* [阿里MQC](https://cn.aliyun.com/product/mqc)
* [UC岩鼠](https://yanshu.effirst.com/)
* [TestBird](https://www.testbird.com/)
* [腾讯WeTest](https://wetest.qq.com)
* [腾讯优测](https://utest.21kunpeng.com/home)
* [Testin云测](https://testin.cn/)
* [百度MTC](http://mtc.baidu.com/tinypace/mobileCloudy)

这些平台基本上都是基于开源框架[STF](https://github.com/DeviceFarmer/stf)或[scrcpy](https://github.com/Genymobile/scrcpy)搭建的远程安卓真机控制。

云真机包括两个方面，设备界面同步和用户操作同步，现有的实现方式有：

##### 界面同步框架

| 名称 | 备注 |
| -- | -- |
| [minicap](https://github.com/DeviceFarmer/minicap) | android新版本需要适配 |
| [scrcpy](https://github.com/Genymobile/scrcpy) | 需二次开发 |
| adb |  |

##### 操作同步框架

| 名称 | 备注 |
| -- | -- |
| [minitouch](https://github.com/DeviceFarmer/minitouch) | 电视不支持 |
| [scrcpy](https://github.com/Genymobile/scrcpy) |  |
| adb |  |


### 环境配置

`Android`直接官网下载安装即可，`windos sdk`使用`SDK Manager`安装即可，ndk 可以通过`Android studio`进行安装。

### scrcpy

如果想测试下效果可以直接到[项目地址](https://github.com/Genymobile/scrcpy/releases/tag/v1.16)下载最新版，使用windows开发测试，所以下载了[scrcpy-win64-v1.16.zip](https://github.com/Genymobile/scrcpy/releases/download/v1.16/scrcpy-win64-v1.16.zip)版本。
![99958ac086eec7469cc0d70d3b3f8270.png](https://shuibo.me/assets/images/202008/c773515b-8267-4beb-a744-f1231a558740.png)

执行exe文件。

``` shell
INFO: scrcpy 1.16 <https://github.com/Genymobile/scrcpy>
E:\automation\scrcpy-win64-v1.16\scrcpy-server: 1 file pushed, 0 skipped. 1.2 MB/s (33622 bytes in 0.026s)
[server] INFO: Device: samsung SM-G9600 (Android 10)
INFO: Renderer: direct3d
INFO: Initial texture: 1080x2216
```

直接弹出手机界面，简单便捷。
![b107c908d2907cb5ff92a338a5b63494.png](https://shuibo.me/assets/images/202008/6ecbde07-74e4-4b44-8d21-a53d01fa6b23.png)

由于是PC应用，不能满足我们搭建云平台的需求。查找了资料后，找到了依赖scrcpy搭建的web应用[ws-scrcpy](https://github.com/NetrisTV/ws-scrcpy)。

#### ws-scrcpy

``` shell
>git clone https://github.com/NetrisTV/ws-scrcpy.git
>cd ws-scrcpy
>npm install
>npm start

...
...

> ws-scrcpy@0.3.0 start E:\automation\ws-scrcpy\dist
> cd server && node index.js

Listening on: http://xxxxx-d5:8000 http://[fe80::914a:84d5:b744:d6d0]:8000 http://10.18.124.101:8000 http://[::1]:8000 http://127.0.0.1:8000
```

访问http://127.0.0.1:8000
![ac3478c973a39fd9eabeca48fddeaf56.png](https://shuibo.me/assets/images/202008/cf5dc9cc-4e6a-4363-9449-8f561d1ca919.png)

scrcpy是将屏幕数据直接发给PC，然后在PC上解码显示，所以在这边有四种解码方式选择。前三个具有相同的想法：将C代码的解码器编译为WebAssembly，将每个帧解码，然后在画布上绘制。
* [mbebenita/Broadway](https://github.com/mbebenita/Broadway)：使用了来自android的原始解码器而未做任何修改。
* [oneam/h264bsd](https://github.com/oneam/h264bsd)：使用相同的代码，但进行了一些优化
* [udevbe/tinyh264](https://github.com/udevbe/tinyh264)：是h264bsd的分支，具有更多修改
* [xevokk/h264-converter](https://github.com/xevokk/h264-converter)：原生解码

根据我使用经验，Broadway是相对稳定的解码器。

访问应用界面的时候需要再执行命令，将设备的8886端口映射出来给pc使用。
```
>adb forward tcp:8886 tcp:8886
8886
```

并且弹出的链接中不含有ip信息，需要手动添加IP地址127.0.0.1
![466cc950a8fed2d05662f08ee6a30b0b.png](https://shuibo.me/assets/images/202008/6ecbde07-74e4-4b44-8d21-a53d01fa6b23.png)
这样就实现了web页面可视化操作了。不过在使用过程中，一加3（Android 10）显示黑屏了，原生的pc应用打开没有影响。打算继续看下stf实现，比对下效果。

### STF

[STF](https://github.com/DeviceFarmer/stf)是一整套的WEB平台，通过浏览器访问实现控制管理安卓设备。

首先查看cpu及系统版本：
```
>adb shell getprop ro.product.cpu.abi
arm64-v8a

>adb shell getprop ro.build.version.sdk
28

```
minicap和minitouch要根据不同的CPU架构及Android版本使用相应的sdk版本。

#### minicap

```
>git clone https://github.com/openstf/minicap
>cd minicap
>git submodule init
>git submodule update
>ndk-build APP_PLATFORM=android-29 APP_ABI=arm64-v8a

>adb push libs\arm64-v8a\minicap.so /data/local/tmp/
>adb push libs\arm64-v8a\minicap /data/local/tmp/
>adb shell chmod 777 /data/local/tmp/minicap

>adb shell wm size
Physical size: 1080x1920

>adb shell LD_LIBRARY_PATH=/data/local/tmp /data/local/tmp/minicap -P 1080x1920@1080x1920/0 –t
OK
```

#### minitouch

```
>git clone https://github.com/openstf/minitouch
>cd minitouch
>git submodule init
>git submodule update
>ndk-build APP_PLATFORM=android-29 APP_ABI=arm64-v8a

>adb push libs\arm64-v8a\minitouch /data/local/tmp/
>adb shell chmod 777 /data/local/tmp/minitouch

```

如果遇到不能正常工作，可以直接启动程序查看相信问题。
```
>adb shell /system/bin/sh -c /data/local/tmp/minicap
CANNOT LINK EXECUTABLE "/data/local/tmp/minicap": library "minicap.so" not found
```

使用minicap内的example样例，刷新访问。
```
>adb forward tcp:7912 tcp:7912
7912
```

![505e9323d8c6f9ce121cab9b7a378851.png](https://shuibo.me/assets/images/202008/452def20-5e57-4a4e-aebc-71ea81898a3a_LI.jpg)

windows下折腾stf经常出错。`npm install`要解决一堆依赖，启动的时候还需要安装`rethinkdb`，然后再执行，`zmq`报错。
```
Could not locate the bindings file. Tried:
 → C:\Users\xx\AppData\Roaming\npm\node_modules\stf\node_modules\zmq\build\zmq.node
 → C:\Users\xx\AppData\Roaming\npm\node_modules\stf\node_modules\zmq\build\Debug\zmq.node
 → C:\Users\xx\AppData\Roaming\npm\node_modules\stf\node_modules\zmq\build\Release\zmq.node
 → C:\Users\xx\AppData\Roaming\npm\node_modules\stf\node_modules\zmq\out\Debug\zmq.node
 → C:\Users\xx\AppData\Roaming\npm\node_modules\stf\node_modules\zmq\Debug\zmq.node
 → C:\Users\xx\AppData\Roaming\npm\node_modules\stf\node_modules\zmq\out\Release\zmq.node
 → C:\Users\xx\AppData\Roaming\npm\node_modules\stf\node_modules\zmq\Release\zmq.node
 → C:\Users\xx\AppData\Roaming\npm\node_modules\stf\node_modules\zmq\build\default\zmq.node
 → C:\Users\xx\AppData\Roaming\npm\node_modules\stf\node_modules\zmq\compiled\12.18.3\win32\x64\zmq.node
2020-08-24T08:34:53.670Z FTL/cli:local 12704 [*] Child process had an error ExitError: Exit code "1"
    at ChildProcess.<anonymous> (C:\Users\caishuibo\AppData\Roaming\npm\node_modules\stf\lib\util\procutil.js:49:23)
    at ChildProcess.emit (events.js:315:20)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:275:12)
2020-08-24T08:34:53.670Z INF/cli:local 12704 [*] Shutting down all child processes
```
考虑到windows下坑比较多，我就不再深入，且有一个可以运行在Linux下的开源平台。

### atxserver2

当基本原理功能都调试通了，并找到一个好用的工具[atx-agent](https://github.com/openatx/atx-agent)，这是用Golang编写的一个执行在手机内，提供接口的工具。支持`adb`/`minicap`/`minitouch`/`uiautomator`，有了它可以直接接入到自己搭建的平台中。

`openatx`除了有`atx-agent`，还有一个已经编写好了的web平台，也就是基于Python编写的[atxserver2](https://github.com/openatx/atxserver2)。

这个平台直接就是个拿来即用的云真机平台，提供`docker-compose`部署，不用自己在配置环境了。

``` bash
$ docker-compose up
```

启动之后，浏览器打开 http://ip:4000，完成认证之后就可以顺利的看到设备列表页了。不过目前还是空的，什么都没有。

![7336fa45cad6ec6e993e5f3d0b100859.png](https://shuibo.me/assets/images/202008/e425c888-44a6-4095-a244-3cf101a3b546.png)

需要设备接入连接到平台，安装[atxserver2-android-provider](https://github.com/openatx/atxserver2-android-provider)。

```
git clone https://github.com/openatx/atxserver2-android-provider
cd atxserver2-android-provider

# 安装依赖
npm install

# 准备Python虚拟环境（可选）
python3 -m venv venv
. venv/bin/activate
# venv/Scripts/activate.bat  # for windows

pip install -r requirements.txt

# 启动，需要指定atxserver2的地址, 假设地址为 http://server_ip:4000
python3 main.py --server server_ip:4000
```

操作完成后，在平台上就可以进行控制了。
![d6fda1d194e36bc2eed77254b26752ca.gif](https://shuibo.me/assets/images/202008/54807405-187c3f00-4cb8-11e9-8a8e-8092c5472dc1.gif)

一个简单的云真机平台就实现了。

如果要进行二次开发，代码修改完成后，执行`docker restart atxserver2_web容器id`就能展示修改后的界面。

系统示意图：
![4872419bc9a6723fe8e1702b485ed7d5.png](https://shuibo.me/assets/images/202008/Pixelbook.png)

