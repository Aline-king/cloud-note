# MDS（元数据服务器）

Ceph 元数据服务器（MDS\[Metadata Server]、ceph-mds）代表 Ceph 文件系统存储元数据。

&#x20;Ceph 元数据服务器允许 POSIX（为应用程序提供的接口标准） 文件系统用户执行基本命令（如 ls、find 等），而不会给 Ceph 存储集群带来巨大负担。
