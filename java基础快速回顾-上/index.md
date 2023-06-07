# java基础快速回顾-上


## java语言开发环境搭建

### java虚拟机——JVM

* 实质就是翻译，把.class文件翻译成计算机能认识的机器语言
* java所谓的跨平台指的是java程序跨平台。JVM不是跨平台的，各个平台（Windows，Linux，mac）的JVM是不同的。
* <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219152513.png" style="zoom:80%;" />
* <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219153329.jpeg" style="zoom: 80%;" />
* **Java这个语言很非凡。** 
  一、你可以说它是**编译型**的。因为所有的Java代码都是要编译的，.java不经过编译就什么用都没有。 
  二、你可以说它是**解释型**的。因为java代码编译后不能直接运行，它是解释运行在JVM上的，所以它是解释运行的，那也就算是解释的了。 

### JRE和JDK

* JRE：java Runtime Environment，包含JVM和运行时需要的核心类库
* JDK：java Development Kit，是java程序开发工具包，包含JRE和开发人员使用的工具
* 想要运行一个已有的java程序，那么只需安装JRE即可；想要开发一个全新的java程序，那么必须安装JDK

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219152929.png" style="zoom:80%;" />

### 配置JDK环境变量

* 在全局系统变量中2个操作
  * 新建JAVA_HOME变量：（jdk的安装路径）
  * 修改Path：添加%JAVA_HOME%\bin

## HelloWorld入门程序

### 程序开发步骤

* java程序开发三步骤：**编写**——》.java——》**编译**（编译器javac.exe）——》.class——》**运行**（JVM解释执行：java.exe）
* 新建文件名为HelloWorld的java文件，编辑：

```java
public class HelloWorld{//public的类名必须和文件名完全一致
    public static void main(String[] args){
        System.out.println("Hello,world!");
    }
}
```

```shell
//编译
javac HelloWorld.java
//JVM解释执行
java HelloWorld
```

### HelloWorld程序说明

* 单行注释，多行注释：

```java
//单行注释
/*多行注释*/
```

* `public static void main(String[] args)`是程序入口

### 关键字说明：java中有特殊含义的保留字

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219161346.png" style="zoom:80%;" />

### 标识符：

* 指的是在程序中，我们自定义的内容中，如类的名字，方法的名字，变量的名字等都是标识符，
* 命名规则（硬性要求）
  * 标识符可以包含：**英文字符，数字，$符，_下划线**
  * 标识符不能以数字开头
  * 标识符不能是关键字
* 命名规范（软性要求）
  * 类名规范：首字符大写，后面每个单词首字母大写（大驼峰式）
  * 变量名规范：首字符小写，后面每个单词首字母小写（小驼峰式）
  * 方法名规范：同变量名

## 常量

* 概念：在程序运行期间固定不变的量
* 分类：
  * 字符串常量：双引号引起来的
  * 整数常量：直接写上的数字，没有小数点
  * 浮点数常量：直接写上的数字，有小数点
  * 字符常量：单引号引起来的单个字符，有且仅有一个字符
  * 布尔常量：true或者false
  * 空常量：null，代表没有任何数据

## 数据类型

### 引用数据类型

* 字符串，数组，类，接口，Lambda等

### 基本数据类型

* 整数型：byte，short，int，long
* 浮点型：float，double
* 字符型：char
* 布尔型：boolean

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219182115.png" style="zoom:67%;" />

注意事项：

* 字符串不是基本类型，是引用类型
* 浮点型可能只是一个近似值
* 数据范围与字节数不一定相关
* 浮点数默认是double，float需要加后缀F
* 整数默认是int，long需要加上后缀L

## 变量

* 程序运行期间，内容可以发生改变的量

* 格式：

  ```java
  数据类型 变量名称; //创建一个变量
  变量名称 = 数据值; //赋值
  数据类型 变量名称 = 数据值; //一步到位
  ```

* 局部变量没有默认值，没有进行赋值不能直接使用

* 变量使用不能超过作用域（从定义变量开始，一直到直接所属的大括号结束为止）的范围

## 数据类型转换

