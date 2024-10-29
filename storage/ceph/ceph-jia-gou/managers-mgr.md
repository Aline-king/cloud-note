# Managers（Mgr）

Ceph 管理器 (守护进程ceph-mgr) 负责<mark style="color:purple;">**跟踪运行时指标**</mark>和 <mark style="color:purple;">**Ceph 集群的当前状态**</mark>，包括_存储利用率_、_当前性能指标_和_**系统负载**_。&#x20;

Ceph 管理器守护进程还托管基于 Python 的模块来管理和公开 Ceph 集群信息，包括基于 Web 的 Ceph 仪表板和 REST API。&#x20;

高可用性通常至少需要两个管理器。基于 raft 协议实现节点间的信息同步。
