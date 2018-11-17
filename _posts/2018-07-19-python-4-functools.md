---
layout:     post
title:      "Python 学习笔记17-偏函数"
subtitle:   ""
date:       2018-07-19 08:57:56
author:     "maratrix"
header-img: "img/about-bg-walle.jpg"
tags:
    - python
---

Python 的`functools`模块提供了很多有用的功能，其中一个就是`偏函数（Partial function）`。

在介绍函数参数的时候，我们讲到，通过设定参数的默认值，可以降低函数调用的难度。而偏函数也可以做到这一点。

int()函数可以把字符串转换为整数，当仅传入字符串时，int()函数默认按十进制转换：

```
>>> int('123')
123
```

但int()函数还提供额外的base参数，默认值为10。如果传入base参数，就可以做 N 进制的转换：

```
>>> int('123', base=8)
83
>>> int('123', 16)
291
```

假设要转换大量的二进制字符串，每次都传入`int(x, base=2)`非常麻烦，于是，我们想到，可以定义一个`int2(`)的函数，默认把`base=2`传进去：

```
def int2(x, base=2):
    return int(x, base)

>>> int2('1000000')
64
>>> int2('1010101')
85
```


`functools.partial`就是帮助我们创建一个偏函数的，不需要我们自己定义`int2()`，可以直接使用下面的代码创建一个新的函数`int2`:

```
>>> import functools
>>> int2 = functools.partial(int, base=2)
>>> int2('1000000')
64
>>> int2('1010101')
85
```

所以，简单总结`functools.partial`的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。

注意到上面的新的`int2`函数，仅仅是把`base`参数重新设定默认值为`2`，但也可以在函数调用时传入其他值：

```
>>> int2('1000000', base=10)
1000000
```

## 小结

当函数的参数个数太多，需要简化时，使用`functools.partial`可以创建一个新的函数，这个新函数可以固定住原函数的部分参数，从而在调用时更简单。
