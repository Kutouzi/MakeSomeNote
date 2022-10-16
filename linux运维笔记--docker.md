## linux运维笔记-->docker

### 介绍

Docker可以看做虚拟机，每一个虚拟机叫一个镜像。把应用装入容器打包成镜像，可以在本地隔离的运行多个应用

### 基本命令

以下命令执行环境为centos8下

docker开启命令

```bash
/*开启docker服务*/
systemctl start docker
/*查看docker状态*/
systemctl status docker
/*让docker开机启动*/
systemctl enable docker
```

docker控制容器和镜像命令

```bash
/*搜索某镜像*/
docker search <镜像名>
/*拉取镜像*/
docker pull <镜像名:版本号>
/*从容器制作镜像*/
docker commit <容器id> <镜像名称:版本号>
/*压缩镜像*/
docker save -o <压缩文件名.tar> <镜像名称:版本号>
/*解压镜像*/
docker load -i <压缩文件名.tar>
/*从dockerfile创建镜像*/
docker build -t <镜像名> <dockerfile文件位置>
/*列出所有镜像*/
docker image
/*列出所有在运行的容器，a查看所有容器*/
docker ps
/*用镜像启动一个容器，i在无连接时也保持运行，t立刻打开，d后台运行（否则exit就停止容器），p设置端口映射（外部端口:容器端口），v设置文件映射方便修改容器内部的东西（外部目录绝对路径:容器目录绝对路径），e设置环境变量，name可以给容器起名*/
docker run -id <镜像id/镜像名>
/*停止容器*/
docker stop <容器id/容器名>
/*重启容器*/
docker restart <容器id/容器名>
/*退出容器终端，此命令进入容器内部才有效*/
exit
/*删除容器*/
docker rm <容器id/容器名>
/*进入一个容器*/
docker exec -it <容器id/容器名> /bin/bash
/*查看容器内目录内容*/
docker inspect <容器id/容器名>
/*在容器中使用gpu，需要安装nvidia-container-toolkit*/
docker run --gpus '"device=<gpu编号>"' <镜像id/镜像名>
```

### 容器拉取

如果官方有制作镜像可以直接拉取下来用

```bash
docker search <镜像名>
# 默认拉取latest版本
docker pull <镜像名>
```



### Dockerfile文件

用于快速创建统一的镜像文件名叫Dockerfile无扩展名

| 关键字  | 作用                   | 备注                                                         |
| ------- | ---------------------- | ------------------------------------------------------------ |
| FROM    | 指定父镜像             | 指定dockerfile基于哪个image构建                              |
| RUN     | 执行命令               | 在build时执行bash命令，格式：["指令","操作符1","操作符2",...] |
| CMD     | 执行指令               | 在run时执行bash命令，格式：["指令","操作符1","操作符2",...]  |
| COPY    | 复制文件               | build的时候复制文件到image中                                 |
| ADD     | 添加文件               | build的时候添加文件到image中，不仅仅局限于当前build上下文    |
| ENV     | 环境变量               | 指定build时候的环境变量，格式：环境名=值                     |
| VOLUME  | 定义外部可以挂载的数据 | 就是启动容器时候用v绑定的那个操作，格式：VOLUME["目录"]      |
| EXPOSE  | 暴露端口               | 指定容器对外暴露端口，格式：EXPOSE 端口/协议                 |
| WORKDIR | 工作目录               | 进入容器默认目录                                             |



### 数据卷

数据卷（volume）是所有容器共享的一个区间，是外部目录或文件对容器内的反射，任何一方修改都会改变文件内容。但创建反射时候如果外部目录有数据，则容器内直接同步外部目录数据。当某一被容器删除它不受影响。

```
/*创建数据卷*/
docker volume create <数据卷名称>
/*挂载到某一容器的某一路径下，不写外部绝对路径他会默认设置为/var/lib/docker/容器内部绝对路径/随机值/_data*/
/*因此不建议不写外部绝对路径*/
docker run -d -v <外部绝对路径>:<容器内绝对路径> <容器id>
```

通过上面-v的方式每创建一次容器都要指定一次路径很麻烦，因此有↓

数据卷容器：通过把所有的普通容器挂载到数据卷容器上，再把数据卷容器挂载到数据卷上

```
/*创建数据卷容器*/
docker run -it --name=<数据卷容器名> -v <外部绝对路径>:<容器内绝对路径> /bin/bash
/*把普通容器挂载到数据卷容器上*/
docker run -it --name=<容器名> --volumes-from <数据卷容器名> /bin/bash
```

### 容器编排

当容器一多，创建也成为麻烦。因此需要容器编排技术，如docker自己的docker-compose（这是单一服务器上创建多个容器的工具。现在已推出docker-swarm、k8s，可以多个服务器上创建多容器集群，更为强大）

```bash
/*创建docker-compose目录*/
mkd1ir ~/docker-compose
cd !$
```

```yml
/*在上面目录下，编写docker-compose.yml*/
version: '3'
services:
	<服务名>:
		image: <服务镜像名:版本号>
		ports:
			- <外部端口>:<容器内暴露端口>
		links:
			- <挂载的应用名>
		volumes:
			- <外部绝对路径>:<容器内绝对路径>
			- ...
	<挂载的应用名>:
		image: <应用镜像名:版本号>
		expose:
			- "<应用对外暴露端口名>"
```



### 常用命令

```bash
docker run -itdp <外部端口>:<容器内暴露端口>  镜像名
docker run -itd -p 65425:26900/tcp -p 65425:26900/udp -p 65427:26902/udp -p 8081:8081 -v /usr/steam/7day:/usr/steam/7day --name 7day2 7day2:latest
```