* 当等号两边的数据类型不一致时，将会发生数据类型转化，分为自动类型转换（隐式）&强制类型转换（显式）

### 自动转换（隐式）

* 规则：数据范围小的转化为大的。与字节数不一定相关。

### 强制转换（显式）

* 规则：范围小的类型 范围小的变量名 = （范围小的类型）范围大的数据
* 整形强制转换可能发生数据溢出：long——》int
* 浮点型强制转换可能精度损失：double——》int
* byte/short/char三种类型都可以发生数学运算，在运算时首先会提升为int，再进行运算
* boolean类型不能发生任何数据类型转换

## ASCII&Unicode

* 48代表'0'，65代表'A'，97代表'a'
* 数字和字符的对照关系表：是ASCII码表（American Standard Code for Information Interchange）
* Unicode码表（万国码）：也是数字和符号的对照关系，开头0-127部分和ASCII完全一样，但是从128开始含有更多字符，包含中文字符。

## 运算符

* 运算符：运算特定操作的符号
* 表达式：用运算符连起来的式子叫做表达式
* 运算符分类：
  * 算数运算符：+，-，*，/，%，++，--
    * 如果运算中有不同类型的数据，那么结果将会是数据类型范围大的那种（类型自动转换）
    * 加法：对于数值来说，就是数学加法；对于char来说，先按照ascii或unicode提升为int，然后再计算；对于String来说，是字符串的连接操作。
    * 任何数据类型和字符串进行加法操作时，结果都会连接变成字符串。
    * 常量不能使用自增or自减

  * 赋值运算符：

    * =，+=，-=，*=，/=，%=

    * 只有变量才能写在赋值运算符左边

    * 复合赋值运算符隐含了一个强制类型转换

      ```java
      byte num = 30;
      num += 5;//num = (byte) (int + int)
      ```

  * 比较运算符：

    * ==，<，>，<=，>=，!=
    * 比较运算符的结果一定是一个boolean值
    * 多次判断不能连着写

  * 逻辑运算符：

    * &&，||，！
    * &&和||具有短路效果，从而节省一定的性能
    * 逻辑运算符只能用于boolean值
    * &&和||可以连续写
    * 对于`1<x<3`的连续情况，应该拆分为两个部分，然后使用运算符连接起来`1<x&&x<3`

  * 三元运算符：

    * 格式：数据类型 变量名称 = 条件判断？表达式A：表达式B
    * 条件判断true，表达式A的值赋给变量；条件判断false，表达式B的值赋给变量

## 方法

* 引入：先把握整体，再把握局部

* 方法的定义

  * 方法定义的**先后顺序无所谓**

  * 方法定义必须是挨着的，不能在一个方法的内部定义另一个方法

  * 格式：

    ```java
    修饰符 返回值类型 方法名称(参数类型 参数名称,...){
        方法体;
        return 返回值;
    }
    ```

* 方法的调用

  * 方法定义后要执行需要在另一个方法中调用

  * 三种调用格式

    ```java
    //单独调用
    方法名称(参数);
    //打印调用
    System.out.println(方法名称(参数));
    //赋值调用
    数据类型 变量名称 = 方法名称(参数);
    ```

* 方法三要素：返回值，方法名称，参数列表

* return两个作用：结束方法执行；返回一个返回值。

* 方法注意事项：

  * 方法应该定义在类当中，不能在方法中定义方法。
  * 如果方法有返回值，必须写上return 返回值;
  * 如果方法返回值类型为void，可以不写return语句，或者写return;（此时return是结束方法的作用）
  * return 后面的值类型必须和方法的返回值类型对应
  * 一个方法中可以有多个return语句，但是必须保证一次执行只有一个会被执行到

## Jshell

* Java Shell工具（JShell）是一个用于学习Java编程语言和Java代码原型的交互式工具。JShell是一个Read-Evaluate-Print循环（REPL），它在输入时评估声明，语句和表达式，并立即显示结果。该工具从命令行运行。

## 编译器的两点优化

