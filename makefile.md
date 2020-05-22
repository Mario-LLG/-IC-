[TOC]

# [Makefile](<https://blog.csdn.net/weixin_38391755/article/details/80380786>)

## 1 Background

- RedHat Linux 8.0，make的版本是3.80,遵循于IEEE 1003.2-1992 标准的（POSIX.2）。

- 默认的编译器是UNIX下的GCC和CC

### 1.2 程序的编译和链接

- 是C、C++、还是pas，首先要把源文件编译成中间代码文件，在Windows下也就是 .obj 文件，UNIX下是 .o 文件，即 Object File，这个动作叫做编译（compile）。然后再把大量的Object File合成执行文件，这个动作叫作链接（link）。

- 首先，我们用一个示例来说明Makefile的书写规则。以便给大家一个感兴认识。这个示例来源于GNU的make使用手册，在这个示例中，我们的工程有8个C文件，和3个头文件，我们要写一个Makefile来告诉make命令如何编译和链接这几个文件。我们的规则是：
              1.如果这个工程没有编译过，那么我们的所有C文件都要编译并被链接。

  ​            2.如果这个工程的某几个C文件被修改，那么我们只编译被修改的C文件，并链接目标程序。

  ​            3.如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的C文件，并链接目标序。

```makefile
edit:main.o kbd.o command.o display.o\
	insert.o searh.o files.o utils.o
	cc -o edit main.o kbd.o command.o display.o\
    	insert.o search.o files.o utils.o
	
	mian.o :main.c defs.h 
		cc -c main.c 
	kbd.o:kbd.c defs.h command.h
		cc -c kbd.c
	command.o :command.c defs.h command.h
		cc -c command.c
	display.o:display.c defs.h buffer.h
		cc -c display.c
	search.o:search.cdefs.h buffer.h
		cc -c search.c
	files.o:files.c defs.h buffer.h command.h
		cc -c files.c
	utiles.o : utils.c defs.h
		cc -c utils.c
	clean:
		rm edit main.o kbd.o command.o display.o\
			insert.o search.o files.o utils.o
	
```

- 执行文件edit
- 中间目标文件（*.o）
- 依赖文件（prerequisites）   .c 文件和 .h文件
- 每一个 .o 文件都有一组依赖文件
- 这些 .o 文件又是执行文件 edit 的依赖文件
- 依赖关系的实质上就是说明了目标文件是由哪些文件生成的

### 1.3 make是如何工作的

1.   make会在当前目录下找名字叫“Makefile”或“makefile”的文件。
2.   如果找到，它会找文件中的第一个目标文件（target），在上面的例子中，他会找到“edit”这个文件，并把这个文件作为最终的目标文件。
3.   如果edit文件不存在，或是edit所依赖的后面的 .o 文件的文件修改时间要比edit这个文件新，那么，他就会执行后面所定义的命令来生成edit这个文件。
4.   如果edit所依赖的.o文件也存在，那么make会在当前文件中找目标为.o文件的依赖性，如果找到则再根据那一个规则生成.o文件。（这有点像一个堆栈的过程）
5.   当然，你的C文件和H文件是存在的啦，于是make会生成 .o 文件，然后再用 .o 文件声明make的终极任务，也就是执行文件edit了。

### 1.4 makefile中使用变量

``` makefile
edit:main.o kbd.o command.o display.o\
	insert.o searh.o files.o utils.o
	cc -o edit main.o kbd.o command.o display.o\
    	insert.o search.o files.o utils.o
```

- 使用宏定义代替重复文件=="$(objects)"==

``` makefile
#pro
objects = main.o kbd.o command.o display.o\
			insert.osearch.o files.o utils.o
	edit : $(objects)
		cc -0 edit %(objects)
	main.0: main.c defs.h
		cc -c main.c
	kbd.o: kbd.c defs.h command.h
		cc -c kbd.c
	command.o: command.c defs.h command.h
		cc -c command.o
	display.o: display.c defs.h buffer.h
		cc -c display.c
	insert.o: insert.c defs.h buffer.h
		cc -c insert.c
	search.o: search.c defs.h buffer.h
		cc -c search.c
	files.o:files.c defs.h bufferh command.h
		cc -c search.c
	utils.o: utils.c defs.h
		cc -c utils.c
	clean:
		rm edit$(object)
```

- 于是如果有新的 .o 文件加入，我们只需简单地修改一下 objects 变量就可以了。
- 关于变量更多的话题，我会在后续给你一一道来。

### 1.5 让make自动推导

- GNU的make很强大，它可以自动推导文件以及文件依赖关系后面的命令，于是我们就没必要去在每一个[.o]文件后都写上类似的命令，因为，我们的make会自动识别，并自己推导命令。
- 只要make看到一个[.o]文件，它就会自动的把[.c]文件加在依赖关系中，如果make找到一个whatever.o，那么whatever.c，就会是whatever.o的依赖文件。并且 cc -c whatever.c 也会被推导出来，于是，我们的makefile再也不用写得这么复杂。我们的是新的makefile又出炉了。

