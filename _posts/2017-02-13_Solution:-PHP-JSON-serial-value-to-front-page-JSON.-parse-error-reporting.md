---
layout: post
title: "解决：php json串传值到前端页面JSON.parse报错"
date: 2017-02-13 03:03:51.000000000 +09:00
tags: ""
---
>使用yii2传值apps到views时，json_decode()完后再用js的JSON.parse转成对象时报错。
>原因是还需要对字符串进行转义。 主要是\\n \\"这两个

```php
$find = [''\\n'',''\\"''];
$replace = [''\\\\\\n'', ''\\\\\\"''];
```
```js
apps: JSON.parse(''<?= str_replace($find,$replace,json_encode($apps))?>'')
```