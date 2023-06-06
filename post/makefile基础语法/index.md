
## C/C++文件到可执行文件的过程

~~~
gcc -E xx.c -o xx.i  预处理
gcc -s xx.i -o xx.s  编译
gcc -c xx.s -o xx.o  汇编
gcc xx.o -o xx       链接
~~~

​	对于单个文件的编译，可以浓缩为一条命令

~~~
gcc xx.c -o xx
~~~

​		但是在实际开发中，我们的一个项目有多个文件，并且有多层文件夹的嵌套。makefile文件就是为了解决这个问题而出现的

## makefile文件的基础语法

### 1. 第一层次：easiest

​		**基本格式：**

~~~
目标文件： 依赖文件
(一个tab)gcc/g++指令
~~~

​		**伪目标：**	

~~~
.PHONY:
clear（名字自定义）:
    rm -rf xx.o xx.s xx.i xx.exe
(运行的时候：make clear)
~~~

​		**For Example:**

~~~makefile
#circle.c circle.h cube.c cube.h main.c main.h

test: circle.o cube.o main.o
    gcc circle.o cube.o main.o -o test

circle.o :circle.c
    gcc circle.c -o circle.o

cube.o :cube.c
    gcc cube.c -o cube.o

main.o :main.c
    gcc main.c -o main.o

.PHONY
clearall:
    rm -rf circle.o cube.o main.o test
clear:
    rm -rf circle.o cube.o main.o
~~~

### 2. 第二层次：（变量）（用美元符$(TAR)表示）

> **Makefile中变量有四种赋值方式:**
>
> 1. 简单赋值**( := )** 编程语言中常规理解的赋值方式，只对当前语句的变量有效
> 2. 递归赋值**( = )**赋值语句可能影响多个变量，所有目标变量相关的其他变量都受影响
> 3. 条件赋值**( ?= )**如果变量未定义，则使用符号中的值定义变量。如果该变量已经赋值，则该赋值语句无效。
> 4. 追加赋值**( += ）**原变量用空格隔开的方式追加一个新值

~~~makefile
TAR=test
OBJ=circle.o bube.o main.o
CC:=gcc

使用的时候：$(TAR)  $(OBJ)   $(CC)
~~~

### 3. 第三层次：（通配符）

~~~makefile
%：应用在这个Makefile文件中的
*：应用在系统中的
$@：表示目标文件
$^：表示所有的依赖文件    
$<：表示第一个依赖文件
$?：表示比目标还要新的依赖文件列表
~~~

​		%和*的区别:[引用一篇博文](https://www.cnblogs.com/warren-wong/p/3979270.html)

### 4. 第四层次：（函数）

​		暂时没有涉及，之后会补齐