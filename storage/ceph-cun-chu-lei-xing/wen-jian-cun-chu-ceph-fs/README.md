# 文件存储 Ceph FS

配配额有了 RBD，远程磁盘挂载的问题就解决了，但 RBD 的问题是不能多个主机共享一个磁盘，如果有一份数据很多客户端都要读写该怎么办呢？这时 Ceph FS 作为文件系统存储解决方案就派上用场了。

## 简介

<figure><img src="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FCciQTOc6CLGkCYciwhF1%2Fuploads%2FEsGRiQBkBbHv0uNyJ2k3%2Fimage.png?alt=media&#x26;token=3dc28202-976e-4278-93c5-4f0742712e9e" alt=""><figcaption></figcaption></figure>

Ceph 文件系统（Ceph FS）是个  兼容的文件系统，它直接使用 Ceph 存储集群来存储数据。

Ceph FS是一个可移植操作系统接口兼容  POSIX 的分布式存储系统，与通常 的网络文件系统一样，要访问Ceph FS，需要有对应的客户端。Ceph FS 支持两种客户端：

Ceph FS FUSE和Ceph FS Kernel。也就是说有两种使用 Ceph FS的方式：

1. 是通过Kernle Module，Linux内核里包含了Ceph FS的 实现代码；
2. 是通过FUSE（用户空间文件系统）的方式。通过调用 libcephfs 库来实现Ceph FS 的加载， 而libcephfs 库又调用librados 库与 RADOS进行通信。

## 原理

<figure><img src="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FCciQTOc6CLGkCYciwhF1%2Fuploads%2FpiOQH13QweMXzUTxW5VG%2Fimage.png?alt=media&#x26;token=850c5c62-ab62-42fc-a4b1-e5399b655f3e" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

上层是支持客户端的Ceph FS Kernel Object、Ceph FS FUSE、Ceph FS Library等，

底层还是基础的OSD和Monitor，此外添加了元数据服务器 （MDS）。

Ceph FS要求Ceph存储集群内至少有一个元数据服务器，负责 提供文件系统元数据（目录、文件所有者、访问模式等）的存储与操 作。MDS只为Ceph FS服务，如果不需要使用Ceph FS，则不需要配置 MDS。

{% hint style="info" %}
只有 Ceph FS（文件存储）才会用到元数据服务器（mds），其他块存储和对象存储用不到
{% endhint %}

MDS虽然称为元数据服务，但是它却不存储任何元数据信息，它存在的目的仅仅是 让我们rados集群提供的存储接口能够兼容POSIX 的文件系统接口&#x20;

简单来说，它是真正元数据信息的索引服务。 客户端在访问文件接口的时候，首先连接到 MDS上，在MDS的内存里面维持元数据的索引信息，这些信息真正的保存在RADOS集群的metadata pool上面，而对应的数据，保存在对应的 data pool上面。 而且以将元数据服务作为一个非状态的效果，便于扩展。

相较于NFS来说，它主要有以下特点优势：

1. 底层数据冗余的功能，底层的roados 提供了基本的数据冗余功能，因此不存在nfs的单点故障因素。
2. 底层的roados系统有n个存储节点组成，所以数据的存储可以分散IO，吞吐量较高，ceph提供的扩展性要想当的高。&#x20;



## 拓展内容

<details>

<summary>M<strong>ulti Active MDS</strong></summary>

在Sage的论文中，提到过动态分布式元数据管理：“因为文件系统元 数据的操作占据典型文件系统一半的工作负载，所以有效率的元数据管 理肯定能提高系统整体性能，Ceph利用了一个新的元数据集群架构，基 于动态子树划分，可在10个甚至上百个MDS上管理文件系统目录结构， 一个动态的、层次分明的分区在每MDS工作负载中被保留位置，可促进 有效更新和预取，可共同提高工作负载性能，值得注意的是，元数据服 务器的负载分布是基于当前的访问状态的，使Ceph能在任何工作负载下 有效地利用当前的MDS资源，获得近似线性扩展性能。”当然这是最理想 的状态和最终要实现的目标。