```makefile
#progress  也就是make的“隐晦规则”
objects = main.o kbd.o command.o display.o\
		insert.o search.o files.o utils.o
edit: $(objects)
	cc -o edit$(objects)
main.o: defs.h
kbd.o: defs.h command.h
command.o: defs.h command.h
display.o: defs.h buffer.h
insert.o: defs.h buffer.h
search.o: defs.h buffer.h
files.o: defs.h buffer.h command.h
utils.o: defs.h

.PHONY: clean  #“.PHONY”表示，clean是个伪目标文件。
clean:
	rm edit$(objects)
	
```

### 1.6 另类风格的makefile

- 即然我们的make可以自动推导命令，那么我看到那堆[.o]和[.h]的依赖就有点不爽，那么多的重复的[.h]，能不能把其收拢起来，好吧，没有问题，这个对于make来说很容易，谁叫它提供了自动推导命令和文件的功能呢？来看看最新风格的makefile吧。

  ```makefile
  objects = main.o kbd.o command.o display.o\
  		insert.o search.o files.o utils.o
  edit:$(objects)
  	cc -o edit$(objects)
  
  $(objects):defs.h
  kbd.o cmmand.o files.o:command.h
  display.o insert.o search.o files.o :buffer.h
  
  .PHONY:clean
  clean:
  	rm edit$(objects)
  ```

- 这种风格，让我们的makefile变得很简单，但我们的文件依赖关系就显得有点凌乱了。鱼和熊掌不可兼得。还看你的喜好了。我是不喜欢这种风格的，一是文件的依赖关系看不清楚，二是如果文件一多，要加入几个新的.o文件，那就理不清楚了。

### 1.7 清空目标文件规则

- 每个Makefile中都应该写一个清空目标文件（.o和执行文件）的规则，这不仅便于重编译，也很利于保持文件的清洁。这是一个“修养”（呵呵，还记得我的《编程修养》吗）。一般的风格都是：

  ```makefile
  clean:
  	re edit$(objects)
  ```

- 更为稳健的做法是：

```makefile
.PHONY:clean
clean:
	-rm edit$(objects)
```

- 前面说过，.PHONY意思表示clean是一个“伪目标”，。而在rm命令前面加了一个小减号的意思就是，也许某些文件出现问题，但不要管，继续做后面的事。当然，clean的规则不要放在文件的开头，不然，这就会变成make的默认目标，相信谁也不愿意这样。不成文的规矩是——“clean从来都是放在文件的最后”。

## 2 Makefile 总述

### 2.1 Makefiles 里有什么

- Makefile里主要包含了**==五个东西==：显式规则、隐晦规则、变量定义、文件指示和注释。**

1. 显式规则。显式规则说明了，如何生成一个或多的的目标文件。这是由Makefile的书写者明显指出，要生成的文件，文件的依赖文件，生成的命令。
2. 隐晦规则。由于我们的make有自动推导的功能，所以隐晦的规则可以让我们比较粗糙地简略地书写Makefile，这是由make所支持的。
3. 变量的定义。在Makefile中我们要定义一系列的变量，变量一般都是字符串，这个有点你C语言中的宏，当Makefile被执行时，其中的变量都会被扩展到相应的引用位置上。
4. 文件指示。其包括了三个部分，一个是在一个Makefile中引用另一个Makefile，就像C语言中的include一样；另一个是指根据某些情况指定Makefile中的有效部分，就像C语言中的预编译#if一样；还有就是定义一个多行的命令。有关这一部分的内容，我会在后续的部分中讲述。
5.  注释。Makefile中只有行注释，和UNIX的Shell脚本一样，其注释是用“#”字符，这个就像C/C++中的“//”一样。如果你要在你的Makefile中使用“#”字符，可以用反斜框进行转义，如：“\#”。

- 还值得一提的是，在Makefile中的命令，必须要以[Tab]键开始。

### 2.2 Makefile的文件名

- 默认的情况下，make命令会在当前目录下按顺序找寻文件名**“GNUmakefile”、“makefile”、“Makefile”**的文件，找到了解释这个文件。在这三个文件名中，最好使用“Makefile”这个文件名，因为，这个文件名第一个字符为大写，这样有一种显目的感觉。最好不要用“GNUmakefile”，这个文件是GNU的make识别的。有另外一些make只对全小写的“makefile”文件名敏感，大多数的make都支持**makefile”**和**“Makefile”**这两种默认文件名。

- 当然，你可以使用别的文件名来书写Makefile，比如：“Make.Linux”，“Make.Solaris”，“Make.AIX”等，如果要**指定特定的Makefile，你可以使用make的“-f”和“--file”参数**，如：make -f Make.Linux或make --file Make.AIX。

### 2.3 引用其他的Makefile

- Makefile使用include关键字可以把别的Makefile包含进来，这很像C语言的#include，被包含的文件会原模原样的放在当前文件的包含位置。include的语法是：

