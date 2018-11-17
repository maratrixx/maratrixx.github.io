---
layout:     post
title:      "Python 学习笔记09-条件语句与循环"
subtitle:   ""
date:       2018-07-09 09:30:42
author:     "maratrix"
header-img: "img/home-bg-o.jpg"
tags:
    - python
---

## 条件语句

python 中使用 `if-elif-else` 条件语句来执行代码块。

**注意：**
- 每个条件后面使用`:`来表示满足条件后执行的代码块。
- 使用缩进来划分语句块，相同缩进的语句一起组成一个语句块。
- python 中没有 `switch-case` 语句。

```
>>> age = 20
>>> if age >= 18:
...     print('your age is: ', age)
...
your age is:  20
```

**注意不要少写了冒号`:`。**

> 在条件语句中，只要是非零数值、非空字符串、非空 list 等，就判断为True，否则为False。


## 循环语句

python 中的循环语句有 `for` 和 `while`。

## while 循环

需要使用冒号`:`来缩进：

```
total, num = 0, 1

while num <= 100:
    total += num
    num += 1

print('1+2+3+...+100 = ', total)
```

**注意：**在 python 中没有 `do...while` 语句。

## while 循环使用else

`while...else` 在条件为 `False` 时候执行 `else` 的语句块：

```
aa = 1
while aa < 3:
    print('aa is: ', aa)
    aa += 1
else:
    print('while end')
```

执行结果：

```
aa is:  1
aa is:  2
while end
```

## 简单语句

类似 if 语句的语法，如果你的 while 循环体中只有一条语句，你可以将该语句与 while 写在同一行中:

```
while True: print('test code')
```

## for 语句

Python for 循环可以遍历任何序列的项目，如一个列表或者一个字符串。

```
l = [1,2,3]
for x in l:
    print(x, end='\t')

for x in 'hello':
    print(x, end=' ')
```

## for 中使用 else

类似 `while...else` dfor 循环中也可以有 else 子句，它在穷尽列表 (以 for 循环) 或条件变为 false (以 while 循环) 导致循环终止时被执行, 但循环被 break 终止时不执行。

```
l = [1,2,3]
for x in l:
    print(x, end='\t')
else:
    print('for end')
```

## break 和 continue 语句及循环中的 else 子句

- break 语句可以跳出 for 和 while 的循环体。
- continue 语句被用来告诉 Python 跳过当前循环块中的剩余语句，然后继续进行下一轮循环。
- 循环语句可以有 else 子句，但循环被 break 终止时不执行。

## pass 语句

Python pass 是空语句，是为了保持程序结构的完整性。

pass 不做任何事情，一般用做占位语句：

```
while True:
    pass

for x in [1,2]:
    pass
```

## 小结

- `break` 语句可以在循环过程中直接退出循环
- `continue` 语句可以提前结束本轮循环
- 循环语句可以有 `else` 子句，但循环被 `break` 终止时不执行
