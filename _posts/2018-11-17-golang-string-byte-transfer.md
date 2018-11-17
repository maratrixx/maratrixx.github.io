---
layout:     post
title:      "高性能实现string和[]byte互转"
subtitle:   ""
date:       2018-11-17 09:00:00
author:     "maratrix"
header-img: "img/post-bg-unix-linux.jpg"
tags:
    - golang
---

### 问题

在 golang 语言中，`string` 和 `[]byte` 变量之间可以很方便的互转，得益于他们的数据结构很相似，底层都是通过一个数组保存元素的:

```
s := "hello world"
b := []byte(s)
s = string(b)
```

通常情况下，这种转换是对底层数组的复制，所以对转换后的 slice 的更改不会影响原来的字符串，这也保证了字符串的不可变。

但是，数组的复制是有代价的，内存的分配和数据的拷贝以及垃圾回收都会带来性能等开销，所以在追求性能的场合，比如一些Web框架中，可以采用来了一些"花招"实现"零拷贝"。

### 高性能实现

首先我们看看 stirng 和 slice 的数据结构是怎么样的：

```
type SliceHeader struct {
        Data uintptr
        Len  int
        Cap  int
}

type StringHeader struct {
        Data uintptr
        Len  int
}
```

看起来结构类似，不过 slice 对 string 多了一个 Cap 字段。

所以我们可以根据它们的结构进行转换，不需要拷贝底层的数据 Data :

```
// []byte 转 string
func Bytes2String(b []byte) string {
    return *(*string)(unsafe.Pointer(&b))
}

// string 转 []byte
func String2Bytes(s string) []byte {
    x := (*[2]uintptr)(unsafe.Pointer(&s))
    h := [3]uintptr{x[0], x[1], x[1]}
    return *(*[]byte)(unsafe.Pointer(&h))
}

```

### 压测效果

使用 golang 自带工具 `go test` 进行压测对比：

```
package convert

import "testing"

func BenchmarkBytes2String(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Bytes2String([]byte{'h', 'e', 'l', 'l', 'o'})
    }
}

func BenchmarkBytes2String2(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Bytes2String2([]byte{'h', 'e', 'l', 'l', 'o'})
    }
}

func BenchmarkString2Bytes(b *testing.B) {
    for i := 0; i < b.N; i++ {
        String2Bytes("hello")
    }
}

func BenchmarkString2Bytes2(b *testing.B) {
    for i := 0; i < b.N; i++ {
        String2Bytes2("hello")
    }
}

// 压测结果
BenchmarkBytes2String-4         2000000000               0.33 ns/op
BenchmarkBytes2String2-4        200000000                6.62 ns/op
BenchmarkString2Bytes-4         2000000000               0.33 ns/op
BenchmarkString2Bytes2-4        200000000                6.88 ns/op
```

可以看出，性能提升还是很明显的。

### 最后

以上，是高性能实现 `string` 和 `[]byte` 互转的小技巧，在一些追求性能的场合可以借鉴使用，比如有名的 `rpcx` 框架内就是使用这种转换方式。