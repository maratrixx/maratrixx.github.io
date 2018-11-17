---
layout:     post
title:      "Python学习笔记10-函数"
subtitle:   ""
date:       2018-07-11 09:06:01
author:     "maratrix"
header-img: "img/home-bg-o.jpg"
tags:
    - python
---

## 函数定义

- 函数代码块以 `def` 开头，后接函数标识符名称和圆括号 `()`
- 圆括号之间用于定义参数
- 函数的第一行代码可以选择性地使用文档字符串来存放函数说明
- 函数内容以冒号 `:` 开始，并且缩进
- `return` 表达式结束函数，不带表达式的 `return` 或者没有 `return` 相当于返回 `None`

## 语法

```
def 函数名():
    函数体
```

## 空函数

如果想定义一个什么事也不做的空函数，可以用pass语句：

```
def nop():
    pass
```

pass 语句什么都不做，那有什么用？实际上 pass 可以用来作为占位符，比如现在还没想好怎么写函数的代码，就可以先放一个 pass，让代码能运行起来。

## 函数调用

```
def say_hello(name = 'maratrix'):
    print('I am ', name.title())
    return

say_hello()
```

## 多返回值

Python 函数支持多返回值：

```
def my_info():
    return 'hanson', 18

name, age = my_info()
print(name, age)
```

但其实这只是一种假象，Python 函数返回的仍然是单一值：

```
def my_info():
    return 'hanson', 18

print(my_info())

输出：('hanson', 18)
```

原来返回值是一个 tuple！但是，在语法上，返回一个 tuple 可以省略括号，而多个变量可以同时接收一个 tuple，按位置赋给对应的值。

所以，Python 的函数返回多值其实就是返回一个 tuple，但写起来更方便。

## 参数传递

Python 的函数定义非常简单，但灵活度却非常大。除了正常定义的必选参数外，还可以使用默认参数、可变参数、关键字参数和命名参数，使得函数定义出来的接口，不但能处理复杂的参数，还可以简化调用者的代码。

### 变量没有类型

在 python 中，类型属于对象，变量没有类型。

```
a = [1,2,3]
a = 'foo'
```

以上代码中，[1,2,3] 是 List 类型，"foo" 是 String 类型，而变量 a 是没有类型，她仅仅是一个对象的引用（一个指针），可以是指向 List 类型对象，也可以是指向 String 类型对象。

### 可更改 (mutable) 与不可更改 (immutable) 对象

在 python 中，strings, tuples, 和 numbers 是不可更改的对象，而 list,dict 等则是可以修改的对象。

- 不可变类型：变量赋值 a=5 后再赋值 a=10，这里实际是新生成一个 int 值对象 10，再让 a 指向它，而 5 被丢弃，不是改变 a 的值，相当于新生成了 a。

- 可变类型：变量赋值 la=[1,2,3,4] 后再赋值 la[2]=5 则是将 list la 的第三个元素值更改，本身 la 没有动，只是其内部的一部分值被修改了。

python 函数的参数传递：

- 不可变类型：类似 c++ 的值传递，如 整数、字符串、元组。如 fun（a），传递的只是 a 的值，没有影响 a 对象本身。比如在 fun（a）内部修改 a 的值，只是修改另一个复制的对象，不会影响 a 本身。

- 可变类型：类似 c++ 的引用传递，如 列表，字典。如 fun（la），则是将 la 真正的传过去，修改后 fun 外部的 la 也会受影响

> python 中**一切都是对象**，严格意义我们不能说值传递还是引用传递，我们应该说传不可变对象和传可变对象。

### 参数种类

- 必选参数
- 关键字参数
- 默认参数
- 可变参数
- 命名参数

### 必选参数

必选参数必须以正确的顺序传入函数，调用时的数量必须和声明时的一样。

```
def say_hello(name, age):
    print('name is: ', name, 'age is:', age)

say_hello('maratrix', 18)

输出：name is:  maratrix age is: 18
```

### 关键字参数

使用关键字参数允许函数调用时参数的传递顺序和声明时不一样，Python 解释器会自动用参数名匹配参数值。

```
def say_hello(name, age):
    print('name is: ', name, '，age is:', age)

say_hello(age = 20, name='Monica')

输出：ame is:  Monica age is: 20
```

### 默认参数

调用函数时，如果没有传递参数，则会使用默认参数。

```
def say_hello(name, age = 18):
    print('name is: ', name, '，age is:', age)

say_hello('Monica')

输出：name is:  Monica ，age is: 18
```

