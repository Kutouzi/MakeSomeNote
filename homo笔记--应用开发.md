## homo笔记-->应用开发

### 程序包

分为三种HAP、HAR、HSP

HAP相当于安卓的apk，但是区别于apk的是，hap可以多hap协同工作

HAR相当于c++的lib，静态代码库。作为第三方库使用加载，但是直接打包进应用里面

HSP相当于c++的dll，动态代码库。作为第三方库使用加载，只在运行时多应用共享

### 运行时加载库

可以使用import或lazy-import在运行时加载三方库，支持加载HSP模块、HAR模块、OHPM包、Native库

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

### stage模型

使用设计模式中的**上下文模式**来进行环境信息查询、资源调用

项目下会有一个app.json5在根目录，在根目录下的每一个模块目录下都会有个module.json5

app.json5包含有：

- 应用的全局配置信息，包含应用的Bundle名称、开发厂商、版本号等基本信息
- 特定设备类型的配置信息

module.json5包含有：

- 模块的基本配置信息，包含Module名称、类型、描述、支持的设备类型等基本信息
- 应用组件信息，包含UIAbility组件和ExtensionAbility组件的描述信息
- 应用运行过程中所需的权限信息

### 统一数据类型

数据首先需要定义好标准，然后才能传输和存储（持久化）

#### 统一类型描述符

数据的定义在homo中使用统一类型描述符（Uniform Type Descriptor，简称UTD）来进行，这样做是为了解决系统中的类型模糊问题

支持MIME type转换为UTD标准类型

UTD中定义的标准化数据类型在设计原则上按物理和逻辑分为两类：

- **按物理分类**的根节点为general.entity，用于描述类型的物理属性，比如文件、目录等
- **按逻辑分类**的根节点为general.object，用于描述类型的功能性特征，如图片、网页等

一般被标准化数据类型都会有以下通用属性：

- **typeId：** 定义标准化数据类型的ID，该ID具有唯一性
- **belongingToTypes：** 定义标准化数据类型的归属关系，即该标准化数据类型归属于哪个更高层级的类型，允许存在一个标准化数据类型归属于多个类型的情况
- **description：** 标准化数据类型的简要说明
- **referenceURL：** 标准化数据类型的参考链接URL，用于描述类型的详细信息
- **iconFile：** 标准化数据类型的默认图标文件路径，可能为空字符串（即没有默认图标），应用可以自行决定是否使用该默认图标
- **filenameExtensions：** 标准化数据类型所关联的文件名后缀列表
- **mimeTypes：** 标准化数据类型所关联的多用途互联网邮件扩展类型列表

#### 基础类型

这个是homo给出的常用类型，已经被定义好了

有以下：

| 数据结构           | 数据类型               | 说明   |
| ------------------ | ---------------------- | ------ |
| PlainText          | 'general.plain-text'   | 纯文本 |
| Hyperlink          | 'general.hyperlink'    | 超链接 |
| HTML               | 'general.html'         | 富文本 |
| OpenHarmonyAppItem | 'openharmony.app-item' | 图标   |

用法：

1. 首先导入模块

   ```ts
   import { uniformDataStruct, uniformTypeDescriptor, unifiedDataChannel } from '@kit.ArkData';
   ```

2. 创建数据内容

   ```ts
   // 这是文本类型的
   let hyperlinkDetails : Record<string, string> = {
     'attr1': 'value1',
     'attr2': 'value2',
   }
   ```

3. 创建UTD实例

   ```ts
   let unifiedData = new unifiedDataChannel.UnifiedData();
   let plainTextRecord = new unifiedDataChannel.UnifiedRecord(uniformTypeDescriptor.UniformDataType.PLAIN_TEXT, plainText);
   ```

4. 添加数据内容到UTD实例里，一个UTD实例可以添加的record不止一条

   ```ts
   unifiedData.addRecord(plainTextRecord);
   ```

5. 假设已经传输到别的地方后，进行数据的获取

   ```ts
   let records = unifiedData.getRecords();
   ```

6. 遍历UTD里的记录，因为可能不止一条record所以需要用遍历

   ```ts
   for (let record of records) {
     // 先进行UnifiedRecord类型的转换
     let unifiedDataRecord = record as unifiedDataChannel.UnifiedRecord;
     let obj = unifiedDataRecord.getValue() as object;
     // 如果不为undefined，则读取该数据记录的类型
     if (obj != undefined) {
       // 验证看是什么基础类型
       let type : string = obj["uniformDataType"];
       switch (type) {
         case uniformTypeDescriptor.UniformDataType.PLAIN_TEXT:
           Object.keys(obj).forEach(key => {
             console.info('show records: ' + key + ', value:' + obj[key]);
           });
           break;
         default:
           break;
       }
     }
   }
   ```

#### 自定义类型

如果需要自定义数据类型至少需要满足上面的通用属性

1. 首先在entry\src\main\resources\rawfile\arkdata\utd\目录下新增utd.json5文件

