## 全栈笔记--dart常用包

### freezed

类似Lombok的自动生成getset

```yaml
dependencies:
  freezed_annotation: ^2.4.1
  json_annotation: ^4.8.1

dev_dependencies:
  build_runner: ^2.4.6
  freezed: ^2.4.7
  json_serializable: ^6.8.0
```

使用方法，还可以添加fromJson以支持对象序列化

```dart
@freezed
class User with _$User {
  const factory User({
    required int id,
    required String name,
  }) = _User;

  // 如果要支持 JSON 序列化
  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
}
```

### riverpod

可以用来进行状态管理，类似于pinia，解决多文件状态共享的问题

```dart
// 声明式共享状态，在任意地方声明，任意地方都能访问到
@riverpod
class Count extends _$Count {
  @override
  int build() => 0;

  void increment() => state++;
}

// 轻松访问到
class Title extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(countProvider);
    return Text('$count');
  }
}
```

### go_router

路由跳转和管理

### shimmer

提供UI加载还没有数据时的一种加载动画

### flutter_slidable

提供列表项左右滑动的动画

### dio

比原生更好的网络请求

###  json_serializable

自动JSON序列化反序列化，自动生成代码

### shared_preferences

轻量的本地存储，只建议存用户配置这些少量的东西

### flutter_screenutil

分辨率适配

### url_launcher

支持打开外部链接，发送邮件等操作

### get_it

一般用riverpod就可以了。类似spring的bean，在任意地方可以访问对象，并且对象只会创建一次，会自动创建

```dart
// 注册就自动创建了这个AuthService对象，只会创建一次
getIt.registerSingleton<AuthService>(AuthService());
// 其他地方使用获取这个AuthService对象
final authProvider = getIt<AuthService>()
```

### flutter_svg

可以在dart里写svg，并且使用它

### path_provider

可以访问操作系统下其他可访问目录

### iconify_flutter_plus

可以使用iconify里的所有图标

### flame

游戏框架

### getwidget

高度自定的UI库，可用组件很多

### animations

动画库，主要是组件之间、页面之间切换的动画

### flutter_animate

动画库，主要是组件的渲染，渐入渐出震动变换等的动画
