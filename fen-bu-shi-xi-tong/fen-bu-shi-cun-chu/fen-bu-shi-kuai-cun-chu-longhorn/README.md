# 分布式块存储Longhorn

Longhorn是一个轻量级、可靠且易于使用的Kubernetes分布式块存储系统。

Longhorn 是免费的开源软件。它最初由 Rancher Labs 开发，现在作为云原生计算基金会的孵化项目进行开发。

官方文档： [https://longhorn.io/docs/1.6.0/](https://longhorn.io/docs/1.6.0/)

使用 Longhorn，你可以：

* 使用 Longhorn 卷作为 Kubernetes 集群中分布式有状态应用程序的持久存储
* 将块存储分区为 Longhorn 卷，以便您可以在有或没有云提供商的情况下使用 Kubernetes 卷
* 跨多个节点和数据中心复制块存储以提高可用性
* 将备份数据存储在外部存储中，例如 NFS 或 AWS S3
* 创建跨集群灾难恢复卷，以便主 Kubernetes 集群中的数据可以从第二个 Kubernetes 集群中的备份快速恢复
* 计划卷的定期快照，并计划定期备份到 NFS 或 S3 兼容的辅助存储
* 从备份恢复卷
* 在不中断持久卷的情况下升级 Longhorn
* Longhorn 带有独立的 UI，可以使用 Helm、kubectl 或 Rancher 应用程序目录进行安装。

**Longhorn的底层存储协议**

iSCSI（Internet Small Computer Systems Interface）是一种网络协议，用于在 TCP/IP 网络上传输 SCSI 命令，允许两台计算机进行远程存储和检索操作。iSCSI 是一种流行的存储区域网络（SAN）技术，广泛用于连接存储设备，如磁盘阵列和磁带库，与服务器和数据中心。
