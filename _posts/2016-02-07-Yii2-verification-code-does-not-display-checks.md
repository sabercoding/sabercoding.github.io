---
layout: article
title: "yii2验证码不显示检查"
date: 2016-02-07 06:28:00.000000000 +09:00
tags:  yii php
---

下载了个例子玩玩的时候发现了个问题，验证码竟然不显示。
502一般是nginx发送到php的时候，php没有正确响应。所以先去看看php的问题，打开phpinfo看了下。 有gd扩展的。

```php
<php?
    $im = imagecreatetruecolor(120, 20);
    $text_color = imagecolorallocate($im, 233, 14, 91);
    imagestring($im, 1, 5, 5, "A Simple Text String", $text_color);
    // Set the content type header - in this case image/jpeg
     header("Content-Type: image/jpeg");
    // // Output the image
     imagejpeg($im);
    // // Free up memory
     imagedestroy($im);
     ?>
    <?php
    $im = imagecreatetruecolor(120, 20);
    $text_color = imagecolorallocate($im, 255, 0, 0);
    imagestring($im, 1, 5, 5, "A Simple Text String", $text_color);
    header("Content-Type: image/png");
    imagepng($im);
    imagedestroy($im);
?>
```

这两个测试都能正常打开，所以感觉不是php的问题，就开始测试yii2的代码 从新弄了一个yii2的应用。还是不行。。。但是这次是404。是应用权限的问题，改了下，又变成502了。。。。。。。本来就不是应用的问题。。。。

然后去php-fpm看日志吧。。。发现我没开日志显示＝ ＝

我把代码放到服务器里面然后测试下，它是可以打开的。。。。瞬间觉得自己机子上的php太坑了。
