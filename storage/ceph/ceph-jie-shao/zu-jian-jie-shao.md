# 组件介绍

## Monitors

Ceph Monitor (守护进程ceph-mon) 维护集群状态的映射，包括<mark style="color:blue;">监视器映射</mark>、<mark style="background-color:red;">管理器映射</mark>、<mark style="background-color:orange;">OSD 映射</mark>、<mark style="background-color:purple;">MDS 映射</mark>和 <mark style="background-color:green;">CRUSH</mark> 映射。&#x20;

这些映射是 Ceph 守护进程相互协调所需的关键集群状态。

监视器还负责管理守护进程和客户端之间的身份验证。 通常至少需要三个监视器才能实现冗余和高可用性。

> 基于 paxos 协议实现节点间的信息同步。

## Managers

Ceph 管理器 (守护进程ceph-mgr) 负责<mark style="color:purple;">**跟踪运行时指标**</mark>和 <mark style="color:purple;">**Ceph 集群的当前状态**</mark>，包括_存储利用率_、_当前性能指标_和_**系统负载**_。&#x20;

Ceph 管理器守护进程还托管基于 Python 的模块来管理和公开 Ceph 集群信息，包括基于 Web 的 Ceph 仪表板和 REST API。&#x20;

高可用性通常至少需要两个管理器。基于 raft 协议实现节点间的信息同步。

## Ceph OSDs

Ceph OSD（对象存储守护进程，ceph-osd）存储数据，<mark style="color:purple;">处理数据复制</mark>、<mark style="color:orange;">恢复</mark>、<mark style="color:yellow;">重新平衡</mark>，并通过检查其他 Ceph OSD 守护进程的心跳来向 Ceph 监视器和管理器提供一些监控信息。&#x20;

通常至少需要 3 个 Ceph OSD 来实现_**冗余**_和_**高可用性**_。本质上osd就是一个个host主机上的存储磁盘。

## MDSs

Ceph 元数据服务器（MDS\[Metadata Server]、ceph-mds）代表 Ceph 文件系统存储元数据。

&#x20;Ceph 元数据服务器允许 POSIX（为应用程序提供的接口标准） 文件系统用户执行基本命令（如 ls、find 等），而不会给 Ceph 存储集群带来巨大负担。

## PG

PG全称Placement Grouops，是一个逻辑的概念，一个PG包含多个OSD。

引入PG这一层其实是为了更好的分配数据和定位数据。

写数据的时候，写入主osd，冗余两份。
