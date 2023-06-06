
**JVM重点问题**

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210101184141.png)

1. JVM位置
2. JVM体系结构
3. 类加载器
4. 双亲委派机制
5. 沙箱安全机制
6. Native
7. PC寄存器
8. 方法区
9. 三种JVM
10. 栈
11. 堆
12. 新生区，老年区
13. 永久区
14. 堆内存调优
15. GC机制（常用算法）
16. JMM
17. 单点登录（SSO）

## JVM位置

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210101184005.png)

## JVM体系结构（JVM内存模型）

jvm内存模型主要分为5个部分，是java在运行时，jvm虚拟机拿到了自己能支配的内存之后，将内存进行了割分

* 栈：存储函数运行中的一些引用类型的变量&基础类型变量
* 堆：存储对象，被栈中的引用类型变量所指向
* 本地方法栈：存储C++的native方法运行时的栈区
  * java中有两种方法：java方法和本地方法
  * java方法由java语言编写，编译为class文件，运行时放在jvm中
  * 本地方法由其他语言编写，编译成和处理器相关的机器代码，本地方法保存在动态链接库中，在windows系统中就是.dll文件中，各个平台都有自己独有的
* 程序计数器：指向程序当前运行的位置
* 方法区：存储元数据信息。static，cloader等

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210101183226.png)

## 类加载器

* .class文件经过类加载器，初始化，在方法中形成一个Class类型的对象用来做实例化操作

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210102093505.png" style="zoom:80%;" />

* 类加载器分类：（层次从低到高）

  * 应用程序加载器：AppClassLoader：**记载自定义的类**
  * 扩展加载器：ExtClassCloader：**加载java扩展库中的类**
  * 引导类(根)加载器：由C++实现，**加载java核心库rt.jar，创建扩展类加载器和应用程序类加载器**
  * 虚拟机自带的加载器：

## 双亲委派机制

* 目的：为了保证安全，重点理解委派
* 双亲**委派**机制原理：类加载器收到类加载请求——》一直向上(父类)委托，直到根加载器——》引导类加载器检查是否能加载当前这个类——》能加载就是用当前加载器，否则就通知子加载器进行加载——》重复上个步骤知道将类进行加载
* 如果最终都没有找到的话，那么会报异常：Class Not Found
* 用一个classLoader一直重复getCloader，如果出现了null的情况，那么有两种情况：要么不存在，要么就是C++写的native方法(java无法获取到)
* APP——》EXT——》BOOT（最终执行）
* <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210102093505.png" style="zoom: 50%;" />

## 沙箱安全机制

* 沙箱机制就是一个限制程序运行的环境，通过设置相应的权限。

* 组成沙箱的基本组件：

  * 字节码校验器：确保java类文件遵循java语言规范，帮助java实现内存保护。核心类不会经过字节码校验。

  * 类加载器：

    * 在3个方面对java沙箱起作用
      * 防止恶意代码干涉善意代码，例如双亲委派（自定义String类根本不会生效）
      * 守护被信任的类库边界
      * 将代码归入保护域，确定了代码可以进行哪些操作（例如自己的代码调不了C++的库）

    * JVM为不同的类加载器载入的类提供不同的命名空间
    * 包含哪些关于沙箱机制类：
      * 存取控制器（access controller）
      * 安全管理器（security manager）
      * 安全软件包（security package）

## Native关键字

* 凡是带了native关键字的方法，这个方法会调用C库
* 带native关键字的方法在执行时会进入本地方法栈，然后调用本地方法接口JNI
* JNI作用：扩展java的使用，融合不同编程语言为java所用。最初融合的是java诞生时如日中天的C和C++

## PC寄存器

* Program Counter Register
* 线程私有
* 就是一个指针，指向方法区中的方法字节码（用来存储指向一条即将要执行的指令的地址），在执行引擎读取下一条指令。
* 如果执行的是一个native方法，计数器是空的

## 方法区

* Method Area
* 所有线程共有：所有字段，方法字节码，构造函数，接口代码
* 静态变量`static`，常量`final`，类信息`Class模板`(构造方法，接口定义)，运行时的常量池`fianl Pool`也存在内存中。
* 实例变量存在堆内存中

## 栈

* 先进后出，后进先出

* 举个栗子：

  * 栈：喝多了吐
  * 队列：吃多了拉

* 栈内存：主管程序的运行，生命周期，线程同步

* 线程结束，栈内存也就是释放了，程序也就结束了

* 栈中放哪些类型：

  * 8大基本类型
  * 对象引用
  * 实例的方法

* 栈帧：一帧一帧地存储

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210102113520.png" style="zoom: 67%;" />

* 栈满了会报错：StackOverFLowError

