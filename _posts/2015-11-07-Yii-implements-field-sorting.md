---
layout: post
title: "yii 实现字段排序"
date: 2015-11-07 03:38:00.000000000 +09:00
---

为了实现yii1里面字段排序，看了些资料还是没有解决，最后不行了，就直接在header添加连接。。。。
![QQ截图20151107113658.png](https://o8ekw8sx0.qnssl.com/upload/201511/KQQmDjE7ebJwM26Z1WrbGUXswytUDrkO.png "QQ截图20151107113658.png")
`array(
            ''name''   => ''insertDate'',
            ''header'' => "<a href=''".Yii::app()->createUrl(''data/user/list2'')."''>注册时间</a>",
            ''value''  => function ($data){
            	echo date(''Y-m-d H:i:s'',$data->insertDate);
            }
            //''class''  => ''application.components.columns.GridColumnDateChange'',
		    //''attribute'' => ''type'',
        ),`