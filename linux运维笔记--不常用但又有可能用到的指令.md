## linux运维笔记 -->会忘但有可能用到的

### 命令

#### 单纯命令

|            命令             |             作用              |                             选项                             |
| :-------------------------: | :---------------------------: | :----------------------------------------------------------: |
| chown <账号名> <文件或目录> |       改变文件的所有者        |                        -r子目录下都改                        |
|  chgrp <组名> <文件或目录>  |       改变文件的所属组        |                        -r子目录下都改                        |
|             ls              |        查看目录下文件         |                   -A加隐藏除.. -l详细信息                    |
|             cat             |         查看文件内容          |                                                              |
|      df <选项> <目录>       |         查看磁盘用量          |                          -h按GB显示                          |
|      du <选项> <目录>       |        查看目录或文件         |               -h按GB显示，-d表示显示到哪一层级               |
|           netstat           |           查看端口            |                  -natp显示进程号和端口状态                   |
|          ifconfig           |         查看网卡信息          |                                                              |
|           strace            |           跟踪进程            |                 -ff跟踪所有线程 -o 输出文件                  |
|             top             |     查看内存和cpu使用情况     |                                                              |
|           screen            |  保存会话（详见多会话笔记）   |                       -r 进入某个会话                        |
|            tail             |       动态追踪文件尾部        |                         -f 动态追踪                          |
|            which            |       查询命令是否存在        |                       -a 显示安装路径                        |
|            uname            |       查看linux系统信息       |                       -r 查看内核版本                        |
|             rpm             |           安装rpm包           |                    -i 安装 -e卸载 -qa查询                    |
|             cp              |           拷贝文件            | -i 覆盖提示 -f覆盖不提示 -b覆盖前备份 -r递归复制 -a原属性复制 |
|            watch            | 监控变化的输出（比如netstat） |                       -n多少秒进行更新                       |
|         journalctl          |           系统日志            |     -f 实时滚动 -u搜索service -x额外信息 -e立即跳到末尾      |

#### 特殊用途命令组合

关于swap分区

```bash
# 用于关闭swap分区
swapoff -a && sed -ri 's/.*swap.*/#&/' /etc/fstab
# 确认是否关闭
free -m
```

关于selinux

```bash
# 查看selinux状态
sestatus
# 永久关闭selinux，如果是容器化运行的那么关闭这个对安全影响不大
setenforce 0 && sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
```

关于时区

```bash
# 设置时区
timedatectl set-timezone Asia/Shanghai
timedatectl set-local-rtc 0
```

关于日志

```bash
# 开启日志系统
systemctl start rsyslog
```

关于定时任务

```bash
# 开启定时任务
systemctl start crond
# 查看当前运行的任务
crontab -l
# 删除所有定时任务
crontab -r
# 进入crond的任务表，e为编辑模式
crontab -e
```

关于服务

```bash
# 设置服务开机自启
systemctl enable <服务名>
# 关闭服务开机自启
systemctl disable <服务名>
# 开启服务
systemctl start <服务名>
# 关闭服务
systemctl stop <服务名>
# 查询服务状态
systemctl list-unit-files | grep '<服务名>'
```

关于内核升级

```bash
# 1.导入elrepo的key
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
# 2.安装elrepo仓库
yum -y install https://www.elrepo.org/elrepo-release-<最新版本号>.rpm
# 3.安装kernel-ml长期稳定版，-lt是长期维护版
yum --enablerepo="elrepo-kernel" -y install kernel-ml.x86_64
# 4.设置grub2引导
grub2-set-default 0
# 5.生成新的grub2引导文件并重启
grub2-mkconfig -o /boot/grub2/grub.cfg
reboot
```

关于换源

```shell
# 换成阿里的centos7的yum源
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum makecache
```



### 配置文件

#### 带路径命令

|            文件             |       作用       | 配置 |
| :-------------------------: | :--------------: | :--: |
| cat /etc/vsftpd/vsftpd.conf | 查看所有用户信息 |      |
|       cat /etc/group        |   查看所有组群   |      |
| /etc/init.d/vsftpd restart  |  重启vsftpd服务  |      |
|       cat /etc/passwd       | 查看所有用户信息 |      |
|       cat /etc/group        |   查看所有组群   |      |
| /etc/init.d/vsftpd restart  |  重启vsftpd服务  |      |
|                             |                  |      |

#### 重要的路径

|                   路径                    |                           详细说明                           |
| :---------------------------------------: | :----------------------------------------------------------: |
|           /etc/nginx/nginx.conf           |                nginx的配置文件，安装nginx才有                |
| /etc/sysconfig/network-scirpts/ifcfg-eth0 |                网卡配置文件，eth0是第一块网卡                |
|                /etc/hosts                 |           hosts文件，修改后需要重启network服务生效           |
|           /etc/systemd/system/*           |                   所有的开机启动服务都在这                   |
|                /etc/passwd                |                       内有所有用户信息                       |
|                /etc/group                 |                       内有所有组群信息                       |
|                  /proc/                   | 目录下的只有数字的文件为进程，这个数字下的目录中文件描述符fd中有0,1,2分别对应输入，输出和报错流 |
|                                           |                                                              |

```shell
# 接续/etc/sysconfig/network-scirpts/ifcfg-eth0里的配置
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



### 重要的包

|      包名       |     作用     | 其他                                 |
| :-------------: | :----------: | ------------------------------------ |
| ca-certificates | 用于ssl连接  | update-ca-trust来更新ca证书          |
|  ntpd和ntpdate  | 用于时间同步 | 建议用ntpd直接修改conf来达成实时同步 |

### 一些bash快捷键

|  快捷   |            用途            |
| :-----: | :------------------------: |
| alt加。 | 快速输入上个命令最后的参数 |
| ctrl加u |    删除光标到开头的东西    |
| ctrl加k |    删除光标到末尾的东西    |
| alt加t  |      与前一个单词交换      |
| ctrl加z |          强制挂起          |
|   fg    |       重启挂起的任务       |
|   bg    |      把任务放后台执行      |
| ctrl加c |     强制中断并杀死进程     |
| ctrl加d |       退出当前shell        |
