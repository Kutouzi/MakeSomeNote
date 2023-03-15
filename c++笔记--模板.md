## c++笔记-->模板

模板用于不同类型，进行同一种逻辑时。最常用于数据结构，比如列表、栈、队列和链表以及它们互相嵌套的这种实现

### 函数模板

最简单模板的例子

```c++
//一个简单的减法，传入任何类型时，都想实现一个减的逻辑
//一般写class T，但也可以换成typename T，在这里表示同一个意思
//返回值在c++11后可以auto
template<class T>
T Sub(T a,T b){
	return a - b;
}
//多个的
template<class T,class S>
auto Sub(T a,S b){
	return a - b;
}
int main(){
    int retInt = Sub<int>(5,1);
    auto retFloat = Sub<>(5.4f,1.2f);
    //这里重载，使用的是第二个Sub
    double retDouble = Sub<int,double>(5,2.5);
}
```

#### 函数模板约束

最简单模板的例子中，如果传入的是不能相减的类型就会编译错误，比如string，它没有重载减法运算符，程序不知道怎么算它的减法

使用模板特化，就可以在ide中提前得到提示，什么类型是可以传的，什么类型是不能传的。而不必等到编译时候才报错

```c++
//单个类型约束
template<class T>
concept V = std::is_same_v<T,int>; //这里的作用是比较传入的T和int是否类型一致
auto Sub(V auto a,V auto b){
    return a - b;
}

int main() {
    int retInt = Sub<int>(5,1);
    auto retFloat = Sub<>(5.4f,1.2f);  //这个是会被标红线的，因为float传入类型和规定不一致
    return 0;
}
```

#### 函数模板特化

最简单模板的例子中，如果需要对某种特定组合的类型进行特定处理，那么需要进行特化，特化有全特化和偏特化

```c++
template<class T，class S>
auto Sub(T a,T b){
	return a - b;
}
//这里对传入双float类型用全特化，函数模板没有偏特化，因为重载就能实现
template<>
auto Sub<>(float a,float b){
    //这里做的是将float强转为int
    return static_cast<int>a - static_cast<int>b;
}

int main(){
    int retInt = Sub<int,int>(5,1);
    //下面传float会优先调用float特化的模板
    retInt = Sub<float,float>(5.4f,1.2f);
}
```

### 类模板

简单的例子

```c++
template<class T>
class MyClass{
    private:
    T a;
    public:
    MyClass(T& a){
        this.a=a;
    }
    void Print(){
        cout<<a<<endl;
    }
}
int main(){
    string a = "aaaaa";
    MyClass<string> myClass(a);
    myClass.Print();
}
```

#### 类模板特化

类模板与函数模板不同，类模板可以有偏特化

```c++
//这是偏特化，全特化的写法和函数模板一样，把class T去掉就行
template<class T>
class MyClass<T，int>{
    private:
    T a;
    int b;
    public:
    MyClass(T& a,int b){
        this.a=a;
        this.b=b;
    }
    void Print(){
        cout<<a<<" "<<b<<endl;
    }
}
```

#### 模板类继承

普通的类和一个模板的类继承

```c++
template<class T>
class MyTemplateClass<T>{
    public:
    T templ;
    MyClass(T& templ){
        this.templ=templ;
    }
}
//要把类模板的声明也要写上
template<class T>
class MyNormalClass:public MyTemplateClass<T>{
    private:
    int normal;
    public:
    MyClass(int normal,T& templ){
        this.normal=normal;
        MyTemplateClass<T>(templ);
    }
    void Print(){
        cout<<normal<<" "<<templ<<endl;
    }
}
```

模板类和模板类继承

```c++
template<class T>
class MyTemplateClass<T>{
    public:
    T templ;
    MyClass(T& templ){
        this.templ=templ;
    }
}
template<class T,class V>
class TemplateClass:public MyTemplateClass<T>{
    private:
    V temp;
    public:
    MyClass(V& temp,T& templ){
        this.temp=temp;
        MyTemplateClass<T>(templ);
    }
    void Print(){
        cout<<temp<<" "<<templ<<endl;
    }
}
```

#### 传递模板类

模板类传递的简单例子，这里以函数模板为例，类模板也可以类似做法

```c++
template<class T>
class MyTemplateClass<T>{
    public:
    T templ;
    MyClass(T& templ){
        this.templ=templ;
    }
    void Print(){
        cout<<templ<<endl;
    }
}
//这种也是一种函数模板，只要有template头，这是针对一个类（MyTemplateClass）的特化版本
template<class T>
void MyTemplateFunc(MyTemplateClass<T>& myTemplateClass){
    myTemplateClass.Print();
}
//这是上面的泛化的版本，可以直接用T代替MyTemplateClass<T>，一般会选择这种写法
template<class T>
void MyTemplateFunc(T& myTemplateClass){
    myTemplateClass.Print();
}
```

#### 传递类模板

类模板传递的简单例子，类模板也可以类似做法。注意和传递模板类的区别，上面要求的是传入实例化的模板类，这里直接把模板给传入，因此又称为模板模板参数

```c++
template<class T>
class MyTemplateClass<T>{
    public:
    T templ = 0;
    MyClass(T& templ){
        this.templ=templ;
    }
    void Print(){
        cout<<templ<<endl;
    }
}
//模板模板形参
template<class V,template<class T> class C>
void MyTemplateFunc(){
    //传入的是模板，所以需要实例化才能用
    MyTemplate<V>myTemplateClass;
    myTemplateClass.Print();
}
int main(){
    MyTemplateFunc<int,MyTemplateClass>func;
    func();
}
```

### 模板形参包



### 模板的推导指引

在c++17以后可以用

```c++
template<class T>
class MyTemplateClass<T>{
    public:
    T templ;
    MyClass(T& templ){
        this.templ=templ;
    }
    void Print(){
        cout<<templ<<endl;
    }
}
//这是一个推导指引，如果匹配，那么就推导为int
template<class T>
MyTemplateClass<float> -> MyTemplateClass<int>;

int main(){
    //不匹配
    MyTemplateClass t(1);
    //匹配,float被转int
    MyTemplateClass t(1.0f);
}
```

### 特定模板的弃置

在c++20的约束之前，使用delete可以实现

```c++
template<class T>
class MyTemplateClass<T>{
    public:
    T templ;
    MyClass(T& templ){
        this.templ=templ;
    }
    //删除掉了int的构造
    MyClass(int) = delete;
    void Print(){
        cout<<templ<<endl;
    }
}

//表示MyTemplateFunc不允许传入MyTemplateClass，除此之外都可以传入
template<class T>
void MyTemplateFunc(MyTemplateClass<T>) = delete;
template<class T>
void MyTemplateFunc(T myClass){
    myClass.Print();
}
```

### typename修饰

typename用于修饰类型名，一般用在类型不被编译器识别时

```c++
//这是用于类型别名的场合，下面代码的意思是，用pointer这个名字来表示_Alty_traits::pointer这个类型
//这样可以达到化简的作用
using pointer = typename _Alty_traits::pointer;
```

