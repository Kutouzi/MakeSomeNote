## java笔记-->设计模式

当设计一个项目或者框架时候，为了解决代码冗余，降低耦合度而引入设计模式。设计模式是所有语言通用的。

### UML关系

#### 依赖关系

某个类的方法通过局部变量、方法的参数或者对静态方法的调用来访问另一个类（被依赖类）中的某些方法来完成一些职责

图示：使用带箭头的虚线从使用类指向被依赖的类

#### 关联关系

关联可单向可双向，它包含组合和聚合关系

图示：双向的关联可以用带两个箭头或没有箭头的实线来表示；单向的关联用带一个箭头的实现来表示

##### 聚合关系

是关联关系的一种。一个类（作为整体）的成员变量的一部分包含另一个类（作为部分）

图示：空心菱形的实线表示，菱形指向整体

##### 组合关系

是关联关系的一种，是强关联关系。一个类（作为整体）的成员变量中有另一个类（作为部分）

图示：用实心菱形的实线来表示，菱形指向整体

#### 泛化关系

类之间是父子关系，对抽象类的继承也是

图示：带空心三角箭头的实线从子类指向父类

#### 实现关系

类实现了接口功能

图示：用带空心三角箭头的虚线从实现类指向接口

### 设计模式的六项原则

所有设计模式都围绕这些来实现

#### 单一原则

一个类或一个方法只干一件事

#### 里氏替换原则

子类不能改变父类功能

#### 接口隔离原则

接口应该按需求细分成多个。保证调用接口时，接口内尽量没有用不到的东西

#### 依赖倒置原则

具体实现类不应该对其他具体实现类有依赖（比如把其他类作为成员变量，又或者是函数参数），而是对其他实现类的接口有依赖

#### 开闭原则

扩展功能时尽量用继承而不是修改源码

#### 迪米特法则

一个类对自己依赖的类知道越少越好，所以无继承关系的类不要以局部变量形式出现，尽量去组合聚合

### 二十三种设计模式

#### 1. *单例模式

- 使用场合：这个对象在程序生命过程中只需要一个
- 解决方法：让类自己创建私有对象，给出公开方法供访问
- 具体写法

一般使用这种写法，其他七种不介绍

```java
public class A{
    /*静态创建此类对象*/
    private static final A INSTANCE = new A();
    /*禁止别人调用构造函数*/
    private A();
    /*公开获取对象的方法*/
    public static A getInsatance(){
        return INSTANCE;
    }
}
```

#### 2. *策略模式

- 使用场合：想实现一个泛用的函数，但不知道使用者会传什么类型的东西进来时（并且只用泛型不能解决，重载量过于大）
- 解决方法：让使用者自己implement并实现给定接口特定函数，再在函数的参数传入接口，这样函数内就可以访问使用者创建好的函数。
- 实现写法示例

接口写法具体参考java.lang.Comparable，这是排序算法的示例，但别的也可以用这种思想做

```java
public class A implements Comparable{
    int weight;
    public A (int weight){
        this.weight = weight;
    }
    /*让使用者自己写出比较方法*/
    @Override
    public int compareTo(A a){
        if(this.weight <= a.weight) return 0;
        else return 1;
    }
}
```

泛用函数写法示例

```java
public static sort(Comparable[] arr){
    for(int i=0;i<arr.length;i++){
        int min = i;
        for(int j=i+1;j<arr.length;j++){
            /*在自己的算法中调用使用者的方法进行运算*/
            min = arr[j].compareTo(arr.[min]) ? j : min;
        }
        Comparable temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

#### 3. *工厂模式

产生对象的方法或者类都称之为工厂，单例也可以是工厂但new除外。

##### 工厂方法

工厂模式的一个分支

- 使用场合：在创建对象之前想要做一些其他操作（比如记录日志或检测权限）。并且需要频繁加入新的被创建对象，但不太需要创建新的组合
- 解决方法：让每一个实现类都有一个工厂类
- 对应写法

```java
public class A implements Alphabet{
    
}
public class B implements Alphabet{
    
}
public class AFactory implements Factory{
    public Alphabet create(){
        //这里做一些想要的预操作
        return new A();
    }
}
public class BFactory implements Factory{
    public Alphabet create(){
        //这里做一些想要的预操作
        return new B();
    }
}
```

##### 抽象工厂

工厂模式的一个分支

- 使用场合：在创建对象之前想要做一些其他操作（比如记录日志或检测权限）。并且需要频繁创建新的组合，但不太需要加入新的被创建对象
- 解决方法：让被创建对象和工厂都继承自抽象类
- 对应写法

```java
public class A1 abstract A{
    
}
public class A2 abstract A{
    
}
public class B1 abstract B{
    
}
public class B2 abstract B{
    
}
public class C1 abstract C{
    
}
public class C2 abstract C{
    
}
public class _1Factory extend AbstractFactory{
    @Override
    A createA(){
        //这里做一些想要的预操作
        return new A1();
    }
    @Override
    B createB(){
        //这里做一些想要的预操作
        return new B1();
    }
    @Override
    C createC(){
        //这里做一些想要的预操作
        return new C1();
    }
}

