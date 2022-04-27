# DOCKER

> Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的[镜像](https://baike.baidu.com/item/镜像/1574)中，然后发布到任何流行的 [Linux](https://baike.baidu.com/item/Linux)或[Windows](https://baike.baidu.com/item/Windows/165458)操作系统的机器上，也可以实现[虚拟化](https://baike.baidu.com/item/虚拟化/547949)。容器是完全使用[沙箱](https://baike.baidu.com/item/沙箱/393318)机制，相互之间不会有任何接口。



一个完整的Docker有以下几个部分组成：

1. DockerClient客户端
2. Docker Daemon守护进程
3. Docker Image镜像
4. DockerContainer容器 [2] 



| Docker | 面向对象 |
| ------ | -------- |
| 容器   | 对象     |
| 镜像   | 类       |



### 特性：

- Automating the packaging and deployment of applications（使应用的打包与部署自动化）

- Creation of lightweight, private PAAS environments（创建轻量、私密的PAAS环境）

- Automated testing and continuous integration/deployment（实现自动化测试和持续的集成/部署）

- Deploying and scaling web apps, databases and backend services（部署与扩展webapp、数据库和后台服务)

  

### 常用命令：

Docker环境信息   info、version
镜像仓库命令      login、logout、pull、push、search
镜像管理          build、images、import、load、rmi、save、tag、commit
容器生命周期管理  create、exec、kill、pause、restart、rm、run、start、stop、unpause
容器运维操作      attach、export、inspect、port、ps、rename、stats、top、wait、cp、diff、update
容器资源管理      volume、network
系统信息日志      events、history、logs
1.events打印容器的实时系统事件
2.history 打印出指定镜像的历史版本信息
3.logs打印容器中进程的运行日志

docker --help       #查看docker命令
docker info         #docker 详细信息，镜像和容器
docker version      #查看docker版本



### 镜像命令：

docker images # 查看docker镜像；

> REPOSITORY  #镜像仓库源                
> TAG         #镜像的标签                 
> IMAGE ID    #镜像id            
> CREATED     #创建时间             
> SIZE        #大小

docker images -a            #列出本地所有的镜像
docker images -q            #只显示镜像ID
docker images --digests     #显示镜像的摘要信息
docker images --no-trunc    #显示完整的镜像信息

docker search tomcat    # 从Docker Hub上查找tomcat镜像
STARS：                 # 关注度
docker search --filter=stars=300 tomcat     #从Docker Hub上查找关注度大于300的tomcat镜像

> NAME            #名称
> DESCRIPTION     #描述
> STARS           #点赞，关注度，类似GitHub
> OFFICIAL        #是否官方
> AUTOMATED       #是否自动构建

**镜像下载**

docker pull tomcat      #从Docker Hub上下载tomcat镜像，默认是最新版本。等价于：docker pull tomcat:latest
docker pull tomcat:8  # 选择指定版本下载



**删除镜像**

#单个镜像删除，相当于：docker rmi java:latest
docker rmi java
#强制删除(删除正在运行的镜像，注：以后台方式运行的不能被强制删除)
docker rmi -f java
#多个镜像删除，不同镜像间以空格间隔
docker rmi -f java tomcat nginx
#删除本地全部镜像
docker rmi -f $(docker images -q)



### 容器命令：

docker ps  //查看正在运行的容器

docker ps -a  //查看所有容 包括停止的容器

docker ps -q  //-q参数，只显示container id

docker inspect demo1  //查看容器详细信息



**容器启动与停止**

* docker run -i -t --name mycentos 镜像名称/镜像ID

   ||新建并启动容器，参数：-i  以交互模式运行容器；-t  为容器重新分配一个伪输入终端；--name  为容器指定一个名称

* docker run -d mycentos

   ||后台启动容器，参数：-d  已守护方式启动容器

* docker start 容器id

   ||启动容器

* docker restart 容器id

   ||重启容器

* docker kill 容器id

* docker stop 容器id

  ||关闭容器

**参数**

> -t 参数让Docker分配一个伪终端并绑定到容器的标准输入上
>
> -i 参数则让容器的标准输入保持打开
>
> -c 参数用于给运行的容器分配cpu的shares值
>
> -m 参数用于限制为容器的内存信息，以 B、K、M、G 为单位 
>
> -v 参数用于挂载一个volume，可以用多个-v参数同时挂载多个volume 
>
> -p 参数用于将容器的端口暴露给宿主机端口 格式：host_port:container_port 或者 host_ip:host_port:container_port
>
> --name 容器名称 
>
> --net 容器使用的网络 
>
> -d 创建一个后台运行容器