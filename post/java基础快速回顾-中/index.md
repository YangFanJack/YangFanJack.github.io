
## Java某些类

### Object类

* java的根类

#### toString()

* Object类的toString()方法默认返回该对象实现类的“类名+@+hashcode”值
* 看一个类是否重写了toString方法，可以通过直接打印对象名字，没有重写的显示对象地址值，重写的按照toString重写内容自定义打印

#### equals()

* Object类的equals()方法默认就是用==来进行比较

```java
public class Person{
    public String name;
    public int age;
    @Override
    public boolean equals(Object obj){
        Person p = (Person)obj;
        return this.name.equals(p.name) && this.age==p.age;
    }
}
public class Test{
    public static void main(String[] args){
        Person p1 = new Person();
        p1.name = "jack";
        p1.age = 10;
        Person p2 = new Person();
        p2.name = "sam";
        p2.age = 14;
        p1.equals(p2);
    }
}
```

### Objects类

* Objectsq工具类提供了一些静态方法来操作对象，这些方法是空指针安全，容忍空指针的

```java
String s1 = null;
String s2 = "jack";
System.out.println(s1.equals(s2));//会报空指针异常
System.out.println(Objects.equals(s1,s2));//一切正常
```

### Date类

* 毫秒是日期计算的最小单位
* 0毫秒点：1970年1月1日00:00:00（中国属于东八区，会+8小时）
* 日期转化为时间戳，就是距离0毫秒点经过了多少毫秒

```java
import java.util.Date;
Date date = new Date();//获取系统当前日期时间
Date date = new Date(long ms);//时间戳转化为日期时间
long ms = date.getTime();//把日期转化为毫秒时间戳
```

### DateFormat类

* 根据自定义格式相互转化Date & String

```java
//是抽象类，无法直接使用
import java.text.DateFormat;
//使用SimpleDateFormat
import java.text.SimpleDateFormat;
//参数是格式(y年，M月，d日，H时，m分，s秒)
DateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//两个方法：format(Date d)和parse(String s)
DateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
Date date = new Date();
String text = sdf.format(date);//Date——>String
Date date2 = sdf.parse(text);//String——>Date，这是一个抛异常的方
```

### Calendar类

```java
//是抽象类，无法直接使用
import java.util.Calendar;
//获得Calendar类的子类对象
Calendar c = Calendar.getInstance();
int y = c.get(Calendar.YEAR);
int m = c.get(Calendar.MONTH);//0——11
int d = c.get(Calendar.Date);
int h = c.get(Calendar.HOUR);
int m = c.get(Calendar.MINUTE);
int s = c.get(Calendar.SECOND);

c.set(Calendar.YEAR,1999);
c.add(Calendar.YEAR,2);//2001
c.add(Calendar.YEAR,-4);//1997

Date date = c.getTime();//Calendar——>Date
```

### System类

```java
long s = System.currentTimeMillis();//一般用来测试程序执行时间
//arraycopy(源数组，源数组中的起始位置，目标数组，目标数组中的起始位置，要复制的数组元素数量);
int[] src = {1,2,3,4,5};
int[] dest = {6,7,8,9,10};
System.arraycopy(src,0,dest,0,3);//复制后dest={1,2,3,9,10}
```

### StringBuilder类

* 字符串底层是一个final常亮，每次对String修改后都需要新建一个String类型的对象，效率很低，为了应对这种情况，StringBuilder应运而生。
* StringBuiler是字符串缓冲区，可以提高字符串操作效率，可以看做是长度可以变化的字符串，底层是一个不被final修饰的byte数组，初始容量16，在修改过程中如果不够会自动扩容

```java
StringBuilder sb = new StringBuilder("ab");
StringBuilder sb2 = sb.append("cde");//返回sb自己，内部return this
System.out.println(sb == sb2);//true
//String——>StringBuilder
StringBuilder sb = new StringBuilder("ab");
//StringBuilder——>String
String s = sb.toString();
```

### 基本数据类型的包装类

* 目的是提供一些方法来操作数据类型

| Byte | Short | Integer | Long | Float | Double | Character | Boolean |
| ---- | ----- | ------- | ---- | ----- | ------ | --------- | ------- |
| byte | short | int     | long | float | double | character | boolean |

