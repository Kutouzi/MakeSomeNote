## java笔记-->反射

在此记录一些反射涉及到的东西

### 动态代理

1.用来干什么：
代理对象代理真实对象，达到增强真实对象功能。可以用来修改原本该传入的参数值，也可以把原来的方法给修改掉。

2.怎么用：
```java
/*第一步创建动态代理对象(假定已存在有真实对象)*/
真实对象类型 代理对象名 = (真实对象类型) Proxy.newProxyInstance(真实对象名.getClass().getClassLoader(),真实对象.getClass().getInterfaces(),new InvocationHandler{
/*第二步覆写参数一是代理对象本身，二是该代理对戏那个代理的函数，三是代理的函数的参数列表*/
@Override
public Object invoke(Object proxy,Method method,Object[] args) throws Throwable{
/*第三步判断是否是你要增强的函数*/
if(method.getName().equals("真实对象函数名")){
/*第四步增强参数,如果只有一个参数就是args[0],假定原本参数是int型的*/
(Integer) newargs = (Integer) args[0]
/*第五步随便修改一下传入的参数值*/
newargs = newargs++;
/*第六步使用真实对象调用该函数，但返回参数为新的参数*/
Object obj = method.invoke(真实对象,newargs);
return obj;
}
/*第七步不符合就不增强，参数也不改原样调用该函数，如果没有这行它光代理不干任何事*/
return method.invoke(真实对象,args);
}
})
/*第八步使用代理方法代理*/
代理对象名.真实对象函数名
```

