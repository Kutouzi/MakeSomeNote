## java笔记-->架构模式

### MVVM模式

此架构分为三层

- model层：负责获取数据

  ↑↓

- viewmodel层：持有model和view的控制权，负责传递数据和指令（这里指令主要是告诉ui应该怎么绘制）

  ↑↓

- view层：负责绘制UI