* 装箱：基本类型——>包装类：

  ```java
  Integer i = new Integer(1);
  Integer i = Interger.valueOf(1);
  ```

* 拆箱：包装类——>基本类型：

  ```java
  int ii = i.intValue();
  ```

* 自动装拆箱

  ```java
  ArrayList<Integer> list = new ArrayList<>();
  list.add(1);//自动装箱
  int a = list.get(0);//自动拆箱
  ```

* 基本类型和字符串之间转换：

  * 基本类型——>字符串
    1. `+""`
    2. 包装类中的静态方法toString
    3. String类中的静态方法valueOf
  * 字符串——>基本类型
    1. 包装类中的静态方法parseXX

## Java集合

* 会使用集合存储数据
* 会遍历集合，把数据取出来
* 掌握每种集合的特性
* 所有集合的父类：**Collection**

### Collection集合（单列集合）

* Collection< T >

公共方法：

1. boolean add(E e)
2. boolean remove(E e)
3. void clear()
4. boolean contains(E e)
5. boolean isEmpty()
6. int size()
7. Object[] toArray()

#### Iterator迭代器

1. boolean hasNext()
2. E next()

```java
//通过Collection中的iterator()方法获得
Collection<String> coll = new ArrayList<>();
coll.add("jack");
coll.add("sam");
Iterator<String> it = coll.iterator();
while(it.hasNext()){
    String s = it.next();
    System.out.println(s);
}
```

#### 增强for

```java
Collection<String> coll = new ArrayList<>();
coll.add("jack");
coll.add("sam");
for(String s : coll){
    System.out.println(s)
}
```

#### 泛型

```java
//含有泛型的类
public class ArrayList<E>{
    public boolean add(E e){}
    public E get(int index){}
}
```

* 集合如果不使用泛型，默认就是Object类型

```java
//含有泛型的方法
//修饰符 <泛型> 返回值类型 方法名(参数列表){方法体}
public static <M> void method(M m){
    System.out.println(m);
}
//调用的时候传递什么参数就调用什么类型的方法
```

```java
//含有泛型的接口
public interface GenericInterface<I>{
    public abstract void method(I i);
}
//第一种用法：实现的时候确定泛型
public class GenericInterfaceImpl implements GenericInterface<String>{
    public abstract void method(String s){
        System.out.println(s);
    }
}
//第二种用法：创建对象的时候确定泛型
public class GenericInterfaceImpl<I> implements GenericInterface<I>{
    public abstract void method(I i){
        System.out.println(i);
    }
}
```

```java
//传值的时候：泛型通配符
public static void printArray(ArrayList<?> list){
	Iterator<?> it = list.iterator();
    while(it.hasNext){
        System.out.println(it.next());
    }
}
//上限限定：? extends E：?只能是E的子类或本身
//下限限定：? super E：?只能是E的父类或本身
```



### List接口：Collection的第一个子类

* 有序的集合
* 允许存储重复的元素
* 有索引index，可以使用普通的for遍历

特有方法：(带索引)

1. public void add(int index,E element)
2. public E get(int index)
3. public E remove(int index)
4. public E set(int index,E element)

#### Vector

* 底层是数组，查询快，增删慢
* 单线程（所以被ArrayList取代）

#### ArrayList

* 底层是数组，查询快，增删慢
* 多线程

#### LinkedList

* 底层是双向链表，查询慢，增删快
* 多线程

特有方法：

1. public void addFirst(E e) == public void push(E e)
2. public void addLast(E e) == public void add(E e)
3. public E getFirst()
4. public E getLast()
5. public E removeFirst() == public E pop()
6. public E removeLast()

### Set接口：Collection的第二个子类

* 无序的集合
* 不允许存储重复元素（原理上来看，必须保证元素类重写hashcode和equals方法）
* 没有索引index，不能使用普通的for遍历，只能用迭代器遍历

#### TreeSet

* 底层是二叉树，一般用于排序

#### HashSet

* 底层是`哈希表(数组+红黑树)`实现的，查询速度极快

#### LinkedHashSet（有序）

* 底层是`哈希表(数组+红黑树)+链表`实现的，**可以保证存取顺序**

### Collections工具类

