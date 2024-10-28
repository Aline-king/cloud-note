# 应用场景

Ceph uniquely(独特的) delivers object, block, and file storage in one unified(统一) system. 注意：这个介绍在官网这几年基本没有变化

RadosGW、RBD和CephFS都是RADOS存储服务的客户端，它们把RADOS的存储服务接口(librados)分别从不同的角度做了进一步抽象，因而各自适用于不同的应用场景。 也就是说，ceph将三种存储类型统一在一个平台中，从而实现了更强大的适用性。

LIBRADOS：通过自编程方式实现数据的存储能力

RADOSGW：通过标准的RESTful接口，提供一种云存储服务

RBD：将ceph提供的空间，模拟出来一个一个的独立块设备使用。 ceph环境部署完毕后，服务端就准备好了rbd接口

CFS：通过一个标准的文件系统接口来进行数据的存储
