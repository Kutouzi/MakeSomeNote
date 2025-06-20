## 全栈笔记-->dart语法

不是纯入门教程，得首先会其他几大语言：c++/java/python/c#

### 变量

除了int和double分别来表示不带小数和带小数的数字外，还能使用num来声明变量两个都可以是

```dart
num a = 2;	// 可以有小数也可以没有
```

有通配变量_来代替不想要用的值

```dart
list.asMap().forEach((_, value) {
  print(value);  // 忽略索引
});
```

变量可以先声明不赋初始值，但在用之前必须有值

```dart
// 编译器会自己判断用之前有没有赋值，如果没有正常判断，就用late表示延迟初始化
late var a;
s = 'ass'
print('$a');
```

被final修饰的变量只能赋值一次，被const修饰的在编译器阶段就会被赋值

```dart
final a = user.number;
const b = 2; // 因为是编译器阶段，赋的值需要是常量
```

### 运算符

二元运算??

```dart
// 可以使用??来表示如果左边的为null，那么就返回右边的值
String playerName(String? name) => name ?? '访客';
```

### 级联/链式调用

可以像java一样进行对象.方法.set方法.set方法进行赋值

```dart
class User {
  int? id;
  String? name;
}

var user = User()
  ..id = 1
  ..name = 'Alice';
user = null; // 使用..进行级联调用，如果对象可能为空就用?..
user?..id = 1;
```

### 模式匹配

支持返回多类型

```dart
(String, int) getUser() {
    return ('auser', 18)
};

void main() {
  // 模式匹配 + 解构
  var (name, age) = getUser(); 
  print('$name - $age');
}
```

支持增强switch拆解对象

```dart
class Point {
  final int x, y;
  const Point(this.x, this.y);
}

void handle(Point p) {
  switch (p) {
    case Point(x: 0, y: var y):
      print('在 Y 轴上，y 是 $y');
    case Point(x: var x, y: 0):
      print('在 X 轴上，x 是 $x');
    default:
      print('普通点');
  }
}
```

支持解构键值对

```dart
// key:key也可以被省略成:key
for (var MapEntry(key: key, value: count) in hist.entries) {
  print('键：$key ，值： $count 。');
}
```

### 导入依赖

依赖可以被延迟导入

```dart
// 使用deferred as给个别名
import 'package:greetings/hello.dart' deferred as hello;

// 然后在需要的地方使用这个别名.loadLibrary()加载
Future<void> greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```

### 函数可选参数

定义函数时，使用{}把参数括起来表示可选参数为

```dart
void AppBar({String? key,num? title}){}
```

在使用时没有传参顺序要求，但需要指定为哪个参数传值

```dart
// 指定只传key值
AppBar(key: "text");
```

> 这个函数也可以是类的构造函数

### 类的修饰符

类有以下修饰符

- `abstract`表示一个类是抽象类，可以实现方法但不能实例化成对象
- `base`表示一个类是基类
- `final`表示这个类不能再被继承
- `interface`表示一个类是纯接口，不能实例化成对象，也没有方法实现
- `sealed`表示这个类只能在当前项目中被继承
- `mixin`表示这个类用来代码复用
- `enum`表示这个类是枚举类

### 类变量

当对象可能是空的时候也可以使用?.

```dart
user?.name
```

### 类构造

调用构造可以用new也可以不用

```dart
// 这两个一样的
var p1 = Point(2, 2);
var p1 = new Point(2, 2);
```

可以用runtimeType获取运行时属性的类型

```dart
print('类型是 ${a.runtimeType}');
```

工厂构造函数可以返回已有的对象，如果没有就先创建，然后调用就依旧可以使用普通的构造方法而不是getInstance()

```dart
class User {
  final String name;
  static final Map<String, User> _cache = {};
	// 用factory关键词来表示这是个工厂构造，不会重复创建对象
  factory User(String name) {
    // 如果已经创建过，就复用
    return _cache.putIfAbsent(name, () => User._internal(name));
  }

  User._internal(this.name); // 私有构造函数
}
```

### 类运算符重载

常见的运算符都可以如c++一样被重载，但不推荐使用，运算符定义会变得混乱

