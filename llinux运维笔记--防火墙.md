## llinux运维笔记-->防火墙

本次使用的防火墙是centos7的firewalld，centos7以下用的是iptables

### 开启服务器并自启动

```bash
systemctl enable firewalld
systemctl start firewalld
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

