# 总线

## 总线介绍

总线是信息高铁中的通信模块，独立于信息高铁gluenet核心代码运行。总线由一台总线服务器和相应的client构成，每个使用总线进行消息收发的发送者/接收者都需要通过一个client上的接口向对应的总线服务器发起发/收请求。在信息高铁系统中，总线client的接口由gluenet的data adapter模块进行封装。

![总线示意图](../pic/04/neuron.jpg)

总线的核心逻辑由 **Nats** 消息中间件实现。

总线的两个主要功能为：以特定的主题收发消息，向特定的工作单元发送RPC请求。

- 收发消息

    收发消息基于Pub-Sub模型。发送者和接收者事前共同确定一个主题，即Subject。总线的消息传输基于该Subject完成。发送者在该Subject上发送一条消息到总线服务器，接收者在该Subject上接收被发出的消息。可以有多个Client发送同一个Subject下的消息，也可以有多个Client接收同一条消息。Subject可以重复使用。

- RPC请求

    RPC(Remote Procedure Call, 远程过程调用)请求基于Request-Respond模型。Request指令在指定的Subject上发送消息并等待回复，Respond方法在收到消息后向发送者发送回复信息。
    
    RPC发送者通过包装后的Request请求向工作单元注册一个远程方法，并向其传递参数。工作单元收到RPC注册请求后，启动对应的方法，并在执行结束后获取返回值，用Responde将其回复给发送者。发送者接收回复值。

接下来请阅读[NATS](sec01_Bus.md)

# 任务: Nats源码阅读

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

nats服务器URL: `nats://39.101.140.145:6222`

1. 编写两个Test方法，一个连接服务器并发送 `hello world` 消息，一个连接服务器并接收消息
2. 使用**协程**，编写一个方法 F 让上面两个方法可以并发运行，并接收对应的 `hello world` 消息

> 提示：
> - 建议仿照 `example_test.go` 的逻辑编写收发消息的方法
> - 如果不清楚go协程的概念，请自行查找资料了解
