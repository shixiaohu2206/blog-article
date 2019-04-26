---
title: PHP usort自定义排序规则
date: 2018-08-16
tags:
  - PHP
categories:
  - 代码
---

```php
<?php

	$demo = [0,1,2,3];

	usort($demo , "mySort");

	function mySort($a, $b) {

			$sort = $a - $b;

			/* 注释：如果两个元素比较结果相同。
			则它们在排序后的数组中的顺序未经定义。
			到 PHP 4.0.6 之前，用户自定义函数将保留这些元素的原有顺序。
			但是由于在 4.1.0 中引进了新的排序算法，结果将不是这样了，
			因为对此没有一个有效的解决方案。*/
			if ($sort == 0) return 0;

			// 返回1，将$a排在$b的后面
			return $sort > 0 ? 1 : -1;
}
```
