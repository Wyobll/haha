# kvm
开启虚拟化功能

关闭防火墙和SE l inux
              systemctl stop firewalld
              setenforce 0
             查看是否关闭selinux
             getenforce

查看虚拟化功能是否安装成功开启
             grep -E “（vmx）|（svm）”/proc/cpuinfo

安装相关软件包
              yum -y install qemu -kvm openssl libvirt net-tools

开启 libvirt
              systemctl start libvirtd
 查看是否开启
              systemctl status libvirtd

创建软连接
             ln -s /usr/libexec/qemu-lvm /usr/bin/qemu -kvm给

给文件加执行权限
            chmod +x qemu -ifup-NAT.txt