```java
Collections.addAll(list,"a","b","c","d");//同时加多个
Collections.shuffle(list);//打乱顺序
Collections.sort(list);//按照默认顺序排序；要排序自定义类型，元素类必须实现Comparable接口，重写compareto方法
Collections.sort(list,new Comparator<String>{
    public int compare(Student o1, Student o2){
        return o1.getAge()-o2.getAge();//升序
    }
})
```

### Map集合（双列集合）

* Map< K,V >

* 双列集合，一个元素包含两个值
* key不能重复，value不能重复，且一一对应

公共方法：

1. public V put(K key,V value)：key存在返回null，key不存在返回被替代的value
2. public V get(Object key)：key存在返回对应value，key不存在返回nul
3. public V remove(Object key)：key存在返回被删除value，key不存在返回null
4. public boolean containsKey(Object key)：判断是否包含指定key
5. public boolean containsValue(Object value)：判断是否包含指定value
6. keySet：Map集合的第一种遍历方式

```java
Set<String> set = map.keySet();//获得map集合的所有key，存在set集合中
Iterator<String> it = set.iterator();
while(it.hasNext()){
    String key = it.next();
    Integer value = map.get(key);
    System.out.println(value);
}
for(String key : set){
    Integer value = map.get(key);
    System.out.println(value);
}
```

7. entryset：Map集合的第二种遍历方式

```java
Set<Map.Entry<String,Integer>> set = map.entrySet();
for(Map.Entry<String,Integer> e : set){
    System.out.println(e.getKey());
    System.out.println(e.getValue());
}
```

* 如果要存储自定义元素，对于key的元素，必须重写hashCode和equals方法来保证key唯一

#### HashMap

* 底层是`哈希表(数组+红黑树)`
* 无序

#### LinkedHashMap

* 底层是`哈希表(数组+红黑树)+链表`
* 有序(存储顺序==取出顺序)

#### Hashtable

* 不能存储null键，null值，已经被hashmap取代
* Hashtable的子类**Properties**依然在IO流中使用

### JDK9中集合添加的优化

* 只适用于List，Set，Map接口
* of返回值是一个不能改变的集合，之后不能再使用add，put等方法
* Set和Map在调用of方法时，不能有重复的元素，否则会报错

```java
List<String> list = List.of("a","b","c","d");
Set<String> set = Set.of("a","b","c","d");
Map<String,Integer> map = Map.of("张三",14,"赵四",12,"刘能",20);
```

## 可变参数

* 当参数列表数据类型确定，但是参数个数不确定，就可以使用可变参数
* 底层会根据传递参数的不同创建不同长度的数组（0到多个）
* 一个方法中最多有一个可变参数
* 如果参数有多个，可变参数必须写在参数列表最后

```java
//实际上传递的是数组
public static void add(int...arr){
    System.out.println(arr);
    System.out.println(arr.length);
}
```

## 异常

* 语法错误——>编译失败
* 异常——>编译/运行失败
* Throwable的两个子类：Error 和 Exception
  1. Error类一般是指与虚拟机相关的问题，如**系统崩溃，虚拟机错误，内存空间不足，方法调用栈溢**等。对于这类错误的导致的应用程序中断，仅靠程序本身无法恢复和和预防，遇到这样的错误，建议让程序终止。
  2. Exception类表示程序可以处理的异常，**可以捕获且可能恢复**。遇到这类异常，应该尽可能处理异常，使程序恢复运行，而不应该随意终止异常。

### 异常分类

#### Exception：编译期异常

* 在方法名后 throws Exception：虚拟机中断程序打印处理
* try catch处理异常：无需中断，程序正常执行

#### RuntimException：运行期异常

* try catch处理异常

#### Error：错误

* 程序无法治愈，**必须修改源码**

### 异常产生过程

![image-20210308122500908](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223638.png)

### 异常的处理

* 5个关键字：**try、catch、finally、throw、throws**

#### throw

* 只能用在方法内部
* 后边的对象只能是Exception或者Exception子类对象
* 必须处理throw的异常对象
  * 如果throw RuntimeException或者RuntimeException子类对象，可以自动交给JVM处理，自己不做手动处理
  * 如果throw 编译期异常，必须手动处理，要么连续throws到jvm处理，要么try...catch...

