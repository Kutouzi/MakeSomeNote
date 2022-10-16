## linux运维笔记-->kubernetes

继docker swarm（现在已少用）后的容器编排工具，现已成为行业标准，下面简称kubernetes为k8s。它可编排的容器不止是有docker，还兼容其他的容器如红帽的podman，原因是他们都有实现OCRI容器接口标准（docker没有，它是CRI，但它有自己进行OCRI到CRI的转换）。

### 各基本组件结构介绍

#### etcd

它是一个使用raft算法的键值对数据库，用来存储集群服务器信息、集群资源占用信息等

在v2版本中数据存储在内存中，不稳定已弃用；在v3版存储在磁盘中，变得更可靠

#### 调度器（scheduler）

它是用来让资源合理的分发到各节点上并且监控节点运行情况的，核心算法是不能让节点空闲

### 控制器（controller）

它是用来管理一组pod的，通过和api server交互来管理pod组的状态。在一个集群中可以有多个控制器，k8s提供多种内置的控制器，如Deployments和job等。

一些常用的控制器命令，以deployment控制器为例

```shell
# 创建控制器
kubectl create deployment <控制器名称> --image=<docker镜像名> -n <命名空间名称>
# 删除控制器，当控制器删除时底下的容器组一同被删除
kubectl delete deployment <控制器名称>
```

也可以使用yaml和kubectl创建

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <控制器名称>
  namespace: <所属命名空间名称>
  labels:
    app: <控制器名称>
spec:
  replicas: <集群容器数量>
  selector:
    matchLabels:
      app: <容器名称>
  template:
    metadata:
      labels:
        app: <容器名称>
    spec:
      containers:
      - name: <容器名称>
        image: <docker镜像名>
        ports:
        - containerPort: <容器对外暴露端口号>
```

控制器创建好后可以在kuboard图形界面中的工作负载中可以查看到，还可以看到它管理的pod组

#### 接口服务（api server）

它是所有管理员请求、用户请求的入口，我们需要k8s执行任务、查询请求、访问pod中服务等，都要下发给这个入口

下面是一些常用的命令

```shell
# 想要使用server访问到pod，首先要建立与控制器的联系，以deployment控制器为例
# 服务模式有NodePort模式可以让服务对集群外暴露端口，ClusterIP只对集群内暴露
kubectl expose deployment <控制器名称> --port=<server暴露端口> --target-port=<容器对外暴露端口> --type=<服务模式> -n <命名空间名称>
# 查看创建好的服务
kubectl get service -n <命名空间名称>
# 删除服务，服务名称一般和控制器名称会一样
kubectl delete service <服务名称> -n <命名空间名称>
```

也可以通过yaml文件来创建服务

```yaml
apiVersion: v1
kind: Service
metadata:
  name: <和控制器名称相同的服务名称>
  namespace: <所属命名空间名称>
  labels:
    app: <和控制器名称相同的服务名称>
  resourceVersion: ‘<service对集群外暴露端口号>’
spec:
  clusterIP: <service的集群ip>
  selector:
    app: <联系的容器名称>
  port:
  - port: <server暴露端口>
    targetPort: <联系的容器对外暴露端口>
  type: <服务模式>
```



建立好联系后，可通过service的地址加端口访问pod服务，并且访问时是负载均衡的

#### 命令行管理工具（kubectl）

用来管理k8s的命令行工具，可安装在任何节点上，只要能连接到接口服务（但一般安装在master节点）

一些常用的pod操作命令

```shell
# 用于查看pod状态信息的命令
kubectl describe pod <pod名称> -n <命名空间名称>
# 和docker一样可以进入容器内
kubectl exec -it <pod名称> -n <命名空间名称> -- bash
# 使用yaml文件创建集群
kubectl apply -f <yaml文件>
# 使用yaml文件删除集群，和上面同一个文件，但是反向操作
kubectl deleted -f <yaml文件>
```

#### 网页UI（web UI）

用来管理k8s的网页，需要连接到接口服务，一般只用来看数据

#### node组件-kubelet

它会根据接口服务发来的命令，对docker容器进行操作

#### node组件-kube proxy

它会监听接口服务，调用linux内核的net link接口，对linux的ipvs和防火墙进行管控。负载均衡和自动转发都由它实现

### k8s名词解释

#### 节点

k8s通过将容器放入在节点（Node）上运行的 Pod 中来执行你的工作负载。 节点可以是一个虚拟机或者物理机器，取决于所在的集群配置。 每个节点包含运行Pod所需的服务； 这些节点由控制平面负责管理。

#### Pod

Pod是k8s中能够创建和部署的最小单元，是k8s集群中的一个应用实例，也是扩容的一个最小单元。它总是部署在同一个节点上。Pod中包含了一个或多个容器，还包括了存储、网络等各个容器共享的资源。Pod支持多种容器环境，Docker则是最流行的容器环境。它一般会挂在控制器下，用一对多的方式管理pod。

- 单容器Pod，最常见的应用方式。
- 多容器Pod，对于多容器Pod，k8s会保证所有的容器都在同一台物理主机或虚拟主机中运行。多容器Pod是相对高阶的使用方式，除非应用耦合特别严重，一般不推荐使用这种方式。一个Pod内的容器共享IP地址和端口范围，容器之间可以通过 localhost 互相访问。

以下命令都需要在master节点下执行

通过这个命令可以查看所有的pod

```shell
# 不加-A就只查看当前命名空间下的
kubectl get pods -A
# 加-n可以查看指定命名空间下的
kubectl get pod -n <命名空间名称>
```

通过这个指令可以根据镜像运行pod，如果镜像拉取失败也可以使用docker命令直接拉取，或者检查dns和work节点问题

```shell
# pod名称英文名可随意取，--image指定启动的镜像，镜像名docker官网可以是url地址，-n指定所属命名空间，不指定就默认
kubectl run <pod名称> --image=<docker镜像名> -n <命名空间>
```

通过这个指令可以删除运行的pod

```shell
# 切记指定命名空间，否则可能误删其他命名空间下同名pod
kubectl delete pod <pod名称> -n <命名空间>
```

也可以通过yaml文件夹kubectl来创建一个多容器的pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: <pod名称>
  namespace: <命名空间名称>
spec:
  containers:
  - image: <docker镜像名>
    name: <容器名称>
  - image: <docker镜像名>
    name: <容器名称>
```