* 对于byte/short/char三种类型来说，如果右侧赋值数值没有超过范围，那么javac编译器将会自动隐含地为我们补上一个(byte)/(short)/(char)；如果右侧超过了左侧范围，那么会直接编译器报错
* 在给变量赋值的时候，如果右侧的表达式当中全都是常量，没有任何变量，那么编译器javac将会直接将若干个常量表达式计算得到结果

## 流程

### 顺序结构

### 选择结构

* 单if
* if ；else
* if ；else if；else if；else
* switch ；case break；default [break]
  * 最后一个break语句可以省略，但是一般不省略
  * switch后面的小括号中之只能是byte/short/char/int/String/enum枚举
  * switch语句格式可以很灵活，前后顺序可以颠倒。匹配哪一个case就从哪一个位置向下执行，直到遇到break或者整体结束为止。
  * 如果执行到某个case且该case没有break，会穿透它下面的那个case

### 循环结构

* 循环结构一般分为**四部分**：

  * 初始化语句：在循环开始最初执行，而且只执行一次
  * 条件判断：如果成立循环继续，否则循环退出
  * 循环体：重复要做的内容，若干语句
  * 步进语句：每次循环后的扫尾工作

* for语句：

  * 结构

    ```java
    for(初始化表达式;条件判断布尔表达式;步进表达式){
        循环体
    }
    ```

* while语句

  * 结构：

    ```java
    初始化语句;
    while(条件判断){
        循环体;
        步进语句;
    }
    ```

* do-while语句

  * 结构：无条件执行第一次

    ```java
    初始化语句;
    do{
        循环体;
        步进语句;
    }while(条件判断);
    ```

### 三种循环区别

* 如果条件判断从来没有满足过，那么for和while执行0次，但是do-while**至少**会执行1次
* for循环的变量定义在小括号中，那么循环内部才能使用
* 次数确定用for循环，否则多用while循环

### break关键字和continue关键字

* break用在switch中：一旦执行，整个switch立刻结束
* break用在循环中：一旦执行，break的外第一层循环语句立刻结束
* continue用在循环中：一旦执行，立刻跳过当前次循环剩余内容，马上开始下一次循环

## IDEA快捷键

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201220205132.png)

## 方法重载

* 对于功能类似的方法来说，因为参数列表不同却需要不同的方法名，太麻烦，所以引入了方法的重载。
* 方法的重载（override），**多个方法的方法名一样，但是参数列表不一样**。只需一个方法名，就可以实现多个功能
* 所谓的参数列表不一样，指的是：
  * 参数个数不同
  * 参数类型不同
  * 参数的多类型顺序不同
  * 但是和“参数名称，返回值类型，方法修饰符”无关
* Java中的典型重载：`System.out.println()`方法

## 数组

* 概念：一种容器，可以同时存放多个同类型数据

* 特定：

  * 是一种引用类型
  * 数组中的多个数据，类型必须统一
  * 数组的长度在程序运行中不可改变

* 数组的初始化：在内存中创建一个数组，并且向其中赋予一位默认值

  * 动态初始化：指定长度

    ```java
    数据类型[] 数组名称 = new 数据类型[数组长度];
    int[] arrayA = new int[300];
    String[] stringA = new String[10];
    ```

  * 静态初始化：指定内容

    ```java
    数据类型[] 数组名称 = new 数据类型[]{元素1,元素2...};
    int[] arrayB = new int[]{1,2,3,4,5};
    String[] stringA = new String[]{"Hello","World"};
    
    //静态初始化的省略格式
    数据类型[] 数组名称 = {元素1,元素2...};
    String[] stringA = {"Hello","World"};
    ```

    * **注意：**

      * 静态初始化没有直接指定长度，但是可以根据大括号里的元素的具体内容直接推算出长度

      * 静态初始化&动态初始化的标准格式可以差分为两个步骤，但是静态初始化的省略格式不能拆分。

        ```java
        int[] array1;
        array1 = new int[]{1,2,3};
        
        int[] array1;
        array1 = new int[5];
        ```


* 直接打印数组名，得到的是数组对应的内存地址哈希值

* 访问数组元素：

  ```java
  int[] arrayA = {1,3,2};
  System.out.println(arrayA[1]);
  ```