public class _2Factory extend AbstractFactory{
    @Override
    A createA(){
        //这里做一些想要的预操作
        return new A2();
    }
    @Override
    B createB(){
        //这里做一些想要的预操作
        return new B2();
    }
    @Override
    C createC(){
        //这里做一些想要的预操作
        return new C2();
    }
}
```

##### Spring工厂

工厂模式的一个分支，结合了上面两个的优点

- 使用场合：需要频繁创建新的组合和加入新的被创建对象
- 实现写法

```java
//先创建一个可以读取xml的接口
public interface BeanFactory{
    Object getBean(String id);
}
//读取xml的实现类
public class ClassPathXmlApplicationContext implements BeanFactory {
	
	//把所有配置文件里的东西读出来保存在map里面
	private Map<String,Object> container = new HashMap<String,Object>();
	
	//定义构造函数
	public ClassPathXmlApplicationContext(String fileName) throws Exception {
		
		SAXBuilder sb = new SAXBuilder();
		Document doc = sb.build(this.getClass().getClassLoader()
				.getResourceAsStream(fileName));   //拿到配置文件
		Element root = doc.getRootElement();
		List list = XPath.selectNodes(root, "/beans/bean");   
		
		// 循环遍历节点
		for (int i = 0; i < list.size(); i++) {
			Element bean = (Element) list.get(i);
			String id = bean.getAttributeValue("id");
			String clazz = bean.getAttributeValue("class");
			Object o =Class.forName(clazz).newInstance();
			
			container.put(id, o); //存放对象
		}
	}
 
	@Override
	//调用getBean将拿到的信息返回给客户
	public Object getBean(String id) {
		return container.get(id);
	}
}
//创建两个被创建对象
public class A implements Alphabet{
    
}
public class B implements Alphabet{
    
}
```

在自己写的xml里添加类路径和id

```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans>
    <bean id="a" class="xxx.A"></bean>
    </beans>