yaml写好后使用这个命令就可以创建出一个多容器的pod

```shell
kubectl apply -f <yaml文件>
```



### kuboard网页管理集群插件

kuboard是一个图形化的网页管理集群插件，kuboard官网（kuboard.cn）有安装方式。

#### IngressClass

它是一个允许使用域名去访问service提供的动态ip的插件，从而做到无论service如何重启如何变更ip，域名都能够访问到service的效果。这个插件需要额外安装，在kuboard的网络下可以自动安装并且创建ingress容器。

安装好后可以通过yaml来创建容器

```yaml
apiVersion: neworking.k8s.io/v1
kind: Ingress
metadata:
  name: <Ingress名称>
  namespace: <命名空间名称>
spec:
  ingressClassName: <安装时被指定的IngressClass名称>
  rules:
  - host: <域名名称>
    http:
      paths:
      - path: <url路径名>
        pathType: <nginxlocation的路径匹配规则，Prefix>
        backend:
          service:
            name: <ingress要映射的service的名称>
            port:
              number: <service的内部暴露端口>
```



### namespace命名空间

命名空间为区分业务或各种环境而存在，可以通过kubectl创建命名空间

```shell
# 查看所有的命名空间
kubectl get namespace
# 创建命名空间
kubuctl create ns <命名空间名称>
```

也可以用yaml创建

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: <命名空间名称>
```

yaml写好后用这个命令可以直接

```shell
kubectl apply -f <yaml文件>
```

> 注：命名空间被删除下面的东西都会被删除。

### 使用控制器管理Pod

#### RC

它支持副本数量与期望值之间的管理

#### RS

它的功能和RC类似，但增加了集合式的标签选择器

#### Deployment

它支持滚动更新和回滚

#### HPA

根据Pod资源使用情况调整副本数量，依赖于RC、RS和Deployment之上，目标是自动扩容缩

#### StatefulSet

它是为了解决有状态服务和无状态服务设计的。简单来说有状态服务指的是不管容器新建还是重启，里面的数据都一直存在。比如mysql数据库就是典型的有状态服务，如果让mysql以无状态方式运行那么再容器宕机新建后保存的数据将不复存在。

#### DeamonSet

它是为了确保每个一节点上都有且只有一个Pod，并且是动态的，在删除或者新增节点时都会让全部节点保持有且只有一个Pod。

#### Job

它保证批处理任务的一个或多个Pod成功结束。目标是成功退出，返回码为0。

#### CronJob

它建立在Job之上，负责基于时间管理周期性的在某个时间点运行的一次任务。例如数据库的定时多次冗余备份就可以用到。

#### replication controller

副本控制器，它是用来管理容器的状态，维稳集群的。处理一些节点扩容，重启节点等

#### 自定义控制器

可以根据k8s的手册开发自己的控制器

### 网络通信

#### 容器隔离

容器之间用namespace network进行网络隔离

#### 同Pod内不同容器间通信

使用IO的方式，因为距离近所以这也是通信效率最高的方式，一般是通信比较频繁的应用会放在同一个Pod内（点名nginx和php-fpm）

#### 不同Pod通信-扁平网络空间

k8s网络模型假定所有Pod都在一个可以直接连接的网络内，即使不在同一个物理机上（同一个物理机直接docker网桥），Pod之间也能通过虚拟ip轻易互相连通。但在搭建私有云情况下（谷歌云除外），需要自己实现这个假设的网络，将不同节点上的docker容器之间的互相访问先打开，然后运行k8s。

#### Flannel插件

它是CoreOS团队专门为了解决搭建这个网络模型而针对k8s开发的一个插件。它的功能时让集群中的不同节点的主机创建的docker容器都有全集群中唯一的虚拟ip地址，并且建立一个覆盖网络，通过覆盖网络把数据包原封不动（指ip用覆盖网络内部ip即可）且无视物理ip地传递到目标容器内。

无视物理ip的原理是用数据包套数据包，即用物理上真实要传输的数据包的数据体内套入容器内要传的数据包，最后在到达目标物理主机时拆开就能得到容器内用的数据包。此过程需要etcd了解物理机ip和容器内设置的ip之间的对应关系。在此过程中使用的是UDP，因为速度快但不稳定（使用UDP的原因是不同节点一般都会在同一个机房内部，传输链路短基本UDP不会丢）

