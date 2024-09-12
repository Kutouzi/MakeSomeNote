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
- @Entry：作用于结构体上。用于作为应用进入的第一个界面，只能有一个

#### 数据传递同步型注解

- @State：作用于变量上。作为管理状态的变量，当状态改变时UI会发生对应的渲染改变，只传递一个层级
- @Prop：作用于变量上。无法在@Entry中使用，管理子组件自己的状态的变量，当状态改变时父组件UI不会发生对应的渲染改变，只有子组件会改变，只传递一个层级
- @Link：作用于变量上。无法在@Entry中使用，管理父子组件的状态的变量，当状态改变时父子组件UI都会发生对应的渲染改变，只传递一个层级
- @Provide('变量名')与@Consume('变量名')：作用于变量上。需要成对设置，可以多级和跨级传递，@Provide变化会导致所有后代组件状态改变，@Consume来获取变量
- @ObjectLink和@Observed：作用于类上。用于管理对象状态，或同步对象
- @Watch('回调函数名')：作用于变量上。用于设置一个回调，只要变量的状态发生变化，就会触发这个回调

#### 存储型注解

见**状态存储**小节

- @LocalStorageProp：作用于变量上，页面级。让此变量可以单向同步
- @LocalStorageLink：作用于变量上，页面级。让此变量可以双向同步
- @StorageLink：作用同上面的，但是是应用级
- @StorageProp：作用同上面的，但是是应用级

### $$语法

$$是变量引用运算符，在组件中使用引用运算符可以让变量和组件状态自动同步

支持基础类型变量，以及@State、@Prop和@Link装饰的变量，以及支持大部分内置组件