```dart
class Vector {
  final int x, y;

  Vector(this.x, this.y);

  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  Vector operator -(Vector v) => Vector(x - v.x, y - v.y);

  @override
  bool operator ==(Object other) =>
      other is Vector && x == other.x && y == other.y;

  @override
  int get hashCode => Object.hash(x, y);
}
```

### 类私有成员

在变量名前加_就是私有的，和其他语言的private关键字等效

```dart
class User {
  String _name = 'auser';   // 这是私有成员
}
```

### 类变量访问器

类里的成员变量大部分都隐式的实现了get和set

```dart
// 直接使用.调用就是get
var p = Person();
p.name = 'auser';
```

被final修饰的只能在编译期或者构造时确定，因此没有set。被显式声明了get，但没有写set的也没有

```dart
class Person {
  final String name = 'auser'; // 编译期常量或构造时确定，不可修改

  int _age = 20;

  int get age => _age; // 显式声明getter，因此只有 getter没有 setter
}
```

### 混合类

这个东西是以便代码复用的。可以使用mixin声明混合类

```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```

用with来调用，使用了with的这个类就会复制一份mixin类里面的所有东西

```dart
class Musician extends Performer with Musical {
  // ···
}
```

### 扩展方法

可以在原有对象的基础上，再增加一个方法而不需要继承它

```dart
extension EmailCheck on String {
    // 扩展了原生的String类的对象，多了一个isEmail方法
  bool get isEmail => RegExp(r'^\S+@\S+\.\S+$').hasMatch(this);
}
void main() {
  var s = 'test@example.com';
  print(s.isEmail);
}
```

### 扩展类型

可以自定义业务类型，避免原生类型混淆，只在编译期起作用，运行时还是用原生类型

### 可调用对象

实际上是c++里的对象函数，通过重载()达到和函数调用一样的效果

在dart创建这种对象函数只需要在类里实现call方法就行了

```dart
class WannabeFunction {
  String call(String a, String b, String c) => '$a $b $c!';
}

var wf = WannabeFunction();
var out = wf('Hi', 'there,', 'gang');

void main() => print(out);
```

### 网络

可以使用http来进行进行网络请求

> 如果是post并且body传入对象，需要先把对象实现序列化，它不会像java和typescript自己序列化

```dart
Future<void> fetchData() async {
    // 这个是异步的，需要await才获取到
  final response = await http.get(Uri.parse('https://example.com'));
  print(response.body);
}
```

### 异步

使用async和await来实现，依旧在单个线程里，只是通过调度来实现不阻塞。一般会这样使用

```dart
void main() async {
  // 1. 先开启异步任务（启动任务，但不 await）
  Future<String> futureData = _readFileAsync();

  // 2. 在等待结果之前，先执行别的操作
  print('做一些其他工作...');

  // 3. 之后再获取异步任务的结果（等待完成）
  String fileData = await futureData;

  print('读取到的数据长度：${fileData.length}');
}

Future<String> _readFileAsync() async {
  await Future.delayed(Duration(seconds: 2)); // 模拟耗时操作
  return '这是文件内容';
}
```

如果有多个任务需要等待全部结束可以这样

```dart
void main() async {
  Future<String> task1 = doTask(1, 2);
  Future<String> task2 = doTask(2, 3);
  Future<String> task3 = doTask(3, 1);

  // 等待所有任务完成，用Future.wait方法
  List<String> results = await Future.wait([task1, task2, task3]);

  print('所有任务完成，结果:');
  for (var r in results) {
    print(r);
  }

  print('继续后续操作');
}

Future<String> doTask(int id, int seconds) async {
  await Future.delayed(Duration(seconds: seconds));
  return '任务 $id 完成';
}
```

### 多线程

isolate是轻量级的多线程，和异步不同，它会用多核来处理。线程之间使用消息传递，不共享内存

```dart
import 'dart:isolate';

// isolate具体执行方法体
void worker(SendPort sendPort) {
  // 这里是耗时任务
  int result = 0;
  for (int i = 0; i < 100000000; i++) {
    result += i;
  }
  // 任务完成后把结果发送回主 isolate
  sendPort.send(result);
}

void main() async {
  // 创建接收端口
  ReceivePort receivePort = ReceivePort();

  // 启动 isolate，传入发送端口
  await Isolate.spawn(worker, receivePort.sendPort);

  // 等待 worker 发消息
  int result = await receivePort.first;

  print('计算结果: $result');
}
```

如果需要传参数