在Luminous版本之前，整个文件系统只支持一个Active MDS，但是 可以存在一个或多个Standby MDS做冗余。在集群比较大，元数据操作比 较频繁的情况下，一个Active MDS就会成为瓶颈。在Luminous版本中， Multi Active MDS已经稳定，这样可以提高Ceph FS元数据的处理能力。

目前在Multi Active MDS功能基础上，并没有一步实现所谓的动态分 布式数据管理。而是折中实现了静态划分绑定：允许用户将不同的目录 绑定在不同的MDS上，以此达到比较好的负载均衡。

当目录变得越来越大或访问频率越来越高时，目录所在的MDS就会 变成瓶颈。最简单的方法就是将目录分割，放在不同的MDS上。但是又 希望这种分割是自动的，是对用户透明的。同样我们也希望随着删除、 目录内容减少或访问频率减少，目录可以合并在一起。可以根据目录长 度和访问频率两个维度来进行分割或合并。

* 目录长度。

当目录长度超过`mds_bal_split_siz`e（默认为10 000）后，就会进行分 割。但是在正常情况下并不会马上分割，因为分割动作会影响正常操 作，所以会在`mds_bal_fragment_interval`秒后分割。如果目录长度超过 `mds_bal_fragment_fast_ factor` 就会马上分割。分割子目录数是2 ^mds\_bal\_split\_bits。

mds\_bal\_fragment\_size\_max是目录片段大小的硬限制。如果达到，客 户端会在片段中创建文件时收到ENOSPC错误。在正确配置的系统上，永 远不应该在普通目录上达到此限制。

当目录片段的大小小于mds\_bal\_merge\_size时，会开始进行合并。

* 访问频率。

MDS 为每个目录维护单独的时间衰减负载计数器 （mds\_decay\_halflife），用于对目录片段进行读/写操作。写操作（包括 元数据I/O，如重命名、删除和创建）导致写操作计数器增加，并与 mds\_bal\_split\_wr进行比较，如果超过则触发拆分。同样读操作导致读操 作计数器增加，并与mds\_bal\_split\_rd进行比较，如果超过则触发拆分。

需要注意的是，根据访问频率进行分割后，它们仅基于大小阈值 （mds\_bal\_merge\_size）进行合并，因此根据访问频率进行分割可能导致 目录永远保持碎片。

</details>

<details>

<summary><strong>配额</strong></summary>

Ceph FS允许给系统内的任意目录以大小或文件数目的形式设置配 额。配额以xattr 的方式存放： ceph.quota.max\_files & ceph.quota.max\_bytes。Ceph FS虽然支持quota，但是有以下局限性。

* Ceph FS的配额功能依赖于挂载它的客户端的合作，在达到上限时 要停止写入；无法阻止篡改过的或对抗性的客户端，它们可以想写多少 就写多少。在客户端完全不可信时，用配额防止多占空间是靠不住的。
* 配额是不准确的。因为配额是客户端和服务器端合作，需要两种 数据同步，所以在达到配额限制一小段时间后，正在写入文件系统的进 程才会被停止，很难避免它们超过配置的限额、多写入一些数据等问 题。超过配额多大幅度主要取决于时间长短，而非数据量。
* 内核客户端还没有实现配额功能。用户空间客户端（libcephfs、 ceph-fuse）已经支持配额，但是Linux内核客户端还没有实现配额功能。
* quota是针对目录进行设置的，并没有根据UID/GID进行设置。
* 基于路径限制挂载时必须谨慎地配置配额。客户端必须能够访问 配置了配额的那个目录的索引节点，这样才能执行配额管理。如果某一 个客户端被MDS能力限制只能访问一个特定路径（如/home/user），并且 它无权访问配置了配额的父目录（如/home），这个客户端就不会按配额 执行。所以，基于路径进行访问控制时，最好在限制了客户端的那个目 录（如/home/user）或者在它下面的子目录上配置配额。

</details>
