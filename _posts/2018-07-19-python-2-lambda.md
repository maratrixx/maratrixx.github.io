---
layout:     post
title:      "Python 学习笔记15-lambda"
subtitle:   ""
date:       2018-07-19 08:56:56
author:     "maratrix"
header-img: "img/home-bg-art.jpg"
tags:
    - python
---

在 Python 中，我们使用 `lambda表达式` 来表示匿名函数。

Python 对匿名函数提供了有限支持。

```
>>> list(map(lambda x:x**2, range(1, 11)))
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

通过对比可以看出，匿名函数 `lambda x:x ** 2` 实际上就是：

```
def f(x):
    return x**2
```

关键字`lambda`表示匿名函数，冒号前面的`x`表示参数。

注意事项：

- lambda 表达式可以接受任何多个参数（包括可选参数等）。
- 匿名函数有个限制，就是只能有一个表达式，不用写`return`, 返回值就是该表达式的结果。
- lambda 函数不能包含命令，包含的表达式不能超过一个。

用匿名函数有个好处，因为函数没有名字，不必担心函数名冲突。此外，匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数：

```
>>> f = lambda x:x**2
>>> f
<function <lambda> at 0x1088c0ea0>
>>> f(5)
25
```

同样，也可以把匿名函数作为返回值返回：

```
def get_func():
    return lambda x, y: x**y

ff = get_func()
print(ff(5, 2))
```