**默认参数有个大坑，演示如下：**

```
def add_list(l = []):
    l.append('end')
    print(l)

```

正常调用时，貌似没问题：

```
add_list([1,2,3])
add_list(['a', 'b', 'c'])

输出：
[1, 2, 3, 'end']
['a', 'b', 'c', 'end']
```

当你第一次使用默认参数时候也是正确的：

```
add_list()
输出：['end']
```

但是，当你再次调用`add_list()`时候，就不对了：

```
add_list()
输出：['end', 'end']
```

咦？神奇的事情发生了，默认参数是`[]`，但是函数似乎每次都 "记住了" 上次添加了`'end'`后的 `list`。

**原因解释如下：**

Python 函数在定义的时候，默认参数`l`的值就被计算出来了，即`[]`，因为默认参数`l`也是一个变量，它指向对象`[]`，每次调用该函数，如果改变了`l`的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的`[]`了。


**因此，必须牢记：**

> 定义默认参数要牢记一点：默认参数必须指向不可变对象！！！

### 可变参数

在 Python 函数中，还可以定义可变参数。顾名思义，可变参数就是传入的参数个数是可变的，可以是 1 个、2 个到任意个，还可以是 0 个。

可以再参数名前加星号`*`接受多个参数，参数会以元组的形式传递：

```
def more_params(*params):
    print(params)

more_params(1, 2.3, False, [], {})

输出：(1, 2.3, False, [], {})
```

定义可变参数和定义一个 list 或 tuple 参数相比，仅仅在参数前面加了一个`*`号。在函数内部，参数接收到的是一个 tuple，因此，函数代码完全不变。但是，调用该函数时，可以传入任意个参数，包括 0 个参数。

如果已经有了一个list或者tuple，要调用一个可变参数怎么办？

```
more_params(*[1,2,3])
more_params(*(4,5,6))
```

`*参数名`表示把`参数名`这个序列的所有元素作为可变参数传进去。这种写法相当有用，而且很常见。

#### 关键字可变参数

可变参数允许你传入 0 个或任意个参数，这些可变参数在函数调用时自动组装为一个 tuple。而关键字参数允许你传入 0 个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个 dict。

```
def more_params(**params):
    print(params)

more_params(name = 'hanson', age = 18)

输出：{'name': 'hanson', 'age': 18}
```

关键字参数有什么用？它可以扩展函数的功能。比如，在`more_params`函数里，我们保证能接收到name和age这两个参数，但是，如果调用者愿意提供更多的参数，我们也能收到。

试想你正在做一个用户注册的功能，除了用户名和年龄是必填项外，其他都是可选项，利用关键字参数来定义这个函数就能满足注册的需求。

和可变参数类似，如果已经有了一个dict，要调用一个可变关键字参数怎么办？

```
d = {'name':'Maratrix', 'hobby': 'python', 'sex':'male'}
more_params(**d)

输出：{'name': 'Maratrix', 'hobby': 'python', 'sex': 'male'}
```

`**d`表示把d这个 dict 的所有 key-value 用关键字参数传入到函数的`**params`参数，`params`将获得一个 dict，注意`params`获得的 dict 是`d`的一份拷贝，对`params`的改动不会影响到函数外的`d`。

### 命名关键字参数

对于关键字参数，函数的调用者可以传入任意不受限制的关键字参数，至于到底传入了哪些参数，需要在函数内部进行检查。

```
def person(name, age, **kw):
    if 'city' in kw:
        # 判断是否有city参数
        pass

    if 'job' in kw:
        # 判断是否有job参数
        pass
    
    print(name, age, kw)

person('hanson', 18, city = 'BJ', job = 'IT')
```

但是，调用者仍然可以传入不受限制的关键字参数：

```
person('hanson', 18, city = 'BJ', addr = 'beijing')
```

如果想要限制关键字参数名字，就可以使用命名关键字参数。

例如，只接收`city`和`job`作为关键字参数：

```
def person(name, age, *, city, job, **kw):
    print(name, age, city, job, kw)

person('hanson', 18, city = 'BJ', job = 'it', addr = 'beijing')
```

和关键字参数`**kw`不同，命名关键字参数需要一个特殊分隔符`*`，`*`后面的参数被视为命名关键字参数。

如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符`*`了：

```
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
```

命名关键字参数必须传入参数名，这和位置参数不同，如果没有传入将会报错：

