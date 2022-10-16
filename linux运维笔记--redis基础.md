## linux运维笔记-->redis基础

### 配置文件

redis.windows.conf文件

改完配置后需要重启redis服务器并指定配置名称

> C:\>redis-server.exe redis.windows.conf

### 数据类型和对应存取方式

- string

  set 键 值

  get 键

  del 键 值

- hash：是一种(键（键 值；键 值；键 值）)的嵌套模式

  hset 键1 键2 值

  get 键1 键2

  del 键1 键2 值

- list：可以重复的双向链表

  lpush 键 值

  rpush 键 值

  lrange 键 开始 结尾

  lpop 键

  rpop 键

- set：不可重复元素

  sadd 键 值

  smember 键

  srem 键 值

- sortedset

  zadd 键 权 值

  zrange 键 开始 结尾

  zren 键 值

### 通用命令

- keys 正则表达式：查询键，*为查询所有
- type 键：获取键的类型
- del 键：删除指定键的键和值

### 变长期储存的方式

一般混合使用

- RDB：一定时间间隔中，检测key变化，然后持久化

- AOF：日志记录的方式，记录每一条命令的操作。可以每次命令后都持久化数据

  ​	要把appendonly设置为on