---
layout: article
title: "构造函数与析构函数执行顺序，记一次小bug修复。 "
date: 2017-03-24 08:16:34.000000000 +09:00
tags: php
---

```
class Test {
    protected $client;
    protected static $name;

    public function __construct() {
        self::$name = "test";
    }

    public function __destruct() {
        self::$name = null;
    }

    public function get() {
        return self::$name;
    }
}
$a = new Test();
$a = (new Test());
echo $a->get();
```
执行完以上代码时输出：
```

```
此时你会发现，输出的是空，而没有输出test。修改代码，查看下整个流程。
```
class Test {
    protected $client;
    protected static $name;

    public function __construct() {
        self::$name = "test";
        echo "执行构造函数!\\n";
    }

    public function __destruct() {
        self::$name = null;
        echo "执行析构函数!\\n";
    }

    public function get() {
        return self::$name;
    }
}
$a = new Test();
echo "第一次new完毕!\\n";
$a = (new Test());
echo "第二次new完毕!\\n";
echo $a->get();
```
执行输出：
```
执行构造函数!
第一次new完毕!
执行构造函数!
执行析构函数!
第二次new完毕!
执行析构函数!
```
终于逮到你了。。。当新创建类赋值于同一个变量时，到底是先创建，还是先销毁呢？是先创建新对象，然后再销毁旧对象。这就导致了先执行构造函数，然后再执行析构函数。在这一过程中切记不要操作对象内的静态属性，否则会导致静态属性丢失。