```makefile 
include<filename> #filename可以是当前操作系统Shell的文件模式（可以保含路径和通配符）
```

- **在include前面可以有一些空字符，但是绝不能是[Tab]键开始。include和可以用一个或多个空格隔开。**

- **eg**: 举个例子，你有这样几个Makefile：a.mk、b.mk、c.mk，还有一个文件叫foo.make，以及一个变量$(bar)，其包含了e.mk和f.mk，那么，下面的语句：

```makefile
include foo.make *.mk$(bar) #等价于 include foo.make a.mk b.mk c.mk e.mk f.mk
```

- make命令开始时，会把找寻include所指出的其它Makefile，并把其内容安置在当前的位置。就好像C/C++的#include指令一样。如果文件都没有指定绝对路径或是相对路径的话，make会在当前目录下首先寻找，如果当前目录下没有找到，那么，make还会在下面的几个目录下找：

1. 如果make执行时，有“-I”或“--include-dir”参数，那么make就会在这个参数所指定的目录下去寻找。
2. 如果目录/include（一般是：/usr/local/bin或/usr/include）存在的话，make也会去找。

- 如果有文件没有找到的话，make会生成一条警告信息，但不会马上出现致命错误。它会继续载入其它的文件，一旦完成makefile的读取，make会再重试这些没有找到，或是不能读取的文件，如果还是不行，make才会出现一条致命信息。如果你想让make不理那些无法读取的文件，而继续执行，你可以在include前加一个减号“-”。如：

```makefile
-include<filename>
```

- 其表示，无论include过程中出现什么错误，都不要报错继续执行。和其它版本make兼容的相关命令是sinclude，其作用和这一个是一样的。

###  2.4 环境变量

- 如果你的当前环境中定义了环境变量MAKEFILES，那么，make会把这个变量中的值做一个类似于include的动作。这个变量中的值是其它的Makefile，用空格分隔。只是，它和include不同的是，从这个环境变中引入的Makefile的“目标”不会起作用，如果环境变量中定义的文件发现错误，make也会不理。
- **但是在这里我还是建议不要使用这个环境变量，因为只要这个变量一被定义，那么当你使用make时，所有的Makefile都会受到它的影响，这绝不是你想看到的。在这里提这个事，只是为了告诉大家，也许有时候你的Makefile出现了怪事，那么你可以看看当前环境中有没有定义这个变量。**

### 2.5 make的工作方式

1. 读入所有的Makefile。

2. 读入被include的其它Makefile。

3. 初始化文件中的变量。

4. 推导隐晦规则，并分析所有规则。

5. 为所有的目标文件创建依赖关系链。

6. 根据依赖关系，决定哪些目标要重新生成。

7. 执行生成命令。

## 3 Makefile书写规则

- 规则包含两个部分，一个是==**依赖关系**==，一个是==**生成目标的方法**==。

- **在Makefile中，规则的顺序是很重要的**，因为，**Makefile中只应该有一个最终目标**，其它的目标都是被这个目标所连带出来的，所以一定要让make知道你的最终目标是什么。一般来说，定义在Makefile中的目标可能会有很多，但是第一条规则中的目标将被确立为最终的目标。如果第一条规则中的目标有很多个，那么，第一个目标会成为最终的目标。make所完成的也就是这个目标。

### 3.1规则举例

```makefile
foo.o:foo.c defs.h  #foo模块
	cc -c -g foo.c
```

- 看到这个例子，各位应该不是很陌生了，前面也已说过，foo.o是我们的目标，foo.c和defs.h是目标所依赖的源文件，而只有一个命令“cc -c -g foo.c”（以Tab键开头）。这个规则告诉我们两件事：

1.  文件的依赖关系，foo.o依赖于foo.c和defs.h的文件，如果foo.c和defs.h的文件日期要比foo.o文件日期要新，或是foo.o不存在，那么依赖关系发生。

2.  如果生成（或更新）foo.o文件。也就是那个cc命令，其说明了，如何生成foo.o这个文件。（当然foo.c文件include了defs.h文件）

### 3.2 规则的语法

``` makefile
targets:prerequisites
	command
	
	...
targets:prerequisites;command
	command
	...
```

- targets是文件名，以空格分开，可以使用通配符。一般来说，我们的目标基本上是一个文件，但也有可能是多个文件。**command是命令行，如果其不与“target:prerequisites”在一行，那么，必须以[Tab键]开头**，如果和prerequisites在一行，那么可以用分号做为分隔。（见上）
- prerequisites也就是目标所依赖的文件（或依赖目标）。如果其中的某个文件要比目标文件要新，那么，目标就被认为是“过时的”，被认为是需要重生成的。这个在前面已经讲过了。
- 如果命令太长，你可以使用反斜框（‘\’）作为换行符。make对一行上有多少个字符没有限制。规则告诉make两件事，文件的依赖关系和如何成成目标文件。一般来说，make会以UNIX的标准Shell，也就是/bin/sh来执行命令。

### 3.3 在规则中使用通配符

