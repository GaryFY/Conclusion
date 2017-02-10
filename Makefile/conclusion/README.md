Makefile总述
====

####概述
Makefile里主要包含了五个东西：显式规则、隐晦规则、变量定义、文件指示和注释
* 显式规则：指明要生成的文件、文件的依赖文件、生成的命令
* 隐晦规则：比较粗糙地简略书写Makefile
* 变量的定义：类似C语言中的宏，变量都会被扩展到相应的引用位置上
* 文件指示：其包括了三个部分，一个是在一个Makefile中引用另一个Makefile，就像C语言中的include一样；
另一个是指根据某些情况指定Makefile中的有效部分，就像C语言中的预编译#if一样；
还有就是定义一个多行的命令。
* 注释：`“#”`，**在Makefile中的命令，必须要以[Tab]键开始。**

####引用其它的Makefile

	include <filename>
	
**在include前面可以有一些空字符，但是绝不能是[Tab]键开始。**include和<filename>可以用一个或多个空格隔开。  
寻找：
* 如果make执行时，有“-I”或“--include-dir”参数，那么make就会在这个参数所指定的目录下去寻找。
* `-include <filename>`  
其表示，无论include过程中出现什么错误，都不要报错继续执行。

####make的工作方式
GNU的make工作时的执行步骤入下：（想来其它的make也是类似）

1. 读入所有的Makefile。  
2. 读入被include的其它Makefile。  
3. 初始化文件中的变量。  
4. 推导隐晦规则，并分析所有规则。  
5. 为所有的目标文件创建依赖关系链。  
6. 根据依赖关系，决定哪些目标要重新生成。  
7. 执行生成命令。  

####书写规则
* 依赖关系
* 生成目标的方法
```
targets : prerequisites
command
...
或是这样：
targets : prerequisites ; command
command
...
```
targets是文件名，以空格分开，可以使用通配符。一般来说，我们的目标基本上是一个文件，但也有可能是多个文件。  
command是命令行，如果其不与“target:prerequisites”在一行，那么，必须以[Tab键]开头，如果和prerequisites在一行，那么可以用分号做为分隔。  

####文件搜索
`"VPATH"`：当make需要去找寻文件的依赖关系时，你可以在文件前加上路径，但最好的方法是把一个路径告诉make，让make在自动去找。
```
VPATH = src:../headers
```
上面的定义指定两个目录，`src`和`../headers`，make会按照这个顺序进行搜索。目录由“冒号”分隔。 

或者使用make的`“vpath”`关键字
* `vpath <pattern> <directories>`
为符合模式<pattern>的文件指定搜索目录<directories>。
* `vpath <pattern>`
清除符合模式<pattern>的文件的搜索目录。
* `vpath`
清除所有已被设置好了的文件搜索目录。   
例如：
```
vpath %.h ../headers
```
该语句表示，要求make在“../headers”目录下搜索所有以“.h”结尾的文件。` % `的意思是匹配零或若干字符.

####伪目标
为了避免和文件重名的这种情况，我们可以使用一个特殊的标记`.PHONY`来显示地指明一个目标是“伪目标”

####多目标
```
bigoutput littleoutput : text.g
generate text.g -$(subst output,,$@) > $@
上述规则等价于：
bigoutput : text.g
generate text.g -big > bigoutput
littleoutput : text.g
generate text.g -little > littleoutput
```

其中，`-$(subst output,,$@)`中`$`表示执行一个Makefile的函数，函数名为subst，后面的为参数。
关于函数，将在后面讲述。这里的这个函数是截取字符串的意思，`$@`表示目标的集合，就像一个数组，`$@`依次取出目标，并执于命令。   
例：
```
objects = foo.o bar.o
all: $(objects)
$(objects): %.o: %.c
$(CC) -c $(CFLAGS) $< -o $@
```
上面的例子可以看出：
* 指明了我们的目标从$object中获取；
* “%.o”表明要所有以“.o”结尾的目标，也就是“foo.o bar.o”，也就是变量$object集合的模式
* 依赖模式“%.c”则取模式“%.o”的“%”，也就是“foo bar”，并为其加下“.c”的后缀。我们的依赖目标就是“foo.c bar.c”。
* 命令中的“$<”和“$@”则是自动化变量，“$<”表示所有的依赖目标集（也就是“foo.c bar.c”），“$@”表示目标集（也就是“foo.o bar.o”

####自动生成依赖性
GNU组织建议把编译器为每一个源文件的自动生成的依赖关系放到一个文件中，为每一个“name.c”的文件都生成一个“name.d”的Makefile文件，[.d]文件中就存放对应[.c]文件的依赖关系。
我们可以写出[.c]文件和[.d]文件的依赖关系，并让make自动更新或自成[.d]文件，并把其包含在我们的主Makefile中，这样，我们就可以自动化地生成每个文件的依赖关系了。

