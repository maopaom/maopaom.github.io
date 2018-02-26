---
layout: default
title:  "廖雪峰python教程"
date:   2018-02-25 23:08:49 +0800
categories: Notes
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

---------

##列表生成器<br/>

	>>> [x * x for x in range(1, 11)]
	[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

---------

for循环后面还可以加上if判断

	>>> [x * x for x in range(1, 11) if x % 2 == 0]
	[4, 16, 36, 64, 100]

---------

还可以使用两层循环，可以生成全排列

	>>> [m + n for m in 'ABC' for n in 'XYZ']
	['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']

---------

## 生成器(generation)<br/>

要创建一个generator
第一种方法：把一个列表生成式的[]改成()

	>>> L = [x * x for x in range(10)]
	>>> L
	[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
	>>> g = (x * x for x in range(10))
	>>> g
	<generator object <genexpr> at 0x1022ef630>

可以通过next(g)获得下一个返回值，最后一个元素后会抛出‘StopIteration’的错误
generator是可迭代对象

---------

第二种方法：如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator

	def fib(max):
	    n, a, b = 0, 0, 1
	    while n < max:
	        yield b
	        a, b = b, a + b
	        n = n + 1
	    return 'done'

*generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行*

使用for循环来迭代

	>>> for n in fib(6):
	...     print(n)
	...
	1
	1
	2
	3
	5
	8

发现拿不到generator的return语句的返回值。如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的value中

	g = fib(6)
	>>> while True:
	...     try:
	...         x = next(g)
	...         print('g:', x)
	...     except StopIteration as e:
	...         print('Generator return value:', e.value)
	...         break
	...
	g: 1
	g: 1
	g: 2
	g: 3
	g: 5
	g: 8
	Generator return value: done

---------

## 迭代器

可以直接作用于for循环的对象统称为可迭代对象：Iterable
可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator
生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator
把list、dict、str等Iterable变成Iterator可以使用iter()函数

	>>> isinstance(iter([]), Iterator)
	True
	>>> isinstance(iter('abc'), Iterator)
	True

---------

Python的for循环本质上就是通过不断调用next()函数实现的

	for x in [1, 2, 3, 4, 5]:
	    pass

实际上完全等价于

	# 首先获得Iterator对象:
	it = iter([1, 2, 3, 4, 5])
	# 循环:
	while True:
	    try:
	        # 获得下一个值:
	        x = next(it)
	    except StopIteration:
	        # 遇到StopIteration就退出循环
	        break

---------

## 高阶函数(Higher-order function)

一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。

### map()

map()函数接收两个参数，一个是函数，一个是Iterable，map将传入的函数依次作用到序列的每个元素，并把结果作为新的Iterator返回。

	>>> list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
	['1', '2', '3', '4', '5', '6', '7', '8', '9']

map()作为高阶函数，事实上它把运算规则抽象了

### reduce()

reduce把一个函数作用在一个序列[x1, x2, x3, ...]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算
等价于：

	reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)

*from functools import reduce*

---------

eg：
	from functools import reduce

	DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

	def str2int(s):
	    def fn(x, y):
	        return x * 10 + y
	    def char2num(s):
	        return DIGITS[s]
	    return reduce(fn, map(char2num, s))

---------





