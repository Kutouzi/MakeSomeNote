## llinux运维笔记-->防火墙

本次使用的防火墙是centos7的firewalld；centos7以下用的是iptables；另一个是ufw是ubuntu等debian家族的防火墙。三种不互斥，所以当开启firewalld时就不要开ufw，反之亦然。

### 开启服务器并自启动

```bash
systemctl enable firewalld
systemctl start firewalld
```

### 开启日志

```bash
# 将LogDenied=all，然后重启服务
vim /etc/firewalld/firewalld.conf
# 验证是否改成功，显示all就成功
firewall-cmd --get-log-denied
# 查看日志
dmesg --| grep -i reject
```



### 查询所有配置

```bash
firewall-cmd --list-all
```

### 查询端口是否开放

```bash
firewall-cmd --query-port=8080/tcp
```

### 开放移除端口

```bash
# 使用此方法开启是所有源ip都可以入站的，permanent参数表示永久生效
firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --permanent --remove-port=8080/tcp
```

### 开放移除服务

```bash
# firewall有内置的服务规则，可以直接使用，一般默认放行的服务有dhcpv6-client和ssh
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --remove-service=http
```

### 添加删除访问规则

```bash
# 一定要以rule开头。family表示协议；source表示源；address写ip地址；port写端口；protocol表示网络协议；accept表示放行（如果是drop就是拒绝）
# 在规则冲突时优先会拒绝，而不会像iptables一样按顺序
firewall-cmd --permanent --add-rich-rule 'rule family=ipv4 source address=10.10.10.10 port=80 protocol=tcp accept'
firewall-cmd --permanent --remove-rich-rule 'rule family=ipv4 source address=10.10.10.10 port=80 protocol=tcp accept'
```

### 重载规则

```bash
# 任何操作完之后都要重载规则才能生效
firewall-cmd --reload
```

### 常见端口开放

```bash
# 开放k8s的etcd集群所需端口2379（客户端监听）和2380（节点间内部通信）和6443api端口和8443虚拟ip监听的服务端口以及10248的kubelet检查健康服务、10250的kubelet服务
firewall-cmd --permanent --add-port=2379/tcp
firewall-cmd --permanent --add-port=2380/tcp
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=8443/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10248/tcp
# 开放k8s的flannel网络组件的端口
firewall-cmd --permanent --add-port=8472/tcp
# 开放mysql的端口3306
firewall-cmd --permanent --add-port=3306/tcp
```

