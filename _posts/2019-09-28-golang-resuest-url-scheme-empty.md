---
layout:     post
title:      "Go采坑系列|为什么request.URL.Scheme取不到值"
subtitle:   ""
date:       2019-09-28 12:00:00
author:     "maratrix"
header-img: "img/home-bg-art.jpg"
catalog: true
tags:
    - golang
    - Go采坑系列
---



## 遇到的问题

最近在阅读`echo`框架的源码，发现`context.go`文件在读取请求的`scheme`时是单独封装了个方法。就很奇怪，go语言标准库不是自带了方法吗，干嘛不用？

于是写了段代码来验证：

```
func main() {
	http.HandleFunc("/foo", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintln(w, "Scheme:", r.URL.Scheme)
	})
	log.Fatal(http.ListenAndServe(":9000", nil))
}
```

当我们请求`http://127.0.0.1:9000/foo`时期待的输出是：

```
Scheme: http
```

然而，真实的输出却是这样的：

```
Scheme:
```

有没有很意外？



## 什么原因

那是什么原因导致的呢？

其实标准库文档有提到，只是大家一般不会注意到（比如像我），只有踩到这个“坑”了才会去研究下。

查看标准库`http.Request`注释：

```
type Request struct {
	//***

	// URL specifies either the URI being requested (for server
	// requests) or the URL to access (for client requests).
	//
	// For server requests, the URL is parsed from the URI
	// supplied on the Request-Line as stored in RequestURI.  For
	// most requests, fields other than Path and RawQuery will be
	// empty. (See RFC 7230, Section 5.3)
	//
	// For client requests, the URL's Host specifies the server to
	// connect to, while the Request's Host field optionally
	// specifies the Host header value to send in the HTTP
	// request.
	URL *url.URL
	//***
```

注意看第二段注释，简单解释下就是，对于服务端收到的请求，`http.Request`只会解析请求行到`url.URL`，也就是说`url.Path`和`url.RawQuery`之外的字段都是空的。

另外，在google groups也有讨论：

[http.req.URI.Scheme inside is empty?](https://groups.google.com/forum/#!topic/golang-nuts/pMUkBlQBDF0)



## 如何解决

比较推荐的做法是判断`request.TLS`是否为` nil`，不为空即为`https`，为空即为`http`。

或者，可以参考`echo`框架的实现来处理：

```
func (c *context) Scheme() string {
	// Can't use `r.Request.URL.Scheme`
	// See: https://groups.google.com/forum/#!topic/golang-nuts/pMUkBlQBDF0
	if c.IsTLS() {
		return "https"
	}
	if scheme := c.request.Header.Get("X-Forwarded-Proto"); scheme != "" {
		return scheme
	}
	if scheme := c.request.Header.Get("X-Forwarded-Protocol"); scheme != "" {
		return scheme
	}
	if ssl := c.request.Header.Get("X-Forwarded-Ssl"); ssl == "on" {
		return "https"
	}
	if scheme := c.request.Header.Get("X-Url-Scheme"); scheme != "" {
		return scheme
	}
	return "http"
}
```

在github issues上也有讨论，地址在这里：

[net/http: Request.URL.Scheme returns an empty string. No alternative way present to get request url scheme.](https://github.com/golang/go/issues/28940)

## 小结

作为服务端接受的请求，获取`Scheme`需要特殊处理下；

作为客户端发送的请求，可以正常通过`request.URL.Scheme`取到发送请求的`Scheme`；