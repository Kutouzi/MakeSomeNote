## c#笔记-->进阶注意项

### 泛型

1. 应用场景：多种数据类型的东西要做同一种事

2. 性能：泛型(void Method<T>(T e)) == 原生代码(void Method(任一个基本数据类型 e)) > 上转型(void Method(object e))
   注：由于上转型需要复制内存，所以耗时是原生代码的两倍

3. 原理：每一个不同的T生成一个不同的副本，适合不同的类型。

4. 泛型的约束

   泛型的基类约束

   	- void Method<T>(T e) where T:基类类名
   	- 用作指定传进来的类是此基类或其子类
   	- 不能约束是密封类（即不能继承的sealed，java叫final）

   泛型的接口约束

   	- void Method<T>(T e) where T:接口名

   泛型的引用类型约束

   	- void Method<T>(T e) where T:class

   泛型的值类型约束

   	- void Method<T>(T e) where T:struct
   	- T e = default(T) 关键字default可以根据T的不同自动赋予默认值

   泛型的无参构造函数约束

   	- void Method<T>(T e) where T:new()

   以上约束某些可以用逗号分割自身或多样叠加

5. 泛型的协变(out)和逆变(in)

   声明了<out T>后T只能在返回值出现，声明了<in T>后T只能在参数出现

   为了解决泛型转为合法的有父子类关系的类型可以编译通过

   IEnumerable类，由于在<out T>中有out关键字，只能返回值是泛型因此可以实现有继承关系的泛型传入同时声明

   ```
   IEnumerable<Bird> b1 = new List<Bird>();
   IEnumerable<Bird> b1 = new List<Sparrow>();
   //class Sparrow:Bird
   如果是List<>则以上语法错误
   ```

6. 泛型缓存：在泛型里如果有静态，那么每次第一次创建T时都会创建一次静态，后面就变常驻静态。这个特性在每一个需要缓存一份数据的时候效率高

### 反射

1. 使用方法：Assembly assembly = Assembly.Load("dll文件") 不要带扩展名

2. 反射创建对象

   ```c#
   Type type = assembly.GetType("dll文件.类名");
   object obj = Activator.CreateInstance(type); //参数加个true可以访问私有
   //然后记得强转obj类型为要用的那个类名
   //流程为加载dll→获取类型信息→调用构造函数创建对象→类型强转→调用对象方法
   ```
   
3. 用途：使用工厂+配置模式创建对象实现控制反转，即可配置可扩展。使用ConfigurationManager.AppSettings[“key值”]读取配置文件，做到只修改配置文件不改代码，创建不同对象。

4. 反射其他的东西

   - 反射多构造函数类

     ```c#
     object o = Activator.CreateInstance(type,new object[]{匹配类型的参数})
     ```

   - 反射泛型
   
     ```c#
     Type type = assembly.GetType("dll文件.类名`占位符数量"); //读取dll
     //更改原type为限定类型的Type
     Type newType = type.MakeGenericType(new Type[]{typeof(第一个站位符类型),typeof(第二个站位符类型),...});
     object o = Activator.CreateInstance(newType);
     ```
   
     