-   如果我们想定义一系列比较类似的文件，我们很自然地就想起使用通配符。make支持**三各通配**符：**“*”，“?”和“[...]**”。这是和Unix的B-Shell是相同的。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         

==**~**==  

- 波浪号（“**~**”）字符在文件名中也有比较特殊的用途。如果是“**~/test**”，这就表示当前用户的**$HOME**目录下的test目录。而“**~hchen/test**”则表示**用户hchen**的宿主目录下的test目录。（这些都是Unix下的小知识了，make也支持）而**在Windows或是MS-DOS下，用户没有宿主目录，那么波浪号所指的目录则根据环境变量“HOME”而定**。

==*==

- 通配符代替了你一系列的文件，如“***.c**”表示所以后缀为**c**的文件。一个需要我们注意的是，如果我们的文件名中有通配符，如：“**\***”，那么可以用转义字符“**\\**”，如“**\\***”来表示真实的“*”字符，而不是任意长度的字符串。

eg:

```makefile
clean:
	rm -f *.o
```

```makefile
print: *.c
	lpr -p $?
	touch print
	#上面这个例子说明了通配符也可以在我们的规则中，
	#目标print依赖于所有的[.c]文件。其中的“$?”是一个自动化变量，我会在后面给你讲述。
```

```makefile
objects = *.o
#这个例子表示了通配符同样可以存在于变量中。并不是说[*.o]会展开,
#不！objects的值就是“*.o”。Makefile中的变量其实就是C/C++中的宏。
#如果你要让通配符在变量中展开，也就是让objects的值是所有[.o]的文件名的集合，
#那么，你可以这样：
```

```makefile
objects := $(wildcard *.o)
#objects的值是所有[.o]的文件名的集合
```

### 3.4 文件搜寻

-  在一些大的工程中，有大量的源文件，我们通常的做法是把这许多的源文件分类，并存放在不同的目录中。所以，当make需要去找寻文件的依赖关系时，你可以在文件前加上路径，但最好的方法是把一个路径告诉make，让make在自动去找

- Makefile文件中的特殊变量**“VPATH”**就是完成这个功能的，如果没有指明这个变量，make只会在当前的目录中去找寻依赖文件和目标文件。如果定义了这个变量，那么，make就会在当当前目录找不到的情况下，到所指定的目录中去找寻文件了。

```makefile
VPATH = src:.../headers
```

- 上面的的定义指定两个目录，**“src”和“../headers”**，make会按照这个顺序进行搜索。目录由“冒号”分隔。（当然，**当前目录永远是最高优先搜索**的地方）

- 另一个设置文件搜索路径的方法是使用make的“vpath”关键字（注意，它是全小写的），这不是变量，这是一个make的关键字，这和上面提到的那个VPATH变量很类似，但是它更为灵活。它可以指定不同的文件在不同的搜索目录中。这是一个很灵活的功能。它的使用方法有三种：

```makefile
vpath < pattern> < directories>    #为符合模式< pattern>的文件指定搜索目录<directories>。
vpath < pattern>                   #清除符合模式< pattern>的文件的搜索目录。
vpath                              #清除所有已被设置好了的文件搜索目录。
```

- vapth使用方法中的< pattern>需要包含“%”字符。“%”的意思是**匹配零或若干字符**，例如，“%.h”表示所有以“.h”结尾的文件。< pattern>指定了要搜索的文件集，而< directories>则指定了的文件集的搜索的目录。例如：

```makefile
vpath %.h ../headers   # “%.h”表示所有以“.h”结尾的文件
                       # 在headers目录下搜寻以 .h 结尾的文件
```

- 该语句表示，要求make在**“../headers”目录下**搜索所有以“.h”结尾的文件。（如果某文件在当前目录没有找到的话）

- 我们可以连续地使用vpath语句，以指定不同搜索策略。如果连续的vpath语句中出现了相同的< pattern>，或是被重复了的< pattern>，那么，make会按照vpath语句的先后顺序来执行搜索。如：

```makefile
vpath %.c foo
vpath % blish
vpath %.c bar
# 其表示“.c”结尾的文件，先在“foo”目录，然后是“blish”，最后是“bar”目录。
```

```makefile
vpath %.c foo;bar
vpath % blish
#表示“.c”结尾的文件，先在“foo”目录，然后是“bar”目录，最后才是“blish”目录。
```

### 3.5 伪目标

- 最早先的一个例子中，我们提到过一个“clean”的目标，这是一个“伪目标”，

```makefile
clean:
	rm *.o temp
```

- 正像我们前面例子中的“clean”一样，即然我们生成了许多文件编译文件，我们也应该提供一个清除它们的“目标”以备完整地重编译而用。 （以“make clean”来使用该目标）
- 因为，我们并不生成“clean”这个文件。“伪目标”并不是一个文件，只是一个标签，由于“伪目标”不是文件，所以make无法生成它的依赖关系和决定它是否要执行。我们只有通过显示地指明这个“目标”才能让其生效。当然，“伪目标”的取名不能和文件名重名，不然其就失去了“伪目标”的意义了。
- 当然，为了**避免和文件重名**的这种情况，我们可以使用一个**特殊的标记“.PHONY”来显示**地指明一个目标是“伪目标”，向make说明，不管是**否有这个文件，这个目标就是“伪目标**”。

