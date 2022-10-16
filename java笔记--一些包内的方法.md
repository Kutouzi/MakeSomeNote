## java笔记-->一些包内的方法

在此记录一些包里面的方法可以用来做什么事

### 原生java类

- java.text.DateFormat	可以将日期和文本互相转换
- java.util.Date  获取系统日期
- java.lang.System   有currentTimeMillis()用来获取系统当前毫秒时间；有arraycopy()用来复制数组到另一个数组上
- StringBuilder   此类对象字符串相加更高效；构造函数可以把String对象变此对象（toString可以变回去）；append()返回原对象，可以反复相加

### 其他

- 包装类中的valueOf()把基本数据类型变相应包装类，用xxxValue()转回去（xxx代表基本数据类型）；java5后会自动转
- 