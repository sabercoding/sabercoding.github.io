---
layout: article
title: "yii的gridview使用"
date: 2015-11-27 07:04:00.000000000 +09:00
tags: yii php
---

``` php
$this->widget("zii.widgets.grid.CGridView",
    array("dataProvider" => $dataProvider,//数据源
        "pager" => array(              //通过pager设置样式   默认为CLinkPager
            "prevPageLabel" => "上一页",
            "firstPageLabel" => "首页",  //first,last 在默认样式中为{display:none}及不显示，通过样式{display:inline}即可
            "nextPageLabel" => "下一页",
            "lastPageLabel" => "末页",
            "header" => "",
        ),
        "ajaxUpdate" => false,           //是否使用ajax分页   null为ajax分页
        "columns" => array(
            array(                       //具体设置每列的header
                "name" => "ID",
                "value" => "$data->id",
            ),
            "parentid",
            array(
                "name" => "标题",         //需要连接可使用CLinkColumn
                // "value"=>"$data->title",
                ‘value‘ => function ($m) {
                    return $m->status == 1 ? ‘显示‘ : ‘影藏‘;
                }
            ),

            array(
                "name" => "模型",
                "value" => "$data->module",
            ),


            array(                        //自定义按钮操作列
                "header" => "操作",
                "buttons" => array(
                    "preview" => array(
                        "label" => "审核",
                        "url" => "",// 通过PHP表达式生成URL    例如createUrl
                        "imageUrl" => "",// 按钮图片地址
                        "options" => array(),// HTML 标签属性设置
                        "click" => "",// js 代码或函数
                        "visible" => "",// PHP表达式 用于权限控制
                    ),
                    "recommend" => array(
                        "label" => "推荐",
                    ),
                ),

                "class" => "CButtonColumn",
                "template" => "{preview}  {recommend}",

            ),
            array(            //仅使用默认CButtonColumn不具体设置按钮，在显示为查看 修改 删除图片按钮

                "class" => "CButtonColumn",
            ),
        ),//end columns
    )//end dataprovicer

);//end widget
```
