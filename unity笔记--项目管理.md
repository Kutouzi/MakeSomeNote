## unity笔记-->项目管理

### 项目结构

一般unity项目结构会是这样的

#### 3D项目

```bash
Assets
│
├── Scripts ##放置所有的 C# 脚本,根据功能或模块进行分类
│   ├── Player
│   ├── Enemy
│   ├── UI
│   └── Utilities
│
├── Scenes ##存放所有的场景文件,不同的场景（如主菜单、关卡场景、过场场景）可以放在单独的文件夹中
│   ├── MainMenu.unity
│   ├── Level1.unity
│   └── Level2.unity
│
├── Prefabs ##存储所有的预制件,按类别（如玩家、敌人、环境）分类存放
│   ├── Player
│   ├── Enemy
│   └── Environment
│
├── Materials ##存储所有材质文件，每种材质可以按使用对象（如玩家、敌人、环境）分类
│   ├── PlayerMaterials
│   ├── EnemyMaterials
│   └── EnvironmentMaterials
│
├── Textures ##存放项目中使用的所有纹理图片，建议按不同的对象分类
│   ├── PlayerTextures
│   ├── EnemyTextures
│   └── EnvironmentTextures
│
├── Models ##存放项目中的 3D 模型（如 .fbx、.obj 等），建议按不同的对象分类
│   ├── PlayerModels
│   ├── EnemyModels
│   └── EnvironmentModels
│
├── Animations ##动画文件（Animator 和 AnimationClip）应存放在此，按不同的对象分类
│   ├── PlayerAnimations
│   ├── EnemyAnimations
│   └── UIAnimations
│
├── Audio ##存放音频资源，推荐按类型分类，例如背景音乐、音效和语音
│   ├── Music
│   ├── SoundEffects
│   └── Voice
│
├── UI ##这里存放所有的 UI 相关资源，包括 UI 预制件、Sprite、字体等
│   ├── Sprites
│   ├── Fonts
│   └── Prefabs
│
└── Shaders ##存放所有的 Shader 文件，根据使用的对象（如 UI、环境、水体、天空盒等）进行分类
    ├── WaterShaders
    ├── SkyboxShaders
    └── UIShaders
```

#### 2D项目

```bash
Assets
│
├── Scripts ##作用与3d项目无区别
│   ├── Player
│   ├── Enemy
│   ├── UI
│   └── Utilities
│
├── Scenes ##作用与3d项目无区别
│   ├── MainMenu.unity
│   ├── Level1.unity
│   └── Level2.unity
│
├── Sprites ##存放所有2dsprite资源
│   ├── Characters ##存放玩家和其他可玩角色的 Sprite 文件
│   ├── Enemies ##敌人的 Sprite，建议根据不同敌人类型进一步划分子文件夹
│   ├── Environment ##场景中使用的背景图、地面、障碍物等环境元素的 Sprite
│   ├── UI ##游戏里UI 相关的 Sprite（如对话框、快进自动按钮等），与UI下的Sprite区分
│   └── Items ##道具、可收集物品等的 Sprite
│
├── Animations ##作用与3d项目无区别
│   ├── Characters
│   ├── Enemies
│   ├── UI
│   └── Environment
│
├── Tilemaps
│   ├── Tiles ##存放所有的 Tile 图块资源（如地面、墙壁、平台等）
│   ├── TilePalettes ##存储 Tilemap 的调色板，帮助你在编辑器中创建和设计关卡
│   └── TilemapGrids ##存放场景中使用的 Tilemap Grid 配置文件
│
├── Prefabs ##作用与3d项目无区别
│   ├── Characters
│   ├── Enemies
│   ├── Items
│   └── Environment
│
├── Materials
│   └── Shaders ##自定义的 Shader 文件，用于实现特殊的渲染效果，如水波、光照等
│
├── Audio ##作用与3d项目无区别
│   ├── Music
│   ├── SoundEffects
│   └── Voices
│
├── Fonts
│
├── UI
│   ├── Sprites ##这里作用变更为主UI，即主菜单、暂停菜单等场景的UI的Sprite
│   ├── Prefabs
│   └── Fonts
│
└── Resources ##文件夹 是 Unity 中一个特殊的文件夹，存放需要通过代码动态加载的资源。任何放在这个文件夹中的资源都可以使用 Resources.Load 动态加载，适合需要按需加载的场景。
    └── (for dynamically loaded assets)
```

