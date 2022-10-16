## java笔记-->项目结构

在此记录标准的项目应该有的东西

### 标准项目

示例：
```c
-ProjectA  //项目名
 -lib //存放一些引用的jar包
 -src
  -main
   -java //这是根目录
   module-info.java //自动生成
    -indi  //项目性质，此处为个人项目的意思，公司的一般org
     -ktz  //项目人名字
      -annotation //放置项目自定义注解
      -aspect //放置切面代码
      -config //放置配置类
      -constant //放置常量、枚举等定义
       -consist //存放常量定义
       -enums //存放枚举定义
      -controller //放置控制器代码
      -filter //放置一些过滤、拦截相关的代码
      -mapper(dao) //放置数据访问层代码接口
      -model //放置数据模型代码
       -entity //放置数据库与服务层实体对象定义
       -dto //存放控制与服务层数据传输对象定义
       -vo //存放显示层对象定义
      -service //放置具体的业务逻辑代码（接口和实现分离）
       -impl //存放业务逻辑实际实现
      -utils //放置工具类和辅助代码
      Main.java
   -resources //存放资源
    -mapper //存放mybatis的XML映射文件
    -sql
    -static //放网页静态资源
     -js
     -css
     -img
     -font
    -templates //放网页模板如thymeleaf/freemarker等
     -header
     -sidebar
     -botton
     index.html
  -test //测试用的
 -out //编译后自动生成
```
> 注意事项
> 1、Contorller层参数传递建议不要使用HashMap，建议使用数据模型定义
>2、Controller层里可以做参数校验、异常抛出等操作，但建议不要放太多业务逻辑，业务逻辑尽量放到Service层代码中去做
>3、Service层做实际业务逻辑，可以按照功能模块做好定义和区分，相互可以调用
>4、功能模块Service之间引用时，建议不要渗透到DAO层（或者mapper层），基于Service层进行调用和复用比较合理
>5、业务逻辑层Service和数据库DAO层的操作对象不要混用。Controller层的数据对象不要直接渗透到DAO层（或者mapper层）；同理数据表实体对象Entity也不要直接传到Controller层进行输出或展示。

### web项目
示例：
```c
-ProjectA  //项目名
 -src //这是根目录
  -main
   -java
    -indi  //项目性质，此处为个人项目的意思，公司的一般com
     -ktz  //项目人名字
      -proxy //放代理java文件
      -bean //放数据库的model的java文件
      -web //放servlet使用的东西
       -fillter //servlet的过滤器java文件
       -listener //servlet的监听器java文件
       -servlet //放servletjava文件
 -web  //放网页文件
  -css  //放层叠样式表文件
  -js  //放javasript文件
  -WEB-INF  //放网页不可访问的隐藏资源
  web.xml
   -lib	//放jsp网页需要用到的包
   jdbc.jar
   -classes //放一些从src中编译好的字节码文件，一般自己生成，路径等同src
 index.jsp
```
