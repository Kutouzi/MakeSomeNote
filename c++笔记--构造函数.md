## c++笔记-->构造函数

一个类可以有四种构造函数，分别是构造、拷贝、移动、析构。

所有的构造函数都可以default。除了构造，其他都可以delete

```c++
class MyClass{
    public:
    //构造
    MyClass()=default;
    //拷贝
    MyClass(const MyClass&)=delete;
    //移动
    MyClass(MyClass&&)=delete;
    //析构
    ~MyClass()=default;
};
```

### 构造

当对象创建时，首先会分配空间，接着就是调用构造函数初始化对象

它可以按参数列表进行重载。如果重载中没有符合的，决定调用哪个重载函数的行为叫重载决议

```c++
class MyClass{
    public:
    MyClass(){}
    MyClass(int a,float b):myInt(a+1),myFloat(b+1.5f){}
    private:
    int myInt =0;
    float myFloat = 0.0f;
}
int main(){
    //调用第一个构造
    MyClass myClass();
    //调用第二个构造
    MyClass my2Class(1,1.0f);
    //进行重载决议，由编译器决定用哪个
    MyClass my2Class("sssss");
}
```

