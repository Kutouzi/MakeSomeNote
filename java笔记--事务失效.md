## java笔记-->事务失效

### 事务失效的场景

- 基类不为RunTimeException
- 方法不属于springboot，即不为component、service、mapper等
- 方法为private修饰
- 方法不是动态代理对象的方法
- 使用了多线程
- 自己捕获了异常

### 解决方法

- 包装异常类为RunTimeException的子类再抛出
- 检查写了注解
- 检查为public
- 使用@Autowire自动注入对象，再通过对象来调用该方法
- 用分布式事务的注解而不是springboot原生的
- 不建议在开启了事务的方法里自己捕获异常