```makefile
.PHONY:clean
```

- 只要有这个声明，不管是否有“clean”文件，要运行“clean”这个目标，只有“make clean”这样。于是整个过程可以这样写：

```makefile
.PHONY:clean
clean:
	rm *.o temp
```

- 伪目标一般没有依赖的文件。但是，我们也可以为伪目标指定所依赖的文件。伪目标同样**可以作为“默认目标”，**只要将其放在第一个。一个示例就是，如果你的**Makefile需要一口气生成若干个可执行文件**，但你只想简单地敲一个make完事，并且，所有的**目标文件都写在一个Makefile中**，那么你可以**使用“伪目标”这个**特性：

```makefile
all:prog1 prog2 prog3
.PHONY:all

prog1:prog1.o utils.o
	cc -o prog1 prog1.o utils.o
prog2:prog2.o
	cc -o prog2 prog2.o
prog3:prog3.o sort.o utils.o
	cc -o prog3 prog3.o sort.o utils.o
```

- 我们知道，Makefile中的第一个目标会被作为其默认目标。我们声明了**一个“all”的伪目标**，其依赖于其它三个目标。由于**伪目标的特性是，总是被执行的，所以其依赖的那三个目标就总是不如“all”这个目标新**。所以，其它三个目标的规则总是会被决议。也就达到了我们一口气生成多个目标的目的。**“.PHONY : all”声明了“all”这个目标为“伪目标”。**
- 随便提一句，从上面的例子我们可以看出，**目标也可以成为依赖**。所以，**伪目标同样也可成为依赖**。看下面的例子：

```makefile
.PHONY: cleanall cleanobj cleandiff
cleanall:cleanobj cleandiff
	rm program
cleanobj:
	rm *.o
cleandiff:
	rm *.diff
```

- "makeclean" 将要清除所有要被清除的文件，“cleanobj” 和"cleandiff"这两个伪目标有点像“子程序”的意思，我们可以输入“makecleanall”和“make cleanobj” 和“makecleandiff”命令来达到清除不同种类文件的目的

### 3.6 多目标

- Makefile的规则中的目标可以不止一个，其支持多目标，有可能我们的多个目标同时依赖于一个文件，并且其生**成的命令大体类似**。于是我们就能把其合并起来。当然，多个目标的生成规则的执行命令是同一个，这可能会可我们带来麻烦，不过好在我们的可以使用一个**自动化变量“$@”**（关于自动化变量，将在后面讲述），这个变量表示着目前规则中所有的目标的集合，这样说可能很抽象，还是看一个例子吧。

```makefile
bigoutput littleoutput:text.g
	generate text.g -$(subst output, $@)>$@
#其中-$(subst output,,$@) 中的“$”表示执行一个Makefile的函数，函数名为subst,后面的为参数。关于函数，将在后面讲述。这里的这个函数是截取字符串的意思，“$@”表示目标的集合，就像一个数字，“$@”依次取出目标，并执与命令
```

- 等价于

```makefile
bigoutput:text.g
	generate text.g -big > bigoutput
littleoutput:text.g
	generate text.g -little > littleoutput
```

### 3.7 静态模式

- 静态模式可以更容易定义多目标的规则，可以让规则变的更加有弹性和灵活。语法如下：

```makefile
<targets...>:<target-patten>:<prereq-patterns...>
	<commands>
```

- targets定义了一系列的目标文件，可以有通配符。是一个目标的集合。
- target-parrtern是指明了targets的模式，即**目标集模式**。
- prereq-parrtern是目标的**依赖模式**，它对target-parrtern形成的模式再进行一次依赖目标的定义。

- 这样描述这三个东西，可能还是没有说清楚，还是举个例子来说明一下吧。如果我们的<target-parrtern>定义成“%.o”，意思是我们的集合中都是以“.o”结尾的，而如果我们的<prereq-parrterns>定义成“%.c”，意思是对<target-parrtern>所形成的目标集进行二次定义，其计算方法是，取<target-parrtern>模式中的“%”（也就是去掉了[.o]这个结尾），并为其加上[.c]这个结尾，形成的新集合。

- 所以，我们的“目标模式”或是“依赖模式”中都应该有“%”这个字符，如果你的文件名中有“%”那么你可以使用反斜杠“\”进行转义，来标明真实的“%”字符。

eg:

```makefile
objects = foo.o bar.o

all:$(objects)

$(objects):%.o:%.c
	$(CC) -c $(CFLAGS) $< -o $@
```

