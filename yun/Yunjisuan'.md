# 云计算

* 云计算是计算能力、网络能力与安全能力的共享
* 云计算是互联网服务的基础设施
* 云计算的主要类型：laas（基础设施）、Paas（平台）、Saas（软件）
* 云计算优势：适应波峰波谷、弹性扩充、减少运维成本10000：1
* 镜像包括应用、数据、运行环境配置、中间件、操作系统
* 网站运行环境=镜像+云主机+云数据库+网络
* 云数据库CDB指在云端虚拟环境下实现的数据库，可按需付费、按需扩展、高可用性。支持Mysql、TDSQL、Sqlserver。
* COS（Cloud Object Storage），面向厂商和开发者的网络硬盘，代替传统服务器硬盘和数据存放方式的存储解决方案。
* CDN（Content Delivery Network，内容分布网络），通过分布在全球各地的机房为用户提供就近接入。 
* DNS，域名系统。通过主机名，最终得到该主机名对应的IP地址。



## open stack

* 三大核心组件

> Nova：Compute
>
> Neutron（Quantum）：Networking
>
> Swift：Storage

### Nova

* Nova是云计算环境中的主要控制器，主要采用python语言编写
* 使用目前成熟的虚拟化技术（LVM、XenServer）来管理和自动化计算资源池的操作
* open stack只是作为一个平台存在，并不充当计算资源的提供者和资源的消费者

### Swift

* Swift是open stack的对象存储（Object Storage）项目，是一个可扩展并且提供了冗余的存储系统
* 对象和文件分散存储在同一集群中的多台服务器的磁盘上，由open stack负责数据的复制和一致性
* 对象储存系统是用于存储大量静态数据的分布存储系统，没有主节点或者管理节点，便于系统的扩展和数据的冗余和持久化
* 存储的集群可以通过添加服务器完成横向的扩展
* 如果集群中服务器或者磁盘出现失败的情况，open stack会复制数据到集群中的其他节点

### Cinder

* cinder是open stack的块存储服务
* 为云计算提供块设备的创建、添加和卸载
* cinder支持多种存储平台
* 块设备适用于对性能要求较高的应用场景：比如数据库
* 块设备的快照功能可以实现基于块存储卷的数据备份，而且也可以利用快照进行数据恢复





---

路由器管理

AR1:

```shell
undo terminal monitor
system-view
sysname AR1
interface GigabitEthernet 0/0/1
ip address 192.168.1.1 30
quit
interface GigabitEthernet 0/0/2
ip address 192.168.2.1 24
quit
ospf 1
area 0
network 192.168.1.0 0.0.0.3
network 192.168.2.0 0.0.0.255
```

AR2:

```shell
undo terminal monitor
system-view
sysname AR2
interface GigabitEthernet 0/0/1
ip address 192.168.1.2 30
quit
interface GigabitEthernet 0/0/2
ip address 192.168.3.1 24
quit
ospf 1
area 0
network 192.168.1.0 0.0.0.3
network 192.168.3.0 0.0.0.255
```

