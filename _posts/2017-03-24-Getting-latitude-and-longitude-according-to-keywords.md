---
layout: article
title: "根据关键词获取经纬度"
date: 2017-03-24 08:11:38.000000000 +09:00
tags: php gps
---

>找了半天的地图平台的api都没发现单一调用的根据关键词获取经纬度，只能退而求其次，找到了高德的周边搜索，从里面获取了一个能请求在一定范围内的关键词搜索

```
$content = file_get_contents("http://restapi.amap.com/v3/place/around?key=你的key&page=1&radius=5000&offset=10&keywords=%E5%9B%BD%E9%99%85%E8%8A%B1%E5%9B%AD%E5%9F%8E&location=118.387716%2C24.956172&s=rsv3&rf=h5&utm_source=litemap&callback=jsonp3");
$content = ltrim($content, "jsonp3(");
$content = rtrim($content, ")");
print_r(json_decode($content,true));
```

请求地址解析：
```
http://restapi.amap.com/v3/place/around?
key= 高德平台的key
&page=1
&radius=5000   //中心点多远
&offset=10
&keywords= 关键词
&location=118.387716,24.956172 //中心点
&s=rsv3&rf=h5&utm_source=litemap&callback=jsonp3
```
结果分析：
```
Array
(
    [status] => 1
    [count] => 12  //结果数量
    [info] => OK   //返回结果
    [infocode] => 10000
    [suggestion] => Array
        (
            [keywords] => Array
                (
                )

            [cities] => Array
                (
                )

        )

    [pois] => Array
        (
            [0] => Array
                (
                    [id] => B025303E8S
                    [name] => 宏华国际花园城
                    [type] => 商务住宅;住宅区;住宅小区
                    [typecode] => 120302
                    [biz_type] => Array
                        (
                        )

                    [address] => 普莲路中段
                    [location] => 118.400255,24.951351
                    [tel] => Array
                        (
                        )

                    [distance] => 1374
                    [biz_ext] => Array
                        (
                        )

                    [pname] => 福建省
                    [cityname] => 泉州市
                    [adname] => 南安市
                    [importance] => Array
                        (
                        )

                    [shopid] => Array
                        (
                        )

                    [shopinfo] => 0
                    [poiweight] => Array
                        (
                        )
                )
          ...
        )
)
```
