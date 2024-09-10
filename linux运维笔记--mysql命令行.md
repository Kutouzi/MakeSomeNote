## linux运维笔记-->mysql命令行

前言：不知道为什么win10启动exe前面要加个“.\”来表示当前目录下，linux去掉就好了。当然也可以添加环境变量解决

### 一. 创建并修改SQL

根目录下这样写个my.ini文件

```ini
[client]
# 设置mysql客户端默认字符集
default-character-set=utf8
[mysqld]
# 设置3306端口
port = 3306
# 设置mysql的安装目录
basedir=C:\\web\\mysql-8.0.25
# 设置 mysql数据库的数据的存放目录，MySQL 8+ 不需要以下配置，系统自己生成即可，否则有可能报错
# data
# 允许最大连接数
max_connections=20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

进入根目录下bin，初始化，然后会有root的初始密码显示

```cmd
.\mysqld --initialize --console
```

安装并启动mysql

```cmd
.\mysqld install
net start mysql
```

登录它，本机可以不要-h，这里登录root，以密码方式

```cmd
.\mysql -h 10.2.7.1 -u root -p
```
进入mysql>，输入这个修改密码

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
```

退出mysql

```mysql
quit
```

### 二. SQL启停

启动，根目录下

```cmd
.\mysqld --console
```

关闭，根目录下

```cmd
.\mysqladmin -u root shutdown
```

### 三. 库操作

都是在mysql>

```sql
#建
create database <库名>;
#查
show databases;
#进入库，进入后指令就一直在这个库里操作，再use其他的可切换
use <库名>;
```

### 四. 表操作

```mysql
#查看当前库下的表
show tables;
#查看表的结构，含有各字段详细属性
desc <表名>;
#查看创建表的语句
show create table <表名>;

#在某个字段名后增加一个字段
alter table <表名> add <字段名> <数据类型> <约束> after <字段名>
#删除字段
alter table <表名> drop <字段名>
#修改字段属性
alter table <表名> modify <字段名> <新的数据类型> <新的约束>
#修改字段名
alter table <表名> change <字段名> <新字段名> <新的约束>
#修改约束
alter table <表名> modify <字段名> <数据类型> <新的约束>
#添加约束
alter table <表名> add constraint <约束名> <约束> (<字段名1>,<字段名2>,...);

#删除表
drop table <表名>

#插入数据，此种方法可以非全字段，其他的字段都为空
insert into <表名> (<字段名1>,<字段名3>) values (<字段值1>,<字段值3>);
#插入数据
insert into <表名> values (<字段值1>,<字段值2>,<字段值3>,...);

#更新数据，如果没有where会改全表的
update <表名> set <字段名> = <字段值> where <条件>

#删除表中数据，可回滚
delete from <表名> where <字段> <条件>
#删除表中数据，用新建表的方式，不可回滚
truncate table <表名> where <字段> <条件>
```

```sql
#查询去重
select distinct <字段> from <表名>
#计算查询
select <字段> <运算符> <字段或值> from <表名>
#别名查询
select <字段> as '<别名>' from <表名>
#排序查询，默认是升序排，下面是降序
select <字段> from <表名> order by <要排序的字段> desc
select <字段> from <表名> order by <要排序的字段1> asc , <要排序的字段2> desc

#条件查询，注意and和or和not的优先级，可用小括号提升优先级
select <字段> from <表名> where <字段> <条件>
#区分大小写条件查询
select <字段> from <表名> where binary <字段> <条件>
#条件查询范围
select <字段> from <表名> where <字段> between <条件> and <条件>
#枚举范围条件查询
select <字段> from <表名> where <字段> in ('值','值',...)
#模糊条件查询
select <字段> from <表名> where <字段> like '<正则表达式>'
#空和非空条件查询
select <字段> from <表名> where <字段> is null
select <字段> from <表名> where <字段> is not null

#分组查询，分组就是行的值相同它会自动合并
select <字段> from <表名> group by <字段>
#分组查询后筛选
select <字段> from <表名> group by <字段> having <字段> <条件>
#分组查询前筛选
select <字段> from <表名> where <字段> <条件> group by <字段>

#交叉连接多表查询，多个表会以笛卡尔积方式组合，需要条件选出想要的
select <字段> from <表名> cross join <表名>
#自然连接多表查询，自动匹配同名列，同名列值只展示一次，必须用表名点字段的方式指定，不明确会效率低
select <表名>.<字段> from <表名>  natural join <表名>
select <别名>.<字段> from <表名> as '<别名>' natural join <表名> as '<别名>'
#内连接多表查询，只连接on里面的字段，多表查询一般用这个
select <字段> from <表名> inner join <表名> on(<表名>.<字段> = <表名>.<字段>)
#多表查询，两个以上
select <字段> from <表名> join <表名> join <表名> join <表名> ...

#子查询
select <字段> from <表名> where <条件> (select <字段> from <表名> where <条件>)
```

内置函数表

| 函数                                                    | 用途                        |
| ------------------------------------------------------- | --------------------------- |
| lower(<字段>)                                           | 此字段值展示为小写          |
| upper(<字段>)                                           | 此字段值展示为大写          |
| max(<字段>)                                             | 展示列中最大值              |
| min(<字段>)                                             | 展示列中最小值              |
| count(<字段>)                                           | 展示列计数，即有多少行      |
| sum(<字段>)                                             | 展示列和                    |
| avg(<字段>)                                             | 展示列平均值                |
| length(<字段>)                                          | 此字段值的长度              |
| sbstring(<字段>,<下标>,<长度>)                          | 切分此字段值，它下标从1开始 |
| abs(<字段>)                                             | 此字段取绝对值              |
| ceil(<字段>)                                            | 此字段向上取整              |
| floor(<字段>)                                           | 此字段向下取整              |
| round(<字段>)                                           | 此字段四舍五入上            |
| curdate()                                               | 获取当前日期                |
| curtime()                                               | 获取当前时间                |
| now()                                                   | 获取当前日期和时间          |
| sysdate()                                               | 获取函数执行时日期和时间    |
| if(<条件>,'<成立结果>','<不成立结果>')                  | 条件返回值                  |
| case <字段> when <条件> then '<结果>' else '<结果>' end | 选择返回值                  |
| database()                                              | 显示当前使用数据库          |
| user()                                                  | 显示当前用户                |
| version()                                               | 显示sql版本                 |

### 五. 隔离级别操作

```sql
#查看默认隔离级别
select @@transaction_isolation
#设置当前会话的隔离级别
set session transaction isolation level read uncommitted;
set session transaction isolation level read committed;
set session transaction isolation level repeatable read;
set session transaction isolation level serializable;
```

