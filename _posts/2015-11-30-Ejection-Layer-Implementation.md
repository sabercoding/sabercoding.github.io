---
layout: article
title: "弹出层实现"
date: 2015-11-30 13:56:00.000000000 +09:00
---


             array(                        //自定义按钮操作列
                ''header'' => ''操作'',
                ''buttons'' => array(
                    ''ll'' => array(
                        ''label'' => ''信息'',
                        ''imageUrl'' => '''',
                        ''options'' => array(''role'' => "button", ''class'' => "btn"),
                        ''click'' => ''function() {
                                var val=$(this).parent().parent().children().eq(0).html();
                                $.ajax({
                                    type:"POST",
                                    dataType:"json",//dataType (xml html script json jsonp text)
                                    data:{"val":val},//json 数据
                                    url: "'' . Yii::app()->createUrl("/data/user/update/") . ''",
                                    success:function(json) {//成功获得的也是json对象
                                        layer.open({
                                            type: 1,
                                            title: false,
                                            closeBtn: 0,
                                            shadeClose: true,
                                            skin: \\''yourclass\\'',
                                            content: json.val
                                        });
                                    }
                                });
                        }''
                    ),
                ),
                ''class'' => ''CButtonColumn'',
                ''template'' => ''{ll}'',
            ),
            array(
                ''header'' => ''修改'',
                ''buttons'' => array(
                    ''info'' => array(
                        ''label'' => ''信息'',
                        ''options'' => array(),
                        ''url'' => ''"javascript:info(".json_encode($data).");"'',
                    ),
                ),
                ''class'' => ''CButtonColumn'',
                ''template'' => ''{info}'',
            ),

传递这一行的数据，$data是一个对象，想用序列化，然后js再反序列化。chrome显示错误SyntaxError: missing ) after argument list。。。。php传到js的时候变乱码。。。。然后换转json，嘿嘿。。
json_encode()和json_decode()函数
serialize()和unserialize()函数

字符串转对象(strJSON代表json字符串)

      var obj = eval(strJSON);
      var obj = strJSON.parseJSON();
      var obj = JSON.parse(strJSON)；

json对象转字符串(obj代表json对象)


     var str = obj.toJSONString();
      var str = JSON.stringify(obj)

运用时候需要除了eval()以外需要json.js包（切记哦）

