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
[database]
# ...
connection = mysql+pymysql://keystone:000000@controller/keystone
#配置数据库访问
[token]
# ...
provider = fernet #配置fernet令牌
```

#### 填充身份服务数据库：

[root@controller ~]#su -s /bin/sh -c "keystone-manage db_sync" keystone

#### 初始化fernet密匙存储库：

[root@controller ~]# keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone

[root@controller ~]# keystone-manage credential_setup --keystone-user keystone --keystone-group keystone

#### 引导标识服务：

[root@controller ~]# keystone-manage bootstrap --bootstrap-password 000000 \
  --bootstrap-admin-url http://controller:5000/v3/ \
  --bootstrap-internal-url http://controller:5000/v3/ \
  --bootstrap-public-url http://controller:5000/v3/ \
  --bootstrap-region-id RegionOne

#### 配置 Apache HTTP 服务器：

[root@controller ~]# **vi /etc/httpd/conf/httpd.confServerName**

```shell
ServerName controller
```

#### 创建链接：

[root@controller ~]# ln -s /usr/share/keystone/wsgi-keystone.conf /etc/httpd/conf.d

#### 启动服务：

[root@controller ~]# systemctl enable httpd.service

[root@controller ~]# systemctl start httpd.service

> [root@controller ~]# systemctl status httpd.service
>
> ● httpd.service - The Apache HTTP Server
>    Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
>   Drop-In: /usr/lib/systemd/system/httpd.service.d
>            └─openstack-dashboard.conf
>    Active: active (running) since 五 2022-04-01 12:54:37 CST; 8h ago
>      Docs: man:httpd(8)
>            man:apachectl(8)
>   Process: 130705 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=0/SUCCESS)
>   Process: 130764 ExecStartPre=/usr/bin/python2 /usr/share/openstack-dashboard/manage.py compress --force -v0 (code=exited, status=0/SUCCESS)
>   Process: 130733 ExecStartPre=/usr/bin/python2 /usr/share/openstack-dashboard/manage.py collectstatic --noinput --clear -v0 (code=exited, status=0/SUCCESS)
>  Main PID: 130790 (httpd)
>    Status: "Total requests: 2648; Current requests/sec: 0; Current traffic:   0 B/sec"
>    CGroup: /system.slice/httpd.service
>            ├─   602 /usr/sbin/httpd -DFOREGROUND
>            ├─ 16822 /usr/sbin/httpd -DFOREGROUND
>            ├─ 16827 /usr/sbin/httpd -DFOREGROUND
>            ├─ 16830 /usr/sbin/httpd -DFOREGROUND
>            ├─ 16832 /usr/sbin/httpd -DFOREGROUND
>            ├─ 16833 /usr/sbin/httpd -DFOREGROUND
>            ├─ 16834 /usr/sbin/httpd -DFOREGROUND
>            ├─ 17099 /usr/sbin/httpd -DFOREGROUND
>            ├─ 17103 /usr/sbin/httpd -DFOREGROUND
>            ├─ 17104 /usr/sbin/httpd -DFOREGROUND
>            ├─130790 /usr/sbin/httpd -DFOREGROUND
>            ├─130792 /usr/sbin/httpd -DFOREGROUND
>            ├─130793 /usr/sbin/httpd -DFOREGROUND
>            ├─130794 /usr/sbin/httpd -DFOREGROUND
>            ├─130795 /usr/sbin/httpd -DFOREGROUND
>            ├─130796 /usr/sbin/httpd -DFOREGROUND
>            ├─130797 /usr/sbin/httpd -DFOREGROUND
>            ├─130798 /usr/sbin/httpd -DFOREGROUND
>            ├─130799 /usr/sbin/httpd -DFOREGROUND
>            ├─130800 /usr/sbin/httpd -DFOREGROUND
>            ├─130801 /usr/sbin/httpd -DFOREGROUND
>            ├─130802 /usr/sbin/httpd -DFOREGROUND
>            ├─130803 /usr/sbin/httpd -DFOREGROUND
>            ├─130804 (wsgi:keystone- -DFOREGROUND
>            ├─130805 (wsgi:keystone- -DFOREGROUND
>            ├─130806 (wsgi:keystone- -DFOREGROUND
>            ├─130807 (wsgi:keystone- -DFOREGROUND
>            └─130808 (wsgi:keystone- -DFOREGROUND
>
> 4月 01 12:54:10 controller systemd[1]: Starting The Apache HTTP Server...
> 4月 01 12:54:37 controller python2[130764]: Compressing... done
> 4月 01 12:54:37 controller python2[130764]: Compressed 7 block(s) from 4 templ....
> 4月 01 12:54:37 controller systemd[1]: Started The Apache HTTP Server.
> Hint: Some lines were ellipsized, use -l to show in full.

#### 配置环境变量：

```
$ export OS_USERNAME=admin
$ export OS_PASSWORD=000000
$ export OS_PROJECT_NAME=admin
$ export OS_USER_DOMAIN_NAME=Default
$ export OS_PROJECT_DOMAIN_NAME=Default
$ export OS_AUTH_URL=http://controller:5000/v3
$ export OS_IDENTITY_API_VERSION=3
```

#### 创建域：

[root@controller ~]# openstack domain create --description "An Example Domain" example

```
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | An Example Domain                |
| enabled     | True                             |
| id          | 2f4f80574fd84fe6ba9067228ae0a50c |
| name        | example                          |
| tags        | []                               |
+-------------+----------------------------------+
```

#### 创建项目：service

[root@controller ~]# openstack project create --domain default \

-> --description "Service Project" service

```
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | Service Project                  |
| domain_id   | default                          |
| enabled     | True                             |
| id          | 24ac7f19cd944f4cba1d77469b2a73ed |
| is_domain   | False                            |
| name        | service                          |
| parent_id   | default                          |
| tags        | []                               |
+-------------+----------------------------------+
```

#### 创建项目：myproject

[root@controller ~]# openstack project create --domain default \
  --description "Demo Project" myproject

```
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | Demo Project                     |
| domain_id   | default                          |
| enabled     | True                             |
| id          | 231ad6e7ebba47d6a1e57e1cc07ae446 |
| is_domain   | False                            |
| name        | myproject                        |
| parent_id   | default                          |
| tags        | []                               |
+-------------+----------------------------------+
```

#### 创建用户：myuser

[root@controller ~]# openstack user create --domain default \
  --password-prompt myuser

```
User Password:000000
Repeat User Password:000000
+---------------------+----------------------------------+
| Field               | Value                            |
+---------------------+----------------------------------+
| domain_id           | default                          |
| enabled             | True                             |
| id                  | aeda23aa78f44e859900e22c24817832 |
| name                | myuser                           |
| options             | {}                               |
| password_expires_at | None                             |
+---------------------+----------------------------------+
```

#### 创建角色：myrole

[root@controller ~]# openstack role create myrole

```
+-----------+----------------------------------+
| Field     | Value                            |
+-----------+----------------------------------+
| domain_id | None                             |
| id        | 997ce8d05fc143ac97d83fdfb5998552 |
| name      | myrole                           |
+-----------+----------------------------------+
```

#### 将角色添加到项目和用户：

[root@controller ~]# openstack role add --project myproject --user myuser myrole

#无输出

#### 请求身份验证令牌：`admin`

[root@controller ~]# openstack --os-auth-url http://controller:5000/v3 \
  --os-project-domain-name Default --os-user-domain-name Default \
  --os-project-name admin --os-username admin token issue

#### 作为在上一节中创建的用户，请求身份验证令牌：`myuser`

[root@controller ~]# openstack --os-auth-url http://controller:5000/v3 \
  --os-project-domain-name Default --os-user-domain-name Default \
  --os-project-name myproject --os-username myuser token issue

#### 创建脚本

[root@controller ~]# **vi admin-openrc**

```
export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=000000
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
```

[root@controller ~]# **vi demo-openrc**

```
export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=myproject
export OS_USERNAME=myuser
export OS_PASSWORD=000000
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
```

#### 填充环境变量：

[root@controller ~]# . admin-openrc

#### 请求身份验证令牌：

[root@controller ~]# openstack token issue



# 镜像服务

#### 使用数据库访问客户端以用户身份连接到数据库服务器：`root`

[root@controller ~]# mysql -u root -p

#### 创建数据库：`glance`

```
MariaDB [(none)]> CREATE DATABASE glance;
```

#### 授予对数据库的正确访问权限：`glance`

```
MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' \
  IDENTIFIED BY '000000';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' \
  IDENTIFIED BY '000000';
