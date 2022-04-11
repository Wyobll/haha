
# 基础环境配置

#### 双节点网络配置：

**vi /etc/sysconfig/network-scripts/ifcfg-eth33**

```shell
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=4d49342c-a387-4880-ac96-9c8d00b2994e
DEVICE=ens33
ONBOOT=yes
BOOTPROTO=static
IPADDR=192.168.11.140
NETMASK=255.255.255.0
GATEWAY=192.168.11.2
DNS1=114.114.114.114
DNS2=8.8.8.8
:wq 保存退出
```

> systemctl restart network
>
> ping baidu.com
>
> PING baidu.com (220.181.38.148) 56(84) bytes of data.
> 64 bytes from 220.181.38.148 (220.181.38.148): icmp_seq=1 ttl=128 time=123 ms
> 64 bytes from 220.181.38.148 (220.181.38.148): icmp_seq=2 ttl=128 time=109 ms
> 64 bytes from 220.181.38.148 (220.181.38.148): icmp_seq=3 ttl=128 time=91.3 ms
> 64 bytes from 220.181.38.148 (220.181.38.148): icmp_seq=4 ttl=128 time=88.2 ms
>
> ctrl+c 退出

#### 修改控制节点controller主机名：

```shell
hostnamectl set-hostname controller
```

#### 修改计算节点compute主机名：

```shell
hostnamectl set-hostname compute
```

#### 双节点配置域名解析：

**vi /etc/hosts**

```shell
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.11.140 controller  
192.168.11.141 compute
```

> ping controller
>
> ping compute

#### 双节点关闭防火墙：

> systemctl stop firewalld
> systemctl disable firewalld
>
> setenforce 0
>

**vi /etc/selinux/config**

```shell
改SELINUX=disabled
:wq 保存退出
```

> 查看SELINUX状态：
>
> #getenforce
>
> ‘Permissive’

#### 配置双节点yum源163源：

```shell
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
没有wget可以下载yum install -y wget或者用curl
```

> yum clean all 清除原有yum缓存 
> yum makecache 更新



# 安装和配置组件

#### controller：

[root@controller ~]#*yum install chrony -y*

[root@controller ~]#**vi /etc/chrony.conf**

```
server ntp1.aliyun.com iburst
server ntp2.aliyun.com iburst
server ntp3.aliyun.com iburst
server ntp4.aliyun.com iburst
allow 192.168.11.0/24
```

#### 重新启动 NTP 服务：

[root@controller ~]# systemctl enable chronyd.service
[root@controller ~]# systemctl start chronyd.service

> [root@controller ~]# systemctl status chronyd.service
>
>   Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; vendor preset: enabled)
>    Active: active (running) since 四 2022-03-31 20:09:16 CST; 22h ago
>      Docs: man:chronyd(8)
>            man:chrony.conf(5)
>  Main PID: 10270 (chronyd)
>    CGroup: /system.slice/chronyd.service
>            └─10270 /usr/sbin/chronyd
>
> 3月 31 20:09:16 controller systemd[1]: Started NTP client/server.
> 3月 31 20:09:27 controller chronyd[10270]: Selected source 120.25.115.20
> 3月 31 20:51:30 controller chronyd[10270]: Can't synchronise: no selectable so...s
> 3月 31 20:56:55 controller chronyd[10270]: Selected source 120.25.115.20
> 3月 31 20:56:55 controller chronyd[10270]: System clock wrong by 787955.543891...d
> 4月 01 00:30:02 controller chronyd[10270]: Can't synchronise: no selectable so...s
> 4月 01 10:33:38 controller chronyd[10270]: Selected source 120.25.115.20
> 4月 01 18:19:54 controller chronyd[10270]: Can't synchronise: no selectable so...s
> 4月 01 18:29:36 controller chronyd[10270]: Selected source 120.25.115.20
> 4月 01 18:29:36 controller chronyd[10270]: System clock wrong by 2215.363219 s...d
> Hint: Some lines were ellipsized, use -l to show in full.

#### compute：

[root@compute ~]#*yum install chrony*

[root@comopute ~]#**vi /etc/chrony.conf**

```
server controller iburst
注释这三行
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
```

#### 重启服务：

```
[root@compute ~]# systemctl enable chronyd.service
[root@compute ~]# systemctl start chronyd.service
[root@compute ~]# systemctl status chronyd.service
```

#### 验证chronyd：

```
chronyc sources
```

> 210 Number of sources = 1
>
> MS Name/IP address         Stratum Poll Reach LastRx Last sample               
>
> ^* 120.25.115.20                 2   6   377    57    +78ms[  +82ms] +/-  146ms

#### 两个节点安装openstack T版包：

*[root@controller ~]#yum install centos-release-openstack-train -y*

