## javafx组件笔记-->不常用但用到记不起来的方法

### 绑定一个什么玩意到一个快捷键
```java
/*put(参数一(绑定的键代码，对应操作系统的控制键),参数二(Runnable的run方法覆写))*/
mainScene.getAccelerators().put(new KeyCodeCombination(KeyCode.Y, KeyCombination.SHORTCUT_DOWN),()-> System.out.println("a"));
```

### 让布局来管理和设置组件
```java
pane.setAlignment(Pos.CENTER); //所有组件居中
```

### 关于主窗口的设置
```java
primaryStage.getIcons().add(new Image("/icon/logo1.png"));//添加图标
/*窗口初始化几种值*/
primaryStage.initStyle(StageStyle.DECORATED);//正常显示 也是默认
primaryStage.initStyle(StageStyle.TRANSPARENT);//三个组键都没有(最小化 最大化 关闭)
primaryStage.initStyle(StageStyle.UNDECORATED);//背景透明
primaryStage.initStyle(StageStyle.UNIFIED);//无title栏的背景颜色
primaryStage.initStyle(StageStyle.UTILITY);//无最小化最大化 只有关闭按钮
/***/
primaryStage.setMaximized(true);//全屏显示
primaryStage.setResizable(false);//窗口不可改变高宽
```

### 监听事件限制字符输入个数
```java
/*新的值newValue大于8就设置成原来的值oldValue*/
searchBar.textProperty().addListener((observableValue, oldValue, newValue) -> {if(newValue.length() >8 ) searchBar.setText(oldValue);});
```

### 流式布局flowpane
```java
flowPane.setHgap(); //用来设置里面组件的间距
flowPane.setVgap(); //同上，这是设置上下间距的
flowPane.setOrientation(Orientation.VERTICAL); //设置默认把组件垂直排列
```

### 窗口依附
```java
dialogStage.initOwner(primalStage); //依赖于主窗口
dialogStage.initModality(Modality.WINDOW_MODAL);
```

### 动态宽高示例
```java
/**
 * 动态监听高度,给proheight添加监听器，放控制器的init里，并重写changed方法
 */
primaryStage.heightProperty().addListener(new ChangeListener<Number>() {
	@Override
	public void changed(ObservableValue<? extends Number> observable, Number oldValue, Number newValue) {}});
```