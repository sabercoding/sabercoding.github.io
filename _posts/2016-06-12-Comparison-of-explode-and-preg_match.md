---
layout: article
title: "explode和preg_match比较"
date: 2016-06-12 13:42:47.000000000 +09:00
---

    <?php
        $starttime = explode('' '',microtime());
        $a="/tmp/trunk/trunk/app/controllers/Abstract.php - Unexpected token: class, line: 3, col: 14, file: /tmp/trunk/trunk/app/controllers/Abstract.php.";
        // $b=explode('':'', explode(''-'', $a)[1])[0];

        preg_match(''/(\\w+ \\w+):/'', $a, $b);

        $endtime = explode('' '',microtime());
        $thistime = $endtime[0]+$endtime[1]-($starttime[0]+$starttime[1]);
        echo $thistime;

explode执行时间：
4.2915344238281E-5[Finished in 0.1s]
preg_match执行时间：
0.00010108947753906[Finished in 0.1s]
所以，之后要是使用截取所需的单一字符还是用explode这个函数就好了。
