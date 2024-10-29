# OSDs

Ceph OSD（对象存储守护进程，ceph-osd）存储数据，<mark style="color:purple;">处理数据复制</mark>、<mark style="color:orange;">恢复</mark>、<mark style="color:yellow;">重新平衡</mark>，并通过检查其他 Ceph OSD 守护进程的心跳来向 Ceph 监视器和管理器提供一些监控信息。&#x20;

通常至少需要 3 个 Ceph OSD 来实现_**冗余**_和_**高可用性**_。本质上osd就是一个个host主机上的存储磁盘。
