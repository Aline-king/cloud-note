# 网络过滤（L3/L4）Network Filters

网络级别（L3/L4）过滤器构成了Envoy连接处理的核心。

过滤器API允许不同集合的过滤器混合并匹配，以附着到给定的侦听器上。

* 网络过滤器有三种不同类型：
  * **读（Read）**: 当Envoy从下游连接接收到数据时，会调用读取过滤器。这些过滤器负责处理接收到的数据，可以进行解码、验证、修改或基于数据内容做出路由决策等操作。
  * **写（Write）**: 写入过滤器在Envoy即将向下游连接发送数据之前被调用。它们可以用来对要发送的数据进行编码、加密、压缩或进行任何发送前的处理。
  * **读/写**：读/写过滤器在以下两种情况下被调用：一是在Envoy从下游连接接收到数据时；二是在Envoy准备向下游连接发送数据时。

网络级别的过滤器API相对简单，因为最终这些过滤器都是对原始字节和少量的连接事件进行操作。

链上的过滤器可以停止，并随后继续迭代到更多的过滤器。

想象一下，网络过滤器就像是水管中的过滤网，不过这里是用来处理网络数据的。

1. **简单直接的操作**：网络过滤器处理的是最基本的东西——互联网中的原始数据（就像水流），以及发生在网络连接上的简单事件（比如连接建立或断开）。这就像是过滤器只关注水是否干净，或者水管有没有打开、关闭。
2. **逐级处理与控制**：这些过滤器被组织成一条链，当数据流经时，每个过滤器都有机会检查或修改这些数据（像过滤水质）。有趣的是，任何一个过滤器都可以选择暂停流程，可能做一些特别的检查或处理，然后决定是不是让数据继续流到下一个过滤器。这就给了每个过滤器一定程度的自主权，决定数据接下来的旅程。

总的来说，网络过滤器以一种既简单又灵活的方式，在数据流动的过程中扮演着看门人的角色，保证或增强数据的处理过程。

网络过滤器访问和操作 L4 连接上的原始数据，即 TCP 数据包

* 例如， TCP代理过滤器将客户端连接数据路由到上游主机，并生成连接统计信息等

想象网络是个高速公路，而路上跑的货车就是数据包，这些货车按照一种叫TCP的规则行驶，保证货物能安全送到目的地。这里的"L4连接"就像是高速公路上的一个特定车道，专门给遵循TCP规则的货车用的。

网络过滤器就像是在高速公路上设置的检查站。它不仅可以查看这些TCP货车（也就是TCP数据包）里面装了什么，还能做些管理操作。比如，一个叫做TCP代理过滤器的特殊检查站，它的任务很有趣：

1. **引导货车**：它能指挥从农民（客户端）那里来的货车，不直接去原本的目的地，而是先绕到一个中转站（上游主机）去。这样做有时候是为了帮助更快送达，或者是为了统一管理这些货车。
2. **计数和分析**：除了引导工作，这个检查站还会记录有多少货车经过，以及其它一些有用的信息，这就是“生成连接统计信息”。这有点像数货车数量，看看交通忙不忙，帮助以后更好地规划和管理这条车道。

所以，这句话讲的就是在网络里有个工具能查看和操控遵循TCP规则传输的数据，还能改变数据的传送路线并收集相关统计数据。