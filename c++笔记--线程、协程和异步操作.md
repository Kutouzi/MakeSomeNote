## c++笔记-->线程、协程和异步操作

本篇将默认已经阅读过STL篇

### 线程

单cpu也可以多线程，以轮巡抢占cpu的方式进行，多cpu线程之间就是各运行各的，因为执行顺序不定，要互相通信是很麻烦的

注：除非是监听网络等让子线程放飞自我的逻辑，否则不推荐用线程，而是推荐用异步和协程

简单的线程开辟方法

```c++
void task(int&& value,int& ret){
    ret = value;
}
int main(){
    int ret = 0;
    //线程声明会直接启动线程，为什么用ref见STL篇
    //第一个参数是方法名，后面跟着的是参数列表
    std::thread mythread(task,1,std::ref(ret));
}
```

信号量的使用，用来限制访问的线程数量

```c++
//定义信号量，能有3个线程来访问，其他的阻塞
std::counting_semaphore<3> semaphore(3);
void worker(int id) {
    //请求访问，通俗说的p操作
    semaphore.acquire();
    std::cout << "Worker " << id << " is accessing the resource.\n";
    std::this_thread::sleep_for(std::chrono::seconds(1));
    std::cout << "Worker " << id << " is releasing the resource.\n";
    //释放访问，通俗说的v操作
    semaphore.release();
}
int main() {
    //创建一个小线程池
    std::thread threads[10];
    //加入线程
    for (int i = 0; i < 10; ++i) {
        threads[i] = std::thread(worker, i);
    }
	//回收线程
    for (auto& t : threads) {
        t.join();
    }
    return 0;
}
```

互斥量和锁的使用，用来确保同一时间只有一个线程可以访问，防止线程争夺执行

锁在它生命周期还存在的作用域都生效，有guard和unique两种锁

unique可以使用lock.unlock()和lock.lock()在作用域内随意解锁和上锁

guard更简单一点，作用域内全部上锁

```c++
//定义互斥量
std::mutex mtx;
int main(){
    //定义锁
    std::unique_lock<std::mutex> lock(mtx);
    //这也是锁
    std::lock_guard<std::mutex> lock(mtx);
}
```

原子量（原子类型），让一个变量拥有原子性，用于确保同一时间只有一个线程来访问，防止线程争夺变量

原子类型需要用特殊操作来存储数据

- `store(value)`：将值存储到原子变量中
- `load()`：从原子变量中加载值
- `exchange(value)`：将原子变量的值与给定值交换，并返回旧值
- `compare_exchange_weak(expected, desired)`：如果原子变量的当前值等于 `expected`，则将其值设置为 `desired`

原子量有六种不同的内存顺序，用std::memory_order来指定，用于控制该变量内存读写顺序

- `memory_order_relaxed`：

  说明：

  - 不进行同步，仅保证操作的原子性
  - 不会引入任何内存屏障或同步指令

  使用：

  - 当你只需要操作的原子性，而不关心操作的顺序或跨线程的可见性时。
  - 适用于计数器等场景，多个线程可以安全地更新计数器，但不需要同步其他内存操作。

- `memory_order_consume`

  说明：

  - 对依赖于此操作的后续操作进行同步
  - 确保依赖变量在此操作后可见

  使用：

  - 在依赖于某个操作结果的后续操作中使用
  - 在现代处理器上，`memory_order_consume` 通常被实现为 `memory_order_acquire`，因为实际的编译器实现可能不完全支持

- `memory_order_acquire`

  说明：

  - 确保在此操作之前的所有读写操作在此操作之前完成。
  - 用于读取操作，使当前线程看到的结果是其他线程写入的最新结果

  使用：

  - 在获取锁、读取标志或其他同步操作时使用，确保后续的操作都在此操作之后
  - 用于读取操作，需要确保读取到的值及其之前的所有修改都是可见的

- `memory_order_release`

  说明：

  - 确保在此操作之后的所有读写操作在此操作之后完成
  - 用于写入操作，使当前线程的修改对其他线程可见

  使用：

  - 在释放锁、写入标志或其他同步操作时使用，确保之前的操作都在此操作之前
  - 用于写入操作，需要确保该写操作及其之前的所有修改对其他线程是可见的

- `memory_order_acq_rel`

  说明：

  - 结合了 `memory_order_acquire` 和 `memory_order_release` 的语义
  - 确保在此操作之前的所有操作在此操作之前完成，在此操作之后的所有操作在此操作之后完成

  使用：

  - 在读-改-写操作中使用，需要确保此操作前后的所有操作都按顺序进行

- `memory_order_seq_cst`：所有操作按顺序一致性执行，最严格的内存顺序

  说明：

  - 所有操作按顺序一致性执行，提供最严格的内存顺序
  - 确保所有线程看到的操作顺序一致

  使用：

  - 当需要最强的内存序列保证时使用
  - 适用于大多数需要严格同步和顺序一致性要求的场景

