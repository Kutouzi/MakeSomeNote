## linux运维笔记 -->系统中重要的文件

### 1. /etc/passwd

内有所有用户信息

### 2. /etc/group

内有所有组群信息

### 3. /proc/

目录下的只有数字的文件为进程，这个数字下的目录中文件描述符fd中有0,1,2分别对应输入，输出和报错流

### 4. /etc/sysconfig/network-scripts/

目录下为网络接口卡信息：

```yaml
DEVICE=eth0 #系统中的网卡编号
HWADDR=00:00:00:00:00:00 #MAC地址
TYPE=Ethernet #网络类型
ONBOOT=yes #是否开机即启用
NM_CONTROLLED=yes
IPADDR=192.168.1.2 #网络层ip地址
NETMASK=255.255.255.0 #掩码，与ip的二进制与运算后得网络地址，取反得主机地址
GATEWAY=192.168.1.1 #默认网关ip地址
DNS1=8.8.8.8 #主DNS
DNS2=114.114.114.114 #备用DNS
```

