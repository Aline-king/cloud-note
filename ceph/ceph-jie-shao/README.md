# ceph介绍

## ceph官方介绍

Ceph is the future of storage; where traditional(传统的) systems fail to deliver, Ceph is designed to excel(出色). Leverage(利用) your data for better business(业务) decisions(决策) and achieve(实现) operational(运营) excellence(卓越) through scalable, intelligent(智能), reliable(可靠) and highly available(可用) storage software. Ceph supports object, block and file storage, all in one unified(统一) storage system.

### 来源

Ceph项目最早起源于Sage就读博士期间的工作（最早的成果于2004年发表，论文发表于2006年），并随后贡献给开源社区。

{% hint style="info" %}
参考资料：

官方地址：https://ceph.com/en/

官方文档：https://docs.ceph.com/en/latest/#

github地址：https://github.com/ceph/ceph

最新版本：Quincy-17.2.3(2022-04-19)、Pacific-16.2.10(2021-03-31)、Octopus-15.2.17(2020-03-23)

系统支持： 全系列最好的系统支持是Ubuntu

Ceph的发展对于Centos系列和Redhat系列不友好，新版本不支持旧版本系统。
{% endhint %}

### 介绍

Ceph 是一个开源的分布式存储系统，同时支持对象存储、块设备、文件系统.

对象数据的底层存储服务是由多个主机（host）组成的存储集群，该集群也被称之为 **RADOS**（Reliable Automatic Distributed Object Store）存储集群，即可靠、自动化、 分布式对象存储系统 librados是RADOS存储集群的API，它支持C、C++、Java、Python、Ruby和PHP等编程语言。

#### 为什么很多人用ceph？

1. 创新，Ceph能够同时提供**对象存储**、**块存储**和**文件系统存储**三种存储服务的统一存储架构
2. 算法，Ceph得以摒弃了传统的集中式存储元数据寻址方案，通过**Crush算法**的寻址操作，有相当强大的扩展性。
3. 可靠，Ceph中的数据副本数量可以由管理员自行定义，并可以通过Crush算法指定副本的物理存储位置以分隔故障域，支持数据强一致性的特性也使Ceph具有了高可靠性，可以忍受多种故障场景并自动尝试并行修复。

_**基本结构**_

Ceph是一个多版本存储系统，它把每一个待管理的数据流（例如一个文件） 切分为一到多个固定大小的对象数据，并以其为原子单元完成数据存取。