```c++
//创建原子量（原子类型），一些类型的变量可以支持原子操作，一些不可以，看cpu
//可以用std::atomic<T>::is_lock_free来看这个类型是不是支持原子操作
std::atomic<bool> isInvisible(false);
int main() {
    //创建两个线程
    std::thread t1([](){
        //这里不需要考虑顺序，因此使用最低级的memory_order_release
        isInvisible.store(false,std::memory_order_release);
    });
    std::thread t2([](){
        isInvisible.store(true,std::memory_order_release);
    });
    t1.join();
    t2.join();
    //看这个类型（当前是bool）支不支持原子操作
    std::cout << isInvisible.is_lock_free() << std::endl;
    std::cout << isInvisible << std::endl;
    return 0;
}
```

线程回收

```c++
int main(){
    //join可以让主线程等待子线程结束后再结束
    mythread.join()
}
```

条件变量进行线程通信

```c++
std::mutex mtx;
std::condition_variable cv;
void task(int&& value,int& ret){
    ret = value;
    //通知主线程
    cv.notify_one();
}
int main(){
    int ret = 0;
    std::thread mythread(task,1,std::ref(ret));
    std::unique_lock<std::mutex> lock(mtx);
    //这里可以让主线程做其他的事，不受子线程干扰
    
    //等待子线程的通知，通知后再继续运行
    cv.wait(lock);
    std::cout<<ret;
}
```

线程休眠

```c++
//休眠一段时间
std::this_thread::sleep_for(std::chrono::seconds(1));
//获取当前时间
auto a = std::chrono::system_clock::now();
//休眠到指定时间
std::this_thread::sleep_until(a + std::chrono::minutes(10));
```





### 异步

异步可以单cpu实现，也可以多cpu，它本质是线程，单cpu轮巡方式是主动让出cpu而不是普通线程的抢占，多cpu会回调通知需要被通知的线程然后释放自己

在任意两个线程中都可以这样做，因为这是配对的操作

```c++
void task()(int&& value,std::promise<int>& ret){
    //进行赋值，这个操作后会提醒接收器，可以开始接收
    ret.set_value(value);
}
int main(){
    int ret = 0;
    //设置一个返回承诺，用来观测子线程是否返回值了
    std::promise<int> ret;
    //设置一个接收器，用来接受子线程返回值
    std::future<int> f = ret.get_future();
    std::thread mythread(task,1,std::ref(ret));
    //这里可以让主线程做其他的事，不受子线程干扰
    
    //收到子线程的返回才会运行这句，否则停留在这里
    std::cout<<f.get();
}
```

如果想要1对多，比如一个主进程等待多个子线程完成任务后在继续执行，使用share_future

```c++
void task()(int&& value,std::promise<int>& ret){
    ret.set_value(value);
}
int main(){
    int ret = 0;
    std::promise<int> ret;
    //设置一个接收器，用来接受所有子线程返回值
    std::future<int> f = ret.get_future();
    std::share_future<int> sf = f.share();
    //创建线程
    std::thread mythread1(task,1,std::ref(ret));
    std::thread mythread2(task,1,std::ref(ret));
    std::thread mythread3(task,1,std::ref(ret));
    std::thread mythread4(task,1,std::ref(ret));
    //这里可以让主线程做其他的事，不受子线程干扰
    
    //收到子线程的返回才会运行这句，否则停留在这里
    std::cout<<sf.get();
    std::cout<<sf.get();
    std::cout<<sf.get();
    std::cout<<sf.get();
    
}
```

如果只需要简单的一个异步任务，用async更方便

```c++
void task()(int&& value){
    //进行赋值，这个操作后会提醒接收器，可以开始接收
    return value;
}
int main(){
    //开启协程任务，可能调用其他cpu也可能不调用，默认是deferred不会立刻执行
    std::future<int> f = std::async(task,1);
    //如果想强制调用其他cpu，这样写，并且会开启一个线程并立刻执行
    std::future<int> f = std::async(std::launch::async,task,1);
    //这里可以让主线程做其他的事，不受子线程干扰
    
    //收到子线程的返回才会运行这句，否则停留在这里
    std::cout<<f.get();
}
```

### 协程

协程是异步的其中一种实现方式，它在此基础上还增加了中断和恢复，所以被称为协程

创建一个协程函数

```c++
//返回值必须是Result
Result MyCoroutine(int value){
    cout<<value<<endl;
    //这里进行中断
    co_await std::suspend_always{};
    cout<<value +1<<endl;
}
```

中断（挂起）协程，协程被中断前会保存函数执行的状态，以便下次恢复协程时继续运行下去。具体实现是调用operator new在堆上开辟了空间来存放状态信息，如果被编译器优化也可能放在栈上

```c++
```