2. 在文件里按如下例子模仿写，这是开发文档的例子

   ```json
   {
        "UniformDataTypeDeclarations": [
            {
                "TypeId": "com.example.myFirstHap.image",
                "BelongingToTypes": ["general.image"],
                "FilenameExtensions": [".myImage", ".khImage"],
                "MIMETypes": ["application/myImage", "application/khImage"],
                "Description": "My Image.",
                "ReferenceURL": ""
            }
        ]
   }
   ```

3. 如果还需要其他应用也识别得了这个数据类型还要在上面文件里写个Reference

   ```json
       {
           "ReferenceUniformDataTypeDeclarations": [
                {
                    "TypeId": "com.example.myFirstHap.image",
                    "BelongingToTypes": ["general.image"],
                    "FilenameExtensions": [".myImage", ".khImage"],
                    "MIMETypes": ["application/myImage", "application/khImage"],
                    "Description": "My Image.",
                    "ReferenceURL": ""
                }
           ]
       }
   ```

#### 查询文件归属类型

获得了某个文件，想要知道这个文件是属于UTD里的哪个类型，需要按以下步骤进行

1. 导入uniformTypeDescriptor模块

   ```ts
   import { uniformTypeDescriptor } from '@kit.ArkData';
   ```

2. 可根据文件后缀(如.mp3)查询对应UTD数据类型

   ```ts
   // 这里前面假设已经获取了文件名，并且分割字符得到了文件扩展名
   let fileExtention = '.mp3';
   let typeId = uniformTypeDescriptor.getUniformDataTypeByFilenameExtension(fileExtention);
   let typeObj = uniformTypeDescriptor.getTypeDescriptor(typeId);
   console.info('typeId:' + typeObj.typeId);
   console.info('belongingToTypes:' + typeObj.belongingToTypes);
   console.info('description:' + typeObj.description);
   console.info('referenceURL:' + typeObj.referenceURL);
   console.info('filenameExtensions:' + typeObj.filenameExtensions);
   console.info('mimeTypes:' + typeObj.mimeTypes);
   ```

3. 也可根据MIMEType(如audio/mp3)查询对应UTD数据类型

   ```ts
   // 假设传过来的https数据包中有MIME的信息  
   let mimeType = 'audio/mp3';
   let typeId = uniformTypeDescriptor.getUniformDataTypeByMIMEType(mineType);
   let typeObj = uniformTypeDescriptor.getTypeDescriptor(typeId);
   console.info('typeId:' + typeObj.typeId);
   console.info('belongingToTypes:' + typeObj.belongingToTypes);
   console.info('description:' + typeObj.description);
   console.info('filenameExtensions:' + typeObj.filenameExtensions);
   console.info('mimeTypes:' + typeObj.mimeTypes);
   ```

>对于通过MIME获取对应扩展名，用typeObj.filenameExtensions；对于通过文件扩展名获取对应MIME，用通用属性中的typeObj.mimeTypes

### 数据持久化

持久化有三种使用场景，存配置文件，存非关系型数据，存关系型数据

#### 用户首选项持久化

用来存应用的设置文件，以键值对方式存取。存取高效快速，但量少

使用方法如下：

1. 导入模块

   ```ts
   import { preferences } from '@kit.ArkData';
   ```

2. 获取Preferences实例，它是专门用来管理这个存储的，需要传入配置文件的文件名

   ```ts
   let options: preferences.Options = { name: 'myStore' };
   let dataPreferences: preferences.Preferences | null = preferences.getPreferencesSync(this.context, options);
   ```

3. 判断里面是否有已经有某个键先，然后再写入，带Sync都是异步进行的

   ```ts
   if (dataPreferences.hasSync('startup')) {
     console.info("已有这个配置了");
   } else {
     dataPreferences.putSync('startup', 'auto');
     // 当字符串有特殊字符时，需要将字符串转为Uint8Array类型再存储
     let uInt8Array1 = new util.TextEncoder().encodeInto("~！@#￥%……&*（）——+？");
     dataPreferences.putSync('uInt8', uInt8Array1);
   }
   ```

4. 读取数据

   ```ts
   // 第一个是键，第二个获取到空值的时候的默认值
   let val = dataPreferences.getSync('startup', 'default');
   // 当获取的值为带有特殊字符的字符串时，需要将获取到的Uint8Array转换为字符串
   let uInt8Array2 : preferences.ValueType = dataPreferences.getSync('uInt8', new Uint8Array(0));
   let textDecoder = util.TextDecoder.create('utf-8');
   val = textDecoder.decodeToString(uInt8Array2 as Uint8Array);
   ```

5. 上面都是在内存进行操作，用flush把它写到磁盘文件里

   ```ts
   dataPreferences.flush((err: BusinessError) => {
     if (err) {
       console.error(`写入磁盘失败. Code:${err.code}, message:${err.message}`);
       return;
     }
   })
   ```

> flush到磁盘中的时候，会触发observer方法，可以用它来处理一些数据变化时候的逻辑

#### 键值型数据库持久化

