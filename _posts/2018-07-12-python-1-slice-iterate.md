---
layout:     post
title:      "Python学习笔记11-切片与迭代"
subtitle:   ""
date:       2018-07-12 14:06:01
author:     "maratrix"
header-img: "img/home-bg.jpg"
tags:
    - python
---

## list 切片

取一个 `list` 或者 `tuple` 的部分元素是很常见的操作，笨方法如下：

```
l = list(range(10))
print([l[0], l[1], l[2]])
输出：
[0, 1, 2]
```

python 提供了切片操作符，大大简化了这种操作：

```
L = list(range(10))
print(L[0:3])
```

`L[0:3]`表示，从索引`0`开始取，直到索引`3`为止，但不包括索引`3`。即索引`0，1，2`，正好是3个元素。

如果索引是`0`，还可以省略：

```
print(l[:3])
```

类似的，既然 Python 支持`L[-1]`取倒数第一个元素，那么它同样支持倒数切片:

```
>>> L = list(range(10))
>>> L
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> L[-3:]
[7, 8, 9]
```

记住倒数第一个元素的索引是`-1`。

前 10 个数，每两个取一个：

```
>>> list(range(100))[:10:2]
[0, 2, 4, 6, 8]
```

所有数，每 5 个取一个：

```
>>> list(range(100))[::5]
[0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95]
```

甚至什么都不写，只写`[:]`就可以原样复制一个 list：

```
>>> list(range(10))[:]
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## tuple 切片

与list类似，tuple 也是一种序列，唯一区别是 tuple 不可变。因此，tuple 也可以用切片操作，只是操作的结果仍是 tuple：

```
>>> tuple(range(10))[:5]
(0, 1, 2, 3, 4)
```

## 字符切片

字符串'xxx'也可以看成是一种 list，每个元素就是一个字符。因此，字符串也可以用切片操作，只是操作结果仍是字符串：

```
>>> 'helloworld'[-5:]
'world'
```

在很多编程语言中，针对字符串提供了很多各种截取函数（例如，substring），其实目的就是对字符串切片。Python 没有针对字符串的截取函数，只需要切片一个操作就可以完成，非常简单。


## 迭代

如果给定一个list或者tuple，我们可以通过`for...in`来循环遍历，我们称这种遍历为迭代。

list 这种数据类型虽然有下标，但很多其他数据类型是没有下标的，但是，只要是可迭代对象，无论有无下标，都可以迭代，比如 dict 就可以迭代

```
d = {'a':1, 'b':2, 'c':3}

for k in d:
    print(k)

输出：
a
b
c
```

因为 dict 的存储不是按照 list 的方式顺序排列，所以，迭代出的结果顺序很可能不一样。

默认情况下，dict 迭代的是key，如果想要迭代value可以用`for v in d.values()`，如果要同事迭代key和value，可以用`for k,v in d.items()`。

判断一个对象是否为可迭代对象：

```
>>> from collections import Iterable
>>> isinstance('abc', Iterable)
True
>>> isinstance([1,2,3], Iterable)
True
>>> isinstance((1,2,3), Iterable)
True
>>> isinstance(123, Iterable)
False
```

如何以`key-value`形式遍历list呢，使用`enumerate()`即可：

```
for k, v in enumerate([0,1,2,3,4]):
    print(k, v)

输出：
0 0
1 1
2 2
3 3
4 4
```

## 小结

- Python 的切片非常灵活，一行代码就可以实现很多行循环才能完成的操作。
- 任何可迭代对象都可以作用于`for`循环，包括我们自定义的数据类型，只要符合迭代条件，就可以使用`for`循环。