```

将对象创建出来

```java
public static void main(String[] args){
		BeanFactory f = new ClassPathXmlApplicationContext("applicationContext.xml");	
		Object o =f.getBean("a");  //传入id
		Alphabet a =(Alphabet)o; 
	}
}
```

#### 4. *门面模式

- 使用场合：有一堆复杂的类相互调用，而外部又需要调用里面某个类的情况
- 解决方法：将这些复杂的类隔离，用一个管理者去按外部需求调用这些复杂类
- 无具体的写法，只要将复杂的互相调用变成中心化的，都算门面模式

#### 5. *装饰器模式

- 使用场合：想要在原本类的基础上增加些功能，但又不方便继承的情况
- 解决方法：让要增加的功能类也继承自原本类的接口作为装饰器使用
- 具体写法

需要添加功能的类

```java
public class A implements Alphabet{
    public void AFunc(){
        
    }
}
```

装饰器接口

```java
public interface Decorator implements Alphabet{
    /*里面要组合一个需要添加功能的类的接口*/
    Alphabet alphabet;
}
```

具体的装饰器

```java
public class ADecorator implements Decorator{
    /*里面要组合一个需要添加功能的类的接口*/
    Alphabet alphabet;
    /*构造传入要需要添加功能的对象*/
    ADecorator(Alphabet alphabet){
        this.alphabet=alphabet;
    }
    public void DecoratorFunc(){
        /*这里添加新功能，然后再调用原功能，需要强制类型转换*/
        (A)alphabet.AFunc();
    }
}
public class BDecorator implements Decorator{
    /*里面要组合一个需要添加功能的类的接口*/
    Alphabet alphabet;
    /*构造传入要需要添加功能的对象*/
    BDecorator(Alphabet alphabet){
        this.alphabet=alphabet;
    }
}
```

使用装饰器来添加功能

```java
public static void main(String[] args){
    A a = new A();
    /*根据传构造参数，可以多个装饰器套娃*/
	ADecorator aDecorator = new ADecorator(a);
    aDecorator.DecoratorFunc();
}
```

#### 6. *观察者模式

没有观察者模式的时候，比如做一些事件处理，最典型的就是unity做事件等待，每帧update都会判断事件有没有发生，这是比较消耗资源的。观察者模式也是钩子函数（回调函数）的原理

- 使用场合：当某一个事件需要进行等待它发生（并且不知道什么时候会发生），在发生后要做某些事情的情况
- 解决方法：给对象增加监听者列表，监听事件触发时调用所有监听者的函数
- 具体实现

创建观察者接口

```java
public interface Observer{
    public void Action(Event e);
}
/*继承观察者接口的一个真实监听对象，可以有多个*/
public class Listener implements Observer{
    @Override
    public void Action(Event e){
        /*这里示范的是获取了触发事件的对象，当然还可以干更多事*/
        A a = (A)e.getSorce();
    }
}
```

定义事件

```java
class Event{
    /*至少要有事件源对象，当然还可以有更多自定义东西放在事件对象成员变量进行传递*/
    Object source;
    public Event(Object source){
        this.source=source;
    }
    public Object getSorce(){
        return source;
    }
}
```

需要监听的对象

```java
public class A{
    /*监听对象列表*/
    private List<Observer> observerList = new ArrayList<>();
    /*添加监听对象的方法*/
    public void addListener(Observer observer){
        observerList.add(observer);
    }
    /*触发监听的事件*/
    public void TriggerEvent(){
        /*当这个函数被调用就创建一个事件*/
        Event e = new Event(this);
        for(Observer o : observerList){
            /*对监听者列表里的方法进行调用*/
            o.Action(e);
        }
    }
}
```

#### 7. 组合模式

一个树状结构专用的模式

#### 8. *享元模式

享元模式最好的例子就是线程池和数据库连接池。

- 使用场合：当需要动态创建的对象数量固定时，想要减少创建对象时的性能损耗
- 解决方法：创建一个对象集合并且将其填满对象，需要一个实例的时候拿出来，不需要再放回去
- 具体实现

```java
public class A{
    public bool isLiving = false;
}
public class Pool{
    /*对象池*/
    private List<A> aList = new ArrayList<>();
    private static final Pool pool = new Pool();
    private Pool(){
        /*池子内对象的最大数量*/
        for(int i=0;i<20;i++){
            aList.add(new A());
        }
    }
    /*单例创建池子对象*/
    public static getInstance(){
        return pool;
    }
    public A getA(){
        for(A a : aList){
            /*判断对象是否被占用*/
            if(!a.isLiving) {
                a.isLiving = true;
                return a;
            } 
        }
        return new A();
    }
}
```

##### 组合享元模式

是享元模式和组合模式的合体

#### 9. 代理模式

和装饰器模式非常相似，区别是装饰器模式不需要调用被装饰对象原本的东西，代理模式需要调用原本的东西。

- 使用场合：没有源码的情况下，想要对某个方法增加新东西

##### 静态代理

代理模式一个通用的解决方案

- 解决方法：将代理和被代理对象都实现同一个接口，在代理中把接口作为成员变量

- 具体实现

上层接口

```java
public interface Alphabet{
    public void Func();
}
```

创建代理

```java
public class Proxy implements Alphabet{
    public Alphabet alphabet;
    public Proxy(Alphabet alphabet){
        this.alphabet = alphabet;
    }
    public void Func(){
        /*调用旧方法*/
        alphabet.Func();
        /*这里新增功能*/
    }
}
```

创建被代理对象

```java
public class A implements Alphabet{
    public void Func(){
        
    }
}
```

调用代理

```java
public static void main(String[] args){
    new Proxy(new A()).Func();
    /*调用新增功能时也调用了旧方法*/
}
```

##### 动态代理

JVM专属的代理方式，搜索ASM动态代理此处不解释

#### 10. 迭代器模式

专门用来遍历的模式，不解释

#### 11. 访问者模式

- 使用场合：不同的类在调用同一个类时，想调用出不同的效果，并且被调用类必须代码不能再变动

- 解决方法：给被调用类继承一个访问者类，让访问者调用一对一被调用类的函数
- 具体实现不解释，使用场合太特殊不会用到

#### 12. 责任链模式

- 使用场合：需要按不同的顺序（正向和反向）来调用某些类的函数时
- 解决方法：在调用的时候传入想要执行类的函数的链条自身，用递归调用隔开不同顺序的调用。
- 具体实现

创建链条

```java
public class Chain implements Action{
    /*存储所有要执行的类*/
    List<Action> actionList = new ArrayList<>();
    /*当前执行到的类*/
    int index =0;
    public Chain add(Action a){
        actionList.add(a);
        return this;
    }
    @Override
    public boolean doAction(A a,B b,Chain chain){
        if(index == actionList.size()){
            return false;
        }
        Action a =actionList.get(index);
        index++;
        return f.doAction(a,b,chain)
    }
}
```

创建要按某个顺序执行的类的接口

```java
public interface Action{
    boolean doAction(A a,B b,Chain chain);
}
public class A{
    
}
public class B{
    
}
```

创建几个要按顺序执行的类

```java
public class 1_Action implements Action{
    @Override
    public boolean doAction(A a,B b,Chain chain){
        /*需要正向做一些事*/
        a.getClass().getName();
        /*调用下一个类*/
        doAction(a,b,chain);
        /*需要反向做一些事*/
        b.getClass().getName();
        return true;
    }
}
public class 2_Action implements Action{
    @Override
    public boolean doAction(A a,B b,Chain chain){
        /*需要正向做一些事*/
        a.getClass().getName();
        /*调用下一个类*/
        doAction(a,b,chain);
        /*需要反向做一些事*/
        b.getClass().getName();
        return true;
    }
}
```

按某个顺序执行

```java
public static void main(String args[]){
    /*给链条里添加类*/
    Chain chain = new Chain();
    chain.add(new 1_Action());
    chain.add(new 2_Action());
    /*触发链条第一个*/
    chain.doAction(new A(),new B(),chain);
}
/*程序的结果是对A类的处理是按1_Action、2_Action，对B类的处理是按2_Action、1_Action*/
```

##### 自由责任链

- 使用场合：需要按不同的顺序（任意顺序）来调用某些类的函数时
- 解决方法：在责任链的基础上加上传入顺序的参数
- 具体实现很简单，不解释

#### 13. *构建器模式

- 使用场合：一个对象的构建非常复杂（参数或成员变量非常多）的情况下
- 解决方法：用构建器进行包装，进行分段分部分构建，不怎么每次都需要构建的部分在build内写默认值
- 具体实现

创建一个很复杂的类

```java
public class Alphabet{
    A a;
    B b;
    C c;
}
```

很复杂类的组合类

```java
public class A{
    
}
public class B{
    
}
public class C{
    
}
```

创建构建器接口

```java
public interface Builder{
    Builder buildA();
    Builder buildB();
    Builder buildC();
    Alphabet build();
}
```

实现构造器

```java
public class AlphabetBuilder implements Builder{
    Alphabet alphabet = new Alphabet();
    /*每一次调用build？()都逐步完善alphabet对象*/
    @Override
    Builder buildA(){
        alphabet.a = new A();
        return this;
    }
    @Override
    Builder buildB(){
        alphabet.b = new B();
        return this;
    }
    @Override
    Builder buildC(){
        alphabet.c = new C();
        return this;
    }
    @Override
    public Alphabet build(){
        return alphabet;
    }
}
```

构造器的使用

```java
public static void main(String args[]){
    AlphabetBuilder builder = new AlphabetBuilder();
    /*使用链式编程*/
    Alphabet alphabet = builder.buildA().buildB().buildC().build();
}
```

#### 14. *适配器模式

- 使用场合：想让一个对象转换成另一个对象来使用的情况，前提是两个对象功能比较相似
- 解决方法：增加一个适配器类（?To?Bridge）进行对象的转换
- 具体实现根据对象各不相同，不做解释

#### 15. *桥接模式

- 使用场合：当抽象类和实现类非常多关系复杂，需要简化关系防止类爆炸的情况
- 解决方法：将实现类组合到抽象类中
- 具体实现

创建抽象类和实现类的上层接口

```java
public interface Alphabet{
    AlphabetImpl alphabetImpl;
    Alphabet(AlphabetImpl alphabetImpl){
        this.alphabetImpl = alphabetImpl;
    }
}
public interface AlphabetImpl{
    
}
```

创建抽象类和实现类

```java
public abstract class Alphabet_1 implements Alphabet{
    
}
public abstract class Alphabet_2 implements Alphabet{
    
}
public class A implements AlphabetImpl{
    
}
public class B implements AlphabetImpl{
    
}
```

组合抽象类和实现类

```java
public static void main(String args[]){
    /*这样做抽象类就和实现类关联上，并且可以做到在抽象类内调用实现类的函数、给别的接口使用等*/
    Alphabet alphabet = new Alphabet_2(new A());
}
```

#### 16. 命令模式

设计命令用的模板

- 使用场合：需要设计命令的时候
- 具体实现

命令的模板

```java
public abstract class Command{
    /*必须要有执行和撤销函数*/
    public abstract void exec(){
        
    }
    public abstract void undo(){
        
    }
}
```

#### 17. *原型模式

- 使用场合：在需要复制对象的时候
- 解决方法：实现Cloneable接口，根据深浅拷贝重写克隆方法
- 具体实现

```java
public class A implements Cloneable{
    @Override
    public Object clone() throws CloneNotSupportedException{
        return super.clone;
    }
}
/*浅拷贝会直接拷贝内存，值类型复制，引用类型还是原来的引用*/
public class B implements Cloneable{
    int x=1;
    int y=1;
    A a = new A();
    @Override
    public Object clone() throws CloneNotSupportedException{
        /*一般克隆都会由语言本身实现*/
        return super.clone;
    }
}
/*深拷贝需要拷贝内存和引用指向的内存，值类型复制，引用类型不是原来的引用*/
/*类中String类型不会被JVM深拷贝*/
public class C implements Cloneable{
    int x=1;
    int y=1;
    A a = new A();
    @Override
    public Object clone() throws CloneNotSupportedException{
        C c = (C)super.clone();
        /*因为是深拷贝，所以要把引用的内存也复制一份*/
        c.a = (A)a.clone();
        return super.clone;
    }
}
```

克隆对象

```java
public static void main(String args[]){
    /*此时b2和b1的地址不一样，但内容都一样的，就是说b2的x也是20，a的地址都一样*/
    B b1 = new B();
    b1.x=20;
    B b2 = b1.clone();
    /*和上面浅拷贝的区别就是，c1和c2中a的地址不一样*/
    C c1 = new C();
    c1.x=20;
    C c2 = c1.clone();
}
```

#### 18. *备忘录模式

专门用来记录状态，便于回滚操作

- 使用场合：需要存储对象的状态时
- 解决方法：创建一个备忘录类，如果对要存储的对象进行克隆就可以暂存到内存上，如果进行序列化就可以存储到硬盘上
- 具体实现

要存储的对象的类

```java
public interface Alphabet implements Serializable{
    
}
public class A implements Alphabet{
    int x = 20;
    int y = 10;
    /*如果加入这个关键字就不序列化这个成员变量*/
    transient int z = 25;
}
public class B implements Alphabet{
    
}
```

备忘录类存到硬盘上

```java
public class Memento{
    public void save(Alphabet a){
        File f = new File("D:/memento.data");
        /*这里必须try*/
        ObjectOutputStream oos = new ObjectOutputStream(
            new FileOutputStream(f));
        oos.writeObject(a);
        oos.writeObject(b);
        oos.close();
    }
    public void load(){
        File f = new File("D:/memento.data");
        /*这里必须try*/
        ObjectInputStream ois = new ObjectInputStream(
            new FileInputStream(f));
        /*谁先写出去就会先读取谁*/
        a = (A)ois.readObject();
        b = (B)ois.readObject();
        ois.close();
    }
}
```

#### 19. 模板模式

即钩子函数，由系统来实现

#### 20. *状态模式

- 使用场合：想让当某个东西在某个状态的时候，调用某个方法

##### 选择状态

- 解决方法：使用枚举类来定义所处的状态，用观察者模式（回调函数）组合实现调用方法

- 具体实现很简单不做解释

##### 状态抽象

- 解决方法：把状态作为结构体类组合到某个东西中，在状态类下继承实现调用的方法
- 具体实现

状态抽象类

```java
public abstract class State{
    abstract void Action_1();
    abstract void Action_2();
}
```

实现状态抽象类

```java
public class State_1 extends State{
    @Override
    public void Action_1(){
        
    }
    @Override
    public void Action_2(){
        
    }
}
public class State_2 extends State{
    @Override
    public void Action_1(){
        
    }
    @Override
    public void Action_2(){
        
    }
}
```

某个东西的类

```java
public class A{
    State state;
    public void Action_1{
        state.Action_1();
    }
    public void Action_2{
        state.Action_2();
    }
}
```

某个东西按某个状态调用方法

```java
public static void main(String args[]){
    new A(new State_2()).Action_1();
    /*状态的变化调度参考有限状态机*/
}
```

#### 21. 解释器模式

解释脚本语言专用，基本不用
