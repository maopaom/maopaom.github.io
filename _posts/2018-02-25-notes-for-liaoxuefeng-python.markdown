---
layout: default
title:  "廖雪峰python教程"
date:   2018-02-25 23:08:49 +0800
categories: jekyll update
---

# 高级特性<br/>

## 迭代<br/>

那么，如何判断一个对象是可迭代对象呢？方法是通过collections模块的Iterable类型判断：


	>>> from collections import Iterable
	>>> isinstance('abc', Iterable) # str是否可迭代
	True
	>>> isinstance([1,2,3], Iterable) # list是否可迭代
	True
	>>> isinstance(123, Iterable) # 整数是否可迭代
	False

---------

如果要对list实现类似Java那样的下标循环怎么办？Python内置的enumerate函数可以把一个list变成索引-元素对，这样就可以在for循环中同时迭代索引和元素本身：


	for i, value in enumerate(['A', 'B', 'C']):
	...     print(i, value)
	...
	0 A
	1 B
	2 C
