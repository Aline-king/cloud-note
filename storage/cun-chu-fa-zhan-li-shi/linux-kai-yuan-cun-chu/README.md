# Linux 开源存储

在由Linux支撑的众多开源软件中，与存储相关的软件占据了很大的 比例，主要包括如下部分。

* 内核自带的服务模块，如iSCSI Target、NVMe-oF Target。&#x20;
* 各种内核支持的文件系统，如ext2/3/4、Btrfs、XFS等。&#x20;
* 基于Linux开发的分布式存储系统，如Ceph、GlusterFS、Sheepdog 等。
* &#x20;基于Linux的开源云计算管理软件，如OpenStack。&#x20;

总的来说，在Linux上部署及开发存储软件，尤其是开源存储软件， 已是大势所趋。

计算机系统是图灵机的高效实现，即根据输入数据的规则得到输出 数据。现在的计算机系统由CPU、内存、总线、外设（如网络设备、存 储设备）等组成，其中，存储是非常重要的一部分。系统的启动需要存 储，操作系统在启动的时候需要从存储系统中（无论是本地存储还是远 端存储）加载相应的镜像数据；在CPU和内存中进行数据计算的时候， 需要把数据进行相应的存储，如放入本地或远端存储系统。&#x20;

为了更好地说明存储的重要性，先回顾一下经典的存储级别图，如 图1-1所示。数据在计算机系统中的存储级别自上而下地划分为4大块： CPU、CPU缓存、易失内存、非易失内存。

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>存储级别</p></figcaption></figure>

由上到下表现为数据存储级别由高到低，数据访问速度 由快到慢，延迟时间由短到长。

* CPU的计算速度越来越快，但是也达到 了一定的瓶颈；
* 计算机工艺（基于硅晶体）可以达到7nm和10nm的级 别，但是再往更低突破也不太理想，于是转向了多核化发展；
* CPU对应 的缓存级别，从L1到L2再到更多级别的缓存，扩大了缓存的大小，能让 更多的数据位于缓存中，从而更好地降低了延迟。

易失内存主要是指内存， 分为**SRAM （ Static Random Access Memory）**和DRAM（Dynamic Random Access Memory），主要以双内联存储器模块（Dual In-line Memory Module，DIMM）接口为主。当然目前 也有支持双内联存储器模块接口的非易失内存，如英特尔的Apache Pass（AEP）。&#x20;

非易失内存种类繁多， 可以基于PCIe 、SATA （ Serial Advanced Technology Attachment ） 、SAS （ Serial Attached SCSI ） 、 AHCI（Advanced Host Controller Interface）等各种协议。根据介质和工作 原理，非易失内存可以分为机械硬盘（Harddisk）、固态硬盘（Sdid State Drive，SSD）及磁带（Tape）。其中，固态硬盘根据颗粒和介质可以分 为NAND和3D XPoint等。

