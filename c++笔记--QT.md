## c++笔记-->QT

全篇使用clion为主，qtcreator为辅来进行qml+qt6的开发

### 新建项目

在qtcreator新建普通的qtquick项目，然后打开在clion

转到clion"设置->构建执行部署->工具链"内，如下图配置一个新的配置。其中的cmake用qt自带的、clion自带的或自己下的都行，但工具集要选中qt的mingw

<img src="./picture/qt工具链设置.png #pic_center =100x100" alt="工具链设置" style="zoom: 80%;" />

然后转到下方的cmake设置，使用工具链使用上面设置的

<img src="./picture/qtcmake设置.png" alt="qtcmake设置" style="zoom:80%;" />

转到系统环境变量设置，添加以下四项，作为运行时使用（未添加会显示找不到qt组件的库文件）

<img src="./picture/qt系统环境变量.png" alt="qt系统环境变量" style="zoom:100%;" />

在项目根目录下的CMakeLists.txt中添加这几项，如果已有相同的，则不需要添加

```makefile
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_STANDARD 23) #这个表示使用c++23语言标准，可自行更改
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_PREFIX_PATH "D:/Environment/Qt/6.5.1/mingw_64/lib/cmake") #这个请改为自己的相应路径，作用是让cmake找到qt所有组件的.cmake文件，它们一般在lib/cmake路径下

qt_standard_project_setup()
```

### 打包运行

把项目切换成release并构建，在目录build/release目录下生成appXXXXX.exe文件

新建一个文件夹，将exe文件复制到文件夹内

命令行执行路径Qt\6.x.x\mingw_64\bin目录下对应平台的windeployqt6.exe

```shell
./windeployqt6.exe appXXXXX.exe
```

之后所有依赖项dll会被复制到该目录下

然后将Qt\6.x.x\mingw_64\qml内所有文件复制到该目录的\qml下

如果依旧不能打开就复制Qt\6.x.x\mingw_64\bin下所有Qt6开头的dll到该exe同级目录下

将该文件夹压缩即可发布
