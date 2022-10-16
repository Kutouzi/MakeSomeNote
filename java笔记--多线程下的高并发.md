## java笔记-->多线程下的高并发

### 并发与并行

并发是轮巡完成多个任务

并行是同一时间点完成多个任务

两者不排斥

### 进程与线程、协程

多个程序需要并发运行占用cpu，每个程序称作一个进程

每个程序内还需要并发运行多个占用cpu的操作称作一个线程，可理解为进程中的进程

一个程序在内部（虚拟机）模拟多线程的线程叫做协程，可理解为进程的进程的进程。

一颗cpu通过进程的函数入口找到进程的入口，再运行进程内的线程，且在一个时间点只能执行一个线程（指令），多线程需要轮巡切换线程，多进程（多个程序）需要轮巡切换进程。

线程共享的资源有：

- 文件描述符表
- 每种信号的处理方式
- 当前工作目录
- 用户ID和组ID
- 内存地址空间

线程不共享的资源有：

- 线程id
- 处理器现场和栈指针（内核栈)
- 独立的栈空间（用户空间栈）
- errno变量
- 信号屏蔽字
- 调度优先级

#### 线程切换

在多个线程切换时需要进行前一个线程执行数据和指令的保存（即记录线程运行到哪里）然后再切换

若需要继续运行前一个程序，则需要读取保存的东西（进行现场恢复）

此过程需要消耗时间，因此大多情况多线程并不一定效率高

#### CFS系统调度策略

linux内核最新版系统调度的算法是完全公平算法

这个算法有以下特点：

- 权重高越优先
- 等待时间越长越优先

### 现代cpu的多核结构

现代一个cpu（cpu集合常被略称为cpu）中有多个cpu，每个组里有多个核，每个核里有一个运算单元（可运行一个线程）。

一个cpu中的核共享L3缓存，单独有自己的L1和L2缓存，多个cpu共享内存。（此处的cpu不是cpu集合的略称)

如8核16运算单元就是这一个cpu（集合）有2个cpu

### 现代计算机的三级缓存

每次从内存中读取连续的64byte

在多线程下如果两个cpu的L1和L2在缓存行读到同一个数据，则会用一个机制（英特尔是MESI协议）来进行同步，机制根据cpu厂商不同而异

#### 缓存行填充（cpu级别优化重点）

java中使用Thread运行一个任务，则必定会交给多个不同的运算单元来进行运算

在这个多线程的情况下，用数据填充的方式，在前后填充64byte-数据量byte大小的数据，可以减少缓存一致性的几率，降低同步开销。

#### 合并写技术（cpu级别优化重点）

为了提高从计算单元往外写的效率，在cpu和L1之间加入4byte的缓存单元，让缓存单元在写入L1时同时写入L2。这个设计使得每4byte为一组往外写且必须有等待够4byte才会外写数据

### cpu的换序执行

由于cpu速度远高于内存、L1到L3缓存，在读到它们之前会进行等待。

如果等待时间过长，cpu会换序先执行下一条指令以提高效率，再执行等待的那一条

#### this逃逸问题

在构造方法中不能开辟线程，否则可能导致主线程对象初始化未完毕（原因为cpu的换序执行），线程就已经开始执行方法

#### 单例-双检测锁需要volatile问题

如果在清空内存后发生初始化和连接换序的情况，则对象未初始化完毕，由于synchronized加锁和未加锁的代码之间可以互访，可能造成别的线程引用未初始化好的对象。因此还需要用volatile保持线程可见性（一个cpu改了某个值其他cpu立马知道）和禁止指令重排序是非常重要的

![单例-双检测问题](.\picture\单例-双检测问题.png)

#### 换序的条件

1. 最终对内存的结果不发生影响的语句可换

   

   ```java
   //他们不可换，对内存的结果发生影响
   a=1
   a++
   ```

   

2. 在JVM等规则中没有规定的语句可换（用cpu内置的屏障指令加在两条指令之间，强制cpu不允许换序）

### 线程比

