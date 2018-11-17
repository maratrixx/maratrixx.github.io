---
layout:     post
title:      "Python学习笔记12-列表生成式"
subtitle:   ""
date:       2018-07-12 09:06:01
author:     "maratrix"
header-img: "img/home-bg-o.jpg"
tags:
    - python
---

列表推导式提供了从序列创建列表的简单途径。通常应用程序将一些操作应用于某个序列的每个元素，用其获得的结果作为生成新列表的元素，或者根据确定的判定条件创建子序列。

如果要生成类似 `[1*1, 2*2, 3*3...n*n]` 这样的列表，该如何做？

笨方法当然可以利用`for`循环来实现了，但是太繁琐。

python 提供了列表生成式可以用一行代码来实现。

## 如何写列表生成式

```
>>> [x**2 for x in list(range(11))]
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

写列表生成式时，把要生成的元素 `x**2` 放到前面，后面跟 `for` 循环，就可以吧 list 生成出来。

## 后面加 `if` 条件判断：

```
>>> [x**2 for x in list(range(11)) if x % 2 == 0]
[0, 4, 16, 36, 64, 100]
```

## 两层循环：

```
>>> [x*y for x in list(range(1,4)) for y in ['a', 'b', 'c']]
['a', 'b', 'c', 'aa', 'bb', 'cc', 'aaa', 'bbb', 'ccc']
```

其中的`for y in ['a', 'b', 'c']`类似于我们平时使用`for`循环的外层循环，`for x in list(range(1,4))`相当于内层循环，以此类推。

## 嵌套列表生成式：

利用嵌套列表表达式可以生成二维、三维的列表：

```
>>> [[x*y for x in [1,2,3]] for y in [3,4]]
[[3, 6, 9], [4, 8, 12]]
```

## 使用key-value

```
>>> [(x, y) for x,y in {'a':1, 'b':2}.items()]
[('a', 1), ('b', 2)]
```

## 调用函数

把所有的字符串变成大写：

```
>>> [x.upper() for x in ['Python', 'luaJIT', 'golang']]
['PYTHON', 'LUAJIT', 'GOLANG']
```

## 简单应用

运用列表生成式，可以写出非常简洁的代码。

例如，列出当前目录下的所有文件和目录名，可以通过一行代码实现：

```
>>> import os
>>> [d for d in os.listdir()]
['type.py', '__pycache__', 'feature.py', 'while.py', 'variable.py', 'func.py']
```

## 小结

运用列表生成式，可以快速生成一个`list`，也可以通过一个`list`推导出另一个`list`，代码十分简洁。