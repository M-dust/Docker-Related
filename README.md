# Docker-Related
### 1. 目的
	隔离，轻量（只需要最核心的环境），不用多次配置
### 2. 对比虚拟机
#####   VM:
	VM 需要运行一个完整的操作系统，体量大，冗余步骤多，启动缓慢，在该系统上安装运行软件，若实现隔离需要多个VM
#####   Container:
	1. 直接运行在宿主机，容器没有自己的内核，也没有虚拟化硬件
	2. 每个容器间相互隔离，每个容器有自己的文件系统，互不影响
##### DevOops

	更快速的交付和部署：省略繁杂文档，打包发布，一键运行

	更便捷的升级和扩缩容： 像搭积木

	更简单的系统运维： 开发测试环境高度一致

	更高效的系统资源利用： Docker是内核级的虚拟化，一个物理机上可运行很多容器实例，服务器性能可以被压榨到极致

### 3.名词
	image: template, for creating container
	container: run one or on group of app, created by using image
	repository: save images

### 4. 底层原理
	Docker is C/S struct. Run on host as daemon(守护进程), visited through Socket.
	DockerServer receive command from docker-client, and run the command.
### 5. Docker common command
##### (1) Help
```shell	
docker version
doker info
docker (command) --help
```	
##### (2) Image

```	
docker images  //check all images on local host
```
```
docker search (image name)
// can use filter
```
	
```
docker pull
//联合文件系统,分层下载
```

```
docker rmi -f (image id)   //delete image
```
##### (3) Container
```
docker run [可选参数] image
# parameter:
--name="Name"  //指定容器名字
     
-d  //run in background
-it //使用交互方式运行, like OS
-p  // -p 8080:8080  hostPort:containePort 端口映射
-P  //random port number
-v  //数据卷挂, 目录映射
-e  //环境配置，例如MySQL密码
```
###### Notice: Run in background requires one foreground app. 
###### If no foreground app is running, docker will stop
##### Exit container
```
exit  # stop container and exit
Ctrl + P + Q # only exit but not stop container
```
```
docker ps //list running container
# parameter:
-a     //list all 
-n=?   //latest n containers
-q     //list container ID    
```
```
docker rm containerID  //remove container
```
```
docker start containerID
docker restart containerID
docker stop containerID
docker kill containerID  //force stop
```
##### Show logs
```
docker logs (containerID)  // show logs
#parameter
-tf
--tail number // limit lines 
```
##### Show container
```
docker ps
docker inspect
```
##### Enter container
```
docker exec -it containerID bashShell  //enter command line
docker attach containerID            
```
##### Copy file from container to Host
```
docker cp containerID:pathInContainer PathOfHost
```
### 6. Command Summary
```
attach 	 Attach to a running container
build    Build an image from a DockerFile
commit   Create a new image from a container change
cp       Copy files/folders from container filesystem to host path
create   Create a new container
diff     Inspect changes on a container's filesystem
events 	 Get real time events from the server
exec     Run a command line in and existing container
export   Stream the contents of a containeras a tar archive
history  Show the history of an image
images   List images
import   Create a new filesystem image from the contents of a tarball
info     Display system-wide information
inspect  Return low-level information on a container
kill     Kill a running container
load 	 Load an image from a tar archive
login 	 Register or login to the docker registry server
logout   Log out from a docker registry server
logs 	 Fetch the logs of a container
port 	 Lookup the public-facing port which is NAT-ed to PRIVATE_PORT
pause 	 Pause all processes within a container
ps 	 List containers
pull 	 Pull an image or a repository from the docker registry server
push 	 Push an image or a repository to the docker registry server
restart  Restart a running container
rm 	 Remove one or more containers
rmi      Remove one or more images(images which are not using)
run      Run a command in a new container
save  	 Save and image to a tar archive
search 	 Search for an image on the Docker Hub
start 	 Start a stopped container
stop 	 Stop a running container
tag 	 Tag an image into a repository
top      Lookup the running processes of a container
unpause  Unpause a paused container
version  Show the docker version information
wait 	 Block until a container stops, then print its exit code
```
### 7.  运行过程

#### (1) docker run: 
	[1] Search in local computer, if found, run
	[2] Search in DockerHub, if not found, return error
	[3] Download to local computer, run 

### 8. 可视化

	- portainer: Docker图形化界面管理工具.提供一个后台面板供我们操作	
	- Rancher(for CL/CD)
### 9. Commit
	类似git
	docker commit -a="authorname" -m="message" containerID 目标镜像名:[TAG]	

-----------------------

