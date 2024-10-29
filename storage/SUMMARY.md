# Table of contents

* [前言](README.md)

## 存储知识

* [重复数据删除](cun-chu-zhi-shi/chong-fu-shu-ju-shan-chu/README.md)
  * [重复数据删除的分类](cun-chu-zhi-shi/chong-fu-shu-ju-shan-chu/chong-fu-shu-ju-shan-chu-de-fen-lei.md)
  * [Page](cun-chu-zhi-shi/chong-fu-shu-ju-shan-chu/page.md)
* [数据寻址方案](cun-chu-zhi-shi/shu-ju-xun-zhi-fang-an.md)

## 存储发展历史

* [Linux 开源存储](cun-chu-fa-zhan-li-shi/linux-kai-yuan-cun-chu/README.md)
  * [为什么需要开源存储](cun-chu-fa-zhan-li-shi/linux-kai-yuan-cun-chu/wei-shen-me-xu-yao-kai-yuan-cun-chu.md)
  * [开源方案介绍](cun-chu-fa-zhan-li-shi/linux-kai-yuan-cun-chu/kai-yuan-fang-an-jie-shao.md)
* [存储类型](cun-chu-fa-zhan-li-shi/cun-chu-lei-xing/README.md)
  * [块存储](cun-chu-fa-zhan-li-shi/cun-chu-lei-xing/kuai-cun-chu.md)
  * [文件系统存储](cun-chu-fa-zhan-li-shi/cun-chu-lei-xing/wen-jian-xi-tong-cun-chu.md)
  * [对象存储](cun-chu-fa-zhan-li-shi/cun-chu-lei-xing/dui-xiang-cun-chu.md)
  * [分布式存储](cun-chu-fa-zhan-li-shi/cun-chu-lei-xing/fen-bu-shi-cun-chu.md)
* [（网络）存储系统架构演进](cun-chu-fa-zhan-li-shi/wang-luo-cun-chu-xi-tong-jia-gou-yan-jin.md)

## ceph

* [Ceph 介绍](ceph/ceph-jie-shao/README.md)
  * [应用场景](ceph/ceph-jie-shao/ying-yong-chang-jing.md)
* [Ceph 架构](ceph/ceph-jia-gou/README.md)
  * [Ceph 寻址流程](ceph/ceph-jia-gou/ceph-xun-zhi-liu-cheng.md)
  * [Monitor（mon）](ceph/ceph-jia-gou/monitor-mon.md)
  * [Managers（Mgr）](ceph/ceph-jia-gou/managers-mgr.md)
  * [MDS（元数据服务器）](ceph/ceph-jia-gou/mds-yuan-shu-ju-fu-wu-qi.md)
  * [OSDs](ceph/ceph-jia-gou/osds.md)
  * [PG](ceph/ceph-jia-gou/pg.md)
* [性能调优与测试](ceph/xing-neng-tiao-you-yu-ce-shi.md)
* [存储实践](ceph/cun-chu-shi-jian.md)
* [核心知识](ceph/he-xin-zhi-shi/README.md)
  * [paxos协议](ceph/he-xin-zhi-shi/paxos-xie-yi.md)
  * [存储原理](ceph/he-xin-zhi-shi/cun-chu-yuan-li.md)
  * [认证管理 Cephx](ceph/he-xin-zhi-shi/ren-zheng-guan-li-cephx/README.md)
    * [认证流程](ceph/he-xin-zhi-shi/ren-zheng-guan-li-cephx/ren-zheng-liu-cheng/README.md)
      * [用户操作](ceph/he-xin-zhi-shi/ren-zheng-guan-li-cephx/ren-zheng-liu-cheng/yong-hu-cao-zuo.md)
      * [秘钥操作](ceph/he-xin-zhi-shi/ren-zheng-guan-li-cephx/ren-zheng-liu-cheng/mi-yao-cao-zuo.md)

## ceph部署

* [Untitled](ceph-bu-shu/untitled.md)
* [集群部署](ceph-bu-shu/ji-qun-bu-shu.md)

## 分布式块存储

* [Longhorn](fen-bu-shi-kuai-cun-chu/longhorn/README.md)
  * [安装](fen-bu-shi-kuai-cun-chu/longhorn/an-zhuang.md)

## ceph存储类型

* [块存储 RBD](ceph-cun-chu-lei-xing/kuai-cun-chu-rbd/README.md)
  * [实践](ceph-cun-chu-lei-xing/kuai-cun-chu-rbd/shi-jian.md)
  * [快照管理](ceph-cun-chu-lei-xing/kuai-cun-chu-rbd/kuai-zhao-guan-li.md)
* [文件存储 Ceph FS](ceph-cun-chu-lei-xing/wen-jian-cun-chu-ceph-fs/README.md)
  * [部署](ceph-cun-chu-lei-xing/wen-jian-cun-chu-ceph-fs/bu-shu.md)
  * [使用方式](ceph-cun-chu-lei-xing/wen-jian-cun-chu-ceph-fs/shi-yong-fang-shi/README.md)
    * [Kernle Module](ceph-cun-chu-lei-xing/wen-jian-cun-chu-ceph-fs/shi-yong-fang-shi/kernle-module.md)
    * [FUSE实践](ceph-cun-chu-lei-xing/wen-jian-cun-chu-ceph-fs/shi-yong-fang-shi/fuse-shi-jian.md)
* [对象存储 RGW接口](ceph-cun-chu-lei-xing/dui-xiang-cun-chu-rgw-jie-kou/README.md)
  * [基础操作](ceph-cun-chu-lei-xing/dui-xiang-cun-chu-rgw-jie-kou/ji-chu-cao-zuo.md)
  * [泛域名实践](ceph-cun-chu-lei-xing/dui-xiang-cun-chu-rgw-jie-kou/fan-yu-ming-shi-jian.md)
  * [s3测试](ceph-cun-chu-lei-xing/dui-xiang-cun-chu-rgw-jie-kou/s3-ce-shi.md)
  * [Swift测试](ceph-cun-chu-lei-xing/dui-xiang-cun-chu-rgw-jie-kou/swift-ce-shi.md)
  * [对象访问](ceph-cun-chu-lei-xing/dui-xiang-cun-chu-rgw-jie-kou/dui-xiang-fang-wen.md)