- 上面的例子中，指明了我们的目标从$object中获取，“%.o”表明要所有以“.o”结尾的目标，也就是“foo.o bar.o”，也就是变量$object集合的模式，而依赖模式“%.c”则取模式“%.o”的“%”，也就是“foobar”，并为其加下“.c”的后缀，于是，我们的依赖目标就是“foo.cbar.c”。而命令中的“$<”和“$@”则是自动化变量，“$<”表示所有的依赖目标集（也就是“foo.c bar.c”），“$@”表示目标集（也褪恰癴oo.o bar.o”）。于是，上面的规则展开后等价于下面的规则：

```makefile
foo.o:foo.c	
	$(CC) -c $(CFLAGS) foo.c -o foo.o
bar.o:bar.c
	$(CC) -c $(CFLAGS) bar.c -o bar.o
```

- 试想，如果我们的“%.o”有几百个，那种我们只要用这种很简单的“静态模式规则”就可以写完一堆规则，实在是太有效率了。“静态模式规则”的用法很灵活，如果用得好，那会一个很强大的功能。再看一个例子：

```makefile
files = foo.elc bar.o lose.o

$(filter %.o $(files)): %.o: %.c
	$(CC) -c $(CFLAGS) $< -o $@
$(filter %.elc, $(files)):%.elc: %.el
	emacs -f batch -byte-comple $<
```

\$(filter%.o,$(files))表示调用Makefile的filter函数，过滤“\$filter”,只要其中模式为“%.o”的内容。其的它内容，我就不用多说了吧。这个例字展示了Makefile中更大的弹性。

### 3.8 自动生成依赖性

- 在Makefile中，我们的依赖关系可能会需要包含一系列的头文件，比如，如果我们的main.c中有一句“#include "defs.h"”，那么我们的依赖关系应该是：

```makefile
main.o : main.c defs.h
```

- 但是，如果是一个比较大型的工程，你必需清楚哪些C文件包含了哪些头文件，并且，你在加入或删除头文件时，也需要小心地修改Makefile，这是一个很没有维护性的工作。为了避免这种繁重而又容易出错的事情，我们可以使用C/C++编译的一个功能。大多数的C/C++编译器都支持一个“-M”的选项，即自动找寻源文件中包含的头文件，并生成一个依赖关系。例如，如果我们执行下面的命令：

```makefile
cc -M main.c
```

- 其输出是：

```makefile
main.o:main.c defs.h
```

- 于是由编译器自动生成的依赖关系，这样一来，你就不必再手动书写若干文件的依赖关系，而由编译器自动生成了。需要提醒一句的是，如果你使用GNU的C/C++编译器，你得用“-MM”参数，不然，“-M”参数会把一些标准库的头文件也包含进来。

- gcc-M main.c的输出是：

```makefile
main.o: mian.c defs.h /usr/include/stdio.h/usr/include/features.h\
	/usr/include/sys/cdefs.h /usr/include/gnu/stubs.h \
    /usr/lib/gcc-lib/i486-suse-linux/2.95.3/include/stddef.h \
    /usr/include/bits/types.h /usr/include/bits/pthreadtypes.h \
    /usr/include/bits/sched.h /usr/include/libio.h \
    /usr/include/_G_config.h /usr/include/wchar.h \
    /usr/include/bits/wchar.h /usr/include/gconv.h \
    /usr/lib/gcc-lib/i486-suse-linux/2.95.3/include/stdarg.h \
    /usr/include/bits/stdio_lim.h
```

gcc-MM main.c的输出则是：

```makefile
main.o:main.c defs.h
```

- 那么，编译器的这个功能如何与我们的Makefile联系在一起呢。因为这样一来，我们的Makefile也要根据这些源文件重新生成，让Makefile自已依赖于源文件？这个功能并不现实，不过我们可以有其它手段来迂回地实现这一功能。**GNU组织建议把编译器为每一个源文件的自动生成的依赖关系放到一个文件中**，为每一个“name.c”的文件都生成一个“name.d”的Makefile文件，[.d]文件中就存放对应[.c]文件的依赖关系。
- 于是，我们可以写出[.c]文件和[.d]文件的依赖关系，并让make自动更新或自成[.d]文件，并把其包含在我们的主Makefile中，这样，我们就可以自动化地生成每个文件的依赖关系了。
- 这里，我们给出了一个模式规则来产生[.d]文件：

```makefile
%.d:%.c
	@set -e;rm -f$@;\
	$(CC) -M $(CPPFLAGS)$<>$@.
;\
	sed 's,
\,o[:]*,\1.o$@:.g'<$@.>$@;\
	rm -f $@.
```

