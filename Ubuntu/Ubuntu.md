# Ubuntu

**Ubuntu(乌班图)是一个以桌面应用为主的Linux操作系统，其名称来自非洲南部祖鲁语或豪萨语的"ubuntu"一词，意思是"人性"、"我的存在是因为大家的存在"，是非洲传统的一种价值观，类似华人社会的"仁爱"思想。**

* ## 基本命令

  ~~~
  
  ls:显示当前目录下的内容
  
  cd [+目录名]：切换文件夹
  
  touch [+文件名]：在当前目录下新建一个文件
  mkdir [+目录名]：在当前目录下创建一个文件夹(创建目录)
  rm [+文件名]：删除指定的文件名
  clear：清屏
  ~~~

* ## 快捷键

  ```
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

  ```
  r 读取 4（数字表示） 可以读取文件夹目录内容
  w 读取 2（数字表示） 可以在文件夹内新建删除文件
  x 读取 1（数字表示） 可以进入到文件夹内
  
  -rwx（所有者） r-x（所属组） r--（其他人）
  ```

* ## 系统命令

  ```
  fdisk fdisk -l 查看系统分区信息
  fdisk fdisk /dev/sdb 为一块新的 SCSI 硬盘进行分区
  chown chown root /home 把/home 的属主改成 root 用户
  chgrp chgrp root /home 把/home 的属组改成 root 组
  Useradd 创建一个新的用户
  Groupadd 组名 创建一个新的组
  Passwd 用户名 为用户创建密码
  Passwd -d 用户名 删除用户密码也能登陆
  Passwd -S 用户名 查询账号密码
  Usermod -l 新用户名 老用户名 为用户改名
  Userdel–r 用户名 删除用户一切
  service [servicename] start/stop/restart 系统服务控制操作
  /etc/init.d/[servicename] start/stop/restart 系统服务控制操作
  uname -a 查看内核版本
  cat /etc/issue 查看 ubuntu 版本
  lsusb 查看 usb 设备
  sudo ethtool eth0 查看网卡状态
  cat /proc/cpuinfo 查看 cpu 信息
  lshw 查看当前硬件信息
  sudo fdisk -l 查看磁盘信息
  ```

  