这种存储方式类似于Redis，存一些键值型但并不属于设置的数据，不常用到因此请参考文档

#### 关系型数据库持久化

这种存储方式用的SQLite组件来实现的，基本和mysql差不多但是是本地的数据库

用法如下：

1. 定义RdbStore设置

   ```ts
   const STORE_CONFIG :relationalStore.StoreConfig= {
     name: 'RdbTest.db', // 数据库文件名
     securityLevel: relationalStore.SecurityLevel.S1, // 数据库安全级别
     encrypt: false, // 可选参数，指定数据库是否加密，默认不加密
     customDir: 'customDir/subCustomDir', // 可选参数，数据库自定义路径。数据库将在如下的目录结构中被创建：context.databaseDir + '/rdb/' + customDir，其中context.databaseDir是应用沙箱对应的路径，'/rdb/'表示创建的是关系型数据库，customDir表示自定义的路径。当此参数不填时，默认在本应用沙箱目录下创建RdbStore实例。
     isReadOnly: false // 可选参数，指定数据库是否以只读方式打开。该参数默认为false，表示数据库可读可写。该参数为true时，只允许从数据库读取数据，不允许对数据库进行写操作，否则会返回错误码801。
   };
   ```

2. 获取RdbStore

   ```ts
   // 需要上下文的原因是不同的应用会有不同的数据库，用context来区别
   let store: relationalStore.RdbStore | undefined = relationalStore.getRdbStore(this.context, STORE_CONFIG, (err, store) => {
     if (err) {
       console.error(`不能获取到RdbStore. Code:${err.code}, message:${err.message}`);
       return;
     }
   }
   ```

3. 准备数据，必须使用relationalStore.ValuesBucket来构造

   ```ts
   const valueBucket: relationalStore.ValuesBucket = {
     "NAME": 'Lisa',
     "AGE": 18,
     "SALARY": 100.5,
     "CODES": new Uint8Array([1, 2, 3, 4, 5]),
     "IDENTITY": BigInt('15822401018187971961171')
   };
   ```

4. 开启事务，插入更新删除成功即已写入磁盘，不需要flush

   ```ts
   // 判断数据库不能没有
   if (store !== undefined) {
       // 有的话把它转换成单独的RdbStore类型，因为之前类型是relationalStore.RdbStore | undefined，现在不能是undefined
       let rdbStore = store as relationalStore.RdbStore;
       try {
           // 开始事务
           rdbStore.beginTransaction();
           // 执行数据库操作
           // 插入更新删除都有，第一个参数是个RdbPredicates类型的变量
           rdbStore.insert('EMPLOYEE', valueBucket);
           rdbStore.update('EMPLOYEE', valueBucket);
           rdbStore.delete('EMPLOYEE', valueBucket);
           // 如果没有异常，提交事务
           rdbStore.commit();
       } catch (RdbException e) {
           // 如果出现异常，回滚事务
           rdbStore.rollback();
           e.printStackTrace();
       }
   }
   ```

5. 查询数据

   ```ts
   rdbStore.query('EMPLOYEE', ['ID', 'NAME', 'AGE', 'SALARY', 'IDENTITY'],
                  (err: BusinessError, resultSet) => {
       if (err) {
         console.error(`获取数据失败. Code:${err.code}, message:${err.message}`);
         return;
       }
       console.info(`列名为: ${resultSet.columnNames}, 有: ${resultSet.columnCount}列`);
       // resultSet是一个数据集合的游标，默认指向第-1个记录，有效的数据从0开始。
       while (resultSet.goToNextRow()) {
         const id = resultSet.getLong(resultSet.getColumnIndex('ID'));
         const name = resultSet.getString(resultSet.getColumnIndex('NAME'));
         const age = resultSet.getLong(resultSet.getColumnIndex('AGE'));
         const salary = resultSet.getDouble(resultSet.getColumnIndex('SALARY'));
         const identity = resultSet.getValue(resultSet.getColumnIndex('IDENTITY'));
         console.info(`id=${id}, name=${name}, age=${age}, salary=${salary}, identity=${identity}`);
       }
       // 释放数据集的内存
       resultSet.close();
     });
   ```

> 如果遇到抛出错误码14800011，是数据库文件损坏。请记得定时用rdbStore.backup来备份数据库，具体用法见文档

### 跨设备数据同步

即分布式，要保证数据库一致

分布式数据库有三种一致等级：

- 强一致性：是指某一设备成功增、删、改数据后，组网内任意设备可立即读取数据获得更新后的值
- 弱一致性：是指某一设备成功增、删、改数据后，组网内设备可能读取到本次更新后的数据，也可能读取不到，不能保证在多长时间后每个设备的数据一定是一致的
- 最终一致性：是指某一设备成功增、删、改数据后，组网内设备可能读取不到本次更新后的数据，但在某个时间窗口之后组网内设备的数据能够达到一致状态

因为移动终端设备的不常在线、以及无中心的特性，所以同应用跨设备数据同步不支持强一致性，**只支持最终一致性**
