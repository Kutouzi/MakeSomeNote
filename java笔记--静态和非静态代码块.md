## java笔记-->静态和非静态代码块


### 测试方法：用别的类来调用它
```java
package indi.ktz;
import java.lang.reflect.*;
/***
 * 这是使用的主类
 */
public class App 
{
    public static void main( String[] args ) {
        System.out.println( "5" );	//主函数
        SubApp.ts();	//不创建对象直接加载类访问里面的静态成员
        new SubApp().ts();	//创建对象访问里面的静态成员（函数）
        new SubApp().ts();	//再来一次
    }
}
```
```java
package indi.ktz;
/***
 * 这是包含静态和非静态代码块以及其他的功能类
 */
public class SubApp {
    static String s = "6";
    static {
        System.out.println("1");	//静态代码块
    }
    SubApp(){
        System.out.println("2");	//构造函数
    }
    {
        System.out.println("3");	//非静态代码块
    }
    static void ts(){
        System.out.println("4");	//类函数
    }
    public static void main( String[] args ) {
        System.out.println( "7" );
    }
}
```
编译运行App.java
结果：输出顺序是514324324
结论：

- 静态代码块只在<b>类加载时候</b>用一次，之后怎么新建对象它都不会再用了
- 有多个入口时忽略其他类内的main
- 非静态在每次创建对象的构造函数前都会调用

