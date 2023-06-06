
## Lambda表达式

* 匿名内部类可以省去定义实现类，但是语法太复杂
* lambda表达式不关注面向对象，返璞归真，仅仅关注内容本身。

```java
//一些参数，一个箭头，一段重写代码
() -> System.out.println("lambda");
```

* 将Runnable接口作为参数进行传递，可以使用两种方法：

![image-20210309120501501](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223437.png)

* 使用lambda必须满足接口中有且仅有一个抽象方法
* 有且仅有一个抽象方法的接口称为 “函数式接口”
* Lambda的省略写法有如下要求：
  1. 括号中参数列表的数据类型可以省略
  2. 括号中的参数只有一个，那么类型和（）都可以省略
  3. 如果{}的代码只有一行，都可以省略{} return 分号，且必须要一起省略，不能只省略部分

## FIle类

* **文件和目录** 的抽象表示，用于文件&目录的创建、查找和删除等操作

```java
//File类的4个成员变量
//路径分隔符(windows分号,linux冒号)
static String pathSeparator;
static char pathSeparatorChar;
//默认名称分隔符(windows反斜杠，linux正斜杠)
static String separator;
static char separator;
//注意：操作路径的时候，路径不能写死了，根据不同系统更换
```

* File构造函数

```java
/**
	File(String pathname);
	File(String parent,String child);
	File(File parent,String child)
	路径可以是文件名，也可以是文件夹名
	可以相对路径，也可以绝对路径
	路径可以是存在的，也可以是不存在的
	创建File对象，只是把字符串封装为File对象，不考虑路径的真假情况
**/
public static void main(){
    File f1 = new File("/home/jack1024/桌面/a.txt");
    File f2 = new File("/home/jack1024/桌面/a");
}
```

* File类常用方法：

```java
public String getAbsolutePath();//文件/目录绝对路径名字符串（无论构造方法中传递相对还是绝对，都返回绝对路径）
public String getPath();//文件/目录路径名字符串（构造方法中传递的字符串）
public String getName();//文件/目录名字
public long length();//文件字节大小(目录没有大小概念)
```

* 判断功能：

```java
public boolean exists();//该文件/目录是否存在
public boolean isDirectory();//是否为目录
public boolean isFile();//是否为文件
```

* 创建 & 删除

```java
public boolean createNewFile();//当该名字的文件不存在时，创建一个新的空文件
public boolean delete();//删除由此File表示的文件或目录，不走回收站
public boolean mkdir();//创建由此File表示的目录
public boolean mkdirs();//创建由此File表示的目录，包括任何必须但不存在的父目录
```

* 目录遍历
  * 包含隐藏文件/文件夹

```java
public String[] list();//返回一个String数组，表示该File目录中所有子文件或目录
public File[] listFiles();//返回一个File数组，表示该File目录中所有子文件或目录
```

### 递归

* 直接递归 & 间接递归
* 递归一定要有停止的条件约束，否则会出现栈内存溢出StackOverFlowError
* 递归的次数也不能太多，否则也可能发生栈内存溢出
* 构造方法中禁止递归

递归图示：

![image-20210310002114259](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223505.png)

### 文件过滤器

```java
public File[] listFiles(FileFilter filter);
public File[] listFiles(FilenameFilter filter);
```

通过重写accept()方法来实现

## IO流

![image-20210310010209212](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223522.png)

### 字节流

#### 字节输出流

* OutputStream：所有输出字节流的超类，接下来主要探讨其子类FileOutputStream：
  1. close()：释放资源
  2. flush()：刷新此输出流并强制写出所有缓冲的输出字节
  3. void write(byte[] b)：往文件写入多个字节
  4. void write(int b)：往文件写入一个字节
  5. void write(byte[] b,int off,int len)：往文件写入指定位置指定长度的放入的数组中的字节
* FileOutputStream构造方法：
  1. FileOutputStream(File file)
  2. FileOutputStream(String name)
  3. FileOutputStream(File file, boolean append)
  4. FileOutputStream(String name, boolean append) 