## 堆

* 三种JVM：
  * SUN公司：HotSpot(TM)，我们学习的
  * BEA公司：：JRockit
  * IBM公司：J0 VM
* Heap
* 一个JVM只有一个堆内存，堆内存的大小是可以调节的
* 类加载器读取的类文件后，一般把什么东西放进堆中？
  * 类的实例，实例中的方法，变量，（jdk1.8后的常量）
  * 保存所有引用类型的真实对象
* 堆中还要细分为三个区域：
  * 新生区（有三个：Eden Space，Survivor1，Survivor2）：轻量级垃圾回收
  * 养老区：重量级垃圾回收
  * 永久区
* GC垃圾回收主要发生在伊甸园区&养老区
* 假设内存满了，会有OOM（java.out.OutOfMemoryError:Java heap space）错误，说明堆内存满了
* JDK8后，永久存储区更名为：元空间

## OOM故障排除

* DEBUG太慢了，所以最好能直接定位代码出错行数，可以使用内存快照分析工具：MAT，Jprofiler

* MAT，Jprofiler作用：

  * 分析Dump内存文件，快速定位内存泄漏问题
  * 获得堆中的数据
  * 获得大的对象
  * ......

* 使用Jprofiler工具剖析java代码运行过程

* ```shell
  #-Xms:设置初始化内存分配大小，默认1/64
  #-Xmx:设置最大分配内存，默认1/4
  #-XX:+PrintGCDetails:打印GC垃圾回收信息
  #-XX:+HeapDumpOnOutOfMemoryError:打印OOM信息
  -Xms8m -Xmx8m -XX:+HeapDumpOnOutOfMemoryError
  ```

* ```java
  //RunTime类（代表应用程序的运行环境）常用方法
  getRuntime();
  exec(String command);
  freeMemory();//返回java虚拟机中的空闲内存区，字节为单位
  maxMemory();//返回java虚拟机试图使用的最大内存量，字节为单位
  totalMemory();//返回java虚拟机内存总量，字节为单位
  ```

## GC垃圾回收机制见博客[Java-GC机制](https://jack1024.club/2020/12/21/Java-GC%E6%9C%BA%E5%88%B6/)

## JMM

* 什么是JMM？Java Memory Model
* 究竟什么是JMM，其实JMM和栈，堆这些JVM内存模型的东西完全不是一个概念的东西。当你第一次听到Java Memory Medel时候，肯定会首先想到这些吧，但是不对。
* **实际上JMM是一个抽象的概念，**
* **在物理实现上，java采用volatile关键字**，该关键字用于修饰变量表示该变量在不同线程中共享。导致编译器和运行时都会注意到该变量是共享的，因此不会对该变量进行重排序。

**JMM实际上就时缓存一致性协议，用于定义数据读写规则**

* **JMM的8种交互操作：**
  * **lock** （锁定）：作用于主内存的变量，把一个变量标识为线程独占状态
  * **unlock** （解锁）：作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定
  * **read** （读取）：作用于主内存变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用
  * **load** （载入）：作用于工作内存的变量，它把read操作从主存中变量放入工作内存中
  * **use** （使用）：作用于工作内存中的变量，它把工作内存中的变量传输给执行引擎，每当虚拟机遇到一个需要使用到变量的值，就会使用到这个指令
  * **assign** （赋值）：作用于工作内存中的变量，它把一个从执行引擎中接受到的值放入工作内存的变量副本中
  * **store** （存储）：作用于主内存中的变量，它把一个从工作内存中一个变量的值传送到主内存中，以便后续的write使用
  * **write** （写入）：作用于主内存中的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中

* **JVM对这8种指令定义了如下的规则：**

  * 不允许read和load、store和write操作之一单独出现。即使用了read必须load，使用了store必须write
  * 不允许线程丢弃他最近的assign操作，即工作变量的数据改变了之后，必须告知主存
  * 不允许一个线程将没有assign的数据从工作内存同步回主内存
  * 一个新的变量必须在主内存中诞生，不允许工作内存直接使用一个未被初始化的变量。就是怼变量实施use、store操作之前，必须经过assign和load操作
  * 一个变量同一时间只有一个线程能对其进行lock。多次lock后，必须执行相同次数的unlock才能解锁
  * 如果对一个变量进行lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新load或assign操作初始化变量的值
  * 如果一个变量没有被lock，就不能对其进行unlock操作。也不能unlock一个被其他线程锁住的变量
  * 对一个变量进行unlock操作之前，必须把此变量同步回主内存


说白了，JMM就是一个关于操作系统读写操作控制的机制，说到这里，过几天要回顾操作系统的知识了。。。。哎