```

#### 获取凭据以获取对仅限管理员的 CLI 命令的访问权限：

[root@controller ~]# . admin-openrc

#### 创建用户：`glance`

[root@controller ~]# openstack user create --domain default --password-prompt glance

#### 将角色添加到用户和项目：

[root@controller ~]# openstack role add --project service --user glance admin

#### 创建服务实体：`glance`

[root@controller ~]# openstack service create --name glance \
  --description "OpenStack Image" image

#### 创建影像服务 API 终端节点：

[root@controller ~]# openstack endpoint create --region RegionOne \
  image public http://controller:9292

[root@controller ~]# openstack endpoint create --region RegionOne \
  image internal http://controller:9292

[root@controller ~]# openstack endpoint create --region RegionOne \
  image admin http://controller:9292

#### 安装软件包：

[root@controller ~]# *yum install openstack-glance*

#### 编辑文件:

[root@controller ~]#`vi /etc/glance/glance-api.conf`

```
[database]
# ...
connection = mysql+pymysql://glance:000000@controller/glance
```

```
[keystone_authtoken]
# ...
www_authenticate_uri  = http://controller:5000
auth_url = http://controller:5000
memcached_servers = controller:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = glance
password = 000000

[paste_deploy]
# ...
flavor = keystone
```

```
[glance_store]
# ...
stores = file,http
default_store = file
filesystem_store_datadir = /var/lib/glance/images/
```

#### 填充影像服务数据库：

[root@controller ~]# su -s /bin/sh -c "glance-manage db_sync" glance

#### 启动映像服务并将其配置为在系统引导时启动：

[root@controller ~]# systemctl enable openstack-glance-api.service

[root@controller ~]# systemctl start openstack-glance-api.service

[root@controller ~]# systemctl status openstack-glance_api.service



# PLACEMENT服务

#### 创建数据库

[root@controller ~]# mysql -u root -p000000

```
MariaDB [(none)]> CREATE DATABASE placement;
```

#### 授予访问权限：

```
MariaDB [(none)]> GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'localhost' \
  IDENTIFIED BY '000000';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'%' \
  IDENTIFIED BY '000000';  
