# 集群内Service to Service访问

<figure><img src="../../../../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

这张图展示了微服务架构中的一种典型设计。在该设计中，每个服务集群（Service Cluster）都由一个Envoy代理和一个实际的服务组成。

* Envoy代理：它是一个边缘服务器，负责处理来自其他服务或外部系统的请求。Envoy代理通常使用HTTP/2协议进行通信，并且可以提供负载均衡、错误恢复等高级功能。
* 实际的服务：这是业务逻辑的核心部分，它接收来自Envoy代理的请求并返回响应。

两个服务集群之间通过RESTful API进行交互。这种设计使得每个服务都可以独立地开发、测试和部署，同时保持与其他服务的松耦合关系。

此外，还有一个名为“Discovery”的组件，它可能是一个服务注册表或者DNS服务器，用于帮助Envoy代理找到需要连接到的实际服务实例。这样就可以实现动态的服务发现和故障转移等功能。
