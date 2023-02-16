# Ubuntu

**Ubuntu(乌班图)是一个以桌面应用为主的Linux操作系统，其名称来自非洲南部祖鲁语或豪萨语的"ubuntu"一词，意思是"人性"、"我的存在是因为大家的存在"，是非洲传统的一种价值观，类似华人社会的"仁爱"思想。**

* ## 基本命令

  ~~~assembly
  ls:显示当前目录下的内容
  cd [+目录名]：切换文件夹
  touch [+文件名]：在当前目录下新建一个文件
  mkdir [+目录名]：在当前目录下创建一个文件夹(创建目录)
  rm [+文件名]：删除指定的文件名
  clear：清屏
  ~~~
  
* ## 快捷键

  ```assembly
  CTRL + ALT + T：打开新的终端
  SHIFT + CTRL + T：在当前终端同级打开新的终端
  TAB：自动补全命令或文件名
  CTRL + SHIFT + C：复制
  CTRL + SHIFT + V：粘贴
  CTRL + D：关闭标签页
  CTRL + L：清除屏幕
  CTRL + R + 文本：在输入历史中搜索
  CTRL + A：移动到行首
  CTRL + E：移动到行末
  CTRL + C：终止当前任务
  CTRL + Z：把当前任务放到后台运行（相当于运行命令时后面加&）
  ```

* ## 权限

  ```assembly
  r 读取 4（数字表示） 可以读取文件夹目录内容
  w 读取 2（数字表示） 可以在文件夹内新建删除文件
  x 读取 1（数字表示） 可以进入到文件夹内
  
  -rwx（所有者） r-x（所属组） r--（其他人）
  ```

* ## 系统管理命令

  ```assembly
  fdisk -l #查看系统分区信息
  fdisk /dev/sdb #为一块新的 SCSI 硬盘进行分区
  chown root /home #把/home 的属主改成 root 用户
  chgrp root /home #把/home 的属组改成 root #组
  Useradd #创建一个新的用户
  Groupadd #组名 创建一个新的组
  Passwd #用户名 为用户创建密码
  Passwd -d #用户名 删除用户密码也能登陆
  Passwd -S #用户名 查询账号密码
  Usermod -l #新用户名 老用户名 为用户改名
  Userdel–r #用户名 删除用户一切
  service [servicename] start/stop/restart #系统服务控制操作
  /etc/init.d/[servicename] start/stop/restart #系统服务控制操作
  uname -a #查看内核版本
  cat /etc/issue #查看 ubuntu 版本
  lsusb #查看 usb 设备
  sudo ethtool eth0 #查看网卡状态
  cat /proc/cpuinfo #查看 cpu 信息
  lshw #查看当前硬件信息
  sudo fdisk -l #查看磁盘信息
  df -h     #查看硬盘剩余空间
  free -m     #查看当前的内存使用情况
  ps -A     #查看当前有哪些进程
  kill #进程号(就是ps -A中的第一列的数字)或者 killall 进程名( 杀死一个进程)
  kill -9 进程号     #强制杀死一个进程 
  ```
  
* ## 打包/解压 

  ```assembly
  tar -c 创建包 –x 释放包 -v 显示命令过程 –z 代表压缩包
  tar –cvf benet.tar /home/benet 把/home/benet目录打包
  tar –zcvf benet.tar.gz /mnt 把目录打包并压缩
  tar –zxvf benet.tar.gz 压缩包的文件解压恢复
  tar –jxvf benet.tar.bz2 解压缩 
  ```

* ## make编译

  ```assembly
  make 编译
  make install 安装编译好的源码包 
  ```

* ## apt命令 

  ```commonlisp
  apt-cache search package     #搜索包
  apt-cache show package     #获取包的相关信息，如说明、大小、版本等
  sudo apt-get install package     #安装包
  sudo apt-get install package - - reinstall     #重新安装包
  sudo apt-get -f install     #修复安装”-f = –fix-missing”
  sudo apt-get remove package     #删除包
  sudo apt-get remove package - - purge     #删除包，包括删除配置文件等
  sudo apt-get update     #更新源
  sudo apt-get upgrade     #更新已安装的包
  sudo apt-get dist-upgrade     #升级系统
  sudo apt-get dselect-upgrade     #使用 dselect 升级
  apt-cache depends package     #了解使用依赖
  apt-cache rdepends package     #是查看该包被哪些包依赖
  sudo apt-get build-dep package     #安装相关的编译环境
  apt-get source package     #下载该包的源代码
  sudo apt-get clean && sudo apt-get autoclean     #清理无用的包
  sudo apt-get check     #检查是否有损坏的依赖
  sudo apt-get clean     #清理所有软件缓存（即缓存在/var/cache/apt/archives目录里的deb包）
  ```



* ## 用户管理和文件权限

  **`Ubuntu`系统中需要在系统安装完成之后，启用root账号（设置root账号密码）**

  ```
  sudo passwd root
  ```

  查看用户id id ashin

  **添加用户（useradd）**

  address （在当前系统下，封装了原本的useradd函数）

  ```ceylon
  adduser ashin02
  ：设置密码
  ：确认密码
  ：输入用户信息（回车使用默认信息）
  ```

  创建完成之后，可以查看系统为当前用户自动创建同名组ashin02，在/etc/passwd下，新增了该用户相关配置信息，用户添加命令还为该用户创建了一个家模流/home/ashin02，作为该用户的主目录。所有者为ashin02

  在创建用户的时候，指定加入某个组，而不是新建一个组：

  ```
  adduser --ingroup root ashin02
  ```

  **设置密码**

  使用passwd命令，修改其他用户的密码（只有root用户才可以给其他账户设置密码，普通用户只能修改自己的密码）

  ```
  passwd ashin02
  ```

  **删除用户deluser**

  ```
  deluser ashin02
  ```

  **用户间切换su**

  su switch user

  ```ceylon
  su - user （连带用户环境配置一起修改切换）
  su user1 （不会切换用户环境系统变量）
  ```

  
