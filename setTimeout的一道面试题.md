---
title: setTimeout的一道面试题
date: 2017-10-16
tags:
  - JavaScript
categories:
  - 代码
---

```JavaScript
// setTimeout的一道面试题

for( var i = 0;i<5;i++) {
    setTimeout(function() {
        console.log(i);
    },1000)
}//5,5,5,5,5
```

> 为什么会输出 5,5,5,5,5，而不是 0,1,2,3,4，因为 setTimeout 在 for 循环中异步的执行，将输出打印的操作，队列在 for 循环执行完之后。

**删除 js 数组中的某个元素**

```JavaScript
var _arr = ["a", "b", "c", "ALL"];
var _str = _arr.join(",");
var _index = _str.indexOf("ALL"); // 获取"ALL"的位置
_arr.splice(_index, 1); // 去除该元素（会改变原数组）
console.log(_arr); // ["a", "b", "c"]
```
