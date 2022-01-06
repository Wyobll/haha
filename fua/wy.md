# 我D笔记

## wc

> wc命令用于统计指定文件中的字节数、字数、行数，并将统计结果显示输出。wc是word count的缩写，即统计单词数。

***如果我想知道一个文件里有多少字？多少行？多少字符？
wc这个命令就可以帮我们计算输出整体数据***

```shell
[root@wy~]#wc 【参数】
参数：
-l ： 仅列出行；
-w ： 仅列出多少字（英文单字）；
-m ： 多少字符；
```

* 例： /etc/man.config 里面到底有多少相关字，行，字符数？
  【root@wy~] #cat /etc/man.config | wc
  101 235 4654

​       (输出的三个数字中，分别代表：行，字数，字符数)

> 使用管道操作符“|”可以把一个命令的标准输出传送到另一个命令的标准输入中，连续的|意味着第一个命令的输出为第二个命令的输入，第二个命令的输入为第一个命令的输出，依次类推。



## 还原系统破解密码

> * 开机启动，到内核界面，按 #e
> * 找到linux16哪一行，跳到行尾，空格输入 #rd.break console=tty0 
>    按ctrl + x 。
> * 界面跳转到 （switch_root:/# ）,代表进入了紧急救援模式
> * 获取修改密码的权限：把系统真实根目录以读写权限临时挂载到当前紧急救援模式下
>   switch_root:/# mount -o remount,rw / /sysroot
> * 把当前紧急救援模式根目录切换到/sysroot
>           #chroot /sysroot （终端由switch_root:/切换为 sh-4.2）
> * 修改密码 #passwd
> *  重置SELinux（上下文标记），#touch /.autorelabel 
>    （有这个文件，系统就会重置SELinux）
> * 退出当前当前目录 exit 
> * 重启 #reboot

---

## 磁盘分区

```shell
fdisk -l (查看磁盘 /dev/vda是系统盘，其他为新增盘)
fdisk 硬盘设备
fdisk /dev/vdb(新增盘)
   n （回车） 新建分区
   p  创建主分区
root@wy~]#lsblk #查看当前系统的识别的硬盘\
  
  创建第一个主分区
  n 创建新分区
  p 选择主分区
  回车（默认分区号）
  回车 （默认磁盘扇区从2048开始）
  +nG （设置区分大小为nG）
  p （查看）
  
  创建逻辑分区
  n 创建新分区
  回车 （默认分区）
  回车 （默认磁盘扇区从10489856开始）
  +nG
  p
  
  q 放弃更改并退出
  w 保存更改并退出
```

  “Partition 1 of type Linux and of size 20 GiB is see”表示分区完成

```shell
[root@wy~]#mkfs ext4 /dev/vdb 系统文件格式化为ext4
[root@wy~]# mkfs.xfs /dev/vdb 系统文件格式化为xfs
[root@wy~]#mkdir /mnt/part 新建挂载点
[root@wy~]# mount /dev/vdb /mnt/part 挂载至/mnt/part上
[root@wy~]# df -Th 查看是否挂载成功
```

---

## 交换空间（虚拟内存）

### 一，利用未使用的分区空间制作交换空间

```shell
 [root@wy~]# ls /dev/sdb1
 [root@wy~]# mkswap /dev/sdb1 
 #格式化交换文件系统
 [root@wy~]# blkid /dev/sdb1  
 #查看文件系统 [root@wy~]# swapon /dev/sdb1  
 #启用交换分区
 [root@wy~]# swapon   
 #查看组成交换空间的成员信息
 [root@wy~]# free -m 
 #查看交换空间总共的大小 [root@wy~]# swapoff /dev/sdb1  
 #停用交换分区
 [root@wy~]# swapon   
 #查看组成交换空间的成员信息
 [root@wy~]# free -m   
 #查看交换空间总共的大小 [root@wy~]# vim /etc/fstab 
 #开机自动启用交换分区
            /dev/sdc1 swap swap defaults 0 0
 [root@wy~]# swapon
 [root@wy~]# swapon -a 
 #专门用于检测交换分区
 [root@wy~]# swapon
```

### 二,利用一个文件，进行制作交换空间

#### 1.生成一个512M的文件

* – dd if=源设备 of=目标设备 bs=块大小 count=次数  

  ```shell
  [root@wy~]# ls /dev/zero 永远产生数据 [root@wy~]# dd if=/dev/zero of=/dev/sdb1 bs=1M count=512M
  [root@wy~]# du -sh /dev/sdb1 
            # 查看占用磁盘空间大小
  ```

#### 2.利用文件占用空间，充当交换空间

```shell
[root@wy~]# mkswap /dev/sdb1  格式化交换文件系统 [root@wy~]# swapon /dev/sdb1  启用交换文件
swapon: /dev/sdb1：不安全的权限 0644，建议使用 0600。
[root@wy~]# swapon  查看交接空间组成的成员信息
```

---

## 逻辑卷的创建与管理

* 创建分区-创建物理卷-创建卷组-创建逻辑卷

  ---

> 1、创建分区
>
> 此时挂载一块20G的硬盘，sdb
> 使用fdisk进行分区
>
> [root@wy~]# **fdisk /dev/sdb**
>
> 先按p，然后一直回车即可，最后按w保存退出
> 格式化分区
>
> mkfs，使用xfs格式
>
> [root@wy~]# **mkfs.xfs /dev/sdb1**

---

> 2、创建物理卷
>
> [root@wy~]# **pvcreate /dev/sdb1**
>
> 查看：pvs或pvdisplay

---

> 3、创建卷组
>
> [root@wy~]# **vgcreate myvg /dev/sdb1**
>
> 查看：vgs或vgdisplay

---

> 4、创建逻辑卷
>
> [root@wy~]# **lvcreate -L 1G -n <u>mylv</u> <u>myvg</u>**
>
> ​                                            <u>逻辑卷名称</u> <u>基于卷组名</u>
>
> 使用命令lvdisplay查看：
>
> [root@wt~]# **lvdisplay | grep mylv**
>
> 使用mkfs进行格式化：
>
> [root@wy~]# **mkfs.xfs /dev/myvg/mylv**
>
> 挂载到/mnt/lv101目录下：
>
> [root@wy~]# **mount /dev/myvg/mylv /mnt/lv101**
>
> 查看：
>
> [root@wy~]# **df -Tf /mnt/lv101**

