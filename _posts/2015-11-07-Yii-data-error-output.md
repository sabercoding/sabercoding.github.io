---
layout: post
title: "yii数据错误输出"
date: 2015-11-07 07:28:00.000000000 +09:00
---

    $record->setAttributes($data);
    $record->save();
    var_dump($record->getErrors());exit;`