* 如果**动态初始化**数组的时候，元素会自动拥有**默认值**，规则如下：

  * 整数类型：0
  * 浮点类型：0.0
  * 字符类型：'\u0000'
  * 布尔类型：false
  * 引用类型：null

* **静态初始化也有默认值**，只是系统自动马上将默认值的替换成为了大括号当中的具体数值

* 数组经常遇到的问题：

  * 数组索引越界异常：`ArrayIndexOutOfBoundsException`

  * ```java
    //未初始化
    int[] array1;
    System.out.println(array1[0]);
    
    //空指针异常NullPointerExcetion
    int[] array2 = null;
    System.out.println(array2[0]);
    ```

  * 获取数组长度：`数组名称.length`；数组一旦创建，程序运行期间+长度不可改变

    ```java
    for(int i=0;i<array.length;i++){
        System.out.println(array[i]);
    }
    ```

* 遍历数组：就是对数组中每一个元素进行逐一处理。

* 数组作为**方法参数**和**方法返回值**：

  * 当调用方法的时候，向方法小括号传递的参数实际上是**数组的地址值**
  * 任何数据类型都能作为方法的参数类型和返回值类型，数组作为参数

  ```java
  public static void main(String[] args){
      int[] array = {10,20,30,40};
      printArray(array);//传进去的就是一个地址
      
      int[] result = calculator(10,20,30);
      System.out.println(result[0]);
      System.out.println(result[1]);
  }
  
  public static void printArray(int[] array){
      for(int i = 0;i<array.length;i++){
          System.out.println(array[i]);//地址值[i]使用
      }
  }
  
  public static int[] calculate(int a,int b,int c){
      int sum = a + b;
      int avg = sum / 3;
      int[] array = new int[2];
      array[0] = sum;
      array[1] = avg;
      return array;
  }
  ```

## Java内存模型

java类的成员变量new的时候会有初始值，但是方法中的局部变量不会有初始值。这条性质能用java的内存模型来解释。

* **栈：** 存放的是方法中的局部变量
  * 局部变量：方法的参数，方法内部的变量，局部变量有作用域，一旦超出作用域，立刻从栈内存中消失
* **堆：** 凡是new出来的东西，都在堆中
  * 堆内存中的数据都有默认值：整数类型：0；浮点类型：0.0；字符类型：'\u0000'；布尔类型：false；引用类型：null
* **方法区：** 存储.class相关信息，包含方法的信息，包括static方法&变量，类加载器classloader。
  * 注意：方法区只是包含了方法固定的相关信息，但是方法真正运行的数据都在栈中
* **本地方法栈：**：与操作系统相关
* **寄存器：** 与CPU相关

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210101180659.png" style="zoom:80%;" />

数组内存图：

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201221092337.png" style="zoom: 67%;" />

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201221092337.png" style="zoom:67%;" />

### 使用对象类型作为方法的参数&返回值

* 当一个对象or数组作为参数，传递到方法中时，实际上传递的是对象or数组的地址值
* 当一个对象or数组作为返回值时，实际上返回的是对象or数组的地址值

### 局部变量 and 成员变量

|          | 定义位置 | 作用域 | 默认值     | 位置 | 生命周期            |
| -------- | -------- | ------ | ---------- | ---- | ------------------- |
| 局部变量 | 方法内   | 方法内 | 没有       | 栈   | 同方法栈的诞生&消失 |
| 成员变量 | 方法外   | 类中   | 有(同数组) | 堆   | 同对象的诞生&消失   |



## 面向对象思想

* 面向过程：当需要实现一个功能时，每一个具体的步骤都要亲力亲为，详细处理每一个细节
* 面向对象：当需要实现一个功能时，不关心具体步骤，而是找一个能实现该功能的人帮我做事，帮你做事的这个人就是对象。

```java
int[] array = {10,20,30,40,50};
//希望能把数组打印为[10,20,30,40,50]
//面向过程
System.out.println("[");
for(int i = 0;i<array.length;i++){
    if(i == array.length-1){
        System.out.println(array[i]+"]");
    }
    else{
        System.out.println(array[i]+",");
    }
}
//面向对象
System.out.println(Arrays.toString(array));
```