```

#### 使用您选择的以下选项创建放置服务用户：`PLACEMENT_PASS`

[root@controller ~]# openstack user create --domain default --password-prompt placement 密码000000

#### 将 Placement 用户添加到具有管理员角色的服务项目中：

[root@controller ~]# openstack role add --project service --user placement admin

#### 在服务目录中创建放置 API 条目：

[root@controller ~]# openstack service create --name placement \
  --description "Placement API" placement

#### 创建放置 API 服务终端节点：

[root@controller ~]# openstack endpoint create --region RegionOne \
  placement public http://controller:8778

[root@controller ~]# openstack endpoint create --region RegionOne \
  placement internal http://controller:8778

[root@controller ~]# openstack endpoint create --region RegionOne \
  placement admin http://controller:8778

#### 安装放置位置和所需的数据库:

[root@controller ~]# *yum install openstack-placement-api -y*

#### 创建文件:

[root@controller ~]#`vi /etc/placement/placement.conf`

```
[placement_database]
connection = mysql+pymysql://placement:000000S@controller/placement

```

```
[api]
auth_strategy = keystone  # use noauth2 if not using keystone

[keystone_authtoken]
www_authenticate_uri = http://controller:5000/
auth_url = http://controller:5000/
memcached_servers = controller:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = placement
password = 000000
```

#### 填充数据库：`placement`

[root@controller ~]# su -s /bin/sh -c "placement-manage db sync" placement

#### 重新启动 httpd 服务：

[root@controller ~]# systemctl restart httpd



#  nova 计算服务

#### 创建数据库

[root@controller ~]# mysql -u root -p000000

```shell
MariaDB [(none)]> CREATE DATABASE nova_api;
MariaDB [(none)]> CREATE DATABASE nova;
MariaDB [(none)]> CREATE DATABASE nova_cell0;
```

#### 授予对数据库的正确访问权限：

```
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'localhost' \
  IDENTIFIED BY '000000';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'%' \
  IDENTIFIED BY '000000';

MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' \
  IDENTIFIED BY '000000';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' \
  IDENTIFIED BY '000000';

MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'localhost' \
  IDENTIFIED BY '000000';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'%' \
  IDENTIFIED BY '000000';
```

#### 获取凭据以获取对仅限管理员的 CLI 命令的访问权限：

[root@controller ~]# . admin-openrc

#### 创建用户：`nova`

[root@controller ~]# openstack user create --domain default --password-prompt nova

#### 将角色添加到用户：

[root@controller ~]# openstack role add --project service --user nova admin

#### 创建服务实体：

[root@controller ~]# openstack service create --name nova \
 --description "OpenStack Compute" compute

#### 创建计算 API 服务终结点：

[root@controller ~]# openstack endpoint create --region RegionOne \
 compute public http://controller:8774/v2.1

[root@controller ~]# openstack endpoint create --region RegionOne \
  compute internal http://controller:8774/v2.1

[root@controller ~]# openstack endpoint create --region RegionOne \
  compute admin http://controller:8774/v2.1

#### 安装软件包：

[root@controller ~]# yum install openstack-nova-api openstack-nova-conductor \
  openstack-nova-novncproxy openstack-nova-scheduler

#### 编辑文件：

**`vi /etc/nova/nova.conf`**

```shell
[DEFAULT]
# ...
enabled_apis = osapi_compute,metadata
```

```
[api_database]
# ...
connection = mysql+pymysql://nova:000000S@controller/nova_api

[database]
# ...
connection = mysql+pymysql://nova:000000@controller/nova
```

```
[DEFAULT]
# ...
transport_url = rabbit://openstack:000000@controller:5672/ 
```

```
[api]
# ...
auth_strategy = keystone

[keystone_authtoken]
# ...
www_authenticate_uri = http://controller:5000/
auth_url = http://controller:5000/
memcached_servers = controller:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = nova
password = NOVA_PASS
替换为您在标识服务中为用户选择的密码。NOVA_PASSnova
```

```
[DEFAULT]
# ...
my_ip = 192.168.11.140
```

```
[DEFAULT]
# ...
use_neutron = true
firewall_driver = nova.virt.firewall.NoopFirewallDriver
```

**vi /etc/nova/nova.conf**

```
[vnc]
enabled = true
# ...
server_listen = $my_ip
server_proxyclient_address = $my_ip
```

```
[glance]
# ...
api_servers = http://controller:9292
```

```
[oslo_concurrency]
# ...
lock_path = /var/lib/nova/tmp
```

```
[placement]
# ...
region_name = RegionOne
project_domain_name = Default
project_name = service
auth_type = password
user_domain_name = Default
auth_url = http://controller:5000/v3
username = placement
password = PLACEMENT_PASS #替换为您在安装Placement时为创建的服务用户选择的密码
```

#### 填充数据库：`nova-api`

[root@controller ~]# su -s /bin/sh -c "nova-manage api_db sync" nova

#### 创建单元格：cell0

[root@controller ~]# su -s /bin/sh -c "nova-manage cell_v2 map_cell0" nova

#### 创建单元格：`cell1`

[root@controller ~]# su -s /bin/sh -c "nova-manage cell_v2 create_cell --name=cell1 --verbose" nova

#### 填充 nova 数据库：

[root@controller ~]# su -s /bin/sh -c "nova-manage db sync" nova

#### 验证 nova 单元格 0 和单元格 1 是否已正确注册：

[root@controller ~]# su -s /bin/sh -c "nova-manage cell_v2 list_cells" nova

#### 启动计算服务并将其配置为在系统启动时启动：

> systemctl enable \
>     openstack-nova-api.service \
>     openstack-nova-scheduler.service \
>     openstack-nova-conductor.service \
>     openstack-nova-novncproxy.service
> systemctl start \
>     openstack-nova-api.service \
>     openstack-nova-scheduler.service \
>     openstack-nova-conductor.service \
>     openstack-nova-novncproxy.service

#### 配置计算节点

[root@compute ~]#*yum install openstack-nova-compute*

#### 编辑文件：

[root@compute ~]#`vi /etc/nova/nova.conf`

```shell
[DEFAULT]
# ...
enabled_apis = osapi_compute,metadata
```

```shell
[DEFAULT]
# ...
transport_url = rabbit://openstack:000000@controller 
```

```
[api]
# ...
auth_strategy = keystone

