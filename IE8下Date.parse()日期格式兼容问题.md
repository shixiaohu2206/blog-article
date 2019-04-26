---
title: IE8下Date.parse()日期格式兼容问题
date: 2018-08-15
tags:
  - JavaScript
categories:
  - 代码
---

```javascript
/*
	项目中遇到datepicker插件，输出格式为2018-7-18 
	用Date.parse格式化，结果为NaN
	将2018-7-18 转化为2018/7/18，问题得到解决
*/
```
