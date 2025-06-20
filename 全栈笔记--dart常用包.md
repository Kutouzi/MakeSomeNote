## 全栈笔记--dart常用包

### freezed

类似Lombok的自动生成getset

用以下导入依赖

```bash
flutter pub add freezed_annotation
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

使用以下导入依赖

```bash
flutter pub add riverpod
```

在当前文件中引入

```dart
// 文件名: user_notifier_provider.dart
part 'user_notifier_provider.g.dart';
```

使用命令创建该.g.dart文件

```bash
flutter pub run build_runner build
```

任意位置创建状态类

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';
part 'user_notifier_provider.g.dart';

@riverpod
class UserNotifier extends _$UserNotifier {
  @override
    // 这里返回什么就是设置什么作为要监听的量
  User build() => User("张三");

  void set(String value) {
      // state是build创建的那个监听量
    state = User(value); // 替换整个对象以触发更新
  }
}
```

在组件中使用这个对象，

```dart
class MyWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final user = ref.watch(userNotifierProvider); // 自动刷新！

    return Column(
      children: [
        Text('你好，${user.name}'), // 类似于 Vue 的 {{user.name}}
        ElevatedButton(
          onPressed: () {
              // 对象名+Provider调用notifier以获取riverpod帮忙创建的状态对象
              // 再调用自己写的方法来更新它里面的值
            ref.read(userNotifierProvider.notifier).updateName("李四");
          },
          child: Text("改名为李四"),
        ),
      ],
    );
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

使用以下来导入依赖

```bash
flutter pub add json_annotation
```

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

使用以下导入依赖

```bash
flutter pub add flutter_svg
```

### path_provider

可以访问操作系统下其他可访问目录

### iconify_flutter_plus

可以使用iconify里的所有图标

### flame

游戏框架

### getwidget

高度自定的UI库，可用组件很多

使用以下导入依赖

```bash
flutter pub add getwidget
```

### 

### animations

动画库，主要是组件之间、页面之间切换的动画

使用以下导入依赖

```bash
flutter pub add animations
```

### flutter_animate

动画库，主要是组件的渲染，渐入渐出震动变换等的动画
