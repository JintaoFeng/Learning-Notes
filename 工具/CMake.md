# CMake

CMake 是一个跨平台的构建系统生成工具。它使用平台无关的 CMake 清单文件CMakeLists.txt，指定工程的构建过程；源码树的每个路径下都有这个文件。CMake 产生一个适用于具体平台的构建系统，用户使用这个系统构建自己的工程。

## 选择编译器以及设置编译器

在实际的项目平台中可能会安装有多个版本的编译器，同时由于不同的功能可能会需要设置不同的编译参数。

### 选择编译器

#### 使用命令行

在编译时可以通过参数直接选择指定的编译器的完整路径，比如我的gcc8.3.0安装在/usr/local/gcc/bin路径下，在编译时输入：

```
cmake .. -DCMAKE_CXX_COMPILER=/usr/local/gcc/bin/g++
```

就会在编译时选定gcc-8.3.0

#### 在配置文件中指定

在CMakeLists.txt文件中添加：

```
set(CMAKE_C_COMPILER "/usr/local/gcc/bin/gcc")
set(CMAKE_CXX_COMPILER "/usr/local/gcc/bin/g++")
```

直接修改全局变量`CMAKE_C_COMPILER`和`CMAKE_CXX_COMPILER`为指定的编译器路径

**CMAKE_C_COMPILER**
原本是保存环境变量"CC"值的变量，而CC是编译C语言的首选编译器，但是在新的CMP0054策略中如果设置的CMAKE_C_COMPILER则会忽略CC的值。

**CMAKE_CXX_COMPILER**
与CMAKE_C_COMPILER类似，不过这个变量对应的环境变量是CXX，是编译C++语言的编译器。

### 配置编译参数

假设我使用g++编译器,添加"-std=c++11", “-Wall"和”-Werror"等参数为例。

#### 使用`add_compile_options`命令

通过在CMakeLists.txt文件中添加`add_compile_options`命令可以起到添加参数的作用，如：

```
add_compile_options(-std=c++11 -Wall -Werror)
```

但是这个命令是针对所有类型编译器的，也就是说这里添加的选项会在所有的编译器中运用，比如-std=c++11是针对C++的编译器参数，也会被运用在C语言编译器中，虽然不一定会报错但是终究体验感不好。而且此命令添加的参数是递归的，即在多层目录结构中，根文件下设置选项后，在所有的子目录编译时都会运用。

#### 通过设置CMAKE_CXX_FLAGS来配置

CMAKE_CXX_FLAGS是针对C++编译器的参数选项，默认保存环境变量CXX_FLAGS的内容，但是如果直接修改这个参数值，那么系统会忽略原CXX_FLAGS的内容。设置方式如下：

```
set(CMAKE_C_FLAGS  -std=c++11 -Wall -Werror)
```

#### 两种方式比较

|       add_compile_options        |   CMAKE_CXX_FLAGS    |
| :------------------------------: | :------------------: |
|         对所有编译器有效         |   只针对C++编译器    |
| 作用域是全局，对所有编译文件有效 | 作用域是单个编译文件 |
|                                  |                      |

### 命令解析

#### add_compile_options

将编译器选项添加到当前及子目录的源文件的编译中。

```
add_compile_options(<options> ...)
```

option ：编译选项，注意对不同的编译器，支持的选项可能不一样。

#### add_compile_definitions

将预编译参数添加到源文件的编译中，对下级子目录同样有效。

```
add_compile_definitions(<definition> ...)
```

预编译命令会添加到COMPILE_DEFINITIONS目录属性中。

#### CMAKE_FLAGS变量

这里用到的CMAKE_CXX_FLAGS变量只针对C++编译器的选项，对于其他编程语言，只要替换部分就可以。

CMAKE_C_FLAGS:C语言编译选项，对应于环境变量CFLAGS.

CMAKE_CXX_FLAGS:C++编译器选项，对应于环境变量CXXFLAGS.

CMAKE_CUDA_FLAGS:CUDA语言编译器选项，对应于环境变量CUDAFLAGS.