#### 字节输入流

* InputStream：表示字节输入流的所有子类超类。接下来主要探讨其子类FileInputStream：
  1. close()：释放资源
  2. int read(byte[] b)：从文件读取一定数量的字节，并将其存在缓冲区数组b中
  3. int read()：从文件读取一个字节
* FileInputStream构造方法：
  1. FileInputStream(File file)
  2. FileInputStream(String name)

```java
int len = 0;
while((len=fis.read())!=-1){
    System.out.println((char)len);
}
      
byte[] bytes = new byte[1024];
while((len = bis.read(bytes)) != -1){
    bos.write(bytes,0,len);
}
```

### 字符流

#### 字符输入流

* Reader：是一个抽象类

  1. int read()
  2. int read(char[] cbuf)
  3. void close()

* FileReader：文件字符输入流

  构造方法：

  1. FileReader(File file)
  2. FileReader(String name)

#### 字符输出流

* Writer：是一个抽象类
  1. void write(int c)：写入单个字符
  2. void write(char[] cbuf)：写入字符数组
  3. abstract void write(char[] cbuf,int off,int len)：写入字符数组的某一部分，off表示开始索引，len表示写的字符个数
  4. void write(String str)：写入字符串
  5. void write(String str, int off, int len)：写入字符串的某一部分
  6. void flush()：刷新流的缓冲
  7. void close()：刷新缓冲后关闭此流
* 字符输出流和字节输出流的最大的区别就是字符输出流要将内容先写到内存缓冲区中（进行 字符——>字节 过程），所以之后必须进行flush或者close。

* FileWriter构造方法：
  1. FileWriter(File file)
  2. FileWriter(String name)
  3. FileWriter(File file, boolean append)
  4. FileWriter(String name, boolean append) 

* jdk1.7之前进行异常处理的过程

```java
try{
    //可能产生异常的代码
}
catch(异常类参数对象){
    //异常的处理逻辑
}
finally{
    //资源释放
}
```

* jdk1.7后中进行流异常处理的过程

```java
try(定义流对象){//流对象使用完毕自动释放
    //可能产生异常的代码
}
catch(){
    //异常的处理逻辑
}
```

### 属性集Properties

* 唯一一个和io流结合的集合
* store：集合中数据持久化到硬盘存储
* load：硬盘中的键值对读取到集合使用
* Properties是一个双列集合，key和value都默认是字符串
* setProperty方法相当于map中的put方法
* getProperty方法相当于map中的set方法
* stringPropertyNames方法相当于map中的keySet方法

```java
FileWriter fw = new FileWriter("/home/jack1024/a.txt");
Proterties prop = new Properties();
prop.setProperty("aa","11");
prop.setProperty("bb","22");
prop.store(fw,"save data");
fw.close;

prop.load(fw);
Set<String> set = prop.stringPropertyNames();
for(String key : set){
    String value = prop.getProperty(key+"="+value);
    System.out.println(key);
}
```

* properties文件中可以使用等号或者空格来分隔键值对
* 对中文进行处理的时候必须使用字符输入/出流

### 缓冲流

* 增强基本流（普通流都是一个一个地传，效率低下）

* 给基本字节输入输出流增加一个缓冲区，提高传输速率

* BufferedInputStream(InputStream fis)

  BufferedInputStream(InputStream fis, int size)

* BufferedOutputStream(OutputStream fos)

  BufferedOutputStream(OutputStream fos, int size)

* BufferedWriter(Writer fw)

  BufferedWriter(Writer fw, int size)

* BufferedReader(Reader fr)

  BufferedReader(Reader fr, int size)

* **写和读的方法和基本流一致**

  1. BufferedWriter有一个特有的成员方法void newLine：写入一个行分隔符(不同操作系统统一一个方法)
  2. BufferReader有一个特有的成员方法String readLine：读取一行数据(不同操作系统统一一个方法)

测试复制文件的效率：

