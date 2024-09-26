## java笔记-->模组开发

### 基本项目结构

下面的包名从java.github.人名的后面开始，下面的模组名统一被称为lolipop

```bash
src/
 ├── main/
 │   ├── java/                      # Java 代码目录
 │   │   └── github/
 │   │       └── lolipop/          # 包命名，通常为 mod ID 或域名反转
 │   │           ├── Lolipop.java    # Mod 的主要入口类
 │   │           ├── common/		 # 客户端服务端共用的代码
 │   │           │   ├── capabilities/   # 能力相关的类，如能力接口和实现
 │   │           │   ├── events/         # 事件处理器类
 │   │           │   ├── network/        # 网络通信相关类
 │   │           │   ├── items/          # 物品类
 │   │         	 │   ├── blocks/         # 方块类
 │   │         	 │   ├── tiles/			 # 实体方块类
 │   │         	 │   ├── potions/		 # buff效果类
 │   │         	 │   ├── enchantments/   # 自定义附魔类
 │   │         	 │   ├── containers/     # 自定义物品容器类
 │   │           │   └── entities/       # 实体类
 │   │           ├── client/         # 保存仅客户端使用的代码如渲染等
 │   │           │   ├── render/         # 用于存放渲染用的代码
 │   │           │   ├── events/         # 存放客户端事件
 │   │           │   └── screentily/     # 屏幕用的代码
 │   │           └── core/
 │   │               ├── init/           # 初始化用的代码
 │   │               ├── api/         	 # 对外提供的接口
 │   │               ├── enums/          # 枚举对象
 │   │               ├── util/			 # 工具类
 │   ├── resources/                  # 资源文件目录
 │       ├── META-INF/               # 用于 mod 的元数据文件
 │       │   └── mods.toml           # mod 的描述文件
 │       ├── assets/
 │       │   └── lolipop/          # mod 的资源文件，如纹理、语言文件等
 │       │       ├── blockstates/    # 方块状态定义
 │       │       ├── lang/           # 语言文件
 │       │       ├── models/         # 方块和物品的模型文件
 │       │       └── textures/       # 纹理文件
 │       └── data/
 │           └── lolipop/          # 数据包文件夹
 │               ├── loot_tables/    # 自定义战利品表
 │               ├── recipes/        # 自定义配方
 │               └── tags/           # 自定义标签

```



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

### 事件

forge有非常多继承了Event类的事件

```java
// 在事件类上加这个注解进行注册，不标明client就是注册到forge总线
@Mod.EventBusSubscriber(modid = Lolipop.MODID)
public class ModEvent{
    // 在具体处理事件的方法上加这个注解，forge的事件系统就会调用这个方法了
    // 这里是要LivingHurtEvent事件，LivingHurtEvent事件触发时候就会调用这个方法传入event
    @SubscribeEvent
    public static void onPlayerAttack(LivingHurtEvent event){
        
    }
}
```

> @SubscribeEvent修饰的方法只能有一个事件参数，不准接收多个事件

#### 事件联合

为了解决需要多个事件判断进行逻辑处理的情况，就有了**状态追踪器**

它需要：

- 一个自己的逻辑：下面的executeCombinedLogic()就是自己要执行的真正逻辑
- 一个用于跟踪每一个玩家的状态变量hashmap：它用来处理多个玩家的情况，保证不会互相触发其他玩家的事件
- 一个事件状态的bean数据结构：它记录某个事件是否发生过了，经过了多少时间
- 一个检查状态的函数：它用来检查是否满足可以执行自己逻辑的条件，下面的checkEvents()就是
- 多个原始事件：在需要的@SubscribeEvent方法内调用检查状态的函数

```java
    // 事件状态的bean数据结构，设置成getset也行随便你
    public static class PlayerEventStatus {
        public boolean attackEventTriggered = false;
        public boolean hurtEventTriggered = false;
        public long lastAttackEventTime = 0;
        public long lastHurtEventTime = 0;
    }
```



```java
public class MultiEventHandler {

    // 用于跟踪每个玩家的状态，传入玩家作为主键，事件状态结构作为值
    private static final Map<PlayerEntity, PlayerEventStatus> playerEventStatusMap = new HashMap<>();

    @SubscribeEvent
    public static void onLivingAttack(LivingAttackEvent event) {
		checkEvents(player, status);
    }

    @SubscribeEvent
    public static void onLivingHurt(LivingHurtEvent event) {
		checkEvents(player, status);
    }

    private static void checkEvents(PlayerEntity player, PlayerEventStatus status) {
        long currentTime = player.level.getGameTime();

        // 检查两个事件是否都触发，并且在设定的时间窗口内
        if (status.attackEventTriggered && status.hurtEventTriggered) {
            if (Math.abs(status.lastAttackEventTime - status.lastHurtEventTime) <= EVENT_WINDOW_TICKS) {
                // 执行自己的逻辑
                executeCombinedLogic();
            }
            // 执行后或者超时都要重置状态
            status.attackEventTriggered = false;
            status.hurtEventTriggered = false;
        }
    }

    private static void executeCombinedLogic() {
        // 执行你的逻辑
    }
}
```

