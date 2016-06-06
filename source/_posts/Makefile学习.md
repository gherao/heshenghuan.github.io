---
title: Makefile学习
date: 2016-03-02 14:11:24
tags: Makefile
categories: 学习
---

### Makefile介绍

以前在使用windows时，编写一个C/C++项目往往使用Visual Studio，所有头文件、源文件、资源文件等等在编译时IDE都已经做好了所有的相关工作，我只是点一下编译->链接->运行而已。去年将主力机更换为MacBook Pro后，我有很长一段时间没有再写过一个包含多个文件的C++项目了，等真正需要写的时候又懵了，而苹果的Xcode又实在用不习惯，故而学习Makefile的使用方法。

**Makefile关系到了整个工程的编译规则。**一个工程中的源文件不计数，其按类型、功能、模块分别放在若干个目录中，Makefile定义了一系列的规则来指定，哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译，甚至于进行更复杂的功能操作，因为Makefile就像一个Shell脚本一样，其中也可以执行操作系统的命令。Makefile带来的好处就是——“自动化编译”，一旦写好，只需要一个make命令，整个工程完全自动编译，极大的提高了软件开发的效率。make是一个命令工具，是一个解释Makefile中指令的命令工具，一般来说，大多数的IDE都有这个命令，比如：Delphi的`make`，Visual C++的`nmake`，Linux下GNU的`make`。可见，Makefile都成为了一种在工程方面的编译方法。

make命令执行时，需要在当前目录下有一个Makefile文件，保存了make命令如何去编译和链接程序的规则。用一个简单的例子说明（示例来源于GNU的make使用手册），在这个例子中，我们的工程有8个C文件和3个头文件，我们需要写一个Makefile来告诉make命令如何编译和链接这几个文件。规则如下：

1. 如果这个工程没有编译过，那么所有C文件都要编译并链接。
2. 如果这个工程的某几个C文件被修改，那么我们只编译被修改的C文件，并链接目标程序。
3. 如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的C文件，并链接目标程序。

如果Makefile写得足够完整，所有的这一切规则，只需要一个make命令就可以完成。make命令会自动地根据文件的修改情况、依赖关系、包含关系来确定哪些文件需要重新编译，从而自动地完成“编译->链接”。

#### Makefile的规则

看过别人写过的Makefile文件以后，可以发现Makefile的主体部分都有如下的形式：
​	

```
---------------------------------------
target : prerequisites ...
	command
...
...
---------------------------------------
```

其中，*target*是一个目标文件，它可以是Object File，也可以是执行文件，还可以是一个标签(Label)；
*prerequisites*是生成这个target所需要的文件或是其他target，即**前置条件**；
*command*也就是make需要执行的命令。（任意的shell命令）

这个规则其实也是一个文件的依赖关系，可以看到target这一目标依赖于prerequisites中的文件或目标，其生成规则定义在command中。可以假设，prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的命令就会被执行。这就是Makefile的规则，也就是Makefile中最核心的内容。

#### 一个例子

正如前面所说的，如果一个工程有3个头文件，和8个C文件，我们为了完成前面所述的那三个规则，我们的Makefile应该是下面的这个样子的。

```
# Makefile 示例
edit : main.o kbd.o command.o display.o \
	insert.o search.o files.o utils.o
	cc -o edit main.o kbd.o command.o display.o \
		insert.o search.o files.o utils.o

main.o : main.c defs.h
	cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c tils.c
clean :
	rm edit main.o kbd.o command.o display.o \
    	insert.o search.o files.o utils.o
```

将该文件以makefile或Makefile命名，保存到工程目录下，然后在该目录下直接敲`make`命令就可以生成可执行文件edit了。如果需要删除所有执行文件和中间文件，只需要输入命令`make clean`即可。

**注意**:每一个规则中command一定要以一个Tab键开头。

#### `make`是如何工作的

在默认的方式下，`make`命令的工作方式，或者说是更新目标文件的方式如下：

1. `make`会在当前目录下找名为“makefile”或“Makefile”的文件。
2. 找到makefile文件中的第一个target，并将其作为最终的目标文件，在上述例子中，它会找到“edit”这个目标。
3. 如果edit文件存在，或者是edit后的prerequisites中的某个.o文件的修改时间比edit这个文件要新，那么它就会执行后面的command来生成新的edit文件。
4. 如果edit所依赖的.o文件也存在，那么`make`会在当前文件中找目标为.o文件的依赖性，如果发现有依赖文件的更新，则再根据那一个规则生成.o文件。
5. 直到所有edit需要的文件全存在，`make`将会执行命令完成edit的生成。

所以这也就是为什么我以前看到别人写好的makefile文件中，最终的目标都是写在最前面的原因了。`make`命令在执行时，会用深度优先的搜索方式地去找文件的依赖关系，直到最终编译出第一个目标文件，接着编译第二个。

在找寻的过程中，如果出现错误，比如最后被依赖的文件找不到，那么make就会直接退出，并报错。而对于所定义的命令的错误，或是编译不成功，`make`根本不理，它只管文件的依赖性，即如果在找到了文件之间的依赖关系之后，冒号后面的文件如果不存在，那么整个程序就退出了。

而在上述例子中，像clean这种与第一个target没有任何关联的target，其之后的command是不会被自动执行的。不过，当我们需要执行这样的命令时，`make clean`这样显式地指出即可。这样，就可以清楚所有目标文件，以便重新编译。

#### makefile中使用变量以简便书写

在上述的例子中，edit后的一串.o文件重复出现了多次，这样就会在修改时带来不便。如果某处修改了，而另一处没有就有可能在之后的执行过程中因为依赖关系的不同而发生错误。这时，就可以使用变量了。Makefile的变量是一个字符串，可以简单粗暴地理解为宏。

比如，我们声明一个变量，叫objects, OBJECTS, objs, OBJS, obj, 或是 OBJ，反正不管什么啦，只要能够表示obj文件就行了。我们在makefile一开始就这样定义：

```
objects = main.o kbd.o command.o display.o \
         insert.o search.o files.o utils.o
```

于是，我们就可以很方便地在我们的makefile中以“$(objects)”的方式来使用这个变量了，于是我们的改良版makefile就变成下面这个样子：
​	

```
objects = main.o kbd.o command.o display.o insert.osearch.o files.o utils.o 
```

```
edit : $(objects)
       cc -o edit $(objects)
main.o : main.c defs.h
       cc -c main.c
kbd.o : kbd.c defs.h command.h
	cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit $(objects)
```

于是如果有新的 .o 文件加入，我们只需简单地修改一下 objects 变量就可以了。

### 参考资料

1. [Makefile经典教程（掌握这些足够）](http://blog.csdn.net/ruglcc/article/details/7814546/#t1)
2. [g++编译命令选项

