# kvm
> 开启虚拟化功能

> 关闭防火墙和SE l inux

```shell
[root@wy~]#systemctl stop firewalld
[root@wy~]#setenforce 0             
```

> 查看是否关闭selinux

```shell
[root@wy~]#getenforce
```

> 查看虚拟化功能是否安装成功开启

           ```shell
           [root@wy~]#grep -E “（vmx）|（svm）”/proc/cpuinfo
           ```

> 安装相关软件包

   ```shell
   [root@wy~]#yum -y install qemu -kvm openssl libvirt net-tools             
   ```

> 开启 libvirt

```shell
[root@wy~]#csystemctl start libvirtd
```

> 查看是否开启

          ```shell
          [root@wy~]#systemctl status libvirtd
          ```

> 创建软连接

```shell
[root@wy~]# ln -s /usr/libexec/qemu-lvm /usr/bin/qemu -kvm
```

> 给文件加执行权限

  ```shell
  [root@wy~]#chmod +x qemu -ifup-NAT.txt
  ```

​      

​    