##  Docker 原理
### 1. 镜像加载原理
Docker镜像由一层一层的文件系统组成，UnionFS.Bootfs(boot file system)主要包含bootloader和kernel,bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统Docker镜像的最底层是bootfs. boot加载完成后整个内核就在内存中了，此时内存使用权由bootfs转交给内核，系统也会卸载bootfs. rootfs在bootfs之上，包含的就是典型的Linux系统中的/dev,/proc,/bin,/etc等标准目录和文件，rootfs就是各种不同的操作系统发行版，如centos，Ubuntu

Docker container很小是因为是一个精简的OS，rootfs可以很小，只需要包含最基本的命令、工具和程序库就可以，因为底层直接用Host的kernel，自己只需要提供rootfs即可
### 2. 分层文件系统
	例如：操作系统/python/app
	Docker镜像都是只读的，容器启动时，一个新的可写层加到镜像的顶部，就是所谓的容器层，用户所有的操作也都是基于容器层，容器之下都是镜像层

----------------------

## 容器数据卷
	 - Docker的数据都在容器中，如果容器删除，数据就会丢失，所以需要持久化的措施
	 - 数据库需要可以存储在本地
	 所以需要一个数据共享技术，可以将docker容器产生的数据同步到本地
	 数据卷可以将容器内的目录挂载到Linux上
### 1.数据卷的作用：
	 - 容器的持久化和同步操作，容器间进行数据共享
### 2.数据卷的使用方法
	(1) -v
	    docker run -v PathInHost:PathInContainer -v ***:*** ...
	    可以实现容器内和容器外的同步，只需本地修改，容器会自动同步
	(2) docker run -v PathInContainer
	    实现匿名挂载, -v里只写了容器内路径不写宿主路径
	    docker volumes ls #查看所有volumes的情况
	(3) docker run -v 卷名:路径
	    具名挂载,通过查看volumes(卷)信息可以找到路径
	summary: -v 容器内路径               #匿名挂载
	         -v 卷名：容器内路径         #具名挂载
		 -v /宿主机路径：容器内路径  #指定路径挂载

	扩展： docker -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
	       docker -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx
	       通过ro rw改变读写权限,一旦设置容器对挂载的内容有限定权限了
	       ro readonly    只能通过宿主机操作，容器内部无法操作
	       rw readwrite   
	(4) 在DockerFile中指定挂载
### 3. 容器间挂载
	挂载在另一个container的卷上
	-v -volumes-from containerName(or ID)
	双向复制，任一容器删除，数据仍存在
	数据卷容器的生命周期一直持续到没有容器使用
	一旦持久化到本地，本地数据不会删除

----------------

## DockerFile
	DockerFile是用来构建Docker镜像的文件，命令参数脚本
	构建的步骤：
		-编写一个DockerFile
		-docker build 构建成为一个镜像
		-docker run   运行镜像
		-docker push  发布镜像(DockerHub or AliCloud repository)
### 1.基础知识
	 - 每个保留关键字（指令）都必须是大写的
	 - 执行从上到下的顺序
	 - # 注释
	 - 每一个指令创建一个新的镜像层并提交
### 2.指令关键字
	FROM 		# 基础镜像，一切从这里开始构建
	MAINTAINER 	# 镜像由谁写的，姓名+邮箱
	RUN 		# 镜像构建时需要运行的命令
	ADD 		# 添加内容，例如tomcat压缩包
	WORKDIR 	# 镜像的工作目录
	VOLUME 		# 挂载的目录
	EXPOSE 		# 暴露端口，保留端口配置
	CMD 		# 指定容器启动时要运行的命令，只有最后一个生效，可被替代
	ENTRYPOINT 	# 指定容器启动时要运行的命令，可追加命令
	ONBUILD 	# 当构建一个被继承DockerFile时就会运行ONBUILD指令
	COPY 		# 类似ADD，将文件拷贝到镜像
	ENV 		# 构建时设置环境变量

--------------

## Docker 网络
	核心：Linux虚拟化网络技术
	Docker中所有的网络接口都是虚拟的，虚拟的转发效率高（内网传输文件）
	相关知识： 网络模式
	 - bridge     桥接（docker默认）
	 - none       不配置网络
	 - host       与宿主机共享网络
	 - container  容器网络联通（局限性大，用得少） 	
### 1. Docker0
	container共用一个路由器，即Docker0
	容器不指定网络的情况下，Docker会自动分配一个默认的可用ip
### 2. --link 指令  (不建议使用)
	docker run container1 --link container2
	// 单向连接
### 3. 自定义网络
	docker network ls # List network
	docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
	------------------------默认bridge------子网--------------------网关--------------- 网络名
	docker run -d -P --name testcase-01 --net mynet tomcat
	docker run -d -P --name testcase-02 --net mynet tomcat
	可以直接通过容器名称连接容器
	不同的集群使用不同的网络可以保证集群是安全和健康的
	docker network connect   //connect one container to a network
--------------

