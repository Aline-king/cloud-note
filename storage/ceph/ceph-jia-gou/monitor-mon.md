# Monitor（mon）

Ceph Monitor (守护进程ceph-mon) 维护集群状态的映射，包括<mark style="color:blue;">监视器映射</mark>、<mark style="background-color:red;">管理器映射</mark>、<mark style="background-color:orange;">OSD 映射</mark>、<mark style="background-color:purple;">MDS 映射</mark>和 <mark style="background-color:green;">CRUSH</mark> 映射。&#x20;

这些映射是 Ceph 守护进程相互协调所需的关键集群状态。

监视器还负责管理守护进程和客户端之间的身份验证。 通常至少需要三个监视器才能实现冗余和高可用性。

> 基于 paxos 协议实现节点间的信息同步。

