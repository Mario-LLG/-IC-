[TOC]



# makefile



```makefile
say_hellow:
	回显 “Hello World”
Hellow World
```

```makefile
gcc hello.c -o hello #只能编译一个文件
```

- makefile 是一个c/c++  Linux 必须要使用得一个编译脚本

```makefile
ifconfig
cd 
ls
mkdir hellow  //创建hello文件夹

```

1. -E 预处理  .h .c -> .i  //包括宏定义的替换在预处理阶段

```makefile
gcc -E hellow.c -o hellow.i
```

1. -S 汇编  .i -> .s

```makefile
gcc -S hellow.i -o hellow.s
```

1. -c 编译  .s -> .o .obj

```makefile
gcc -C hellow.s -o hellow.o
```

1. -o 链接： .o -> .exe(windows) .  .elf(Linux)

```mackfile
gcc hellow.o -o hellow
```



==secureCRT==   ==Source Insight==编辑器

## 脚本编译

makefile  全新的脚本语言

### level one

- 1. 创建文档  Makefile #  
  2. 目标文件：依赖文件
  3. 第一个文件是最终目标
  4. 伪目标：.PHONY

### level two

1. 变量 = （替换）;  += （追加）； ：=（恒等于）  $()
2. 使用变量  $(变量名);

### level three

1. %.c %.o 任意.c 任意.o;

1. 隐含规则  %。从

### level four

1. 通配符  **\$^** :所有的依赖文件   **\$@**:所有的目标文件，  **\$<**:所有的依赖文件的第一个文件 **$?**：所有更新过的依赖文件
2. 支持三种通配符： ==“*”==，=="?"==, =="~"==.
   1. “\~/test”，这就表示当前用户的$HOME目录下的test目录, “\~hchen/test”则表示用户hchen的宿主目录下的test目录
   2. 

### level five

1.  source insight 
2. include  a.mk    #引入其他函数makefile
3.  文件搜索：**VPATH = src:../headers**
    1.  上面的定义指定两个目录，“src”和“../headers”，make会按照这个顺序进行搜索。目录由“冒号”分割。
    2.  - vpath <pattern><directories>       为符合模式的文件指定搜索目录
        - vpath <pattern>         清除符合模式的文件的搜索目录
        - vpath   清除所有已被设置好了的文件搜索目录
4.  - -M
    - 生成文件的依赖关系，同时也把一些标准库的头文件包含了进来。本质是告诉预处理器输出一个适合 make 的规则，用于描述各目标文件的依赖关系。对于每个源文件，预处理器输出 一个 make 规则，该规则的目标项 (target) 是源文件对应的目标文件名，依赖项 (dependency) 是源文件中 “#include” 引用的所有文件，生成的规则可以是单行，但如果太长，就用’'换行符续成多行。规则显示在标准输出，不产生预处理过的 C 程序。
    - [-MM](<https://blog.csdn.net/qq1452008/article/details/50855810>)
    - 生成文件的依赖关系，和 -M 类似，但不包含标准库的头文件
5.  [函数](<https://blog.csdn.net/afei__/article/details/82696682>)
6.  [CFLAGS](<https://www.cnblogs.com/taskiller/archive/2012/12/14/2817650.html>) 设置参数标志

| 后 缀 名  | 所对应的语言                                         |
| --------- | ---------------------------------------------------- |
| -S        | 只是编译不汇编，生成汇编代码                         |
| -E        | 只进行预编译，不做其他处理                           |
| -g        | 在可执行程序中包含标准调试信息                       |
| -o file   | 把输出文件输出到file里                               |
| -v        | 打印出编译器内部编译各过程的命令行信息和编译器的版本 |
| -I dir    | 在头文件的搜索路径列表中添加dir目录                  |
| -L dir    | 在库文件的搜索路径列表中添加dir目录                  |
| -static   | 链接静态库                                           |
| -llibrary | 连接名为library的库文件                              |

> 在include语句中，“<>”表示在标准路径中搜索头文件，““””表示在本目录中搜索。故在上例中，可把hello1.c的“#include”改为“#include “my.h””，就不需要加上“-I”选项了。

- [静态模式](<https://blog.csdn.net/u010210927/article/details/78125195>)

  SRCS =\$(wildcard $(DIR)/*.c) //提取DIR目录下所有.c文件，附带目录

  CSRCS = \$(notdir $(SRCS)) //提取所有文件，只包含文件名及后缀

  OBJS = \$(patsubst %.c,​\$(OBJDIR)/%.o,$(CSRCS)) //将所有CSRCS中的.c文件后缀改为.o，并增加目录OBJDIR

- 径代表未定的一部分

- ```
  = 是最基本的赋值
  := 是覆盖之前的值
  ?= 是如果没有被赋值过就赋予等号后面的值
  += 是添加等号后面的值
  ```

## code

```makefile
gcc -o hellowmake hellowmake.c hellofunc.c -I

#1
hellowmake:hellowmake.c hellofunc.c
	gcc -o hellomake hellmake.c hellofunc.c -I
#2
CC = GCC
CFLAGS = -I
hellowmake:hellomake.o hellofunc.o
	$(CC) -o hellowmake hellomake.o hellofunc.o
	
```







