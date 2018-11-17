---
layout:     post
title:      "Python 学习笔记18-模块"
subtitle:   ""
date:       2018-07-19 08:58:25
author:     "maratrix"
header-img: "img/about-bg-walle.jpg"
tags:
    - python
---

## 什么是模块
在 Python 中，一个 `.py` 文件就是一个模块`module`。

使用模块有什么好处？
- 最大的好处是大大提高了代码的可维护性。
- 其次，编写代码不必从零开始。当一个模块编写完毕，就可以被其他地方引用。
- 使用模块还可以避免函数名和变量名冲突。

举个例子，一个`abc.py`的文件就是一个名字叫`abc`的模块，一个`xyz.py`的文件就是一个名字叫`xyz`的模块。

## 包

假设现在我们`abc`和`xyz`模块与其他模块冲突了，为了避免冲突，我们可以通过`**包**`来组织。

```
maratrix
├── __init__.py
├── abc.py
└── xyz.py
```

引入了包以后，只要顶层的包名不与别人冲突，那所有模块都不会与别人冲突。

现在，`abc.py`模块的名字就变成了`maratrix.abc`，类似的，`xyz.py`的模块名变成了`maratrix.xyz`。

**请注意:**

每一个包目录下面都会有一个`__init__.py`的文件，这个文件是必须存在的，否则，Python 就把这个目录当成普通目录，而不是一个包。`__init__.py`可以是空文件，也可以有 Python 代码，因为`__init__.py`本身就是一个模块，而它的模块名就是`maratrix`。

可以有多级目录：

```
maratrix
├── __init__.py
├── app
│   ├── __init__.py
│   ├── abc.py
│   └── utils.py
└── utils.py
```

文件`abc.py`的模块名就是`maratrix.app.abc`，两个文件`utils.py`的模块名分别是`maratrix.utils`和`maratrix.app.utils`。

**注意：**

> 自己创建模块时要注意命名，不能和 Python 自带的模块名称冲突。例如，系统自带了 sys 模块，自己的模块就不可命名为 sys.py，否则将无法导入系统自带的 sys 模块。

## 使用模块

### import

```
import module1[, module2[,... moduleN]
```

使用模块的第一步就是导入模块：

```
import sys

def pp():
    print(sys.argv)

if __name__ == '__main__':
    pp()
```

当解释器遇到 import 语句，如果模块在当前的搜索路径就会被导入。

一个模块只会被导入一次，不管你执行了多少次 import。这样可以防止导入模块被一遍又一遍地执行。

### from ... import 

Python 的 from 语句让你从模块中导入一个指定的部分到当前命名空间中，语法如下：

```
from modname import name1[, name2[, ... nameN]]
```

### from ... import *

把一个模块的所有内容全都导入到当前的命名空间也是可行的，只需使用如下声明：

```
from modname import *
```

这提供了一个简单的方法来导入一个模块中的所有项目。然而这种声明不该被过多地使用。

## 安装第三方模块

有两种方式安装模块，我们以安装 `Pillow` 为例来介绍。

一般来说，第三方库都会在 Python 官方的 `pypi.python.org` 网站注册，要安装一个第三方库，必须先知道该库的名称，可以在官网或者 pypi 上搜索，比如 Pillow 的名称叫 Pillow。

### 使用 pip 命令：

```
pip intsall Pillow
```

### 使用 pipenv 命令：

```
pipenv install Pillow
```

这里强烈推荐使用`pipenv`神器。


## 模块搜索路径

当我们试图加载一个模块时，Python 会在指定的路径下搜索对应的 `.py` 文件，如果找不到，就会报错：

```
>>> import mymodule
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: No module named mymodule
```

索路径存放在sys模块的path变量中：

```
>>> import sys
>>> sys.path
['', '/usr/local/Cellar/python/3.6.5/Frameworks/Python.framework/Versions/3.6/lib/python36.zip', '/usr/local/Cellar/python/3.6.5/Frameworks/Python.framework/Versions/3.6/lib/python3.6', '/usr/local/Cellar/python/3.6.5/Frameworks/Python.framework/Versions/3.6/lib/python3.6/lib-dynload', '/Users/liyafeng/Library/Python/3.6/lib/python/site-packages', '/usr/local/lib/python3.6/site-packages', '/usr/local/Cellar/numpy/1.14.2/libexec/nose/lib/python3.6/site-packages']
```

如果我们要添加自己的搜索目录，有两种方法：

### 一是直接修改`sys.path`，添加要搜索的目录：

```
>>> import sys
>>> sys.path.append('/Users/liyafeng/my_py_scripts')
```

这种方法是在运行时修改，运行结束后失效。

### 第二种方法是设置环境变量`PYTHONPATH`

该环境变量的内容会被自动添加到模块搜索路径中。设置方式与设置 Path 环境变量类似。注意只需要添加你自己的搜索路径，Python 自己本身的搜索路径不受影响。

## 深入模块

每个模块有各自独立的符号表，在模块内部为所有的函数当作全局符号表来使用。

所以，模块的作者可以放心大胆的在模块内部使用这些全局变量，而不用担心把其他用户的全局变量搞花。

### `__name__`属性

一个模块被另一个程序第一次引入时，其主程序将运行。如果我们想在模块被引入时，模块中的某一程序块不执行，我们可以用`__name__`属性来使该程序块仅在该模块自身运行时执行。

```
# Filename: using_name.py

if __name__ == '__main__':
   print('程序自身在运行')
else:
   print('我来自另一模块')
```


运行结果：

```
$ python using_name.py
程序自身在运行
$ python
>>> import using_name
我来自另一模块
>>>
```

说明： 每个模块都有一个`__name__`属性，当其值是`__main__`时，表明该模块自身在运行，否则是被引入。

## 小结

模块是一组 Python 代码的集合，可以使用其他模块，也可以被其他模块使用。

创建自己的模块时，要注意：

- 模块名要遵循 Python 变量命名规范，不要使用中文、特殊字符；
- 模块名不要和系统模块名冲突，最好先查看系统是否已存在该模块，检查方法是在 Python 交互环境执行`import ***`，若成功则说明系统存在此模块。