```java
public class Test {
    public static void main(String[] args) throws IOException {
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入需要复制的文件路径:");
        String sourcePath = scanner.next();
        System.out.println("请输入粘贴的路径:");
        String targetPath = scanner.next();

        long t1 = System.currentTimeMillis();
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream(sourcePath));
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(targetPath));

        int len = 0;
        while((len = bis.read()) != -1){
            bos.write(len);
        }
//        byte[] bytes = new byte[1024];
//        while((len = bis.read(bytes)) != -1){
//            bos.write(bytes,0,len);
//        }

        bis.close();
        bos.close();
        long t2 = System.currentTimeMillis();
        long sumT = t2-t1;
        System.out.println("复制过程总共耗时:"+sumT+"毫秒");
    }
```

### 转换流

* 编码：字符——>字节
* 解码：字节——>字符
* 制定了编码，字节文件对应的字符集也就确定了，每个软件也就能根据软件功能转化字节为对应的字符供我们查看了
* 常见的字符集有ASCII字符集，GBK字符集，Unicode字符集
* FileReader只能读取系统默认编码utf-8格式的文件，如果读取GBK格式的文件，就会产生乱码
* 引入转换流来解决：InputStreamReader和OutputStreamWriter是字节流和字符流的桥梁，可以指定编码表

![image-20210311100154292](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223541.png)

* InputStreamReader(InputStream fis) //默认编码

  InputStreamReader(InputStream fis, String charsetName) //指定编码

* OutputStreamWriter(OutputStream fos)

  OutputStreamWriter(OutputStream fos, String charsetName)

```java
OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("/home/jack1024/a.txt","GBK"));
osw.write("你好");
osw.close();

InputStreamReader isr = new InputStreamReader(new FileInputStream("/home/jack1024/a.txt","GBK"));
int len = 0;
while((len = isr.read())!=-1){
    System.out.print((char)len);
}
isr.close();
```

### 序列化 & 反序列化流

把对象以字节的形式写入到文件中存储 并 从文件中读取对象到内存中继续使用，即是**对象持久化**

* ObjectOutputStream(OutputStream os)：对象的序列化流
  * void writeObject(Object obj)
* ObjectInputStream(IutputStream is)：对象的反序列化流
  * Object readObject()
* 要对对象进行序列化 or 反序列化，必须在类中实现**Serializable**接口

* 序列化的内容都是对象的内容，所以static的变量不能被序列化。如果想让一个成员变量不被序列化但是又没有static的含义，可以使用transient关键字声明变量
* 如果一个类实现了Serializable接口，编译后的.class文件中会带有一个序列号。序列化时，会将这个序号一同写入文件；**当反序列化时，会将文件的序列号和.class文件中的序列号作对比**，如果不同会抛**InvalidClassException**异常。（每次对类修改后都会重新生成一个序列号，为了避免异常，我们可以手动给Serializable实现类加上一个序列号）

```java
static final long serivalVersionUID = 42L;
```

### 打印流

* PrintStream：为其他输出流添加功能，使他们能方便打印各种数据值的表示形式
  1. print()
  2. pringln()
  3. 继承自OutputStream的方法（如果使用write方法会查询编码表97->a；如果使用println or print方法会原样输出）
* 构造方法：
  1. PrintStream(File file)
  2. PrintStream(OutputStream out)
  3. PrintStream(String fileName)

```java
PrintStream ps = new PrintStream("/home/jack1024/a.txt");
ps.write(97);//转化为字节
ps.println(97);//原样打印到文件
ps.close;
```

```java
System.setOut(ps);//改变输出语句的目的地为ps的目的地
System.out.println("haha");//会将haha字符串写到/home/jack1024/a.txt中

```

## Java网络编程

TCP/IP协议是Internet中最基本，最广泛的协议，他定义了计算机计算机如何进因特网，数据如何在计算机间传输数据。它的内部包含一系列用于处理数据通信的协议

