## homo笔记-->应用开发

### 应用生命周期

1. **Create**

   UIAbility实例创建完成时触发onCreate()

   一般用于初始化变量以及资源等

2. **WindowStageCreate**

   创建WindowStageCreate后触发onWindowStageCreate()

   一般用传入的参数windowStage来加载页面和设置事件订阅等

3. **Foreground**和**Background**

   在运行期会在这两个状态互换，切换前台时（第一次运行时也会调用）调用onForeground()，切后台时调用onBackground()

   onForeground()一般用于申请系统资源等，onForeground()一般用于释放不可见时无用资源或保存状态等

4. **WindowStageDestroy**

   Destroy前触发onWindowStageDestroy()

   一般用来释放ui界面资源

5. **Destroy**

   UIAbility实例被销毁前触发onDestroy()

   一般用来释放系统资源和保存数据