* **类是对象的模板，对象是类的实例**

* **类：**

  * 成员变量（属性）：直接定义在类中方法之外

  * 成员方法（行为）：普通方法有static，成员方法灭有static

  * 通常类不能直接使用，需要根据类创建一个对象

    1. 导包：`import 包名.类名`，如果两个类同属一个包，可以不import
    2. 创建：`类名称 对象名 = new 类名称()`
    3. 使用：`对象名.成员变量名`，`成员方法名()`

  * new一个对象时，如果成员变量没有赋值会有一个默认值，规则和数组一样

  * 一个对象的内存区：

    * 对于堆中的一个对象来说，他的成员方法保存的实际上是一个地址值，指向方法区中的成员方法内容

  * 一个对象的内存图：

    ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210101180659.png)

  * 两个对象使用同一个方法的内存图：自己思考

  * 两个引用指向同一个对象的内存图：自己思考

### 面向对象三大特征

* 封装
* 继承
* 多态

## 封装性

* 方法就是一种封装：将不同功能模块提取出来单独成一个方法，可以说成面向方法编程
* 关键字private也是一种封装：使用private修饰成员变量和方法。本类中可以随意访问，但是超出了本类无法**直接**访问。可通过public方法访问（例如setter和getter）

含义：将一些细节信息隐藏起来，对外界不可见。

### this关键字

方法中的局部变量和类中的成员变量从名的时候，根据“就近原则”，优先局部变量。如果方法中重名了，并且需要本类中的成员变量，需要加this关键字。

**重点：通过谁调用的方法，谁就是this**

### 构造方法

专门用来创建对象的方法，我们通过关键字new来创建对象的时候，其实就是调用了构造方法。

* 构造方法名称需要和类的名称一模一样
* 构造方法没有返回值，不能return，方法前连void都不要写
* 如果没有编写任何构造方法，编译器会默认一个什么都不做的构造方法。如果自己写了一个有参构造函数，最好加一个无参构造函数，因为编译器不会赠送。
* 一旦编写了至少一个构造方法，编译器不会赠送构造方法了
* 构造方法可以进行重载
  * 重载：方法名相同，参数不同

### 标准的类（JavaBean）

* 所有成员变量都使用private关键字修饰
* 为每一个成员变量编写一对 Getter和Setter 方法
* 编写一个无参构造方法
* 编写一个全参构造方法

### 几个重要的Java API

* 导包：import 包路径.类名称（同包可不导，java.lang下可不导）
* 创建：类名称 对象名 = new 类名称();
* 使用：对象名.成员方法名();

#### Scanner

````java
import java.util.Scanner;
Scanner sc = new Scanner(System.in);//从键盘输入
int a = sc.nextInt();//获取从键盘输入的int变量
String str = sc.next();//获取从键盘输入的字符串变量
````

#### Random

```java
import java.util.Random;
    
Random r = new Random();//随机数种子
int num1 = r.nextInt();
int num2 = r.nextInt(3);//范围：左闭右开[0,3)
```

#### ArrayList

```java
//泛型只能是引用类型，不能是基本类型
ArrayList<String> list = new ArrayList<>();
//常用方法
boolean isAdd = list.add("Sam Smith");
list.add("Mariah Carey");
list.add("Colbie Caillat");
String name = list.get(0);
String delName = list.remove(1);//返回值是删除的元素
int size = list.size();
//ArrayList保存基本类型包装类
//Byte,Short,Integer,Long,Float,Double,Character,Boolean
ArrayList<Integer> list = new ArrayList<>();
list.add(1);//从jdk1.5后，支持自动装箱/自动拆箱
int i0 = list.get(0);
```

#### String：字符串

* 字符串内容不可变
* 存在字符串常量池中的字符串可共享
* 字符串效果上相当于char[]字符数组

```java
//创建字符串
String s1 = new String();
String s2 = new String(char[] array);
String s3 = new String(byte[] array);
String s4 = "hahahahaha";//在字符串常量池中
```

