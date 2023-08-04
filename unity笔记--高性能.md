## unity笔记-->高性能

为制作出一个高性能的游戏，以下记录一些常用提升性能的技巧

### UI的制作

UI和HUD之类跟随摄像机的东西，尽可能的放在渲染模式设置了CameraMode（屏幕空间-摄像机）的Canvas（画布）下

### 距离计算

如果需要计算两个东西的距离，并且不得不在update中计算的时候。要谨慎考虑是否需要精确的距离，如果不需要应该尽量避免开方和重复计算等操作的出现

### 遮挡剔除

遮挡剔除应该尽量开启，并且使用RectMask2D来处理不规则的图形的遮挡，以达到减少overdraw的目的

### 使用协程

虽然unity支持多线程，但是仅限于不处于UnityEngine命名空间下的东西。即使这样也可以使用协程来代替多线程，至于协程是什么，为什么可以通过协程来使用多线程的问题，请见**c++笔记--线程、协程和异步操作**。比较推荐的不是使用unity自带的StartCoroutine方法，而是github上的UniTask插件的协程，比起unity的性能要高很多

### 使用UniTask

unity中的协程需要依赖于monobehaviour，有些不需要继承mono但又需要在update方法中新建线程来处理的脚本就很难做到。这需要使用到c#中的async和await，不符合unity设计。

而UniTask就可以解决这个问题

### 列表定义

在使用List的时候尽量给出长度，避免反复扩列而产生不必要的内存申请

### 隐藏物件

如果物件需要频繁的出现和隐藏，可以尽量使用修改位置到摄像机之外的方法来实现，避免使用SetActive的方法导致的脚本重载

### 封装Debug

新建一个自己的Debug类，包装原有unity的Debug。然后在unity项目设置中的脚本编译里的脚本定义符号下添加上“DEBUG”，就能输出Debug。如果生成release的时候，去掉“DEBUG”就不会输出Debug了

```c#
[Conditional("DEBUG")]
public static void Debug(string massage){
    Debug.Log(massage);
}
```

### 获取组件

使用GetComponent系列的方法时，尽量传进去泛型而不是字符串，例如

```c#
var button = GetComponent<Button>();
```

### 获取节点

使用Tag获取会遍历所有节点信息

### 获取组件的时机

如果在start方法中获取组件会依赖unity的函数生命周期，有时需要获取组件时，会出现组件还没有创建的情况。因此可以使用懒加载的方式在get属性器中获取组件，跳出生命周期。例如

```c#
public class StartButton : MonoBehaviour{
    //为方便理解这里使用到的一个中转对象，当然也可以不用
    private Button _buttonTemp;
    //获取组件对象
    private Button _button{
        get{
            if(_buttonTemp == null){
                _buttonTemp = GetComponent<Button>();
            }
            return _buttonTemp;
        }
    }
    //不在这里获取组件对象了
    private Start(){
        
    }
    //使用获取到的组件对象
    public void GetColor(){
        return _button.color;
    }
}
```

### 文字网格pro

使用TextMeshPro可以比原本的TextMesh获得更高的清晰度，更低的overdraw
