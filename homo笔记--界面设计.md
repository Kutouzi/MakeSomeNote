## homo笔记-->界面设计

这节是关于ArkUI相关的

### 布局

根节点为page，即一个ets文件为一个page(最好给我这样做)，且这个.ets文件要放在ets/pages文件夹下

在这个ets文件里可以使用**布局(layout)**

**布局(layout)**用来控制元素(如按钮、进度条、选择框、甚至是其他布局)的位置关系

所以**布局**一般只用作控制点，而不提供其他(如上背景色、响应点击事件等)功能

- 线性布局(row、column)：元素将按照水平或垂直依次排列
- 层叠布局(stack)：元素将存在上下层级关系，在下方的元素会被上方的挡住
- 相对布局(relative container)：元素将以另一个元素作为参照点进行排列
- 弹性布局(flex)：元素将可以按照任意方向排列，且拥有自适应屏幕**缩放**的能力
- 栅格布局(gridrow、gridcol)：元素将拥有自适应设备**自动换行**能力
- 列表(list)：元素将可以超过屏幕进行延伸
- 网格(grid)：元素将以网格对齐方式排列

以上布局的性能从上到下依次递减，网格布局为性能消耗最高的。所以应该减少布局嵌套。

> **自动换行**和**缩放**分别为自适应能力的两种不同分支。**自动换行**会在断点或屏幕大小不足以再放下原尺寸的元素时，自动向下排列。**缩放**会根据屏幕大小对元素进行放大缩小。

### 组件

**组件(component)**是提供功能的元素，一般会放在**布局**下

- 按钮：可创建按钮
- 单选框：可创建选择框
- 切换按钮：可创建拥有两个状态的按钮
- 进度条：可创建进度条
- 文本显示：显示文本
- 文本输入：可以接收用户输入的单行或多行文本
- 富文本：能做到图文混排，接收事件等
- 图片：能提供网络或本地图片显示的功能
- 弹窗：用于显示提示消息
- 视频：用于视频播放显示
- 图标：能够为元素渲染动画等
- 气泡：可以生成信息弹出提醒用的提示
- 菜单：右键菜单或者长按菜单，且支持二级以上菜单

### 路由

路由关系到页面的切换，现在一般使用navigation来实现

使用方法，将navigation作为根节点：

```typescript
Navigation() {
    
}
.mode(NavigationMode.Auto)
```

> mode支持Stack、Split与Auto模式

使用路由跳转操作：

```typescript
/**
@info NavDestination页面的信息
@animated 转场动画(可空)
*/
pushPath(info: NavPathInfo, animated?: boolean): void

// 先创建一个页面栈对象并传入Navigation
pageStack: NavPathStack = new NavPathStack()
// 调用页面栈对象来跳转，传递一个NavPathInfo对象，对象的结构如下包含一个name和一个param
this.pageStack.pushPath({ name: "PageOne", param: "PageOne Param" })
```

### 状态存储

分页面级和应用级两种存储级别，应用级存储可以被持久化

#### LocalStorage

LocalStorage是页面级别状态变量（即前端用于管理一个页面状态的变量）提供存储的内存内的数据库

一个应用中LocalStorage实例可有多个，可在页面内共享，可通过GetShared接口实现跨页面、UIAbility实例内共享

只有@Entry装饰的组件可以独立分配LocalStorage实例，被@Component装饰的组件最多可以访问一个LocalStorage实例

使用：

```typescript
// 创建要存储的东西，并初始化LocalStorage
let para: Record<string,number> = { 'PropA': 47 };
let storage: LocalStorage = new LocalStorage(para);

// 绑定变量（link方式）
@LocalStorageLink('PropA') a: number = 47;
let link: SubscribedAbstractProperty<number> = storage.link('PropA');
// 绑定变量（prop方式）
@LocalStorageProp('PropA') a: number = 47;
let prop: SubscribedAbstractProperty<number> = storage.prop('PropA');

// 修改变量（link方式），双向同步的，自己改变后别人绑定了这个的也会改变
link.set(10);
// 修改变量（prop方式），单向同步的，自己改变后别人绑定了这个的并不会改变
prop.set(10)；

// 获取变量（link方式）
link.get();
// 获取变量（prop方式）
prop.get()；
```

#### AppStorage

AppStorage是应用级别状态变量（即前端用于管理整个应用状态的变量）提供存储的内存内的数据库，全部页面都可以访问。用法基本和LocalStorage相同，只存储级别不同而已

#### PersistentStorage

不支持嵌套对象，支持联合类型和undefined和null。因为它写入磁盘的操作是同步的，所以不要写大量东西。只可以持久化AppStorage的，LocalStorage不行

使用，只要初始化好PersistentStorage，然后将key和AppStorage的key对应上就可以自动持久化了：

```typescript
// 初始化PersistentStorage
PersistentStorage.persistProp('aProp', 47);

// 获取这个属性
AppStorage.get<number>('aProp');
@StorageLink('aProp') aProp: number = 47
```

