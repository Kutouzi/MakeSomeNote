## c++笔记-->后来的特性

其中标记*号为重要特效

### c++11

#### *auto类型

```c++
/**
 * 可以自动推断类型
 * 下面的变量此时为浮点型
 */
auto i = 10.0
```

#### decltype推断类型

```c++
/**
 * 可以推断某个变量的数据类型
 * 下面的name会是int
 */
int i = 10;
decltype(i) a;
typeid(a).name;
```

#### *追踪返回类型

```c++
/**
 * 和模板一起用可以做到返回任意类型
 * 下面的函数，可以传入任意可+重载的类型
 * c++14后可以不需要->后面的东西，特性叫“返回值类型推导”
 */
template<class T1,class T2>
auto func(T1 &t1,T2 &t2) -> decltype(t1+t2){
    return t1 + t2;
}
```

#### *新容器

```c++
/**
 * tuple可以储存定长不同类型的数据
 * 用tuple_cat来合并两个元组
 */
tuple<double, char, string> t = make_tuple(1.0, 'A', "sssss");
```

```c++
/**
 * array可以在栈空间中开辟类似vector的list
 */
array<int, 4> arr= {1,2,3,4};
```

```c++
/**
 * unordered可以生成无序的容器
 * 它可以有unordered_map、unordered_multimap、unordered_set、unordered_multiset。
 * 因为无序其查询和插入的复杂度为O(1)
 */
unordered_map<string,string> u_map = {{ "a", "A" },{ "b" "B" }};
```

#### *正则表达式

```c++
/**
 * 可以使用正则表达式匹配字符串
 * 下面是匹配只有英文字母的.txt文件示例
 */
regex base_regex("([a-z]+)\.txt");
smatch base_match;
```



#### *类内成员直接初始化

```c++
/**
 * 可以做到成员变量直接附初始值
 */
class A{
    public:
    /*需要初值可以直接这样写，不用构造函数*/
    int a = 1;
    string s = "sss";
};
```

#### *列表初始化

```c++
/**
 * 所有容器都可以简化初始化时的赋值
 * 像下面这个初始化，以前是需要push_back()四次的
 */
vector v = {1,2,3,4};
set s = {1,2,3,4};
list l = {1,2,3,4};
```

#### 列表式初始化防类型收窄

```c++
/**
 * 一般没用
 */
int a = 1024;
/*这个是不可以编译通过的，在c++11以前会以类型收窄的方式编译通过*/
char b = {a};
```

#### *基于范围的for循环

```c++
/**
 * 可以简化for循环
 * 相当于java里的增强for
 */
int a[] = {1,2,3,4,5};
for(int it : a){
    cout<<it<<endl;
}
```

#### 静态断言

```c++
/**
 * 可以用于运行前检测
 */
static_assert(sizeof(void *) == 4,"error. not 64bit");
```

#### noexcept修饰符

```c++
/**
 * 可以让函数不能抛出异常
 * 抛出的话程序就崩溃
 */
void func() noexcept{
    
}
```

#### *nullptr解决null与零的二义性

```c++
/**
 * 可以让指针置空
 * 在之前NULL就是0，会发生歧义导致不能判断
 */
int *p = nullptr;
```

#### *强类型枚举

```c++
/**
 * 可以让枚举作为类存在，更规范
 */
enum class e:int {
    A,B
};
int main(){
    e flag = e::A;
    return 0;
}
```

#### *常量表达式(constexpr)

```c++
/**
 * 用于将运行时才计算的东西提前到编译时计算，要到c++14扩展后才完全体现出来价值
 * 返回值不可以是变量，只能是全局数据或者常量，并且只能return一行写完
 */
constexpr int func(){
    return 1;
}
int main(){
    constexpr int = func(); //这里在编译时直接被优化成int = 1;
    return 0;
}
```

#### *原生字符串

```c++
/**
 * 可以让字符串不转义
 * 下面会输出aaaa\n
 */
cout<<R"(aaaa\n)"<<endl;
```

#### *继承构造

```c++
/**
 * 可以让子类继承父类的构造函数，简化代码量
 * 不能初始化子类的成员变量，且父类构造函数不能私有
 */
class A{
    public:
    A(int x,int y){
        x=x;
        y=y;
    }
    protected:
    int x;
    int y;
};
class B:public A{
    /*继承A的构造函数*/
    using A::A;
    public:
    void display(){
        cout<< x,y <<endl;
    }
};
int main(){
    B b(10,20);
    return 0;
}
```

#### *final和override

```c++
/**
 * 可以阻止派生类，防止虚函数进一步重写
 */
class B final{
     virtual void func() final {}
}
class B{
     virtual void func() final {}
}
int main(){
    return 0;
}
```

