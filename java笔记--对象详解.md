## java笔记-->对象详解

### 对象在内存中占用

#### 对象头（obect header）

对象头又包含两部分，总共占用大小12字节，如下：  

对象标记（markword）。存储对象在运行时的数据，如：哈希码、GC标记、锁信息、线程关联信息。在64位的jvm上占用  8个字节。需要补充一点的是，对象标记部分的存储格式是非固定的，具体要看jvm的实现。这样设计的目的是为了能存储 更多数据。

类元信息（classpoint）。存储对象指向的类元信息的首地址，即指向是创建这个对象的类。类元信息占4个字节。

#### 实例数据（Instance Data）

存储本类对象的实例成员变量和父类所有可见的成员变量

例子a：

```java
public class BaseEntity {
    private int mId;
}

```

BaseEntity类只有一个私有的成员变量，所以它new出来的对象占用（4+12）字节。

例子b：

```java
public class BaseEntity {
    private int mId;
    public long mLong;
    protected double mDouble;
}
public class SampleEntity extends BaseEntity {
}

```

在BaseEntity内新增一个public的long成员变量和protected的double成员变量。所以它的实例对象占用（4+8+8+12）字节。

继承该BaseEntity得到子类SampleEntity。SampleEntity继承了父类的mLong和mDouble。所以实例对象占用（8+8+12）字节。

#### 对齐填充（Padding）

对象的存储空间的分配单位是8字节，当对象大小不是8的整数倍的时候需要填充对其。如上例子2，占用内存是28，不是8的整数倍，这个时候需要填充4个字节，即32。所以实际分配的是32个字节。

#### 基本数据类型占用

| 类型         |  默认值  | 占用内存(字节) |
| :----------- | :------: | :------------: |
| boolean      |  false   |       1        |
| byte         | (byte)0  |       1        |
| char         | '\u0000' |       2        |
| short        | (short)0 |       2        |
| int          |    0     |       4        |
| long         |    0L    |       8        |
| float        |   0.0f   |       4        |
| double       |   0.0d   |       8        |
| 对象引用变量 |   null   |       4        |