```markdown
常见的编译期异常和运行期异常
a)编译期异常：
          1.ClassNotFoundException(类找不到异常)
          2.FileNotFoundException（编译文件夹中找不到，就是发布到tomcat中的，不是工程中）
          3.SQLException ：提供有关数据库访问错误或其他错误的信息的异常。（ 比如SQL语句写错，访问的表不存在，连接数据库失败等等）
          4.IOexception：IO流异常。一般在读写数据的时候会出现这种问题。
          5.EOFException:当输入过程中意外到达文件或流的末尾时，抛出此异常。
b)运行期异常：
          1.NullPointerException(空指针异常)
          2.ArithmeticException（运算非法异常）
          3.IndexOutOfBoundsException(数组越界异常)
          4.ClassCastException(类转换异常)
          5.ArrayStoreException(数据存储异常，操作数组时类型不一致)
          6.BufferOverflowException(字节溢出异常—IO流操作)
```



#### throws

* 一步一步往上抛
* 如果同时throw多个异常且含有父子关系，throws后面只需写 父类

#### try...catch...

* try中如果产生了异常，会直接执行catch中内容，之后执行try...catch后代码
* try中如果没有异常，不会执行catch中内容，直接执行try...catch后代码
* Throwable中的3个异常处理的方法
  1. getMessage()
  2. toString()
  3. printStackTrace()：默认

#### finally

* 解决try语句块中异常后的代码无法执行的问题
* 不能单独使用，只能和try一起使用
* 一般用于资源释放
* 避免在finally语句中写return语句

### 异常处理时注意：

1. 多异常分别处理，多异常一次捕获多次处理，多异常一次捕获一次处理
2. 父类方法抛出多个异常，子类重写时只能抛出同样异常 or 异常子类 or 不抛
3. 父类方法没有抛出异常，子类重写时也不能抛异常，如果子类有异常只能捕获

### 自定义异常类

* 继承Exception：编译时异常
* 继承RuntimeException：运行时异常

```java
public class MyException extends Exception {
    //异常信息
    private String message;

    //构造函数
    public MyException(){
        super();
    }
    public MyException(String message){
        super(message);
        this.message = message;
    }

    //获取异常信息,由于构造函数调用了super(message),不用重写此方法
    //public String getMessage(){
    //    return message;
    //}
}
```

## 多线程

* 进程是操作系统资源分配的基本单位，负责为程序分配内存空间
* 而线程是任务调度和执行的基本单位，负责程序实际的执行
* 一个进程中至少有一个线程
* 同一进程中的多个线程共享代码段(代码和常量)，数据段(全局变量和静态变量)，扩展段(堆存储)。
* 每个线程拥有自己的**栈段**， 寄存器的内容，栈段又叫运行时段，用来存放所有局部变量和临时变量。

### Thread类

* java程序属于抢占式调度，哪个线程优先级高就会优先执行哪个线程；同一优先级会随机选择一个执行

```java
class PrimeThread extends Thread{
    public void run(){//重写Thread类的run方法
        for (int i=0;i<20;i++){
            System.out.println("prime----->"+i);
        }
    }
}
class MainTest{
    public static void main(String[] args){
        PrimeThread p = new PrimeThread();
        p.start();//会调用线程的run方法，只能调用一次
        for (int i=0;i<20;i++){
            System.out.println("main----->"+i);
        }
    }
}
```

![image-20210308165029075](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223647.png)

**多线程内存图**

![image-20210308165656672](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223654.png)

![image-20210308173426698](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223701.png)

```java
//获得指定线程名称
PrimeThread primeThread = new PrimeThread();
System.out.println(primeThread.getName());
//获得当前线程名称
System.out.println(Thread.currentThread().getName());
//修改线程名称
primeThread.setName("线程0");
//也可以修改PrimeThread构造函数直接修改名字
class PrimeThread extends Thread{
    public PrimeThread(){
        
    }
    public PrimeThread(String name){
        super(name);//让父类Thread构造方法给子线程起名字
    }
}
```

#### sleep方法

* 是当前正在执行的线程暂停

```java
class PrimeThread extends Thread{
    public void run(){//重写Thread类的run方法
        for (int i=0;i<20;i++){
            System.out.println("prime----->"+i);
            Thread.sleep(1000);//1000毫秒==1秒
        }
    }
}
```

### Runnable类：创建多线程的第二种方式

