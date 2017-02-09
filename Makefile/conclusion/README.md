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
command是命令行，如果其不与“target:prerequisites”在一行，那么，必须以[Tab键]开头，如果和prerequisites在一行，那么可以用分号做为分隔.  


