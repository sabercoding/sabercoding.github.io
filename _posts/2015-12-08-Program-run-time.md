---
layout: article
title: "程序运行时间"
date: 2015-12-08 10:51:00.000000000 +09:00
---

    //程序运行时间
     $starttime = explode('' '',microtime());
     echo microtime();
     /*········以下是代码区·········*/
     for($i=0;$i<1000000;$i++){
      $i;
     }
     /*········以上是代码区·········*/
     //程序运行时间
     $endtime = explode('' '',microtime());
     $thistime = $endtime[0]+$endtime[1]-($starttime[0]+$starttime[1]);
     $thistime = round($thistime,3);
    echo "本网页执行耗时：".$thistime." 秒。".time();