操作系统的线程和jvm的线程为1:1，即在java内run一个thread，在操作系统也增加一个线程

其他语言如go语言则不是1:1

### 锁

保证代码的原子性（这里意思是上锁之后代码不可被打断，可分多个时间片执行但必须要保证下次进入时还是这个持有钥匙的线程），必须持有钥匙的线程才能进入这段代码

注：原子性最初是指事务的不可分割性，一个事务的所有操作要么不间断地全部被执行，要么一个也没有执行，但此时间可以是分片的，只要保证其他程序不打断即可。

```java
Object o = new Object();
synchronized(o){ //这里对象o为钥匙
    null;
}
```

高并发下被锁住的代码越短效率越高，不必要的代码不放入里面

#### 锁的偏向性

锁有分类：

- 重量级锁（操作系统来管理的等待队列，等待时间长，此层在内核态，如synchronized关键字）
- 轻量级锁（虚拟机自行调度此层在用户态，如自己编程实现的自旋锁）
- 偏向锁（jdk14以前才有，第一个进入的线程进行标记，后来的线程自旋）
- 共享锁（有多个钥匙，可以允许一定量的线程进入，如util包下的semaphore）
- 重入锁（下面解释，如lock接口下的reentrantlock）
- 自定义锁（使用AbstractQueuedSynchronizer类可以自定义出自己想要的锁，上面的共享锁重入锁等都是从这来的）
- 还有更多.....

注：轻和重是对于实现难易度来说，不一定轻更快。在单个线程锁的时间长，等待的线程多时，使用悲观锁（等待队列）效率高。

#### 自旋锁

使用CAS算法来实现

- CAS算法

​		本质乐观锁，在读取写入数据时不上锁。只比较读取前后值有没有变化，相等就写回去，不相等就再次读取判断直到相等为止。

​		但存在ABA问题，即其他线程修改后值回到相等的状态，值是数值的话一般不会有太大问题，如果值是引用则会发生问题。ABA问题可以用加版本号来判断，但不能解决。

​		并且有最核心的问题，在判断时可能会时间片结束导致判断失效。这个必须要在cpu级别解决，cpu支持比较并修改的汇编指令（英特尔cpu的为cmpxchg），必须搭配汇编指令lock（lock可以使用锁缓存，锁总线实现，因此可以实现原子性）完成此操作。这是CAS算法最底层实现，并且这个核心问题得到解决。

#### 公平锁和非公平锁

synchronized是非公平锁；reentrantlock是公平锁

区别为：

- 都需要排列，但公平锁对于新来的线程不可以插队；非公平锁对于新来的线程可以插队进行枪锁

- synchronized队列只有一个，reentrantlock可以自定队列数量和控制唤醒的队列

#### 在锁中用的wait()和notify()方法

区别：

- wait()方法把当前线程加入等待队列
- notify()方法唤醒一个队列中的线程，有ALL参数可以唤醒所有线程但不能控制是哪一个

#### 可重入锁和不可重入锁

可还是不可重入是指是否能在锁里面再套一次锁，其中synchronized，reentrantlock都属于可重入锁

#### synchronized的锁升级过程

偏向锁（仅第一个线程，后面的线程升级）->轻量级锁（自旋锁，计数过十次或到达cpu核数的一半时升级）->重量级锁（等待队列）

### 线程之间通信

让两个线程交替输出字符（线程通信控制）

方法一：

使用util包下的LockSupport

```java
static Thread t1,t2;
/*实现输出1A2B3C4D...*/
public static void main(String[] args){
    char[] a = "1234567".toCharArray(); //创建字符数组
	char[] b = "ABCDEFG".toCharArray();
	t1 = new Thread(()->{
  	  for(char c : a){ //循环读取字符数组
  	      System.out.print(c);
 	       LockSupport.unpark(t2); //让t2线程开始，如果这句先执行了再执行t2park的阻塞，这阻塞也会被解开，不影响结果
 	       LockSupport.park(); //让当前线程等待
	    }
	},"t1"); //线程命名
	t2 = new Thread(()->{
	    for(char c : b){ //循环读取字符数组
	        LockSupport.park(); //首先让当前线程等待,否则会和t1竞争输出
	        System.out.print(c);
	        LockSupport.unpark(t1); //让t1线程开始
	    }
	},"t2");
	t1.start();
	t2.start();
}
```