```c++
/**
 * 可以确保重写了函数
 * 没有进行重写是不能编译通过的
 */
class A {
    public:
    virtual void func(){}
};
class B:public A{
    void func() override{}
}
```

#### *类默认函数

```c++
/**
 * 可以重载后加入默认的无参构造函数
 */
class A{
    /*默认无参函数的效率比用户写的高*/
    A()=default;
    A(int a){
        
    }
    ~A()=default;
}
```

#### 禁用函数

```c++
/**
 * 可以不让拷贝构造函数和赋值运算符重载函数被调用
 */
class A{
    /*默认无参函数*/
    A()=default;
    /*拷贝构造函数，这里让它无法调用*/
    A(const A &) =delete;
    /*赋值运算符重载函数*/
    A & operator=(const A &){return *this;}
    /*开辟空间函数，这里让它无法调用*/
    void *operator new(size_t) = delete;
}
int main(){
    A a1;
    /*拷贝对象，这里已经delete了，对象不可被拷贝*/
    A a2 = a1;
    /*赋值重载对象*/
    a2 = a1;
    /*开辟对象空间，这里已经delete了，对象不可被new*/
    A a3 = new A();
    return 0;
}
```

#### 类型别名

```c++
/**
 * 可以给类型起别名
 */
using my_int = int;
```

#### *默认参数

```c++
/**
 * 可以让函数参数，模板参数有默认值
 */
void func(int a =3){}

template<class T=int>
class A{
    
};

template<class T=int,class R=double> void func(T t,R r){}
```

#### 可变参数模板与参数包

```c++
template<class ... T>
/*三个点叫做参数包*/
void func(T... args){
    /*获取可变参个数*/
    cout<< sizeof...(args)<<endl;
    /*递归调用展开，每次递归去掉一个左数第一个参数*/
    func(args...);
}
/*递归终止函数*/
void func(){
    
}
/*结果是5,4,3,2,1*/
int main(){
    func<int,double,float,int,char>(10,10.0,10.5f,20,'a');
    return 0;
}
```

#### *左值和右值引用

```c++
/**
 * 一个提高性能的机制，见《指针》篇
 * 简单来说地址是左值；数据是右值。在11以前右值绑定变量是复制删除，现在优先直接移动转交内存所有权
 */
int a = 10;
/*左值引用即普通的引用，定义时必须初始化。此时b不再开辟内存储存a的值，而是直接和a等效，a和b是同地址只是变量名不同*/
int &b = a;
/*这样的左值引用不成立*/
//int &b = 1;
const int &c = a;
/*右值引用，必须是没绑定变量的值才行*/
int &&d = 10;
/*如果需要转移左值，使用move移交所有权*/
int &&e = move(a);

```

```c++
/*下面是一个例子，更好的理解右值引用*/
int main() {
    int a = 10;
    int &&d = std::move(a);
    int b = a;
    int &c = a;
    cout << a << endl;
    cout << b << endl;
    cout << c << endl;
    cout << d << endl;
    cout << &a << endl;
    cout << &b << endl;
    cout << &c << endl;
    cout << &d << endl;
    return 0;
}
/*上面的结果数值结果都是10。但是地址除了b以外，acd地址都一样，可见b是复制*/
```

#### 对象返回值优化

```c++
/**
 * 一个提高性能的机制
 * func返回的对象是临时对象，是a的拷贝对象，所以下面main_a就是拷贝对象的内存空间
 * 还有一种编译器会更加优化，拷贝对象也不要了，直接返回原始的对象
 */
class A{
    public:
    A(int x,int y):x,y{}
    A(A && t){
        x = t.x;
        y = t.y;
    }
    int x;
    int y;
}
A func(){
    A a(10,20);
    return a;
}
int main(){
    A main_a = func();
    return 0;
}
```

#### 移动构造函数

```c++
/**
 * 一个提高性能的机制
 * func函数执行结束所有东西都会出栈，留下返回值。移动构造函数就是把原本是开辟在栈的东西在堆中开辟，使得可转移
 * c++11会自带这个移动构造函数，在右值引用的时候会直接获得原始对象
 */
class A{
    public:
    A(int x,int y):x,y{}
    A(A && t){
        x = t.x;
        y = t.y;
        /*必须将栈内的存储的东西置空，否则会导致多次释放空间而崩溃，置空后func函数释放时候就不析构了*/
        t.x = NULL;
        t.y = NULL;
    }
    int x;
    int y;
}
A func(){
    A a(10,20);
    return a;
}
int main(){
    A &&a =  func();
    /*在堆中开辟的内存记得释放*/
    delete a;
    return 0;
}
```

```c++
/*上面的优化相当于这样做，在函数中直接在堆中开辟空间，这样函数结束对象也不会消失*/
class A{
    public:
    A(int x,int y):x,y{}
    int x;
    int y;
}
A* func(){
    A a = new A(10,20);
    return &a;
}
int main(){
    A a =  func();
    delete a;s
    return 0;
}
```