[keystone_authtoken]
# ...
www_authenticate_uri = http://controller:5000/
auth_url = http://controller:5000/
memcached_servers = controller:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = nova
password = 000000
```

```
[DEFAULT]
# ...
my_ip = 192.168.11.141
```

```
[DEFAULT]
# ...
use_neutron = true
firewall_driver = nova.virt.firewall.NoopFirewallDriver
```

```
[vnc]
# ...
enabled = true
server_listen = 0.0.0.0
server_proxyclient_address = $my_ip
novncproxy_base_url = http://controller:6080/vnc_auto.html
```

```
[glance]
# ...
api_servers = http://controller:9292
```

```
[oslo_concurrency]
# ...
lock_path = /var/lib/nova/tmp
```

```
[placement]
# ...
region_name = RegionOne
project_domain_name = Default
project_name = service
auth_type = password
user_domain_name = Default
auth_url = http://controller:5000/v3
username = placement
password = 000000
```

#### 确定计算节点是否支持虚拟机的硬件加速：

[root@compute ~]# egrep -c '(vmx|svm)' /proc/cpuinfo



```
[libvirt]
# ...
virt_type = qemu
```

#### 启动计算服务

> systemctl enable libvirtd.service openstack-nova-compute.service
> systemctl start libvirtd.service openstack-nova-compute.service

#### 将计算节点添加到单元数据库

#### 获取管理员凭据以启用仅限管理员的 CLI 命令，然后确认数据库中存在计算主机：

[root@controller ~]# . admin-openrc

[root@controller ~]# openstack compute service list --service nova-compute

#### 发现计算主机：

[root@controller ~]# su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova



```
[scheduler]
discover_hosts_in_cells_interval = 300
```

###### 验证操作（控制节点）

. admin-openrc

#### 列出服务组件以验证每个进程是否成功启动和注册：

openstack compute service list

#### 列出标识服务中的 API 终结点，以验证与标识服务的连接：

openstack catalog list

#### 列出影像服务中的图像以验证与影像服务的连通性:

openstack image list

#### 检查单元和放置 API 是否成功工作，以及其他必要的先决条件是否到位：

[root@controller ~]# nova-status upgrade check



# **neutron** 服务

#### 使用数据库访问客户端以用户身份连接到数据库服务器：`root`

[root@controller ~]# mysql -u root -p000000

```shell
MariaDB [(none)] CREATE DATABASE neutron;
```

#### 授予对数据库的正确访问权限，并替换为合适的密码：`neutron``NEUTRON_DBPASS`

```shell
MariaDB [(none)]> GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' \
  IDENTIFIED BY '000000';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' \
  IDENTIFIED BY '000000';
