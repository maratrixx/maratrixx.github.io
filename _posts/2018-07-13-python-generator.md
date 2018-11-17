---
layout:     post
title:      "Python学习笔记13-生成器与迭代器"
subtitle:   ""
date:       2018-07-13 11:15:42
author:     "maratrix"
header-img: "img/home-bg-o.jpg"
tags:
    - python
---

## 生成器

当我们需要产生很大的一个列表时候如果使用列表生成式，会占用比较大的内存空间，这时我们一种能够边循环边计算的机制来不断推断后续的元素，我们成为生成器。

## 第一种形式

只要把一个列表生成式的`[]`改成`()`，就创建了一个生成器：

```
>>> it = (x*2 for x in [1,2,3])
>>> it
<generator object <genexpr> at 0x10f81e468>
```

使用`next()`函数获取生成器的下一个返回值。

```
>>> next(it)
2
>>> next(it)
4
>>> next(it)
6
>>> next(it)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

生成器保存的是算法，每次调用`next(g)`，就计算出`it`的下一个元素的值，直到计算到最后一个元素，没有更多的元素时，抛出`StopIteration`的错误。

上面这种使用`next()`是在是太不方便了，我们可以方便的使用`for`循环：

```
>>> it = (x*2 for x in [1,2,3])
>>> for x in it:
...     print(x)
...
2
4
6
```

所以，我们创建了一个生成器 `generator` 后，基本上永远不会调用`next()`，而是通过`for`循环来迭代它，并且不需要关心`StopIteratio`n的错误。

## 第二种形式

如果一个函数包含`yield`关键字，该函数就不在是一个普通函数，而变成一个生成器`generator`：

```
def foo1():
    yield 1
    yield 2
f = foo1()
print(next(f))
print(next(f))
```

这里，最难理解的就是生成器和函数的执行流程不一样。函数是顺序执行，遇到`return`语句或者最后一行函数语句就返回。而变成生成器的函数，在每次调用`next()`的时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行。

同样的，把函数改成生成器后，我们基本上从来不会用`next()`来获取下一个返回值，而是直接使用`for`循环来迭代：

```
def foo1():
    yield 1
    yield 2
f = foo1()
for x in f:
    print(x)
```

但是用`for`循环调用生成器时，发现拿不到生成器的`return`语句的返回值。如果想要拿到返回值，必须捕获`StopIteration`错误，返回值包含在`StopIteration`的`value`中：

```
while True:
    try:
        print(next(o))
    except StopIteration as e:
        print(e.value)
        break
```

## 小结

生成器 `generator` 是非常强大的工具，要理解 `generator` 的工作原理，它是在`for`循环的过程中不断计算出下一个元素，并在适当的条件结束`for`循环。对于函数改成的 `generator` 来说，遇到`return`语句或者执行到函数体最后一行语句，就是结束 `generator` 的指令，`for`循环随之结束。

## 迭代器

我们知道可以直接作用于 `for` 循环的数据类型有：

- 集合数据类型，如`list、tuple、dict、set、str`
- `generator`, 如生成器和带`yield`的函数

这些可以作用于`for`循环的对象，我们统称为可迭代对象：`Iterable`。

可以使用`isinstance()`来判断一个对象是否为`Iterable`对象。

```
>>> from collections import Iterable
>>> isinstance([], Iterable)
True
>>> isinstance((), Iterable)
True
>>> isinstance({}, Iterable)
True
>>> isinstance(set(), Iterable)
True
>>> isinstance('', Iterable)
True
>>> isinstance(123, Iterable)
False
```

而生成器不但可以作用于`for`循环，还可以被`next()`函数不断调用并返回下一个值，直到最后抛出`StopIteration`错误表示无法继续返回下一个值了。

可以被`next()`函数调用并不断返回下一个值的对象称为迭代器：`Iterator`。

```
>>> from collections import Iterator
>>> isinstance([], Iterator)
False
>>> isinstance({}, Iterator)
False
>>> isinstance((), Iterator)
False
>>> isinstance('abc', Iterator)
False
>>> isinstance((x for x in [1,2]), Iterator)
True
```

生成器都是`Iterator`对象，但`list、dict、str`虽然是`Iterable`，却不是`Iterator`。

## 生成迭代器

把`list、dict、str`等`Iterable`变成`Iterator`可以使用`iter()`函数：

```
>>> isinstance(iter('abc'), Iterator)
True
>>> isinstance(iter([]), Iterator)
True
>>> isinstance(iter(()), Iterator)
True
>>> isinstance(iter({}), Iterator)
True
>>> isinstance(iter(set()), Iterator)
True
```

你可能会问，为什么`list、dict、str`等数据类型不是`Iterator`？

这是因为 Python 的`Iterator`对象表示的是一个数据流，`Iterator` 对象可以被`next()`函数调用并不断返回下一个数据，直到没有数据时抛出`StopIteration`错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过`next()`函数实现按需计算下一个数据，所以`Iterator`的计算是惰性的，只有在需要返回下一个数据时它才会计算。

`Iterator`甚至可以表示一个无限大的数据流，例如全体自然数。而使用 `list` 是永远不可能存储全体自然数的。

## 小结

- 凡是可作用于`for`循环的对象都是`Iterable`类型；

- 凡是可作用于`next()`函数的对象都是`Iterator`类型，它们表示一个惰性计算的序列；

- 集合数据类型如`list、tuple、dict、str`等是`Iterable`但不是`Iterator`，不过可以通过`iter()`函数获得一个`Iterator`对象。

