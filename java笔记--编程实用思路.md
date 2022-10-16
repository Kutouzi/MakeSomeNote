## java笔记-->编程实用思路

### 只能创建一次的唯一对象

```java
public final class A{
    /*静态在内存中，同一个类只能有一个，所以对象唯一。除非不同类*/
    private static A _a = null;
    /*创建一个私有构造函数，在程序打开时就会创建a对象*/
    private static A(){
        _a = new A();
    }
    /*外部如果想创建A对象只能类名访问静态方法创建*/
    public static A GetA(){
        return _a;
    }
}
```

```java
/*创建A对象*/
A a = A.GetA();
```