#### Environment

用来查询设备的运行环境设置、环境变量（如语言设置、主题设置等）

### ets注解

ArkUI使用ets文件来实现，是专门用来写前端逻辑的ts文件，相当于html和css

在ets中可以使用@来使用注解来告诉编译器使用一些特殊的功能

#### 功能型注解

- @Component：作用于结构体上。加上后该结构体会被当做一个**组件**，用来自定义比原生的**组件**(如按钮、选框、进度条等)更复杂的**组件**

- @Entry：作用于结构体上。用于作为进入的第一个界面，一个page（即一个ets文件）只能有一个

- @Builder：作用于方法上。加上后该结构体会被当做一个可复用**组件**，可以接受传参，在@Component组件的build()内使用来自定义轻量级的**组件**，一个@Component组件可以有多个

  ```typescript
  // 可以有按引用传递参数和按值传递参数两种方式，如果需要引起ui状态同步变化，就用按引用
  // 此为传引用例子
  @Builder function overBuilder($$: Tmp) {
    Row() {
      Column() {
        Text(`overBuilder===${$$.paramA1}`)
      }
    }
  }
  @Entry
  @Component
  struct Parent {
    @State label: string = 'Hello';
    build() {
      Column() {
        overBuilder({paramA1: this.label})
      }
    }
  }
  
  // 此为传值例子，和上面就相差一个$$
  @Builder function overBuilder(paramA1: Tmp) {
    Row() {
      Column() {
        Text(`overBuilder===${paramA1.paramA1}`)
      }
    }
  }
  @Entry
  @Component
  struct Parent {
    @State label: string = 'Hello';
    build() {
      Column() {
        overBuilder({paramA1: this.label})
      }
    }
  }
  ```

- @LocalBuilder：作用于方法上。基本功能和@Builder一样，但是只能在@Component组件结构体(struct)内部使用，能够比@Builder更好的管理父子组件的状态关系

- @Styles：作用于方法上。可以定义可复用的样式，用于样式的复用

  ```typescript
  // 可以在全局（ets文件内任何地方）或者@Component组件结构体(struct)内部使用
  @Styles function globalFancy  () {
    .width(150)
    .height(100)
    .backgroundColor(Color.Pink)
    .fontSize(30)
  }
  @Component
  struct FancyUse {
        build() {
          Column({ space: 10 }) {
            // 在这里点调用@Styles的方法就可以复用了
            Text('FancyA')
              .globalFancy()
      }
    }
  }
  ```

- @Extend：作用于方法上。可以扩展@Styles，仅支持在@Component组件外使用

- @AnimatableExtend：过于复杂，见参考文档

#### 数据传递同步型注解

- @State：作用于变量上。作为管理状态的变量，当状态改变时UI会发生对应的渲染改变，只传递一个层级

- @Prop：作用于变量上。无法在@Entry中使用，管理子组件自己的状态的变量，当状态改变时父组件UI不会发生对应的渲染改变，只有子组件会改变，只传递一个层级

- @Link：作用于变量上。无法在@Entry中使用，管理父子组件的状态的变量，当状态改变时父子组件UI都会发生对应的渲染改变，只传递一个层级

- @Provide('变量名')与@Consume('变量名')：作用于变量上。需要成对设置，可以多级和跨级传递，但嵌套的观察不到。@Provide变化会导致所有后代组件状态改变，@Consume来获取变量，@Consume不可以本地初始化

- @Provider和@Consumer：作用于变量上。只能在@ComponentV2，基本和@Provide与@Consume一样，但支持function

- @ObjectLink和@Observed：作用于类上。用于管理对象状态，或同步对象

- @ObservedV2和@Trace：@ObjectLink和@Observed对嵌套类对象和继承属性变化直接观测有局限性，在嵌套类和继承类中使用@Trace装饰的属性具有被观测变化的能力，<font color="red">无脑用这个即可</font>

  ```typescript
  @ObservedV2
  class Son {
    @Trace age: number = 100;
  }
  ```

- @Watch('回调函数名')：作用于变量上。用于设置一个回调，只要变量的状态发生变化，就会触发这个回调

- @Monitor：作用于变量上。和@Watch类似，但可以对对象、数组中某一单个属性或数组项变化进行监听了，而且能获取变化之前的值

- @Once：作用于变量上。不允许单独使用，用于仅初始化同步一次的场景

- @Param：作用于变量上。只能在@ComponentV2，用来表示这个参数需要从外部传入。无法观测深层的变量状态

- @Require：作用于变量上。用来强制父组件构造时必须传参。可以和@Prop、@State、@Provide、@BuilderParam和@Param组合使用

- @Event：作用于lambda函数变量上。用于组件对外输出方法

  ```typescript
  @ComponentV2
  struct Index {
    // changeFactory是一个lambda函数变量，这个变量的类型是“函数空”：()=>void，然后初始化了一个函数：()=>{}
    @Event changeFactory: ()=>void = ()=>{};
  }
  ```

