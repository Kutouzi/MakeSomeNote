## c++笔记-->线程、协程和异步操作

本篇将默认已经阅读过STL篇

协程本质是线程，切换方式是主动让出cpu而不是普通线程的抢占，因此被称为协程

异步是协程的一种实现方式

### 线程

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

### 异步

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

### 协程

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