- 这个规则的意思是，所有的[.d]文件依赖于[.c]文件，“rm-f \$@”的意思是**删除所有的目标**，也就是[.d]文件，第二行的意思是，为每个依赖文件“\$<”，也就是[.c]文件生成**依赖文件**，“$@”表示模式“%.d”文件，如果有一个C文件是name.c，那么“%”就是“name”，“ ”意为一个随机编号，第二行生成的文件有可能是“name.d.12345”，第三行使用sed命令做了一个替换，关于[sed](<https://blog.csdn.net/tracing/article/details/9064415>)命令的用法请参看相关的使用文档。第四行就是删除临时文件。

- 总而言之，这个模式要做的事就是在编译器生成的依赖关系中加入[.d]文件的依赖，即把依赖关系：

```makefile
main.o:main.c defs.h
```

- 装换成：

```makefile
main.o main.d : main.c defs.h
```

- 于是，我们的[.d]文件也会自动更新了，并会自动生成了，当然，你还可以在这个[.d]文件中加入的不只是依赖关系，包括生成的命令也可一并加入，让每个[.d]文件都包含一个完赖的规则。一旦我们完成这个工作，接下来，我们就要把这些自动生成的规则放进我们的主Makefile中。我们可以使用Makefile的“include”命令，来引入别的Makefile文件（前面讲过），例如：

```makefile
sources = foo.c bar.c

include $(source:.c=.d)
```

- 上述语句中的“\$(sources:.c=.d)”中的“.c=.d”的意思是做一个替换，把变量$(sources)所有[.c]的字串都替换成[.d]，关于这个“替换”的内容，在后面我会有更为详细的讲述。当然，你得注意次序，因为i**nclude是按次来**载入文件，最先载入的[.d]文件中的目标会成为默认目标

## 4 Makefile 书写命令

- 每条规则中的命令和操作系统Shell的命令行是一致的。make会一按顺序一条一条的执行命令，每条命令的开头必须以[Tab]键开头，除非，命令是紧跟在依赖规则后面的分号后的。在命令行之间中的空格或是空行会被忽略，但是如果该空格或空行是以Tab键开头的，那么make会认为其是一个空命令。
- 我们在UNIX下可能会使用不同的Shell，但是make的命令默认是被“/bin/sh”——UNIX的标准Shell解释执行的。除非你特别指定一个其它的Shell。Makefile中，“#”是注释符，很像C/C++中的“//”，其后的本行字符都被注释。

### 4.1 显示命令

- 通常，make会把其要执行的命令行在命令执行前输出到屏幕上。当我们用“@”字符在命令行前，那么，这个命令将不被make显示出来，最具代表性的例子是，我们用这个功能来向屏幕显示一些信息。如：

```makefile
@echo 正在编译XXX模块
```

- 当make执行时，会输出“正在编译XXX模块....”子窜，但不会输出命令，如果没有“@”，那么，make将输出：

> echo 正在编译XXX模块

>正在编译XXX模块

- 如果make执行时，带入make参数“-n“或”--just-print“，那么其只是显示命令，但不会执行命令，这个功能很利于我们调试我们的Makefile，看着我们书写的命令时执行起来是什么样子的或是什么顺序的。
- 而make参数”-s“或”--slient“则是全面禁止命令的显示

### 4.2 命令执行

- 当依赖目标新于目标时，也就是当规则的目标需要被更新时，make会一条一条的执行其后的命令。需要注意的是，如果你要让上一条命令的结果应用在下一条命令时，你应该使用分号分隔这两条命令。比如你的第一条命令是cd命令，你**希望第二条命令得在cd之后的基础上运行**，那么你就不**能把这两条命令写在两行上**，而应该把这两条命令写在一行上，用分号分隔。如：

eg：

```makefile
exect:
	cd/home/hchen
	pwd
```

```makefile
exect:
	cd/home/hchen;pwd
```

- 当我们执行“make exec”时，第一个例子中的cd没有作用，pwd会打印出当前的Makefile目录，而第二个例子中，cd就起作用了，pwd会打印出“/home/hchen”。
- make一般是使用环境变量SHELL中所定义的系统Shell来执行命令，默认情况下使用UNIX的标准Shell——/bin/sh来执行命令。但在MS-DOS下有点特殊，因为MS-DOS下没有SHELL环境变量，当然你也可以指定。如果你指定了UNIX风格的目录形式，首先，make会在SHELL所指定的路径中找寻命令解释器，如果找不到，其会在当前盘符中的当前目录中寻找，如果再找不到，其会在**PATH环境变量中所定义的所有路径**中寻找。MS-DOS中，如果你定义的命令解释器没有找到，其会给你的命令解释器加上诸如“.exe”、“.com”、“.bat”、“.sh”等后缀。

### 4.3 命令出错

-  每当命令运行完后，make会检测每个命令的返回码，如果命令返回成功，那么make会执行下一条命令，当规则中所有的命令成功返回后，这个规则就算是成功完成了。如果一个规则中的某个命令出错了（命令退出码非零），那么make就会终止执行当前规则，这将有可能终止所有规则的执行。

- 有些时候，命令的出**错并不表示就是错误**的。例如mkdir命令，我们一定需要建立一个目录，如果目录不存在，那么mkdir就成功执行，万事大吉，**如果目录存在，那么就出错了**。我们之所以使用mkdir的意思就是一定要有这样的一个目录，于是我们就不希望mkdir出错而终止规则的运行。

- 为了做到这一点，忽略命令的出错，我们可以在Makefile的命令行前加一个减号“-”（在Tab键之后），标记为不管命**令出不出错都认为是成功的**。如：

```makefile
clean:
	-rm -f *.o
```

- 还有一个全局的办法是，给**make加上“-i”或是“--ignore-errors”参数**，那么，Makefile中所有命令都会忽略错误。而如果一个规则是以“.IGNORE”作为目标的，那么这个规则中的所有命令将会忽略错误。这些是不同级别的防止命令出错的方法，你可以根据你的不同喜欢设置。
- 还有一个要提一下的make的参数的是“-k”或是“--keep-going”，这个**参数的意思是，如果某规则中的命令出**错了，那么就终目该规则的执行，但继续执行其它规则。

### 4.4 嵌套执行make

- 在一些大的工程中，我们会把我们不同模块或是不同功能的源文件放在不同的目录中，我们可以在每个目录中都书写一个该目录的Makefile，这有利于让我们的Makefile变得更加地简洁，而不至于把所有的东西全部写在一个Makefile中，这样会很难维护我们的Makefile，这个技术对于我们模块编译和分段编译有着非常大的好处。                            ·

```makefile
subsystem:
	cd subdir && $(MAKE)
```

> equal to 

```makefile
subsystem:
	$(MAKE) -C subdir
```

- 定义$(MAKE)宏变量的意思是，也许我们的make需要一些参数，所以定义成一个变量比较利于维护。这两个例子的意思都是先进入“subdir”目录，然后执行make命令。

- 我们把这个makefile叫做==“总控的makefile”，==总控makefile 可以传递到下级makefile中（如果有显示的申明），但是不会覆盖下层的Makefile中所定义的变量，除非指定了==“-e”==参数

- 如果你要传递到下级makefile 中，如下

```makefile
export<variable...>
```

- 如果不想传递到下级makefile中，如下

```makefile
unexport<variable...>
```

eg:

```makefile
export variable = value

#等价于
variable = value
export variable

#等价于
export variable := value

#等价于
variable := value
export variable
```

- 如果是传递所有变量，只需要export，后边不加参数，传递所有的变量。
- ==注意==，有两个变量，==SHELL==,==MAKEFLAGS==,无论是否export，总是会传递到下一层Makefile中，尤其是==MAKEFLAGS==包含了make的参数信息，如果执行==“总控Makefile”==，时有make参数或是在上层Makefile中定义了这个变量，那么==MAKEFLAGS==变量将会时这些参数，并且会传递到下层Makefile中，这是一个系统级的环境变量。
- 但是make中有几个参数并不往下传递，“-C”，“-f”,"-h","-o","-W",如果不想往下传递参数，也可以这样。

```makefile
subsystem:
	cd subdir && $(MAKE) MAKEFLAGS=
```

- 如果定义的环境变量==MAKEFLAGES==，确定其中的选项大家能用到，如果其中有“-t”,"-h",和"-q"参数，可能会出现意料之外的结果。

- 还有一个在“嵌套执行”中比较有用的参数，“-w”或是，“--print-directory”会在make的过程中输出一些信息，可以看到目前的工作目录，比如，如果我们的下级make目录是"/home/hchen/gun/make",如果我们使用“make -w"来执行，当我们进入改目录时，我们会看到：

> make: Entering dirctory `/home/home/hchen/gnu/make'.

- 而在完成下层make后离开目录时，我们会看到：

> make: Leaving dirctory `/home/hchen/gnu/make'

- 当你使用”-C"参数来指定make下层Makefile时，“-w"会被自动打开。如果参数中有"-s"("--slient")或是，"--no-print-dirctory", 那么"-w"总是失效的。

### 4.5 定义命令包

- 如果Makefile中出现一些相同命令序列，那么我们可以为这些相同序列定义一些变量，定义这个命令序列的语法以"define"开始，以"end"结束，如：

```makefile
define run-yacc
yacc $(firstword $^)
mv y.tab.c $@
endef
```

- 这里“run-yacc"是这个命令包的名字，不要和Makefile中的变量重名，中间是命令序列，因为yacc总是生成”y.tab.c"的文件，所以第二行命令就是将这个文件改一下名字。使用如下：

```makefile
foo.c foo.y
	$(y.tab.c)
```

- 由此可见使用这个命令包与使用变量规则一样，命令包"run-yacc"中的“$^”就是“foo.y‘，"\$@"就是“foo.c”,make在执行命令包的时候，命令包的每个命令会被依次独立执行。

  

# part II  使用变量

- 在Makefile中定义的变量类似C中的宏定义一样，在运行到这个地方的时候会展开，可以存在于目标，依赖目标，命令或是Makefile的其他部分中，但是格式不能包含==''#''，''=''，''.''==,传统变量一般都使用大写命名，但是推荐使用大小写混用的格式，入==“MakeFlage”==。

##  三 变量的高级用法

- 1. 变量值得替换。

  我们可以替换变量中的共有的部分，其格式是“$(var.a=b)”或是“\${var:a=b}”,其意思是，把变量“var”中所有以“a”