* UDP协议：无连接，效率更高，不安全
* TCP协议：三次握手建立连接，四次挥手解除连接，效率较低，安全

网络编程三要素：

1. 协议
2. IP地址：ipv4(32位：8*4)，ipv6(128位：16*8)，连接两台主机
3. 端口：两个字节(0—1024—65535)，连接两台主机的两个进程

### TCP通信程序

* 客户端主动请求服务端建立连接，连接中包含一个IO对象，这个对象只能是字节流对象

![image-20210311113110227](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223550.png)

**代码实现：**

**客户端**

![image-20210311113550491](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223558.png)

**服务器**

![image-20210311115105384](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223606.png)

```java
//客户端
public class TcpClient {
	public static void main(String[] args) throws IOException {
		Socket socket = new Socket("127.0.0.1", 8888);
		//往服务器写
		OutputStream os = socket.getOutputStream();
		os.write("你好".getBytes());
		//从服务端拿
		InputStream is = socket.getInputStream();
		byte[] bytes = new byte[1024];
		int len = is.read(bytes);
		System.out.println(new String(bytes,0,len));

		socket.close();
	}
}
//服务端
public class TcpServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(8888);
        Socket accept = serverSocket.accept();
        InputStream is = accept.getInputStream();
        OutputStream os = accept.getOutputStream();

        byte[] bytes = new byte[1024];
        int len = is.read(bytes);
        System.out.println(new String(bytes,0,len));

        os.write("收到了".getBytes());
        accept.close();
        serverSocket.close();
    }
}
```

### TCP文件上传案例

![image-20210311142418192](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223615.png)

### 模拟B/S通信案例



## 函数式编程

* 函数式接口：有且仅有一个抽象方法的接口，但是可以包含其他方法（默认，静态，私有）

```java
@FunctionalInterface
修饰符 interface 接口名称{
    public abstract 返回值类型 方法名称(可选参数信息){
        
    }
}
```

* lambda是延迟加载的，可以提升部分代码的性能。也就是说只有满足执行lambda中方法的条件，才会执行方法中的内容，否则不会执行。
* 函数式接口作为方法的参数，我们可以用lambda表达式 或者 匿名内部类 作为实参
* 函数式接口作为方法的返回值，我们可以返回这个接口的匿名内部类 或者 lambda表达式

### 常用的函数式接口

```java
//java.util.funciton.Supplier<T>
public static getString(Supplier<String> sup){
    return sup.get();//用来返回一个指定类型的数据对象
}

public static void main(String[] args){
    String s = getString(()->{
        return "Sam Smith";
    });
}
```

```java
//java.util.function.Consumer<T>
public static void method(String name,Consumer<String> con){
    con.accept(name);//用来消费name字符串
}

public static void main(String[] args){
    method("Mariah Carey",(String name)->{
        //对传递的字符串进行处理消费
        System.out.println(name);
    })
}
//andThen()默认方法，连续组合多个Consumer的accept方法
```

```java
//java.util.function.Predicate<T>
public static boolean checkString(String s,Predicate<String> pre){
    return pre.test(s);//对传入的参数进行判断，返回真假
}

public static void main(String[] args){
    String s = "abcde";
    boolean b = checkString(s,()->{
        return s.length()>5;
    });
}
//Predicate的and()，or()默认方法可以用来连接两个判断条件
//negate()默认方法可以对一个判断条件取反
```

```java
//java.util.function.Function<T,R>
public static void change(String s,Function<String,Integer> fun){
    Integer in = fun.apply(s);
}

public staic void main(String[] args){
    String s = "123";
    change(s,(String s)->{//将一个值从原类型转化为另一个类型
    	return Ingeter.parseInt(s);    
    });
}
//andThen()默认方法，连续组合多个Function的apply方法
```

## Stream流式编程

* **Stream和io流** 不能说差不多，只能说是**毫无关系**
* 极大地简化了对集合进行过滤 & 遍历的操作

```java
list.stream()
    .filter(name->name.startWith("杨"))
    .filter(name->name.length==3)
    .foreach(name->System.out.println(name));
```

