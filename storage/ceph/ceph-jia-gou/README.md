# Ceph 架构

Ceph 集群服务端主要有3 种类型的守护进程，每种类型的守护进程最后都被规划 到特定服务器节点上。下面对这3 种类型的守护进程进行简单描述。

1. Ceph OSD ：利用Ceph 节点上的CPU、内存和网络进行数据复制、纠错、重新 平衡、恢复、监控和报告等。
2. Ceph Monitor ：维护Ceph 集群的主副本映射、Ceph 集群的当前状态以及处理 各种与运行控制相关的工作。
3. Ceph Manager ：维护 Placement Group (放置组) 有关的详细信息，代替Ceph Monitor 处理元数据和主机元数据，能显著改善大规模集群的访问性能。Ceph Manager 处理许多只读Ceph CLI 的查询请求，例如放置组统计信息。Ceph Manager 还提供了RESTful APIo