方法二：

使用自旋锁，要代码短则这个好（原因是不需要从用户态进入内核态的切换时间），否则会浪费cpu。长就重量级锁（内核态的cpu线程队列来管理）

```java
enum ReadyToRun{T1,T2} //标记两个线程为枚举类
static volatile ReadyToRun r = ReadyToRun.T1; //此时r具有指向性，被等于的值的那个线程才能运行。根据题目要求首先指定T1运行
/*实现输出1A2B3C4D...*/
public static void main(String[] args){
    char[] a = "1234567".toCharArray(); //创建字符数组
	char[] b = "ABCDEFG".toCharArray();
	new Thread(()->{
  	  for(char c : a){ //循环读取字符数组
      	  while(r != ReadyToRun.T1){} //如果r没有要求T1运行就空循环，注意单线程不能出现空循环，多线程能够使用是因为每次线程获得时间片都会进行判断
  	      System.out.print(c);
 	      r = ReadyToRun.T2;  //要求T2运行
	    }
	},"t1").start(); //线程命名
	new Thread(()->{
	    for(char c : b){ //循环读取字符数组
	        while(r != ReadyToRun.T2){} //如果r没有要求T2运行就空循环
	        System.out.print(c);
	        r = ReadyToRun.T1; //要求T1运行
	    }
	},"t2").start();
}
```

方法三：

使用synchronized配合notify()和wait()方法

```java
private static CountDownLatch latch = new C(1);
/*实现输出1A2B3C4D...*/
public static void main(String[] args){
    final Object o = new Object();
    char[] a = "1234567".toCharArray(); //创建字符数组
	char[] b = "ABCDEFG".toCharArray();
	new Thread(()->{
        synchronized(o){
            for(char c : a){
                System.out.print(c);
                latch.countDown(); //解除t2的等待
                try{
                    o.notify(); //必须先叫醒再等待，否则会出现两个线程都等待状态
                    o.wait(); //让出锁
                }catch(InterruptedException e){
                    e.printStackTrace();
                }
            }
            o.notify(); //停止程序用
        }
	},"t1").start(); //线程命名
	new Thread(()->{
        latch.await(); //如果先进入线程t2则这个进程先等待，必须在拿到锁之前
        synchronized(o){
            for(char c : b){
                System.out.print(c);
                try{
                    o.notify();
                    o.wait();
                }catch(InterruptedException e){
                    e.printStackTrace();
                }
            }
            o.notify(); //停止程序用
        }
	},"t2").start();
}
```

方法四：

使用重入锁reentrantlock

```java
/*实现输出交替打印*/
public static void main(String[] args){
    final Object o = new Object();
    char[] a = "1234567".toCharArray(); //创建字符数组
	char[] b = "ABCDEFG".toCharArray();
    Lock lock = new ReentrantLock();
    Condition cT1 = lock.newCondition();
    Condition cT2 = lock.newCondition();
    new Thread(()->{
        try{
            lock.lock();
            for(char c : a){
                System.out.print(c);
                cT2.signal(); //唤醒cT2队列
                cT1.await(); //自己进入cT1等待队列
            }
            cT2.signal(); //唤醒另一个线程结束程序
        }catch(Exception e){
            e.printStackTrace();
        }finally{
            loock.unlock();
        }
    },"t1").start();
        new Thread(()->{
        try{
            lock.lock();
            for(char c : b){
                System.out.print(c);
                cT1.signal(); //唤醒cT1队列
                cT2.await(); //自己进入cT2等待队列
            }
            cT1.signal(); //唤醒另一个线程结束程序
        }catch(Exception e){
            e.printStackTrace();
        }finally{
            loock.unlock();
        }
    },"t2").start();
}
```

#### 
