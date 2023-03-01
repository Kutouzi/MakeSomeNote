## c++笔记-->cmake

Cmake是一个管理工具，它使用CmakeLists.txt脚本来生成Makefile文件，以此自动管理整个项目的预处理、编译、链接，同时具备跨平台功能。CmakeLists.txt里面用的语言是cmake自己的一种脚本语言

### 项目结构

cmake项目结构一般会是这样

- MyTest/
  - cmake-build-debug/
  - include/
  - lib/
  - SubTest/
    - CmakeLists.txt
    - xx.cpp
    - xx.h
  - CmakeLists.txt
  - xxx.cpp
  - xxx.h
  - main.cpp

其中lib目录下放置链接库（linux下的.a、.so和win下的.lib、.dll这种文件），include下放置头文件（.h、.hpp这种文件）

### 常用脚本命令

一些简单的

```cmake
#此项目需求最低cmake版本
cmake_mininum_required(VERSION 3.23)
#设置项目名
project(MyTest)
#设置子项目名称
add_subdirectory(SubTest)
#设置使用c++标准
set(CMAKE_CXX_STANDARD 20)
#设置链接标识，这里设置的是静态链接，即生成exe时使用静态链接库
set(CMAKE_EXE_LINKER_FLAGS "-static")
#设置生成文件类型，这里会生成对应平台的可执行文件，分别填的是项目名和入口函数文件
add_executable(MyTest main.cpp)
#设置生成文件类型，这里会生成链接库，上面的生成静态，下面的生成动态，分别填的是项目名和入口函数文件
add_library(MyTest main.cpp)
add_library(MyTest SHARED main.cpp)
#链接一个库到项目中，在链接前需要指定目录，分别填的是项目名和库名称(就是库文件的文件名)
target_link_libraries(MyTest MyLib)
#指定链接库目录，需要同时指定头文件目录才能用(如果有子项目，语句需要置于设置子项目前，否则不生效)
link_directories(${PROJECT_SOURCE_DIR}/lib)
#指定头文件目录(如果有子项目，语句需要置于设置子项目前，否则不生效)
link_directories(${PROJECT_SOURCE_DIR}/include)
```

file的使用，效果是批量匹配文件，作为一个变量使用。当有大量文件要编译时常用

```cmake
#GLOB表示非递归，只取一级目录；后面设定此file的变量名；用双引号写路径和正则表达式
file(GLOB TestFile
	"${PROJECT_SOURCE_DIR}/*.h"
	"${PROJECT_SOURCE_DIR}/*.cpp"
)
#然后当做变量使用就行，效果是
add_executable(MyTest ${TestFile})
```

include导入模块，如FetchContent模块使用要先导入

```cmake
include(FetchContent)
```

FetchContent的使用，可以做简单的包管理（复杂的可以用vcpkg+find_package指令），从仓库中获取并作为子项目存在

```cmake
#从上到下分别是仓库名、仓库地址、发布版本、是否不下载历史版本文件
FetchContent_Declare(
	googletest
	GIT_REPOSITORY https://github.com/google/googletest.git
	GTI_TAG release-1.25.0
	GIT_SHALLOW TRUE
)
FetchContent_MakeAvailable(googletest)

#作为子项目后还需要接入项目，最后填的是链接库名而不是仓库名
target_link_libraries(MyTest PRIVATE gtest_main)
```

### 常用环境变量

设置完项目名后会自动生成三个环境变量PROJECT_NAME,PROJECT_BINARY_DIR,PROJECT_SOURCE_DIR

分别对应了项目名，项目编译输出目录，项目CMakeLists所在目录

```cmake
${PROJECT_SOURCE_DIR}
${PROJECT_NAME}
${PROJECT_BINARY_DIR}
```

### 判断

```cmake
#如果Flag参数被定义且开启，那么就会进入if里
set(Flag ON)
if(Flag)

endif()
```

### 函数

```cmake
#定义
function(MyFunc num)
	
endfunction()

#调用
MyFunc(2)
```

### 项目资源权限

```cmake
#设置权限为私有，当此项目被作为子项目引用时，此项目下的lib和include资源不能被引用此项目的项目使用
#不写PRIVATE就默认是公有
target_link_libraries(MyTest PRIVATE ${PROJECT_SOURCE_DIR}/lib)
target_include_directories(MyTest PRIVATE ${PROJECT_SOURCE_DIR}/include)
```