#### 左右值转换函数

```c++
/**
 * 用处未知
 */
int a =10;
/*转换左值为右值*/
int && b = std::move(a);
```

#### *智能指针

```c++
/**
 * 可以当做可以自动管理的指针来使用，需要引入memory.hpp头文件
 * 这里说是指针，实际上是一个对象
 * 在添加智能指针同时，auto_ptr被弃用，因为复制对象行为不安全
 */
void delfanc(){
    delete a;
    delete b;
}
/*唯一指针，同一时间只有一个指针指向地址，当它指向其他对象时，之前所指向的对象会被摧毁。当指针超出作用域或重新指向时会自动释放之前对象的内存。第二个参数还可以用于自定义释放的方法*/
unique_ptr<int> up(new int(11));
unique_ptr<int,decltype(delfanc)> up(new int(11),delfanc);
auto up=make_unique<delfanc>(); //推荐这个写法
/*共享指针，多个指针可以指向一个内存*/
shared_ptr<int> sp1(new int(11));
shared_ptr<int> sp2 = make_shared<int>();  //推荐这个写法
shared_ptr<int> sp2 = sp1;
/*释放指针，让它不在指向这个空间。如果所有指向这个内存的指针都被reset这个堆区空间才会释放*/
sp1.reset();

```

#### *lambda表达式

```c++
/**
 * 可以简化匿名函数
 * 它可以有也可以没有返回值，所以可以用变量接受返回值
 */
int a = 1;
/*获取所在范围的全部变量的引用，以便在花括号内使用*/
[&](int x,int y){};
/*复制所在范围的全部变量的值，以便在花括号内使用*/
[=](int x,int y){};
/*只获取a的引用，其他的变量都复制值*/
[&a,=](int x,int y){};
/*捕获当前对象，但是只读*/
[this](int x,int y){};
```

#### 委托构造函数

```c++
class A{
public:
    A(int a){
        
    }
    /*可以这样使用，构造调用另一个构造*/
    A():A(0){
        
    }
}
```

### c++14

#### *泛型lambda

```c++
/*在原来的基础上支持了auto声明形参*/
[&](auto x,auto y){};
```

#### *返回值类型推导

```c++
/**
 * 可以用auto作为范围值让编译器自己推导
 * 不能返回多种类型，不能返回初始化列表，不能在虚函数用
 */
auto func(){
    return 0;
}
```

#### *常量表达式扩展

```c++
/*在里面可以用循环和局部变量，并且不需要把所有东西放在一行*/
constexpr int func(int n){
    auto ret = 0;
    for (int i =0;i<n;i++){
        ret++;
    }
    return ret;
}
int main(){
    constexpr int = func(3);  //这个过程已经在编译时被计算，因此这里会在编译后直接被替换成 int = 3;
    return 0;
}
```

#### 二进制字面量与整形字面量分隔符

```c++
/**
 * 可以用单引号分割，看得更清楚
 */
int a = 0b0001'0011'1010;
double b = 3.14'1234'1234'1234;
```

#### *扩展智能指针

```c++
/**
 * 可以直接创建
 */
unique_ptr<int> up = make_unique<int>();
```

```c++
/**
 * 可以实现读写锁
 */
class A {
public:
    mutable shared_timed_mutex mutex_;

    auto read() {
        /*读锁*/
        shared_lock<shared_timed_mutex> lock(mutex_);
    }

    auto write() {
		/*写锁*/
        unique_lock<shared_timed_mutex> lock(mutex_);
        value+=1;
    }

private:
    int value = 0;
};
```

### c++17

#### auto推导规则变更

```c++
/**
 * 在之前所有列表初始化都是std::initializer_list<int>
 */
/*此为std::initializer_list<int>类型*/
auto a = {1};
/*此为int类型*/
auto b{1};
/*不可编译通过*/
//auto a = {1,2,3};
```

#### lambda捕获当前对象拷贝

```c++
[*this](int x,int y){};
```

```c++
/*一个完整的使用示例，会输出33*/
class A {
public:
    auto func(int x, int y) {
        auto f = [*this](int x, int y) {
            return a + b + x + y;
        };
        return f(x,y);
    }

private:
    int a = 1;
    int b = 2;
};

int main() {
    unique_ptr<A> uptr(new A());
    cout << uptr->func(10, 20) << endl;
    return 0;
}
```

#### 内联变量

```c++
```



#### 嵌套命名空间

```c++
```

### c++20

#### 协程

可以挂起和恢复的函数。有了这个函数执行过程可以被打断，就如同断点一样停止在某个地方，等再次被需要时可以接着继续运行。见《协程》