```

#### 获取凭据以获取对仅限管理员的 CLI 命令的访问权限：`admin`

[root@controller ~]# . admin-openrc

#### 创建用户：`neutron`

[root@controller ~]# openstack user create --domain default --password-prompt neutron 密码输入

#### 将角色添加到用户：`admin neutron`

[root@controller ~]# openstack role add --project service --user neutron admin

这段无输出

#### 创建服务实体：`neutron`

[root@controller ~]# openstack service create --name neutron \
  --description "OpenStack Networking" network

#### 创建网络服务 API 终结点：

[root@controller ~]# openstack endpoint create --region RegionOne \
  network public http://controller:9696

[root@controller ~]# openstack endpoint create --region RegionOne \
  network internal http://controller:9696

[root@controller ~]# openstack endpoint create --region RegionOne \
  network admin http://controller:9696 

#### 提供者网络

[root@controller ~]#*yum install openstack-neutron openstack-neutron-ml2 openstack-neutron-linuxbridge ebtables -y*

编辑文件：

`vi /etc/neutron/neutron.conf`

```
[database]
# ...
connection = mysql+pymysql://neutron:000000@controller/neutron

 [DEFAULT]
# ...
core_plugin = ml2
service_plugins =
[DEFAULT]
# ...
transport_url = rabbit://openstack:000000@controller 
[DEFAULT]
# ...
auth_strategy = keystone

[keystone_authtoken]
# ...
www_authenticate_uri = http://controller:5000
auth_url = http://controller:5000
memcached_servers = controller:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = neutron
password = 000000

[DEFAULT]
# ...
notify_nova_on_port_status_changes = true
notify_nova_on_port_data_changes = true

[nova]
# ...
auth_url = http://controller:5000
auth_type = password
project_domain_name = default
user_domain_name = default
region_name = RegionOne
project_name = service
username = nova
password = 000000
[oslo_concurrency]
# ...
lock_path = /var/lib/neutron/tmp
```

编辑文件：

`vi /etc/neutron/plugins/ml2/ml2_conf.ini`

```shell
[ml2]
# ...
type_drivers = flat,vlan
[ml2]
# ...
tenant_network_types =


[ml2]
# ...
mechanism_drivers = linuxbridge

[ml2]
# ...
extension_drivers = port_security

[ml2_type_flat]
# ...
flat_networks = provider

[securitygroup]
# ...
enable_ipset = true

```

编辑文件：

`vi /etc/neutron/plugins/ml2/linuxbridge_agent.ini`

```shell
[linux_bridge]
physical_interface_mappings = provider:ens33 #替换为基础提供程序物理网络接口的名称。
[vxlan]
enable_vxlan = false
[securitygroup]
# ...
enable_security_group = true
firewall_driver = neutron.agent.linux.iptables_firewall.IptablesFirewallDriver

net.bridge.bridge-nf-call-iptables
net.bridge.bridge-nf-call-ip6tables


```

编辑文件：

`vi /etc/neutron/dhcp_agent.ini`

```shell
[DEFAULT]
# ...
interface_driver = linuxbridge
dhcp_driver = neutron.agent.linux.dhcp.Dnsmasq
enable_isolated_metadata = true
```

编辑文件：

`vi /etc/neutron/metadata_agent.ini`

```shell
[DEFAULT]
# ...
nova_metadata_host = controller
metadata_proxy_shared_secret = METADATA_SECRET #替换为元数据代理的合适机密。METADATA_SECRET
```

编辑文件：

`vi /etc/nova/nova.conf`

```shell
[neutron]
# ...
auth_url = http://controller:5000
auth_type = password
project_domain_name = default
user_domain_name = default
region_name = RegionOne
project_name = service
username = neutron
password = 000000
service_metadata_proxy = true
metadata_proxy_shared_secret = METADATA_SECRET
替换为为元数据代理选择的机密。METADATA_SECRET
```

#### 网络服务初始化脚本需要一个指向 ML2 插件配置文件 的符号链接。如果此符号链接不存在，请使用以下命令创建

[root@controller ~]# ln -s /etc/neutron/plugins/ml2/ml2_conf.ini /etc/neutron/plugin.ini

#### 填充数据库：

[root@controller ~]# su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf \
  --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head" neutron

#### 重启：

> systemctl restart openstack-nova-api.service

#### 启动网络服务并将其配置为在系统启动时启动：

> systemctl enable neutron-server.service \
>   neutron-linuxbridge-agent.service neutron-dhcp-agent.service \
>   neutron-metadata-agent.service
> systemctl start neutron-server.service \
>   neutron-linuxbridge-agent.service neutron-dhcp-agent.service \
>   neutron-metadata-agent.service

#### 配置计算节点neutron

[root@compute ~]# *yum install openstack-neutron-linuxbridge ebtables ipset*

编辑文件：

[root@compute ~]#`vi /etc/neutron/neutron.conf`

```shell
[DEFAULT]
# ...
transport_url = rabbit://openstack:000000@controller 
```

```shell
[DEFAULT]
# ...
auth_strategy = keystone

