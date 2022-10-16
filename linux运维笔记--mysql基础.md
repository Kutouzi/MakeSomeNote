## linux运维笔记-->mysql基础

### 关系型和非关系

oracle（收费）、microsoft SQL、DB2（大数据用）、mysql等，为关系数据库

mongoDB、redis、memcached、Hbase（大数据用）等，为非关系数据库

### 默认端口

mysql一般为3306

### 数据库语言

1. 操作语言DML
   - insert，增加数据
   - update，修改数据
   - delete，删除数据
2. 查询语言DQL
   - select，查询数据配合from和where使用
3. 定义语言DDL
   - create，创建对象
   - alter，修改对象
   - drop，删除对象
4. 控制语言DCL
   - grant，授予权限
   - revoke，回收权限
5. 控制语言TCL
   - start transaction，开启事务
   - commit，提交事务
   - rollback，回滚事务
   - set transaction，设置事务属性

### 字符集

utf8mb4才是mysql的utf-8字符集

### 约束

- 主键约束

  ```sql
  primary key
  ```

- 非空约束

  ```sql
  not null
  ```

- 唯一约束

  ```sql
  unique
  ```

- 检查约束

  ```sql
  check(<条件>)
  ```

- 默认值约束

  ```sql
  default <值>
  ```

- 自增约束

  ```sql
  auto_increment
  ```

- 外键约束

  ```sql
  foreign key (<字段名>) references <外表名> (<外表字段名>) <外键策略>
  # 外键策略可以是on update cascade on delete cascade 更新时同时改变从表
  ```

#### 约束等级

分为列级和表级，添加在列定义后面就是列级，逗号constraint添加为表级

```sql
create table stud(
    son int(6) primary key, # 列约束
    sname varchar(10),
    constraint pri_stud primary key (son,sname) # 表约束
);

```

#### 修改约束

```sql
#添加约束
alter table <表名> add constraint <约束名> <约束> (<字段名1>,<字段名2>,...);
#修改约束
alter table <表名> modify <字段名> <数据类型> <新的约束>
```

### 事务

```sql
#开启事务
start transaction
<语句1>
<语句2>
...
#回滚事务
rollback
#提交事务
commit
```

### 视图

视图是真实的数据表的一份映射，视图表也会存储在硬盘上，不会因为关机而消失。

```sql
#创建单表视图表
create view <视图名> as <查询语句>
#检测视图存在替换
create or replace view <视图名> as <查询语句>
#检测插入数据
create view <视图名> as select <字段> from <表名> where <条件> with check option
```

可更新视图是可以在insert、update和delete操作后更新的，也可以对视图表使用这些操作，会影响到真实的数据表。不可更新视图就不会影响真实的数据表。

如果视图包含下述结构中的任何一种，那么它就是不可更新的：

- 聚合函数

- DISTINCT关键字

- GROUP BY子句

- ORDER BY子句

- HAVING子句

- UNION运算符

- 位于选择列表中的子查询

- FROM子句中包含多个表

- SELECT语句中引用了不可更新视图

- WHERE子句中的子查询，引用FROM子句中的表

- ALGORITHM 选项指定为TEMPTABLE（使用临时表总会使视图成为不可更新的）

### 数据库存储过程函数

在数据库里面写如同ssm那样的增删查改函数（一般不这样做，直接用ssm的mybatis访问数据库）。

```sql
#这是一个存储过程函数的示例，如果需要拼接字符串用concat()函数，将返回值传给out参数
create procedure <函数名>(in <参数名> <数据类型>,...,out <返回参数名> <数据类型>)
begin
	if <参数名> is null or <参数名> = "" then
		<查询语句>;
	else
		<查询语句>;
	end if;
	select found_rows() into <返回参数名>
end;
#调用存储过程函数
call <函数名>(<参数>,...@<返回参数名>);
select @<返回参数名>
```