![image-20210301121309678](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgDqkKswfeu3PhVQ8.png)

```java
//常用方法
//equals() & equalsIgnoreCase()
String s1 = "hello";
String s2 = new String("hello");
System.out.println(s1==s2);
System.out.println(s1.equals(s2));
System.out.pringln(s1.equalsIgnoreCase("Hello"));
//length()
int l = s1.length();
//concat()
String s3 = s1.concat(",world!");//返回新的字符串，字符串是常量不变
//charAt()
char ch = s1.charAt(1);
//indexOf()
int index = s1.indexOf("ll");
//subString():截取
String s4 = s3.subString(6);//world
String s5 = s3.subString(0,5);//hello
//toCharArray():转换为char数组
char[] chars = "Hello".toCharArray();
//getBytes():转换为byte数组
byte[] bytes = "Hello".getBytes();
//replace():替代
String str1 = "What's the matter with you,fuck U!";
String str2 = str1.replace("fuck","***");
//split():分割
String str3 = "aaa,bbb,ccc";
String[] array1 = str3.split(",");//参数是正则表达式
```

* **注意：String的getBytes()方法将String转化为字节使用的编码是当前.java文件的编码。**

#### 工具类Arrays，Math

```java
//Arrays
import java.util.Arrays;
//toString():以字符串的形式打印数组
int[] intArray = {2,3,1,4,5};
String intStr = Arrays.toString(intArray);
//sort():如果排序自定义类型数组，该自定义类必须实现Comparable or Comparator接口
Arrays.sort(intArray);//会直接修改intArray数组
```

```java
//Math
import java.util.Math;
//abs()
System.out.println(Math.abs(-3.14));
//ceil():向上取整
System.out.println(Math.ceil(3.14));
//floor():向下取整
System.out.println(Math.floor(3.14));
//round():四舍五入
System.out.println(Math.round(3.14));
//Math.PI:近似圆周率
```

### 其他知识点：

#### 匿名对象

* 格式：new 类名称();

* 确定某个对象只需用使用一次，那么可以使用匿名对象，之后会被自动回收。

* 匿名对象可以作为 参数 & 返回值

* ```java
  new Person().name = "Mariah Carey";
  ```

#### static关键字

* 一个成员变量 or 成员方法 有了static关键字，就是属于类的，本类所有对象共享同一份

* 静态成员方法只能访问静态成员变量（因为静态方法是class初始化就建立在了内存的方法区，此时对象中的普通成员变量可能还没有在内存的堆中建立）

* 静态方法中不能用 this 关键字，**static和类相关，和对象无关**

* 静态代码块

  比构造方法先执行，并且只执行一次

  用来一次性对静态成员变量进行赋值

  ```java
  static{
      //内容
  }
  ```

## 继承性

* 子类和父类关系是 “子类 is a 父类” 的关系

* ```java
  public class Employee{
      private int name;
      private int age;
  }
  public class Teacher extends Employee{
      private int level;
  }
  ```

* 父子类继承关系中，如果成员变量重名，创建子类对象时，有两种方式

  1. 直接通过**子类对象**访问成员变量：等号左边是谁，就优先用谁，没有就向上找
  2. 间接通过**成员方法**访问成员变量：方法属于谁就优先用谁的，没有就向上找

* **重写特点：**（方法重写/覆盖：方法名，参数相同）

  1. 父子类的方法重名，访问成员变量的规则：new创建的对象是谁，就优先用谁，没有就往上找

  2. `@override`注解用来检测是否覆盖正确

  3. 子类方法的返回值类 必须 低于等于 父类方法的返回值类

  4. 子类方法的权限修饰符 必须 大于等于 父类方法的权限修饰符

     * public > protected > (default) > private

  5. 设计原则：对于已经投入使用的类，不要改动它。定义一个新类继承老类重复利用父类共性并添加新的改动

     ```java
     public class Phone{
         public void call(){
             System.out.println("打电话");
         }
         public void send(){
             System.out.println("发短信");
         }
         public void show(){
             System.out.println("显示号码");
         }
     }
     public class NewPhone extends Phone{
         public void snow(){
             super.show();
             System.out.println("显示姓名");
             System.out.println("显示头像");
         }
     }
     ```

  6. 子类构造方法中的**第一个语句**是一个赠送的`super()`，但是不会赠送有参父类构造，需要自己显式声明`super(参数)`来调用父类重载构造

