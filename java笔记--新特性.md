## java笔记-->新特性

### java-9

#### 模块化

使用模块化来区分两个程序，jar包内中src下的module-info.java。没导出的类或者函数在其他模块中不可被使用甚至反射。

```java
module <模块名> {
    requires <模块名>; //导入其他模块
    exporats <包名.类名.方法名>; //导出某个函数
    open <包名.类名.方法名>; //允许某个函数被反射
    
}
```

#### 字符串结构改进

String类型的存储由char字符类型变为byte字节类型为单位来存储在内存中，节省了空间

#### 多版本兼容

在jar包内的META-INF目录下MANIFEST.MF中新增了Multi-Release属性，设置为true可以支持多版本java。在META-INF目录下新建对应版本号的目录，并放入自己写好的对应版本的源码，那么在使用jar时会根据java版本选择对应的源码。

例如如下jar目录结构，在java版本为9的时候就会调用9目录下的源码

a.jar

- META-INF

  - 9
  - a.java
  
- a.java

#### 接口私有方法

在接口内允许使用private修饰方法，所以现在接口里可以有公有的抽象方法、公有的默认方法、公有的静态方法、私有方法，私有静态方法

这使得接口和抽象类更相似，两者区别是接口不能定义非final static的成员变量。一般形容词的需求用接口、名词的需求用抽象类

#### jshell

java允许了类似python的命令行，可以对一行java语句进行评估，像python那样每一块语句都返回一个结果

#### 增强try-with-resources

如果资源已经使用final变量修饰过，在try-with-resources语句中可以直接使用此变量

try-with-resources可以自动关闭try-catch块内的关闭输入输出流等东西

```java
/*try-with-resources在try后增加了()，并且会自动关闭流，即不需要close()*/
try(InputStream stream = new InputStream(new File("D:/"))){
    /*实现要做的事情*/
}catch(Exception e){
    e.printStackTrace();
}
```

在java9中可以这样写

```java
/*final修饰的变量都可以直接拿出来，避免try块内代码过复杂*/
final InputStream stream = new InputStream(new File("D:/"))
try(stream){
    /*实现要做的事情*/
}catch(Exception e){
    e.printStackTrace();
}
```

#### 集合增强

可以使用集合中的of()方法直接创建集合

#### stream增强

可以用Stream.of()创建一个集合，Stream.takeWhile()可以取出元素，Stream.dropWhile()可以去除元素，Stream.ofNullable()允许传入null值返回一个新的空列表

```java
/*取出了等于"1"的值，此时list内只有2、3*/
List<String> list Stream.of("1","2","3").takeWhile("1"::equals).toList();
```

#### optional增强

optional用来处理传null值或不为null值时进行的不同处理

```java
public void Func(List<String> list){
    /*如果传入为空要干什么*/
    list = Optional.ofNullable(list).orElse(new ArrayList<>());
    for(String s :list){
        System.out.println(s);
    }
}
public void Func(List<String> list){
    if(list == null) return new ArrayList<>();
    /*如果传入为不为空要干什么*/
    list = Optional.ofNullable(list).ifPresent(list->{
        for(String s :list){
        	System.out.println(s);
    	}
    });
}
```

新增的函数为ifPresentOrElse()，它是上面两个的结合，因此传入两个方法，分别是不为空时和为空时要做的事

```java
public void Func(List<String> list){
    /*如果传入为空要干什么*/
    list = Optional.ofNullable(list).ifPresentOrElse(list->{
        for(String s :list){
        	System.out.println(s);
    	}
    },list->{
        System.out.println("null list");
    });
}
```

### java-10

#### 局部变量推导

允许使用var来进行类型推导

```java
/*推导a为双精度浮点型*/
var a = 10.0f;
```

### java-11

#### 简化启动单个源代码文件方法

可以让java文件当做脚本来用，这个可以直接用linux的shell运行

```java
#!/usr/locla/java --source 11
public class A{
	public static void main(String args[]){
        System.out.printlin("");
    }
}
```

使用

```shell
./A
```

### java-14

#### 空指针异常抛出改进

抛出错误时，会进一步的追踪哪一个变量为空，给出更多的信息以便排错

### java-15

#### 文本块

支持多行字符串、避免字符转义等，并且可以控制格式（使用\s保持原格式）

```java
String html = """
    aaa/n"aacc"
    uus
    	\s
    fffff
    <a></a>
    """;
```

### java-16

#### instanceof匹配简化

instanceof是用来对强制转型是否成功进行判断

```java
Object object = Apple.getClass().newInstance();
if (object instanceof Apple){
    Apple apple = (Apple) object;
    apple.func();
}
```

新特性是instanceof简化了强制转型部分，也提高了性能

```java
Object object = Apple.getClass().newInstance();
if (object instanceof Apple apple){
    apple.func();
}
```

#### 档案类

增加一个不可变的类，可快速创建只读的bean对象

```java
/*自动生成赋值成员变量的构造方法、自动生成hashCode、toString、equals和get*/
public record A(int x,int y){
}
```

#### 封闭类

只允许包内的类继承，不允许包外的类继承的一种类（此处方便用final，会导致自己也不能继承）

```java
/*这个封闭类只可以B来继承*/
public sealed class A permits B{
    
}
/*指定或者final阻断封闭传递*/
public class B extends A permits C{
    
}
/*取消封闭让任何都可以继承*/
public non-sealed class C extends B{
    
}
```

#### 选择表达式改进

使用箭头自动break，switch可以有返回值，在选择语句块内可以写表达式

```java
public String selectA(int input){
    /*根据input值选择返回一个字符串A或B或C*/
    String s = switch(input){
        case 100 -> "A";
        case 80 -> "B";
        default -> {
            /*可以做任意的事情，也可以调用其他返回值的函数，使用yield返回*/
            System.out.println("default");
            yield returnC();
        };
    }
    return s;
}
public String returnC(){
    return "C";
}
```

