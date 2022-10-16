## linux运维笔记-->系统启动

### 系统启动过程

->按BIOS设置的启动硬盘或U盘启动

->操作系统接管

->读入/boot下内核文件

->运行在/etc/inittab下的init进程

->init进程运行rc.sysinit脚本激活交换分区，检查磁盘，加载硬件模块以及其它一些需要优先执行任务

->init再按运行级别去调用相应的rc脚本启动守护进程（开机启动的进程）

​	主要的运行级别有：

- 1级：单用户root启动，用于系统维护不可远程登录
- 3级：NFS多用户，登录进入命令行模式
- 5级：NFS多用户，登录进入GUI模式

​	注：在每个运行级中将运行哪些守护进程，用户可以通过chkconfig或setup中的"System Services"来自行设定

->在rc脚本运行成功后，init打开6个login shell以便用户登录

### 启动后环境变量加载

1->如果登录系统（如ssh登录）则加载/etc/profile全局变量

​	->执行/etc/profile.d下的脚本

​	->尝试加载用户主目录下的~/.bash_profile用户环境变量，没有则不加载

​	->尝试加载用户主目录下的~/.bashrc，没有则不加载

​	->加载用户主目录下的~/.bash_login

​	->尝试加载用户主目录下的~/.profile，没有则不加载

​	->加载/etc/bashrc

2->如果不登录系统则加载/etc/bash.bashrc

​	->加载户主目录下的~/.bashrc

​	->加载/etc/bashrc

### 关机

->使用sync把内存数据同步到硬盘中

->shutdown或reboot加上-h和时间才关机或重启