* **super关键字** 三种用法：

  1. 子类成员方法中，访问父类成员变量
  2. 子类成员方法中，访问父类成员方法
  3. 子类构造方法中，访问父类构造方法

* **this关键字** 三种用法：

  1. 本类成员方法中，访问本类成员变量
  2. 本类成员方法中，访问本类成员方法
  3. 本类构造方法中，访问本类另一个构造方法

* **this 和 super **不能同时存在于一个构造方法中

![image-20210301194433254](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223227.png)

* Java语言是单继承，多级继承的

### 抽象类 & 抽象方法

```java
public abstract class Animal{
    //抽象方法所在的类必须是抽象类
    public abstract void eat();//抽象方法
    public void normalMethod(){//普通方法
    }
}
```

* 不能直接new抽象类对象。但是抽象类中可以有构造函数，用来供子类对象创建时初始化父类成员变量
* 必须用子类继承抽象类 并 覆盖重写所有的抽象方法

```java
public class Cat extends Animal{
    public void eat(){
        System.out.println("cat eat fish.");
    }
}
```

* 抽象类中不一定包含抽象方法，但是有抽象方法的类一定是抽象类
* 抽象类的子类必须重写父类中的抽象方法，除非子类也是抽象类

### 接口类

* 接口类中，不同jdk版本能包含的内容不同

  1. jdk7：常量，抽象方法

     ```java
     public interface MyInterface{
         //常量：public,static,final是固定的,可以省略
         public static final int NUM_OF_MY_CLASS  = 10;//必须赋值
         //public和abstract是固定的,可以省略
         public abstract void methodAbs();
     }
     public class MyInterfaceImpl implements MyInterface{
         @Override
         public void methodAbs(){
             System.out.println("first method");
         }
     }
     ```

  2. jdk8：默认方法，静态方法

     ```java
     //默认方法：实现类不必实现，因为接口中的默认方法有实现
     public interface MyInterface{
         public default void methodAbs(){
             System.out.println("默认方法");
         }
         //不能通过接口实现类对象来调用静态方法：因为一个类可以实现多个接口。应该通过接口名调用
         public static void methodAbs2(){
             System.out.println("静态方法");
         }
     }
     ```

  3. jdk9：私有方法

     需要解决的问题：需要一个共有方法来解决两个默认方法之间的重复代码问题，但是这个共有方法不该让实现类实现，应该是私有化的。

     1. 普通私有方法：解决多个默认方法之间的重复代码问题
   2. 静态私有方法：解决多个静态方法之间的重复代码问题

  ![image-20210301224807631](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgyjF51ITcwOpZ4SK.png)

  ![image-20210301225536727](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223338.png)

* **接口-接口：多继承**

  类-类：单继承

  类-接口：多实现

## 多态性

* 继承是多态的前提：学生既是学生，也是人，同事拥有多种形态

* ```java
  父类名称 对象名 = new 子类名称();
  接口名称 对象名 = new 实现类名称();
  ```

* 注意：继承 or 实现中，成员方法能覆盖重写，但是成员变量不能。所以：

  ```java
  Fu obj = new Zi();
  obj.成员变量;//看等号左边是谁就访问谁的成员变量，没有就向上找（编译看左边，运行还看左边）
  obj.成员方法();//new的谁就优先用谁的成员方法，没有就向上找（编译看左，运行看右）
  ```

### 对象向上转型

  * 向上转型一定是安全的
  * 但是一旦上转为父类，就无法调用子类特有的方法

  ```java
  //创建一只猫，当做动物看待
  Animal animal = new Cat();
  //类似于自动类型转化(范围大<---范围小)
  double num = 100;
  ```

### 对象向下转型

