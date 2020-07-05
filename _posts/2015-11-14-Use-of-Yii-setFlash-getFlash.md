---
layout: article
title: "Yii setFlash getFlash用法"
date: 2015-11-14 07:01:00.000000000 +09:00
tags: php
---

```php
<?php
Yii::app()->user->setFlash(‘success’,”Data saved!”);   //设置键值名为success的临时信息.在getFlash后删除.  可以定义多种不同的键值名对象的消息.
?>

<?php if(Yii::app()->user->hasFlash(‘success’)):?>  //消息存在里,

<?php echo Yii::app()->user->getFlash(‘success’); ?>  //输出消息内容,输出后,就不存在了.

<?php endif; ?>

<?php  //这是一段,在显示后定里消失的JQ代码,已集成至Yii中.
Yii::app()->clientScript->registerScript(
‘myHideEffect’,
‘$(“.info”).animate({opacity: 1.0}, 3000).fadeOut(“slow”);’,
CClientScript::POS_READY
);

?>
```