```
def person(name, age, *, city, job, **kw):
    print(name, age, city, job, kw)
person('hanson', 18, city = 'BJ', adr = 'beijing')

输出：
Traceback (most recent call last):
  File "code/func.py", line 30, in <module>
    person('hanson', 18, city = 'BJ', adr = 'beijing')
TypeError: person() missing 1 required keyword-only argument: 'job'
```

命名关键字参数可以有默认值，从而简化调用：

```
def person(name, age, *, city, job = 'it...', **kw):
    print(name, age, city, job, kw)
person('hanson', 18, city = 'BJ', adr = 'beijing')

hanson 18 BJ it... {'adr': 'beijing'}
```

**注意：**

> 使用命名关键字参数时，要特别注意，如果没有可变参数，就必须加一个*作为特殊分隔符。如果缺少*，Python 解释器将无法识别位置参数和命名关键字参数。

```
def person(name, age, city, job):
    # 缺少 *，city和job被视为位置参数
    pass
```

## 参数组合

在 Python 中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这 5 种参数都可以组合使用。但是请注意，参数定义的顺序必须是：`必选参数` -> `默认参数` -> `可变参数` -> `命名关键字参数` -> `关键字参数`。

```
def f2(a, b, c=0, *args, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'd =', d, 'kw =', kw)
```

对于任意函数，都可以通过类似`func(*args, **kw)`的形式调用它，无论它的参数是如何定义的:

```
foo(*[1, 2, 3, 4, 5], **{'d':'dd', 'e':'ee', 'f':'ff'})

输出：
a = 1 b = 2 c = 3 args = (4, 5) d = dd kw = {'e': 'ee', 'f': 'ff'}
```

**注意：**

> 虽然可以组合多达 5 种参数，但不要同时使用太多的组合，否则函数接口的可理解性很差。

## 匿名函数

python 使用 `lambda` 来创建匿名函数。

所谓匿名，意即不再使用 `def` 语句这样标准的形式定义一个函数:

- `lambda` 只是一个表达式，函数体比 `def` 简单很多。
- `lambda` 的主体是一个表达式，而不是一个代码块。仅仅能在 `lambda` 表达式中封装有限的逻辑进去。
- `lambda` 函数拥有自己的命名空间，且不能访问自己参数列表之外或全局命名空间里的参数。
- 虽然 `lambda` 函数看起来只能写一行，却不等同于 `C` 或 `C++` 的内联函数，后者的目的是调用小函数时不占用栈内存从而增加运行效率。

### 语法

lambda 函数的语法只包含一个语句:

```
lambda [arg1 [,arg2,.....argn]]:expression
```

比如：

```
sum = lambda a, b: a + b
print(sum(1, 2))
print(sum(3, 4))

3
7
```

## 变量作用域

Python 的作用域一共有 4 中，分别是：

- L （Local） 局部作用域
- E （Enclosing） 闭包函数外的函数中
- G （Global） 全局作用域
- B （Built-in） 内建作用域

以 L --> E --> G -->B 的规则查找，即：在局部找不到，便会去局部外的局部找（例如闭包），再找不到就会去全局找，再者去内建中找。

Python 除了 `def/class/lambda` 外，其他如: `if/elif/else/ try/except for/while` 并不能改变其作用域。定义在他们之内的变量，外部还是可以访问。

```
x = int(2.9)  # 内建作用域
 
g_count = 0  # 全局作用域
def outer():
    o_count = 1  # 闭包函数外的函数中
    def inner():
        i_count = 2  # 局部作用域
```

## global 和 nonlocal 关键字

当内部作用域想修改外部作用域的变量时，就要用到 `global` 和 `nonlocal` 关键字了。

修改全局变量：

```
num = 1
def fun1():
    global num  # 需要使用 global 关键字声明
    print(num) 
    num = 123
    print(num)
fun1()
print(num)
```

如果要修改嵌套作用域（enclosing 作用域，外层非全局作用域）中的变量则需要 nonlocal 关键字了：

```
def outer():
    num = 10
    def inner():
        nonlocal num   # nonlocal 关键字声明
         num = 100
        print(num)
    inner()
    print(num)
outer()
```


## 小结

- 默认参数一定要用不可变对象，否则容易出错。
- `*args` 是可变参数， `args` 接受的是一个 `tuple`。
- `**kw` 是关键字参数，`kw` 接收的是一个 `dict`。
- 命名的关键字参数是为了限制调用者可以传入的参数名，同时可以提供默认值。
- 定义命名的关键字参数在没有可变参数的情况下不要忘了写分隔符*，否则定义的将是位置参数。
- 访问变量时注意作用域链。