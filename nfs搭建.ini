    修改主机名（非必要）
客户端：hostnamectl set-hostname nfs-client
服务端：hostnamectl set-hostname nfs-server
 
    修改源
rm -rf /etc/yum.repos.d/*
curl -o /etc/yum.repos.d/CentOS-Base.repo

     下载nfs安装包
yum install -y nfs-utils

     修改服务端nfs配置
vim /etc/export
lopt/test*(rw,async,no_root_squash)

     创建共享文件夹并启动服务
mkdir /opt/test
systemctl start nfs

     客户端测试访问
showmount -e

     挂载nfs服务
mount -t nfs ip : /opt/test /mnt