```java
public class RunnableImpl implements Runnable{
    public void run(){
        System.out.println("prime----->"+i);
    }
}
class MainTest{
    public static void main(String[] args){
        RunnableImpl ri = new RunnableImpl();
        Thread t = new Thread(ri)
        t.start();//会调用线程的run方法，只能调用一次
        for (int i=0;i<20;i++){
            System.out.println("main----->"+i);
        }
    }
}
```

* 好处：
  1. Runnable方式避免了单继承的局限性
  2. Runnable方式降低了耦合性：RunnableImpl设置线程 & Thread开启线程 解耦

### 使用匿名内部类的方式创建线程

* Thread和Runnable两种方式都可以

### 线程安全问题

* 多线程访问了共享的数据，会出现线程安全问题
* 这种线程安全问题是不能发生的，我们要确保某个时刻只能由一个线程访问共享资源

#### 解决线程安全问题

1. **同步代码块**

   ```java
   synchronized(同步锁){
       //多线程访问共享数据的代码
   }
   ```

![image-20210308180822199](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223712.png)

2. **同步方法**

* 方法修饰符：synchronized

```java
public synchronized void method(){
    //多线程访问共享数据的代码
    //锁对象是this对象
}
//静态同步方法
public static synchronized void method(){
    //多线程访问共享数据的代码
    //锁对象是本类的class属性对象
} 
```

3. **Lock锁机制**

* Lock接口中的两个方法：
  1. void lock()：获取锁
  2. void unlock()：释放锁

```java
Lock l = new ReentrantLock();//创建类的成员变量l

//在某个方法中
l.lock();
//多线程访问共享数据的代码
l.unlock();

```

### 线程状态

```java
Thread.State：Thread类的内部类State
```

* **一共6种状态**

![image-20210308214423553](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223720.png)

* sleep(参数) 和 wait(参数) 其实都是一样的
* notify() 是唤醒一个等待的线程，notifyAll()是唤醒所有等待的线程

### 线程间通信（等待唤醒机制）

* 多个线程处理同一个资源，一个生产，一个消费
* wait和notify方法必须要使用同一个锁对象调用
* wait和notify方法属于Object类的方法
* wait方法和notify方法必须在同步代码块或者同步函数中使用

#### 生产者&消费者问题

* 以包子铺为例

![image-20210308230625128](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223727.png)

```java
//包子类（锁对象）
public class BaoZi {
    String pi;
    String xian;
    boolean flag = false;

}
//包子铺类（生产者）
public class BaoZiPu extends Thread{
    private BaoZi bz;

    public BaoZiPu(BaoZi bz) {
        this.bz = bz;
    }

    @Override
    public void run() {
        while (true){
            synchronized (bz){
                if(bz.flag == true){
                    try {
                        bz.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                bz.pi = "薄皮";
                bz.xian = "三鲜馅";
                System.out.println("包子铺正在生产"+bz.pi+bz.xian+"的包子");
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                bz.flag = true;
                bz.notify();
                System.out.println("包子铺已经生产好了包子");
            }
        }

    }
}
//吃货类（消费者类）
public class ChiHuo extends Thread{
    private BaoZi bz;

    public ChiHuo(BaoZi bz) {
        this.bz = bz;
    }

    @Override
    public void run() {
        while (true){
            synchronized (bz){
                if(bz.flag == false){
                    try {
                        bz.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                System.out.println("吃货正在吃"+bz.pi+bz.xian+"的包子");
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                bz.flag = false;
                bz.notify();
                System.out.println("吃货已经吃完了包子");
                System.out.println("*************************************");
            }
        }

    }
}
//测试类
public class MainTest {
    public static void main(String[] args) {
        BaoZi bz = new BaoZi();
        new BaoZiPu(bz).start();
        new ChiHuo(bz).start();
    }
}

```



### 线程池

* 线程池就是一个容纳多个线程的容器，其中的线程可以反复使用，省去了频繁创建对象的操作，无需反复创建线程而消耗过多资源

![image-20210308232553076](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521223737.png)

```java
//java.util.concurrent.Executors:线程池的工厂类
//生产一个指定线程数量的线程池
ExecutorService es = Executors.newFixedThreadPool();
//实现一个Runnable实现类
//调用ExecutorService中的方法submit，执行线程
es.submit(new RunnableImpl());
es.submit(new RunnableImpl());
es.submit(new RunnableImpl());
//调用ExecutorService中的方法shutdown销毁线程池
```