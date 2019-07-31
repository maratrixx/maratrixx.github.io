---
layout:     post
title:      "Golang平滑重启实践"
subtitle:   ""
date:       2019-07-31 12:00:00
author:     "maratrix"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - golang
---



为了实现Golang业务的平滑重载，研究了一下github上比较成熟的解决方案，找到如下三个库：

* **grace**https://github.com/facebookgo/grace/blob/master/gracedemo/demo.go
* **endless** https://github.com/fvbock/endless/tree/master/examples
* **overseer** https://github.com/jpillora/overseer/tree/master/example



大致看了一下源码，**grace**和**endless**是比较像的，实现步骤如下：

> 1. 监听信号
> 2. 收到信号时fork子进程（使用相同的启动命令），将服务监听的socket文件描述符传递给子进程
> 3. 子进程监听父进程的socket，这个时候父进程和子进程都可以接收请求
> 4. 子进程启动成功之后，父进程停止接收新的连接，等待旧连接处理完成（或超时）
> 5. 父进程退出，升级完成



**overseer**是与**grace**和**endless**实现方式有些不同，主要两点：

> 1. overseer添加了Fetcher，当Fetcher返回有效的二进位流(io.Reader) 时，主进程会将它保存到临时位置并验证它，替换当前的二进制文件并启动。
>    Fetcher运行在一个goroutine中，预先会配置好检查的间隔时间。Fetcher支持File、GitHub、HTTP和S3的方式。详细可查看包[package fetcher](https://godoc.org/github.com/jpillora/overseer/fetcher)
>    我们目前线上的环境还是以File为主。
> 2. overseer添加了一个主进程管理平滑重启。子进程处理连接，能够保持主进程pid不变。



贴一下代码，欢迎大家拍砖：

```
package main

import (
	"fmt"
	"net/http"
	"time"
	"github.com/jpillora/overseer"
	"github.com/jpillora/overseer/fetcher"
)
var BuildID = "0"

func prog(state overseer.State) {
	fmt.Printf("app#%s (%s) listening...\n", BuildID, state.ID)
	http.Handle("/", http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		d, _ := time.ParseDuration(r.URL.Query().Get("d"))
		time.Sleep(d)
		fmt.Fprintf(w, "app#%s (%s) says hello\n", BuildID, state.ID)
	}))
	http.Serve(state.Listener, nil)
	fmt.Printf("app#%s (%s) exiting...\n", BuildID, state.ID)
}

//then create another 'main' which runs the upgrades
//'main()' is run in the initial process
func main() {
	overseer.Run(overseer.Config{
		Program: prog,
		Address: ":5001",
		Fetcher: &fetcher.File{Path: "my_app_next"},
		Debug:   false, //display log of overseer actions
	})
}
```



编译命令：`go build -ldflags '-X main.BuildID=456' -o overseer`

**注意：**

* 考虑到线上环境使用supervisor来守护golang进程，**overseer**可实现pid不变**，**更符合我们的需求
* 当编译命令执行后，会起一个新的子进程，接管主进程，废弃之前的子进程，同时保持主进程pid不变
* 线上ci配置要去掉`supervisorctl restart xxx`
* 在main函数里面的`overseer.Run`执行之前，不要做任何的输出操作（`如log 或fmt等`），否则无法监听bin文件的变更。
* `Featcher.File`里面的Path参数为二进制文件的绝对路径，而非二进制文件所在目录的绝对路径，这个要注意。



**一些补充：**

1. 线上环境使用supervisor来守护进程，我们在reload或着发信号重启项目时，可采用通过supervisor去获取进程id:

   ```
   pid=$(sudo supervisorctl status ${BIN_FILE}|awk '{print $4}'|awk -F, '{print $1}')
   ```

   

2. 使用supervisor和overseer实现平滑重启后，项目在发版时候，会有两个状态：

   * 初次发布时，项目还没有启动，此时需要通过supervisor来启动

   * 后面发布时候，需要通过overseer检测或者通过发信号去平滑重启

   我们可以通过 supervisor去做判断，然后给不同的处理：

   ```
   
   function startOrReload() {
       sudo supervisorctl status ${BIN_FILE} |grep RUNNING > /dev/null
       #进程没有运行则start，运行则reload
       if [ $? -ne 0 ]; then
           start
       else
           reload
       fi
   }
   ```

3. 新版ops是使用软链来发布项目，overseer使用软链需要注意：

   * 线上环境通常有两个目录，一个为固定目录current (例如：`/data/web/$project/current`),一个为历史版本目录releases(例如：`/data/web/$project/releases/$tag`)。Fetcher检测的必须为current目录，而非软链的realpath即releases目录:

   ```
   file, err := exec.LookPath(os.Args[0])
   if err != nil {
       panic(err)
   }
   binPath, err := filepath.Abs(file)
   if err != nil {
       panic(err)
   }
   //注意此时binPath为在执行文件使用pwd的path,即要求执行文件的执行目录必须为current的path.
   ```

   * 另外overseer第一次启动后其自身的realpath的path就不再改变了，程序中就不要再出现以当前执行文件的相对路径或者realpath取获取其它路径了，主要影响为配置文件。