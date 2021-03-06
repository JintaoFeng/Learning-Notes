make的编译思路很简单，如果源程序发生了改变，并需要重新构建程序或者其他输出文件时，make先查看哪些文件时间戳改变了，并按照要求重新构建这些文件，而不浪费时间重新构建其他文件。

在终端输入make命令就会调用make工具，make会在当前目录按照文件名顺序寻找Maefile文件，依次按照：GNUmakefile、makefile、Makefile。如果找到其中任何一个，就读取并按照其中的规则执行，否则报错。

### makefile规则

目标：依赖条件

（一个tab）命令

目标的时间必须晚于依赖条件的时间，否则，更新目标。

依赖条件如果不存在，找寻新的规则去产生依赖条件。

ALL：指定makefile的终极目标。

![](..\picture\微信截图_20200509114947.png)

![](..\picture\微信截图_20200509115011.png)

### 伪目标

如果一个目标名和当前目录下的一个文件名相同，则该目标对应的规则永远不会被执行，哪怕实际需要编译的文件已经被修该过。因此。Makefile引入一个新的目标-伪目标。伪目标是一个标签，这个目标只执行命令，不创建目标，还能避免目标与工作目录下的实际文件名冲突。写法如下：

```
.PHONY:标签
```

例如：

```
.PHONY:clean
clean:
	-rm -v a.out
```

在实际应用中，我们通常会写clean目标，用于清除编译产生的中间文件和可执行文件。

如果在rm命令前加了“-”，含义是如果这条命令执行失败，make将忽略这个错误，继续往下执行，如果不加"-",则make停止。一个工程，连续两次make clean后，第二次clean的时候，由于相关文件不存在，加了"-"的情况下，clean提示出错，但被忽略，而不加"-"则不忽略。 “-v”就是显示删除列表。

### 自定义变量

make支持在Makefile文件中定义变量。一般的定义makefile文件编写中，通常会为源文件、可执行文件以及编译参数分别定义一个变量，并予以赋值，在编译规则中则直接引用这些变量。

在用到变量的时候，通过美元符号($)和括号()一起来完成变量引用。

变量如果有多个值，可用空格隔开，如：

```
SRC=hello.c hello1.c hello2.c
```

除等号(=)赋值外，还可以使用追加符号(+=)进行追加：

```
SRC=hello.c
SRC+=hello1.c hello2.c
```

### 函数

makefile里也有系统定义好的函数可供我们调用，一般常用的函数有以下几个

```
src=$(wildcard ./*.c)
```

匹配当前工作目录下的所有.c文件。将文件名组成列表，赋值给变量src

```
obj=$(patsubst %.c,%.o,$(src))
```

将参数3中，包含参数1的部分，替换为参数2.

### 自动变量

自动变量不用定义，且会随着上下文的不同而发生改变。make的自动变量都是一些比较难记的符号，且都以美元符号$()开头。常用的make自动变量如表：

![](..\picture\微信截图_20200511194809.png)