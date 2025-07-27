## 全栈笔记-->flutter

### 组件

#### Warp

自动控制子组件之间的间隙的组件

```dart
Wrap(
  spacing: 10,      // 主轴方向的间距（如横向）
  runSpacing: 8,    // 交叉轴方向的间距（如纵向）
  children: [
    Chip(label: Text('A')),
    Chip(label: Text('B')),
    Chip(label: Text('C')),
  ],
)
```

#### Expanded

在Flex布局中（如`Row`/`Column`/`Flex`组件中，`Stack`/`ListView`/`GridView`不可用），让子组件占据尽可能多的剩余空间，父组件会跟着子组件占的最大空间来

```dart
Row(
  children: [
    Icon(Icons.star),
    Expanded(
      child: Text(
        '这是一段很长的文字，会占满剩余空间',
        overflow: TextOverflow.ellipsis,
      ),
    ),
    Icon(Icons.more_vert),
  ],
)
```

可以用flex来控制比例

```dart
Row(
  children: [
    Expanded(flex: 1, child: Container(color: Colors.red)),  // 占1份
    Expanded(flex: 2, child: Container(color: Colors.green)), // 占2份
  ],
)
```

#### Spacer

创建一个弹性的空白区域，用来“推开”其它组件，会占据剩余空间，常用来实现两端对齐效果

```dart
Row(
  children: [
    Icon(Icons.menu), // 贴着左边屏幕
    Spacer(), // 中空
    Icon(Icons.search), // 贴着右边屏幕
  ],
)
```

#### SizedBox

和`Spacer`一样可以创建空白空间，不过不是弹性的

```dart
Row(
  children: [
    Icon(Icons.home),
    SizedBox(width: 8), // 需设置间隔多少
    Text("首页"),
  ],
)
```

#### Container

和`SizedBox`类似，但是可以使用阴影背景色圆角等高级设置

```dart
Container(
  height: 200,
  width: double.infinity, // 宽度撑满，也可以在外面套个Expanded
  decoration: BoxDecoration(
    color: Colors.white, // 背景色
    borderRadius: BorderRadius.circular(16), // 圆角半径
    boxShadow: [
      BoxShadow(
        color: Colors.black26,      // 阴影颜色
        blurRadius: 8,              // 模糊程度
        offset: Offset(0, 4),       // 阴影偏移量
      ),
    ],
  ),
  child: Column(
    children: [
      Text('内容'),
    ],
  ),
)
```

> 一般与Expanded搭配使用，把Expanded套在它外面，以便自适应

#### Text

常用的文字显示组件，

经常用的超出显示...省略，前提是它不能换行(`maxLines`设为1)

```dart
Container(
  width: 200,
  color: Colors.amberAccent,
  child: Text(
      '这是一段非常非常非常非常长的文字，会超出容器宽度',
      overflow: TextOverflow.ellipsis, // 溢出时显示省略号，TextOverflow.fade可以超出部分淡出
      maxLines: 1,                      // 限制最多显示一行
	),
)
```

> 会换行，不换行用`TextSpan`，文字会尽可能连着

使用的样式叫TextStyle，可以设置关于字的各种东西

```dart
Text(
  '文本',
  style: TextStyle(
    color: Colors.red, // 设置字体颜色
    fontSize: 16,      // 还可以同时设置字体大小等
  ),
)
```

它可以复用，比如作为成员变量定义

```dart
const TextStyle myFontStyle = TextStyle(
  fontWeight: FontWeight.w500,
  fontSize: 16,
  color: Colors.black,
);
```

它也可以被覆盖其中一个值，使用copyWith

```dart
Text(
  '特殊文字',
  style: myFontStyle.copyWith(
    color: Colors.red,  // 只覆盖颜色，其他保持不变
  ),
)
```

#### ClipPath

与`h5`的`clip`相似，可以裁剪子组件形状，以做出各种不规则图形

定义裁剪类

```dart
class MyClipper extends CustomClipper<Path> {
  @override
    // 裁剪成右上对三角形
  Path getClip(Size size) {
    final path = Path();
    path.lineTo(0, size.height);          // 左下角
    path.lineTo(size.width, 0);           // 右上角
    path.lineTo(size.width, size.height); // 右下角
    path.close();                         // 回到起点
    return path;
  }

  @override
  bool shouldReclip(CustomClipper<Path> oldClipper) => false;
}
```

在父组件中使用，子组件就会被裁剪

```dart
ClipPath(
  clipper: MyClipper(), // 自定义路径裁剪器
  child: Container(
    width: 200,
    height: 100,
    color: Colors.blue,
  ),
)
```

#### BoxDecoration

为该元素添加圆角、背景色、边框、阴影、渐变等

```dart
Container(
  width: 200,
  height: 100,
    // 使用在decoration下
  decoration: BoxDecoration(
    color: Colors.blue, // 背景颜色
    borderRadius: BorderRadius.circular(12), // 圆角
    border: Border.all(color: Colors.black, width: 2), // 边框
    boxShadow: [
      BoxShadow(
        color: Colors.black26,
        blurRadius: 6,
        offset: Offset(2, 2),
      ),
    ],
  ),
  child: Center(child: Text('Hello')),
)
```

> 一旦使用了 `decoration`，就不能再用 `color`，否则会报错

### 属性

#### 自适应

在`Column`/`Row`等布局中有，以适应父组件宽高

```dart
crossAxisAlignment: CrossAxisAlignment.stretch
```

在普通组件中，有以充满宽高

```dart
width: double.infinity,
height: double.infinity,
```
