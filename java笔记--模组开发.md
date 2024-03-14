## java笔记-->模组开发

### 基本项目结构

下面的包名从java.github.人名的后面开始，下面的模组名统一被称为lolipop

- lolipop

- - client：用于存放客户端用的代码

    - render：用于存放渲染用的代码
      - entity：用时实体的渲染
      - model：模型渲染
      - tileentity：方块实体的渲染

    - screentily：屏幕用的代码

    - util：工具

  - common：用于存放通用的代码

    - blocks：方块
    - items：物品
    - containers：容器
    - enchantments：附魔
    - entities：实体
    - potions：效果
    - tiles：实体方块

  - core：核心代码

    - init：初始化代码
    - interfaces：此模组对外提供的接口
    - enums：枚举
    - util：工具

  - lolipop.java：模组入口类

### 事件总线

分为三种：

- ENENT_BUS：一般事件总线，用于通用的事件消息传递

### 模组入口

在模组名的包下，直接创建一个和模组名同名的类作为入口

```java
@Mod(Lolipop.MODID)
public class Lolipop{
    private static final Logger LOGGER = LogManager.getLogger();
    public static final String MODID = "lolopop";
    public Lolipop(){
        //在初始化该类的时候，将模组注册到Forge的总线中
        MinecraftForge.ENENT_BUS.register(this);
        //将注册对象加入到一般事件总线中
        ItemInit.ITEMS.register(FMLJavaModLoadingContext.get().getModEventBus());
    }
}
```

### 注册

在core.init下，创建多个Init类，分别用来注册物品，方块等

```java
public class ItemInit{
    //此处声明了一个注册对象
    public static final DeferredRegister<Item> ITEMS = DeferredRegister.create(FrogeRegistries.ITEMS,模组名ID);//此处模组名ID是在模组入口类中声明了
    //将要注册的物品（实例）加入注册对象ITEMS里面来
    public static final RegistryObject<Item> lolipopItem = ITEMS.register(模组名,物品实例)
}
```

