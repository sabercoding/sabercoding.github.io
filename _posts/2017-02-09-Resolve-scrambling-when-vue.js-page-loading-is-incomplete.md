---
layout: post
title: "解决vue.js页面加载未完成时乱码"
date: 2017-02-09 02:36:50.000000000 +09:00
---

使用vue时遇到一个问题，vue没有渲染完成时，会短暂的显示不带样式的代码，为了解决这个问题，查找了一遍手册发现可以使用v-cloak。


>这个指令保持在元素上直到关联实例结束编译。和 CSS 规则如 [v-cloak] { display: none } 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。

示例：
``` css
[v-cloak] {
display: none;
}
```

``` html
<div v-cloak>
{{ message }}
</div>
```
v-cloak 编译结束后才会显示，就不会显示没编译的内容了。
