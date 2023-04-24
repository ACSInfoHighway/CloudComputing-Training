# 任务1 Nats源码阅读

## 什么是 Nats

Nats 消息中间件是一种消息传输微服务，支持pub-sub和流处理。与部分消息中间件不同，Nats 基于subject主题名而不是host和port来管理寻址和发现。Nats的核心功能为publish-subscribe，其他功能都在这两个方法上拓展得到。Nats默认为 M:N 通信，也可以将其限制为 1:1。

最新版的 Nats 可以划分为 Nats-core 和 Jetstream 两个并列的子服务。二者的核心功能基本相同，不同的是 Jetstream 为流式处理，可以支持数据暂存和QoS控制。二者对比如下。

|           | 组成            | Pub-Sub | Req-Resp | 消息暂存     | QoS               | 服务器参数           |
|-----------|----------------|---------|----------|----------|-------------------|-----------------|
| Nats-Core | Client&Server | 支持      | 支持       | 不支持，即时收取 | 不支持               | nats-server     |
| JetStream | Client&Server | 支持      | 不支持      | 支持       | 支持重发、延迟控制、流量控制等参数 | nats-server -js |

## 任务：阅读Nats.go 源代码

源码仓库：<https://github.com/nats-io/nats.go>

建议使用IDE阅读，如Intellij Goland

1. 阅读 `nats.go`，重点阅读以下方法

   ``` go
   Connect()
   
   Publish()
   
   Subscribe()
   
   Request()
    
   Respond()
   
   Close()
   ```

*任务：阅读完成后，写出消息发送和消息接收的伪代码*

2. 查找 go doc 资料，阅读 `example_test.go`,重点阅读以下方法

    ``` go
    ExampleConnect()

    ExampleConn_Publish()

    ExampleConn_Subscribe()

    ExampleConn_Request()
    ```

*任务：阅读完成后，浏览 `nats_test.go`，观察以 Test 为前缀的函数的结构*

3. 总结 go 程序开发时，开发代码文件 - test文件 - example文件的关系和各自的功能

## 任务：编写最简单的 go 程序

nats服务器URL: `nats://39.101.140.145:5222`

1. 编写两个Test方法，一个连接服务器并发送 `hello world` 消息，一个连接服务器并接收消息
2. 使用**协程**，编写一个方法 F 让上面两个方法可以并发运行，并接收对应的 `hello world` 消息

> 提示：
> - 建议仿照 `example_test.go` 的逻辑编写收发消息的方法
> - 如果不清楚go协程的概念，请自行查找资料了解
