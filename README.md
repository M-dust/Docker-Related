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
--name="Name"
-d  //run in background
-it //使用交互方式运行, like OS
-p  // -p 8080:8080  hostPort:containePort
-P  //random port number
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
build    Build an image from a Dockerfile
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
wati 	 Block untill a container stops, then print its exit code
```
### 7.  运行过程

#### (1) docker run: 
	[1] Search in local computer, if found, run
	[2] Search in DockerHub, if not found, return error
	[3] Download to local computer, run 
