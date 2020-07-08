---
layout: article
title: "adb常用命令"
date: 2020-06-17 18:39:00.000000000 +09:00
tags: adb android
---

本文简单介绍下安卓手机调试必备工具adb常用命令。

## 下载

可以直接从[SDK 平台工具版本说明](https://developer.android.com/studio/releases/platform-tools)页面中下载不通系统下的平台工具。其中包含`adb.exe`调试工具：

![platform-tools.png](https://shuibo.me/assets/images/platform-tools.png)

## 命令

windows环境下，执行命令需要打开`cmd`，`win+r`打开运行，输入`cmd`就能打开命令行界面。

#### 获取版本信息

``` cmd
>adb
Android Debug Bridge version 1.0.41
Version 30.0.1-6435776
Installed as D:\platform-tools\adb.exe
```

#### 启动或关闭adb服务

由于adb并不稳定, 有时候莫名的问题掉线时, 可以先kill-server, 然后start-server来确保Server进程启动。往往可以解决问题。

``` cmd
>adb start-server
>adb kill-server
```

如果是因为5037端口被占用而启动失败的话，可以执行以下操作解决：

``` cmd
>netstat -aon|findstr 5037   # 查看端口占用
  TCP    127.0.0.1:5037         0.0.0.0:0              LISTENING       32568
>tasklist | findstr "32568"      # 查看端口占用程序
adb.exe                      32568 Console                    1     11,444 K
> taskkill /f /t /im adb.exe  # 结束程序
```


#### 连接设备

模拟器连接
* mumu 127.0.0.1:7555
* 雷电模拟器 5555 + index * 2
* 夜神模拟器 127.0.0.1:62001 或 127.0.0.1:52001

```
>adb connect 127.0.0.1:7555
```

安卓真机开启调试后，连接电脑后直接会被adb连接。

#### 获取连接设备

查看已连接的设备列表

``` cmd
>adb devices
List of devices attached
1a74531423027ece        device
```

如果设备后面是`unauthorized`，代表设备没有授权调试，需要在弹出的授权提示中授权。

当连接多个设备时，需要使用`-s`加设备名称指定设备:
`adb -s 1a74531423027ece`

#### 安装应用

``` cmd
>adb install xxxx.apk
```

应用需要比较敏感的权限时，会在App运行时以弹窗等形式进行动态申请，用户可以选择同意或者拒绝。如果想避免这种情况发生的话，可以使用`-g`参数直接一次性授予所有权限。

#### 卸载应用
`adb uninstall 包名`

#### 启动应用
`adb shell am start -n 包名/Activity类名`

#### 关闭应用
`adb shell am force-stop 包名`

#### 登陆手机系统

``` cmd
>adb shell
starqltechn:/ $
```

#### PM包命令

1. 获取模拟器所有包名
   `adb shell pm list packages`
2. 获取第三方应用包名
   `adb shell pm list packages -3`
3. 获取模拟器所有包名并且包括APK路径
   `adb shell pm list packages -f`
4. 获取包名对应的APK路径
   `adb shell pm path packageName`
5. 清理应用数据
   `adb shell pm clear packageName`


#### 截屏
`adb shell screencap -p /sdcard/screencap.png`

#### 录屏
录制十秒
`adb shell screenrecord --time-limit 10 /sdcard/screencap.mp4`
注:ov,huawei不支持adb路屏

#### 文件操作

##### 上传文件
`adb push <PC端文件> <SD目录路径>`

##### 下载文件
`db pull <手机端文件> <PC端目录路径>`

##### 删除文件
使用`adb shell`进入手机系统，直接删除

#### 模拟输入(不支持中文)
`adb shell input text 字符串`

#### 模拟按键
`adb shell input keyevent 键值`
常用键值：
| 键值 | keycode | 含义 |
| --- | --- | --- |
| 3 | KEYCODE_HOME | home键 |
| 4  | KEYCODE_BACK | 返回键 |
| 7-16 | KEYCODE_0-KEYCODE_9 | 0-9 |
| 29-54  | KEYCODE_A-KEYCODE_54 | A-Z |
| 82 | KEYCODE_MENU | 解锁键 |
更多keycode可以查阅[官网](
https://developer.android.com/reference/android/view/KeyEvent#KEYCODE_1)。

#### 模拟鼠标点击
`adb shell input tap X Y`

#### 模拟鼠标滑动
`adb shell input swipe X1 Y1 X2 Y2`

#### 授权及取消授权
`adb shell pm grant com.eg.android.AlipayGphone android.permission.READ_EXTERNAL_STORAGE`
`adb shell pm revoke com.eg.android.AlipayGphone android.permission.READ_EXTERNAL_STORAGE`

#### 设置代理
`adb shell settings put global http_proxy 代理IP地址:端口号`
`adb shell settings put global http_proxy 10.18.224.171:8080`

#### 移除代理
`adb shell settings delete global http_proxy`
`adb shell settings delete global global_http_proxy_host`
`adb shell settings delete global global_http_proxy_port`

移除完还需要重启手机才能删除代理，可以使用设置错误代理来解决需要重启的问题：
`adb shell settings put global http_proxy :0`

#### 检查网络情况
```
>adb shell ping -c 4 www.baidu.com
PING www.wshifen.com (104.193.88.123) 56(84) bytes of data.

--- www.wshifen.com ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3123ms
```

#### 锁屏情况
```
>adb shell dumpsys power|findstr  "Display Power"
Power Manager State:
  mIsPowered=true
  mLastInteractivePowerHintTime=325859145 (108561 ms ago)
  mDisplayReady=true
  mDisplayReadyByProximity=true
  mHoldingDisplaySuspendBlocker=true
  mDecoupleHalAutoSuspendModeFromDisplayConfig=true
  mDecoupleHalInteractiveModeFromDisplayConfig=true
  mDreamsBatteryLevelMinimumWhenPoweredConfig=-1
  mDreamsBatteryLevelMinimumWhenNotPoweredConfig=15
mDisplayOnTime: 0
mSentAllDisplayOnTimeIntent: false
  mTouchKeyForceDisableOverrideFromSystemPowerSaveMode: false
  Looper (PowerManagerService, tid 47) {443413c}
    Message 1: { when=+1s58ms what=4 target=com.android.server.power.PowerManagerService$PowerManagerHandler }
    Message 2: { when=+4m51s437ms what=1 target=com.android.server.power.PowerManagerService$PowerManagerHandler }
  PowerManagerService.WakeLocks: ref count=0
  PowerManagerService.Display: ref count=1
  PowerManagerService.Broadcasts: ref count=0
  PowerManagerService.WirelessChargerDetector: ref count=0
Display Power: state=ON  # 开
  mIsPowered=true
  mPoweredWirelessly=false
```

`Display Power: state=ON`  代表未锁屏


#### 检查电池情况
```
>adb shell dumpsys battery
Current Battery Service state:
  AC powered: false
  USB powered: true
  Wireless powered: false
  Max charging current: 0
  Max charging voltage: 0
  Charge counter: 2845000
  status: 5
  health: 2
  present: true
  level: 100
  scale: 100
  voltage: 4312
  temperature: 302
  technology: Li-ion
  batteryMiscEvent: 0
  batteryCurrentEvent: 0
  mSecPlugTypeSummary: 2
  LED Charging: true
  LED Low Battery: true
  current now: 141
  charge counter: 2845000
  Adaptive Fast Charging Settings: true
USE_FAKE_BATTERY: false
FEATURE_WIRELESS_FAST_CHARGER_CONTROL: true
  mWasUsedWirelessFastChargerPreviously: false
  mWirelessFastChargingSettingsEnable: true
LLB CAL: 20180302
LLB MAN:
LLB CURRENT: YEAR2020M6D6
LLB DIFF: 117
FEATURE_HICCUP_CONTROL: true
FEATURE_SUPPORTED_DAILY_BOARD: false
SEC_FEATURE_USE_WIRELESS_POWER_SHARING: false
BatteryInfoBackUp
  mSavedBatteryAsoc: 86
  mSavedBatteryMaxTemp: 476
  mSavedBatteryMaxCurrent: 2203
  mSavedBatteryUsage: 35099
  FEATURE_SAVE_BATTERY_CYCLE: true
  SEC_FEATURE_PREVENT_SWELLING: false
```

#### 打印log信息
|指令|说明|备注|
| --- | --- | --- |
|adb logcat|打印log|/|
|adb logcat -c|清除手机的log buffer有些手机权限控制, 不支持.|
|adb logcat -b <buffer>|打印指定buffer的log信息|buffer有: main(主log区,默认), events(事件相关的log), radio(射频, telephony相关的log)|
|adb logcat -v <format>|格式化输出log|常用的用adb logcat -v time显示时间|
|adb logcat -f <filename>|输出log到指定文件||