#### 两个节点安装openstack客户端:

*[root@controller ~]#yum install python-openstackclient -y*

*[root@compute ~]#yum install python-openstackclient -y*

#### controller节点安装数据库:

[root@controller ~]#*yum install mariadb mariadb-server python2-PyMySQL -y*

[root@controller ~]#**vi /etc/my.cnf.d/openstack.cnf**

```shell
[mysqld]
bind-address = 192.168.11.140
default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
```

#### **设置数据库开机自启**

[root@controller ~]#systemctl enable mariadb.service

[root@controller ~]#systemctl start mariadb.service

[root@controller ~]#systemctl status mariadb.service

#### 通过运行脚本来保护数据库服务

[root@controller ~]#mysql_secure_installation

#### controller:消息队列

[root@controller ~]#*yum install rabbitmq-server -y*

启动：

[root@controller ~]#systemctl enable rabbitmq-server.service

[root@controller ~]#systemctl start rabbitmq-server.service

[root@controller ~]#systemctl status rabbitmq-server.service

#### 添加用户：`openstack`

[root@controller ~]#rabbitmqctl add_user openstack 000000 

Creating user "openstack" ...   #添加成功

#### 配置、写入和读取访问权限：

[root@controller ~]#rabbitmqctl set_permissions openstack ".*" ".*" ".*"



# memcached 服务

#### 安装memcached服务：

[root@controller ~]#*yum install memcached python-memcached*

#### 编辑文件：

[root@controller ~]#**vi /etc/sysconfig/memcached`**

```
OPTIONS="-l 127.0.0.1,::1,controller"
```

#### 启动 memcached 服务：

[root@controller ~]#systemctl enable memcached.service

[root@controller ~]#systemctl start memcached.service

[root@controller ~]#systemctl status mecached.service

#### 安装 Etcd 服务：

[root@controller ~]#*yum install etcd -y*

#### 编辑 etcd 文件：

[root@controller ~]#**vi /etc/etcd/etcd.conf**

```
#[Member]
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="http://192.168.11.140:2380" #改为自己的IP地址
ETCD_LISTEN_CLIENT_URLS="http://192.168.11.140:2379"
ETCD_NAME="controller"
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://192.168.11.140:2380"
ETCD_ADVERTISE_CLIENT_URLS="http://192.168.11.140:2379"
ETCD_INITIAL_CLUSTER="controller=http://192.168.11.140:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster-01"
ETCD_INITIAL_CLUSTER_STATE="new"
```

#### 启动 etcd 服务：

[root@controller ~]#systemctl enable etcd

[root@controller ~]#systemctl start etcd

> [root@controller ~]#systemctl status etcd 
>
> ● etcd.service - Etcd Server
>    Loaded: loaded (/usr/lib/systemd/system/etcd.service; enabled; vendor preset: disabled)
>    Active: active (running) since 四 2022-03-31 20:29:10 CST; 24h ago
>  Main PID: 11890 (etcd)
>    CGroup: /system.slice/etcd.service
>            └─11890 /usr/bin/etcd --name=controller --data-dir=/var/lib/etcd/defa...
>
> 3月 31 20:29:10 controller etcd[11890]: cd8cb09a1a22cb3d received MsgVoteResp... 2
> 3月 31 20:29:10 controller etcd[11890]: cd8cb09a1a22cb3d became leader at term 2
> 3月 31 20:29:10 controller etcd[11890]: raft.node: cd8cb09a1a22cb3d elected l... 2
> 3月 31 20:29:10 controller etcd[11890]: setting up the initial cluster versio....3
> 3月 31 20:29:10 controller etcd[11890]: published {Name:controller ClientURLs...d6
> 3月 31 20:29:10 controller etcd[11890]: ready to serve client requests
> 3月 31 20:29:10 controller etcd[11890]: set the initial cluster version to 3.3
> 3月 31 20:29:10 controller etcd[11890]: enabled capabilities for version 3.3
> 3月 31 20:29:10 controller etcd[11890]: serving insecure client requests on 1...d!
> 3月 31 20:29:10 controller systemd[1]: Started Etcd Server.
> Hint: Some lines were ellipsized, use -l to show in full.

# keystone服务

#### 创建数据库：

```shell
mysql -uroot -p000000 #登录数据库
MariaDB [(none)]> CREATE DATABASE keystone;  #创建数据库
MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
IDENTIFIED BY '000000';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
IDENTIFIED BY '000000';
MariaDB [(none)]>exit   #退出
```

#### 安装keystone程序包：

[root@controller ~]#*yum install openstack-keystone httpd mod_wsgi*

#### 编辑文件：

[root@controller ~]#**vi /etc/keystone/keystone.conf**

```shell
```