###书写命令
####显示命令
```
@echo 正在编译XXX模块......
```
输出：“正在编译XXX模块......”  
如果make执行时，带入make参数“-n”或“--just-print”，那么其只是显示命令，但不会执行命令，这个功能很有利于我们调试我们的Makefile  
ake参数“-s”或“--slient”则是全面禁止命令的显示。

####命令出错
make加上“-i”或是“--ignore-errors”参数，那么，Makefile中所有命令都会忽略错误   
make的参数的是“-k”或是“--keep-going”，这个参数的意思是，如果某规则中的命令出错了，那么就终目该规则的执行，但继续执行其它规则

####嵌套执行make
```
subsystem:
cd subdir && $(MAKE)
其等价于：
subsystem:
$(MAKE) -C subdir
```
这两个例子的意思都是先进入“subdir”目录，然后执行make命令。   

####定义命令包
```
define run-yacc
yacc $(firstword $^)
mv y.tab.c $@
endef
```
“run-yacc”是这个命令包的名字    
* 第一个命令是运行Yacc程序，因为Yacc程序总是生成“y.tab.c”的文件
* 第二行的命令就是把这个文件改改名字

###使用变量
####变量基础
变量在声明时需要给予初值，而在使用时，需要给在变量名前加上“$”符号，但最好用小括号“（）”或是大括号“{}”把变量给包括起来。   

####变量中的变量
```
foo = $(bar)
bar = $(ugh)
ugh = Huh?
all:
echo $(foo)
```
执行“make all”将会打出变量$(foo)的值是“Huh?”（ $(foo)的值是$(bar)，$(bar)的值是$(ugh)，$(ugh)的值是“Huh?”）    
一个比较有用的操作符是“?=”
```
FOO ?= bar
```
其含义是，如果FOO没有被定义过，那么变量FOO的值就是“bar”，如果FOO先前被定义过，那么这条语将什么也不做，其等价于：
```
ifeq ($(origin FOO), undefined)
FOO = bar
endif
```
####变量高级用法
1. 
```
foo := a.o b.o c.o
bar := $(foo:.o=.c)
```
先定义了一个“$(foo)”变量，而第二行的意思是把“$(foo)”中所有以“.o”字串“结尾”全部替换成“.c”,$(bar)”的值就是“a.c b.c c.c”   
2. “把变量的值再当成变量”
```
x = y
y = z
a := $($(x))
```
$(x)的值是“y”，所以$($(x))就是$(y)，于是$(a)的值就是“z”。（注意，是“x=y”，而不是“x=$(y)”）   
```
x = $(y)
y = z
z = Hello
a := $($(x))
```
这里的$($(x))被替换成了$($(y))，因为$(y)值是“z”，所以，最终结果是：a:=$(z)，也就是“Hello”。   


####追加变量值
```
objects = main.o foo.o bar.o utils.o
objects += another.o
```
使用“+=”操作符给变量追加值,$(objects)值变成：“main.o foo.o bar.o utils.o another.o”

####override指示符
有变量是通常make的命令行参数设置的，那么Makefile中对这个变量的赋值会被忽略。如果你想在Makefile中设置这类参数的值，那么，你可以使用“override”指示符。   

####多行变量
define指示符后面跟的是变量的名字，而重起一行定义变量的值，定义是以endef关键字结束。

####环境变量
如果我们在环境变量中设置了“CFLAGS”环境变量，那么我们就可以在所有的Makefile中使用这个变量。   
向下层Makefile传递，则需要使用exprot关键字来声明。

####目标变量
为某个目标设置局部变量，这种变量被称为“Target-specific Variable”。其语法：   
```
<target ...> : <variable-assignment>
<target ...> : overide <variable-assignment>
```
例：
```
prog : CFLAGS = -g
prog : prog.o foo.o bar.o
$(CC) $(CFLAGS) prog.o foo.o bar.o
prog.o : prog.c
$(CC) $(CFLAGS) prog.c
foo.o : foo.c
$(CC) $(CFLAGS) foo.c
bar.o : bar.c
$(CC) $(CFLAGS) bar.c
```
不管全局的$(CFLAGS)的值是什么，在prog目标，以及其所引发的所有规则中（prog.o foo.o bar.o的规则），$(CFLAGS)的值都是“-g”。   

###使用条件判断
例：判断$(CC)变量是否“gcc”，如果是的话，则使用GNU函数编译目标。
```
libs_for_gcc = -lgnu
normal_libs =
foo: $(objects)
ifeq ($(CC),gcc)
$(CC) -o foo $(objects) $(libs_for_gcc)
else
$(CC) -o foo $(objects) $(normal_libs)
endif
```
####语法
示例一：
```
bar =
foo = $(bar)
ifdef foo
frobozz = yes
else
frobozz = no
endif
```
* 第一个是我们前面所见过的“ifeq”
* 第二个条件关键字是“ifneq”
* 第三个条件关键字是“ifdef”
* 第四个条件关键字是“ifndef”

