# 微服务框架

这一部分接续[第三章介绍的内容](sec03_Multi-APP.md)

## 介绍

根据微服务架构在生产环境下的要求，传统意义上大多数电商与互联网应用所使用的微服务框架需要包含以下能力: 

![MicroService Framework Capability](../pic/05/microservice_capability.png)

现有的绝大多数框架都支持以上很多能力，Spring Boot与Apache Dubbo为使用最为广泛的微服务治理开源框架，支撑了很大一部分应用从单体应用上云的转变。近年来，云原生的开源社区不断壮大，针对不同的微服务能力，有众多的开源工具提供支持，能够根据需要以及工具特性进行组合: 

- 服务注册发现: ZooKeeper, Eureka, Consul, Nacos 
- 服务配置: Spring Cloud Config, Archaius  
- 服务熔断: Hystrix, Resilience4j
- 网关: Zuul, Spring Cloud Gateway  
- 负载均衡: Ribbon, Feign  
- 追踪工具: Zipkin, Sleuth  
- 日志采集: ElasticSearch, Logback  
- 监控平台: Prometheus, Grafana, Spring boot admin

以 Spring Boot 框架为例，可以以官方框架为基础，融合不同微服务支撑技 术后形成了一系列对生产实践支持更全面的框架，其中以 Alibaba 中间件团队开发的的开源框架 Spring Boot Alibaba 支持最为全面：

![SpringCloudAlibaba](../pic/05/SpringCloudAlibaba.png)

主要云服务厂商都针对客户需求对框架的完整性做了很大优化，能够满足开发迭代与生产应用并行的微服务开发环境，以容错与即时恢复处理来保证全时段高可用性。

## 任务

因为两种主流的微服务框架都是用Java语言编写的，因此你需要对Java和面向对象编程有初步的了解。

以下两个任务可以从中选择一个来做：

1. 根据[SpringBoot示例应用](https://spring.io/guides/gs/spring-boot/)的指导构建和运行一个分布式应用
2. 