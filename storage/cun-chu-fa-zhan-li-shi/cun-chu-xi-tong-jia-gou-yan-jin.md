# 存储系统架构演进

粗略分类，存储架构的演进可以划分为以下两个阶段。

## 第一阶段：从离散化到集中化的演进（从DAS到SAN/NAS)。

互联网发展初期， 存储需求相对简单， 数据规模较小， 存储系统架构以存储介质直连 服务器(Direct-Attached Storag e, DAS)为主， 存储介质直接挂载到服务器的总线上来 提供数据访问服务， 数据存储设备与服务器是一种 “ 同生共死 ＂ 的状态。

这种方式可以简洁地解决数据的存储需求， 但也存在着较为明显的弊端。

* 服务器之间的存储系统形成 “ 孤岛 ” ， 限制数据的共享访问；
* &#x20;随着CPU 处理能力逐步增强， SCSI 连接通道会成为1/0 的瓶颈， 制约性能发挥；
* &#x20;随着数据量增长， 存储的安全性（备份／恢复需求）、扩展性问题日益凸显。&#x20;

基千以上症结， 存储区域网络(Storag e Area Net work, SAN)架构以及网络附属存储(Network A ttached Storag e, NAS)架构应运而生。