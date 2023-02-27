## linux运维笔记--三剑客

### awk

awk分为两部分。在开头括号外写匹配什么（多重条件用&&或||等），花括号内写对匹配到的东西干什么

```shell
awk '匹配什么 && 匹配什么{先干什么}END{最后干什么}' 对谁干
```

#### 通用匹配

取某行，默认是遇到换行符看做一行

```shell
#取文件中第三到第六行
awk 'NR>=3 && NR <=6' a.txt
```

```shell
#取文件中8月5日5点到12点的行，且只显示第一列(日志文件常用)
awk '/2022:08:05:05:00:00/,/2022:08:05:12:00:00/{print $1}' a.log
```

取列默认分隔符是空格、连续空格。如果分隔符是其他的，例如xml等需要先用-v FS=指定分隔符

其中$n表示n列；$NF表示最后一列；FS和OFS分别表示输入分隔符和输出分隔符的意思，-v来修改它

```shell
#取出第五列和第七列，分隔符为冒号。column -t用于对齐文本内容
awk -v FS=: '{print $1，$3}' /etc/passwd | column -t
#和上面一样，但是用逗号分割两列
awk -v FS=: '{print $1","$3}' /etc/passwd
#也可以直接修改输出分隔符达到用逗号分割的效果
awk -v FS=: -v OFS=, '{print $1,$3}' /etc/passwd
```

#### 正则匹配符

| 符号 | 作用      |
| ---- | --------- |
| ~    | 包含      |
| !~   | 不包含    |
| ^    | 以...开头 |
| $    | 以...结尾 |
| ^$   | 为空      |

```shell
#以冒号分割，取第三列以1开头的整行
awk -v FS=: '$3~/^1/' /etc/passwd
#在上面基础上，无需显示整行只显示这行的第一列
awk -v FS=: '$3~/^1/{print $1}' /etc/passwd
#以冒号分割，取第三列以1或者2开头的整行
awk -v FS=: '$3~/^(1|2)/' /etc/passwd
#以冒号分割，取第三列包含1和2的整行
awk -v FS=: '$3~/(1|2)/' /etc/passwd
#多个条件可用&&连接，取第三到第六行，以冒号分割，取第三列包含1和2的整行的第一列
awk -v FS=:  'NR>=3 && NR <=6 && $3~/(1|2)/{print $1}' /etc/passwd
```

#### 特殊模式

BEGIN{}会在awk读取内容之前执行，一般计算会在里面做

END{}会在awk读取内容之后执行，一般统计会在里面做

```shell
#END{}一般拼接在{}后面用
awk -v FS=:  'NR>=3 && NR <=6 && $3~/(1|2)/{print $1}END{print $1}' /etc/passwd
```

#### 数组

```shell
#一般的两种数组，数字和字符
awk 'BEGIN{array[0]=0,array[1]=1;print array[0],array[1]}'
awk 'BEGIN{array[0]="one",array[1]="two";print array[0],array[1]}'
#循环取内容
awk 'BEGIN{array[0]="one",array[1]="two";for(i in array) print array[i]}'
#统计第二列每一种字符串出现个数，i是字符串，array[i]是出现次数
awk -v FS=',' '{array[$7]++}END{for(i in array)print i,array[i]}' /etc/passwd
#上面的结果是如下，如果需要排序就用sort -rnk2
/bin/sync 1
/bin/bash 2
/sbin/nologin 39
/sbin/halt 1
/sbin/shutdown 1
```

#### 循环

```shell
awk 'BEGIN{
	for(i=1;i<=100;i++)
		sum+=i;
		print sum
	}'
```

#### 判断

一般使用在第二层判断，要判断匹配到的东西里面的东西的时候用

```shell
awk 'BEGIN{
	if($2>0)
		print '+'
	else
		print '-'
	}'
```

#### 重定向

```shell
#重定向到文件pwd.xml里，覆盖重定向>和追加重定向>>都可以用
awk -v FS=: -v OFS=, '{print $1,$3 > "/home/root/pwd.xml"}' /etc/passwd
```

#### 内置函数

| 函数     | 作用       |
| -------- | ---------- |
| length() | 统计字符数 |
|          |            |
|          |            |

