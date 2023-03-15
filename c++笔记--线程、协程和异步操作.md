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
    std::thread mythread(task,1,std::ref(ret));
}
```

互斥量和锁的使用

```c++
//定义互斥量
std::mutex mtx;
int main(){
    //定义锁
    std::unique_lock<std::mutex> lock(mtx);
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