* 上转后，如果需要调用子类特有的方法，需要再原路下转
* 可以使用instanceof判断类的所属

  ```java
  //上转后，还原为猫
  Animal animal = new Cat();
  Cat cat  = (Cat)animal;
  //类似于强制类型转换(范围小<---范围大)
  int num = (int)10.0;
  //只能原路还原，如果把animal还原为狗，会报错ClassCastException,就像强制类型转换的精度损失
  ```

### final关键字：“断子绝孙”关键字

1. 可以修饰一个类：不能有任何子类

   ```java
   public final class MyClass {}//和abstract关键字矛盾
   ```

2. 可以修饰一个方法：不能覆盖重写

   ```java
   public final void method(){}//和abstract关键字矛盾
   ```

3. 可以修饰一个局部变量

   ```java
   final int num = 10;//常量
   final int num;num=10;//也可以
   //对基本数据类型来说，是变量的数据不可改变
   //对于引用数据类型来说，是引用的数据不可改变，即是引用所指向的地址不可改变
   ```

4. 可以修饰一个成员变量

   ```java
   //和局部变量的区别是成员变量有默认值，所以一旦是final的成员变量，如果不第一时间初始化，他就只能是默认值常量
   /**
   final成员变量赋值的两种方式
   	1. 直接赋值
   	2. 构造方法赋值
   **/
   ```

### 4种权限修饰符 (public > protected > (default) > private)

|              | public | protected | (default) | private |
| ------------ | ------ | --------- | --------- | ------- |
| 同一个类     | √      | √         | √         | √       |
| 同一个包     | √      | √         | √         | ×       |
| 不同包子类   | √      | √         | ×         | ×       |
| 不同包非子类 | √      | ×         | ×         | ×       |

### 内部类

#### 成员内部类

* 内用外，随意访问；外用内，需要内部类对象

```java
public class Body{
    public class Heart{//成员内部类
        public void beat(){
            System.out.println("内部类方法");
            System.out.println(name);//内用外
        }
    }
    private String name;
    public void methodBody{
        new Heart().beat();//外用内
        System.out.println("外部类方法");
    }
}
public class MainTest(){
    public static void main(){
        Body body = new Body();
        
        body.methodBody();//间接使用成员内部类
        
        Body.Heart heart = new Body().new Heart();//直接使用成员内部类
        heart.beat();
    }
}
```

```java
//内部类成员变量重名情况
public class Outer{
    int num = 10;
    public class Inner{
        int num = 20;
        public void methodInner(){
            int num = 30;
            System.out.println(num);//30
            System.out.println(this.num);//20
            System.out.println(Outer.this.num);//10
        }
    }
}
```

#### 局部内部类

* 只有当前所属的方法内部才能使用
* 从jdk8开始，只要局部内部类的局部变量事实不变，那么final关键字可以省略。

```java
public class Outer{
    public void methodOuter(){
        class Inner{//局部内部类
            int num = 10;//jdk8后,final关键字可省略
            public void methodInner(){
                System.out.println(num);
            }
        }
        Inner inner = new Inner();
        inner.methodInner();
    }
}
public class MainTest(){
    public static void main(){
        Outer outer = new Outer();
        outer.methodOuter();
    }
}
```

##### 匿名内部类 

* 如果接口的实现类 or 父类的继承类 只需要使用**唯一的一次**，那么可以省略该类的定义，直接使用【匿名内部类】
* 匿名内部类是省略了【单独实现子类】，匿名对象是省略了【对象引用名称】

```java
public interface MyInterface{
    void method();//抽象方法
}
public class MainTest(){
    public static void main(){
        MyInterface obj = new MyInterface(){
            @Override
            public void method(){
                System.out.println("匿名内部类实现了方法");
            }
        };
        obj.method();
        MyInterface obj2 = new MyInterface(){
            @Override
            public void method(){
                System.out.println("匿名内部类实现了方法");
            }
        }.method();//匿名内部类的匿名对象
    }
}
```



#### 小结

外部类权限：public任何包都可以访问 / (default同一个包可以访问)

成员内部类权限：所有都可以

局部内部类权限：什么都不能写
