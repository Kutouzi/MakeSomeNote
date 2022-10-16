## java笔记-->maven基础

### maven简介

传统项目依赖jar包都放在项目下的lib。

maven项目只在项目下放pom.xml配置依赖jar包的路径。它会寻找本地maven仓库下需求的jar包，如果没有就联网搜索下载来

### 指令

- mvn clean：清除target目录

- mvn comple：编译源码

- mvn test：编译源码+测试码

- mvn package：编译源码+测试码+打包成指定包

- mvn deploy：

生命周期从上到下从短到长

### 部署运行

maven在运行时会找它自己根目录lib来作为运行时依赖的jar包，pom文件里如果也有引用同名jar包就会造成编译和运行时不一致的冲突问题。解决方式是删掉lib里的或者在pom里包引用下加限定范围“<scope></scope>”
限定范围可以限定四个周期：编译时，测试时，运行时，全依赖

### 项目建立

在idea建立项目可选maven模板建立。一般项目不建议quickstart模板，直接建立就行。web项目随意。