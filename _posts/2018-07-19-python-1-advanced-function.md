---
layout:     post
title:      "Python 学习笔记14-高阶函数"
subtitle:   ""
date:       2018-07-19 08:56:32
author:     "maratrix"
header-img: "img/about-bg-walle.jpg"
tags:
    - python
---

## 变量指向函数

函数本身可以赋值给变量，即变量可以指向函数。

```
>>> f = abs
>>> f
<built-in function abs>
>>> f(-100)
100
```

说明变量`f`现在指向了`abs`函数本身，调用`abs()`函数和调用`f()`完全相同。

## 函数名也是变量

函数名其实就是指向函数的变量。

```
>>> abs = 10
>>> abs()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object is not callable
```

把`abs`指向10后，就无法通过`abs(-10)`调用该函数了！因为`abs`这个变量已经不指向求绝对值函数而是指向一个整数10！

**注意：**

由于`abs`函数实际上是定义在`import builtins`模块中的，所以要让修改`abs`变量的指向在其它模块也生效，可以如下操作：

```
>>> import builtins
>>> builtins.abs = 10
>>> type(builtins.abs)
<class 'int'>
```

## 高阶函数

既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为**高阶函数**。

```
>>> def add(x, y, f):
...     return f(x, y)
>>> print(add(-5, 4, max))
4
>>> print(add(-5, 4, min))
-5
```

## 闭包

高阶函数除了可以接受参数外，还可以把函数作为结果值返回。

```
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax += n

        return ax
    return sum

s = lazy_sum(1,2,3,4,5)

print(s())
```

在这个例子中，我们在函数`lazy_sum`中又定义了函数`sum`，并且，内部函数`sum`可以引用外部函数`lazy_sum`的参数和局部变量，当`lazy_sum`返回函数`sum`时，相关参数和变量都保存在返回的函数中，这种称为 `闭包（Closure）`。

**注意:**

- 返回的函数在其定义内部引用了局部变量`args`，所以，当一个函数返回了一个函数后，其内部的局部变量还被新函数引用。

- 另一个需要注意的问题是，返回的函数并没有立刻执行，而是直到调用了`s()`才执行。

```
def count():
    fs = []
    for i in range(1,4):
        def f():
            return i**2
        fs.append(f)
    return fs

f1, f2, f3 = count()

print(f1(), f2(), f3())
```

我们期望的结果是`1 4 9`，实际却是`9 9 9`，原因在于返回的函数引用了变量`i`，但它没有立即执行，等到三个函数都返回时，他们所引用的变量`i`都指向了`3`，因此最终结果都为`9`。

**注意：**

> 返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。


