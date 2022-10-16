## linux运维笔记 -->会忘但有可能用到的

### 命令

#### 单纯命令

|            命令             |            作用            |            选项             |
| :-------------------------: | :------------------------: | :-------------------------: |
| chown <账号名> <文件或目录> |      改变文件的所有者      |       -r子目录下都改        |
|  chgrp <组名> <文件或目录>  |      改变文件的所属组      |       -r子目录下都改        |
|             ls              |       查看目录下文件       |   -A加隐藏除.. -l详细信息   |
|             cat             |        查看文件内容        |                             |
|      df <选项> <目录>       |        查看磁盘用量        |         -h按GB显示          |
|           netstat           |          查看端口          |  -natp显示进程号和端口状态  |
|           strace            |          跟踪进程          | -ff跟踪所有线程 -o 输出文件 |
|             top             |   查看内存和cpu使用情况    |                             |
|           screen            | 保存会话（详见多会话笔记） |       -r 进入某个会话       |
|            tail             |      动态追踪文件尾部      |         -f 动态追踪         |

#### 特殊用途命令组合

关于swap分区

```bash
# 用于关闭swap分区
swapoff -a && sed -ri 's/.*swap.*/#&/' /etc/fstab
setenforce 0 && sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
# 确认是否关闭
free -m
```

关于时区

```bash
# 设置时区
timedatectl set-timezone Asia/Shanghai
timedatectl set-local-rtc 0
```

关于日志和定时

```bash
# 开启日志系统和定时任务
systemctl start rsyslog
systemctl start crond
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

|         路径          |            详细说明            |
| :-------------------: | :----------------------------: |
| /etc/nginx/nginx.conf | nginx的配置文件，安装nginx才有 |