* **流式操作过程：**
  1. 获取数据源(集合,数组)
  2. 数据转换(把数据源转化为Stream流)
  3. 执行操作获取想要的结果

### 获得Stream流对象

1. 所有collection集合可通过stream()方法获得
2. Stream接口有一个static方法of()，可以把数组转化为stream流

### Stream对象的方法

* stream属于管道流，只能使用一次，第一个Stream流调用完毕，数据会流到下一个Stream流身上，此时第一个Stream流就不能再调用方法了

#### 延迟方法

* 返回值类型仍然是Stream接口自身类型的方法，支持链式调用
* 每个流 流到下一步，上一步的流就关闭了，这个要注意，这也是为什么叫流，它是一个动态的过程

**filter方法**

```java
//Stream<T> filter(Predicate<? super T> predicate)
Stream<String> stream = Stream.of("Jack","Sam","Tom");
Stream<String> stream2 = stream.filter(name->{
    reuturn name.startWith("J");
});
```

**map方法**

```java
//Stream<R> map(Function<? super T, ? extends R> mapper)
Stream<String> stream = Stream.of("1","2","3");
Stream<Integer> stream2 = stream.map(String s->{
    reuturn Integer.parse(s);
});
```

**limit方法**

```java
//Stream<T> limit(long maxSize)
Stream<String> stream = Stream.of("哈哈","嘿嘿","咚咚");
//只取前两个元素
stream.limit(2).forEach(name->System.out.println(name));
```

**skip方法**

```java
//Stream<T> skip(long n)：跳过前n个元素
Stream<String> stream = Stream.of("哈哈","嘿嘿","咚咚");
//只取前两个元素
stream.skip(2).forEach(name->System.out.println(name));
```

**concat方法**

```java
//static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b)
//两个流合并为一个新的流
Stream<String> stream1 = Stream.of("1","2","3");
Stream<String> stream2 = Stream.of("a","b","c");
Stream<String> concat = Stream.concat(stream1,stream2);
concat.forEach(name->System.out.println(name));
```

#### 终结方法

* 返回值类型不再是Stream接口自身类型的方法，不再支持链式调用

**count方法**

```java
//long count():统计个数
ArrayList<Integer> list = new ArrayList<>();
list.add(1);
list.add(2);
list.add(3);
list.add(4);
Stream<Ingeter> stream = list.stream();
long count = stream.count();
System.out.println(count);
```

**forEach方法**

```java
//void forEach(Consumer<? super T> con)
Stream<String> stream = Stream.of("Jack","Sam","Tom");
stream.forEach(name->System.out.println(name));
```



## 方法引用

* 虽然lambda已经很简单了，但是方法引用还可以堆lambda表达式继续简化

```java
name->System.out.println(name);//lambda表达式
System.out::println;//方法引用
```

几种情况：

1. 通过对象名引用成员方法
2. 通过类名引用静态方法
3. 通过super引用父类成员方法
4. 通过this引用本类成员方法
5. 构造方法的方法引用
6. 数组的构造器引用



## Junit单元测试

* 黑盒测试：不需要关注代码，只需要看输入是否能得到输出
* 白盒测试：通过代码来测试程序的流程正确性（Junit属于白盒测试）

```java
//定义测试类
/**几个建议
	1.类名：被测试类名Test
	2.包名：XXX.XXX.test
	3.方法名：test被测试的方法名
	4.返回值：void
	5.参数列表：空参
**/
```

```java
//之前需要导入junit依赖
public CaculateTest{
    public void testAdd(){
        int result = 1+2;
        Assert.assertEquals(3,result)
    }
}
```

@Before和@After注解

```java
//所有测试方法前和后都会执行的两个注解@Before和@After
@Before
public void init(){
    
}
@After
public void close(){
    
}
```

## 反射

> 查看之前写的一片博客《java-反射》

## 注解

> 查看之前写的一片博客《java-注解》