[keystone_authtoken]
# ...
www_authenticate_uri = http://controller:5000
auth_url = http://controller:5000
memcached_servers = controller:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = neutron
password = 000000
[oslo_concurrency]
# ...
lock_path = /var/lib/neutron/tmp
```

#### 配置 Linux 桥代理

编辑文件：

[root@compute ~]# `vi /etc/neutron/plugins/ml2/linuxbridge_agent.ini`

```
[linux_bridge]
physical_interface_mappings = provider:ens33 #替换为基础提供程序物理网络接口的名称
[vxlan]
enable_vxlan = false
[securitygroup]
# ...
enable_security_group = true
firewall_driver = neutron.agent.linux.iptables_firewall.IptablesFirewallDriver


net.bridge.bridge-nf-call-iptables
net.bridge.bridge-nf-call-ip6tables
```

#### 将计算服务配置为使用网络服务

编辑文件：

[root@compute ~]# `vi /etc/nova/nova.conf`

```
[neutron]
# ...
auth_url = http://controller:5000
auth_type = password
project_domain_name = default
user_domain_name = default
region_name = RegionOne
project_name = service
username = neutron
password = 000000
```

#### 重新启动计算服务：

> systemctl restart openstack-nova-compute.service

#### 启动 Linux 桥接代理并将其配置为在系统引导时启动：

> systemctl enable neutron-linuxbridge-agent.service
>
> systemctl start neutron-linuxbridge-agent.service



# **dashboard**服务

[root@controller ~]# *yum install openstack-dashboard*

#### 编辑文件：

[root@controller ~]# `vi /etc/openstack-dashboard/local_settings`

```
OPENSTACK_HOST = "controller"
```

```
ALLOWED_HOSTS = ['one.example.com', 'two.example.com']
```

```
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'

CACHES = {
    'default': {
         'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
         'LOCATION': 'controller:11211',
    }
}
```

```
OPENSTACK_KEYSTONE_URL = "http://%s:5000/v3" % OPENSTACK_HOST
```

```
OPENSTACK_KEYSTONE_MULTIDOMAIN_SUPPORT = True
```

```
OPENSTACK_API_VERSIONS = {
    "identity": 3,
    "image": 2,
    "volume": 3,
}
```

```shell
OPENSTACK_KEYSTONE_DEFAULT_DOMAIN = "Default"
```

```shell
OPENSTACK_KEYSTONE_DEFAULT_ROLE = "user"
```

```shell
OPENSTACK_NEUTRON_NETWORK = {
    ...
    'enable_router': False,
    'enable_quotas': False,
    'enable_distributed_router': False,
    'enable_ha_router': False,
    'enable_lb': False,
    'enable_firewall': False,
    'enable_vpn': False,
    'enable_fip_topology_check': False,
}
```

``` shell
TIME_ZONE = "Asia/Shanghai"（可选）
```

如果未包含，则将以下行添加到

[root@controller ~]#`vi /etc/httpd/conf.d/openstack-dashboard.conf`

```shell
WSGIApplicationGroup %{GLOBAL}
```

#### 重新启动 Web 服务器和会话存储服务：

> systemctl restart httpd.service memcached.service



#### http://192.168.11.140/dashboard`