- @Computed：作用于bean对象的getter访问器上。在被计算的值变化的时候，只会计算一次。用于解决UI重用时该属性从而重复计算导致的性能问题

  ```typescript
  @ComponentV2
  struct Index {
    @Local count: number = 100
      
    // 当获取这个count变量发生变化后再调用这个函数，才会触发this.count * 2的计算，否则将原来存有的值返回回去
    @Computed
    get double() {
      return this.count * 2
    }
  }
  ```

- @Type：作用于类变量上。可以让类属性序列化时不丢失类型信息，便于类的反序列化

  ```typescript
  @ObservedV2
  class SampleChild {
    @Trace p1: number = 0;
    p2: number = 10;
  }
  
  @ObservedV2
  class Sample {
    // 复杂对象需要@Type修饰，确保序列化成功
    @Type(SampleChild)
    @Trace f: SampleChild = new SampleChild();
  }
  ```

#### 存储型注解

见**状态存储**小节

- @LocalStorageProp：作用于变量上，页面级。让此变量可以单向同步
- @LocalStorageLink：作用于变量上，页面级。让此变量可以双向同步
- @StorageLink：作用同上面的，但是是应用级
- @StorageProp：作用同上面的，但是是应用级

### $$语法和双向绑定

$$是变量引用运算符，和c++的值传递和引用传递中的引用功能一样

```typescript
@Builder function overBuilder($$: Tmp) {
  Row() {
    Column() {
      Text(`overBuilder===${$$.paramA1}`)
      HelloComponent({message: $$.paramA1})
    }
  }
}
```

#### 双向绑定

$$还可以进行双向绑定，在组件中使用引用运算符可以让变量和组件状态自动同步

支持基础类型变量，以及@State、@Prop和@Link装饰的变量，以及支持大部分内置组件

此外!!语法也可以做到同样的事

### 图形绘制和动画

#### 图形绘制

绘制可以使用Shape来进行

```typescript
Shape(value?: PixelMap)
```

也可以用预制的图形组件，分别有分别为Circle（圆形）、Ellipse（椭圆形）、Line（直线）、Polyline（折线）、Polygon（多边形）、Path（路径）、Rect（矩形）

```typescript
// 例如圆形绘制需要的参数列表如下
Circle(options?: {width?: string | number, height?: string | number}
```

以上组件均可以使用fill来填充颜色、stroke来描边

也可以使用Canvas来进行绘制，在Canvas绘制图像需要路径（path）信息，和opengl差不多

#### 动画

有translate和animation两种方式，也可以使用组件自带的动画效果，详细参开开发文档

### 事件

#### 触屏事件

- 点击事件onclick：一次完整的按下抬起动作才触发

  ```typescript
  // 点击回调函数，event参数提供点击事件相对于窗口或组件的坐标位置，以及发生点击的事件源
  onClick(event: (event?: ClickEvent) => void)
  ```

- 触摸事件ontouch：和鼠标事件区分，并不会触发鼠标点击事件。触碰到屏幕就可以触发，会有三种响应按下（Down）、滑动（Move）、抬起（Up）

  ```typescript
  // 触摸回调函数，event.type可以有TouchType.Down、TouchType.Up、TouchType.Move、TouchType.Cancel，event还提供其他的和点击的相同
  onTouch(event: (event?: TouchEvent) => void)
  ```

> 使用stop

#### 焦点事件

- 获得焦点onFocus：在成为焦点时触发
- 失去焦点onBlur：在失去焦点时触发

> 可以通过clearFocus()清除焦点

#### 拖拽事件

较为复杂，见开发文档

### 渲染控制

build()可以使用if、foreach等条件控制语句来对组件进行渲染的控制

#### 条件渲染

有if、if...else、else可供使用，并且可嵌套

#### 循环渲染

ForEach循环控制的组件创建分两种情况：首次渲染和非首次渲染

- 首次渲染时，会根据前述键值生成规则为数据源的每个数组项生成唯一键值，并创建相应的组件

- 非首次渲染时，它会检查新生成的键值是否在上次渲染中已经存在。如果键值不存在，则会创建一个新的组件；如果键值存在，则不会创建新的组件，而是直接渲染该键值所对应的组件

```typescript
// 第一个参数为数据源；第二个参数是一个函数，告诉ForEach要怎么渲染组件；第三个参数是一个函数，告诉ForEach怎么生成key值
ForEach(arr: Array<Object>, itemGenerator: (item: Object, index: number) => void,keyGenerator?: (item: Object, index: number)
```

#### 懒加载循环渲染

LazyForEach循环和ForEach基本相同，但是它会在组件滑出可视区域外时才创建剩下的组件，看不到的是不会创建的

#### 混合c++渲染

ContentSlot可以提供联合c++进行渲染的能力，过于复杂见开发文档
