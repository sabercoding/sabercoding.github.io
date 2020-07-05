---
layout: article
title: "yii1调用路径"
date: 2015-11-24 08:29:00.000000000 +09:00
tags: yii
---


## 变量
调用YII框架中jquery：`Yii::app()->clientScript->registerCoreScript(‘jquery’);`
framework/web/js/source的js,其中registerCoreScriptkey调用的文件在framework/web/js/packages.php列表中可以查看

在view中得到当前controller的ID方法：`Yii::app()->getController()->id;`

在view中得到当前action的ID方法：`Yii::app()->getController()->getAction()->id;`

yii获取ip地址：`Yii::app()->request->userHostAddress;`

yii判断提交方式：`Yii::app()->request->isPostRequest`

得到当前域名: `Yii::app()->request->hostInfo`

得到proteced目录的物理路径：`YII::app()->basePath;`

获得上一页的url以返回：`Yii::app()->request->urlReferrer;`

得到当前url ：`Yii::app()->request->url;`

得到当前home url ：`Yii::app()->homeUrl`

得到当前return url ：`Yii::app()->user->returnUrl`

项目路径：`dirname(Yii::app()->BasePath)`

## 常量
* system: 指向Yii框架目录; Yii\\framework
* zii: 指向zii library 目录; Yii\\framework\\zii
* application : 指向应用程序基本目录; protected\\
* webroot: 指向包含里入口脚本 文件的目录; .\\
* ext : 指向包含所有第三方扩展的目录; \\protected\\extensions
      　用法：Yii::getPathOfAlias(‘webroot’)
* 取得当前的完整路径

```php
　　Yii::getFrameworkPath() :YII framework路径
```

* 插入meta信息

``` php
Yii::app()->clientScript->registerMetaTag(‘keywords’,"关键字’);
Yii::app()->clientScript->registerMetaTag(‘description’,"一些描述’);
Yii::app()->clientScript->registerMetaTag(‘author’,"作者’);
```


## 方法
　　在控制器添加CSS文件或JavaScript文件
``` php
　　Yii::app()->clientScript->registerCssFile(Yii::app()->baseUrl.’/css/my.css’); Yii::app()->clientScript->registerScriptFile(Yii::app()->baseUrl.’/css/my.js’);
```
　　在view中得到当前controller的ID方法
``` php
　　Yii::app()->getController()->id;
```

　　在view中得到当前action的ID方法：
``` php
　　Yii::app()->getController()->getAction()->id;
```

　　Yii获取ip地址
``` php
　　Yii::app()->request->userHostAddress;
```

　　Yii判断提交方式
``` php
　　Yii::app()->request->isPostRequest
```

　　得到当前域名:

``` php
　　Yii::app()->request->hostInfo
```

　　得到proteced目录的物理路径

``` php
　　Yii::app()->basePath;
```

　　获得上一页的url以返回

``` php
　　Yii::app()->request->urlReferrer;
```

　　得到当前url

``` php
　　Yii::app()->request->url;
```

　　得到当前home url

``` php
　　Yii::app()->homeUrl
```

　　得到当前return url

``` php
　　Yii::app()->user->returnUrl
```

　　项目路径

``` php
　　dirname(Yii::app()->BasePath)
```
