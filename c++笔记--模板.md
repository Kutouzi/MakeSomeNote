## c++笔记-->模板

模板用于不同类型，进行同一种逻辑时

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



### typename修饰

typename用于修饰类型名，一般用在类型不被编译器识别时

```c++
//这是用于类型别名的场合，下面代码的意思是，用pointer这个名字来表示_Alty_traits::pointer这个类型
//这样可以达到化简的作用
using pointer = typename _Alty_traits::pointer;
```

