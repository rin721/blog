---
title: 教泥 Go 分布式入门喵！
published: 2026-04-11
description: 既然你连分布式都搞不明白，本喵就大发慈悲，勉为其难用最简单的 Go 代码点醒你吧！才不是特意为你写的呢！
image: ''
tags: [Go, Distributed Systems, RPC, Tsundere Cat, Tutorial]
category: Nyang's Programming Classroom
draft: false 
lang: zh-CN
---

哈？“分布式”？这种到处都在吹的基础概念，你居然到现在还搞不明白喵？(¬_¬) 

真是个笨蛋……听好了，本喵今天心情好，就勉为其难地给你开个小灶。你可别误会了，本喵才不是特意为了教你才写这篇博客的，只是看你一直抓耳挠腮的样子实在太碍眼了喵！

把耳朵竖起来听好：**所谓的分布式，根本不是什么魔法！说白了，就是“一只喵干不完的活，通过网络打电话叫另一只喵来帮忙”喵！**

在 Go 语言里，最简单的“打电话”方式就是 **RPC（远程过程调用）**。你只需要记住，它能让你在一台电脑上，像调用本地函数一样，去调用另一台电脑上的函数。

现在，本喵就带你写一个“老板喵”和“打工喵”的例子。眼睛别眨，本喵只演示一次！

### 第一步：被迫营业的“打工喵” (服务端)

首先，我们需要一只在后台默默干活的打工喵。它什么都不用管，只需要竖起耳朵听端口里的网络请求，然后帮忙算算今天能吃多少个小鱼干罐头就行了。

新建一个文件叫 `server.go`，给我一字不差地敲进去喵：

```go
package main

import (
	"fmt"
	"net"
	"net/rpc"
)

// 笨蛋，这是我们要通过网络传递的数据包喵！
type CansArgs struct {
	A, B int
}

// 这是我们的打工喵服务
type NyangService struct{}

// 打工喵的核心技能：算乘法。
// 记住了，Go RPC 的方法签名必须长这样：(参数, 返回值指针) error！写错打手心喵！
func (s *NyangService) CalculateCans(args *CansArgs, reply *int) error {
	fmt.Printf("[打工喵] 收到指令喵！正在计算 %d * %d...\n", args.A, args.B)
	*reply = args.A * args.B
	return nil
}

func main() {
	// 把打工喵注册到服务中心
	nyang := new(NyangService)
	rpc.Register(nyang)

	// 监听 8080 端口，就像挂上电话机等待老板呼叫喵
	listener, err := net.Listen("tcp", ":8080")
	if err != nil {
		panic("电话线断了喵！" + err.Error())
	}
	fmt.Println("[打工喵] 哼，本喵已经在 8080 端口准备好了，快点把任务交过来！")

	// 阻塞在这里，接听电话
	rpc.Accept(listener)
}
```

### 第二步：只会发号施令的“老板喵” (客户端)

现在，打工喵已经准备好了。接下来写“老板喵”的代码。老板喵自己是不屑于算数学题的，它只会通过网络把数字甩给打工喵，然后等结果。

新建另一个文件叫 `client.go`，快点：

```go
package main

import (
	"fmt"
	"net/rpc"
)

// 客户端也得知道数据长什么样，不然怎么发喵？
type CansArgs struct {
	A, B int
}

func main() {
	// 1. 拿起电话，拨号给 localhost 的 8080 端口
	// （要是以后打工喵搬到了云服务器上，就把 localhost 换成那边的 IP，懂了吗笨蛋！）
	client, err := rpc.Dial("tcp", "localhost:8080")
	if err != nil {
		panic("呼叫打工喵失败，它是不是摸鱼去了喵？" + err.Error())
	}
	defer client.Close()

	// 2. 准备好要算的数据
	args := CansArgs{A: 7, B: 8}
	var reply int

	// 3. 远程调用！告诉对面的网络：给我执行 NyangService 里的 CalculateCans 方法！
	fmt.Println("[老板喵] 喂？对面的，给本喵算算 7 * 8 等于几喵！")
	err = client.Call("NyangService.CalculateCans", args, &reply)
	if err != nil {
		panic("计算失败喵！" + err.Error())
	}

	// 4. 拿到结果啦！
	fmt.Printf("[老板喵] 算得还挺快，结果是：%d 个罐头喵！\n", reply)
}
```

### 见证奇迹的时刻

好了，代码写完了。现在打开你的终端（Terminal）：

1. 先开一个窗口，把打工喵叫醒：`go run server.go`
2. 再开**另一个**新的窗口，让老板喵打电话：`go run client.go`

看到了没有？！老板喵这边的黑框框里瞬间就拿到了 `56` 的结果，而打工喵那边的窗口也打印出了它收到的任务。

### 为什么这就叫“分布式”了？

你这小脑袋瓜转过弯来了吗？

在这段代码里，真正的乘法计算 `A * B` **完完全全** 是在 `server.go` 里面运行的！`client.go` 里面根本没有算乘法的代码，它只是发送了请求。

这就是分布式的灵魂喵！**把复杂的计算和存储拆分到另一台机器上，当前机器只负责调用。** 只要有一天，你把 `client.Dial` 里的 `localhost` 换成了一台真实的公网 IP，你的这个小破程序就变成了真正的、跨越物理空间的分布式架构了！

哼，其实真实的分布式还要处理网络超时、服务崩溃、并发控制一大堆麻烦事。不过今天就先教到这里，贪多嚼不烂的道理不用本喵教你吧？

代码跑通了就快去给本喵开罐头，要是报错了绝对是你敲错字了，自己去查，本喵要睡午觉去了喵！( ￣^￣) /💨