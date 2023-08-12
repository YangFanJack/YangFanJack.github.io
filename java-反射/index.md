# java-反射


#### 静态语言（强类型语言）

静态语言是在编译时变量的数据类型即可确定的语言，多数静态类型语言要求在使用变量之前必须声明数据类型。 
例如：C++、Java、Delphi、C#等。

#### 动态语言（弱类型语言）

动态语言是在运行时确定数据类型的语言。变量使用之前不需要类型声明，通常变量的类型是被赋值的那个值的类型。 
例如PHP/ASP/Ruby/Python/Perl/ABAP/SQL/JavaScript/Unix Shell等等。

#### 强类型定义语言

强制数据类型定义的语言。也就是说，一旦一个变量被指定了某个数据类型，如果不经过强制转换，那么它就永远是这个数据类型了。举个例子：如果你定义了一个整型变量a,那么程序根本不可能将a当作字符串类型处理。强类型定义语言是类型安全的语言。

#### 弱类型定义语言

数据类型可以被忽略的语言。它与强类型定义语言相反, 一个变量可以赋不同数据类型的值。强类型定义语言在速度上可能略逊色于弱类型定义语言，但是强类型定义语言带来的严谨性能够有效的避免许多错误。

## 什么是反射（反射机制）

* Java时静态语言，通过反射，Java成为了准动态语言

* 反射禁止允许程序在执行期借助于Reflection API取得任何类的内部信息，并能直接操作任意对象的内部属性及方法。

* 加载完类之后，在堆内存的方法区中就产生了一个Class类型的对象（一个类只有一个Class对象），这个对象就包含了完整的类的结构信息。我们可以通过这个对象看到类的结构。

  ```java
  Class c = Class.forName("java.lang.String")
  ```

* 这个存在内存中的Class类型的对象就是一面镜子，我们通过镜子的反射能看到类的结构。

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201218155009.png)

* 反射机制的功能：

  * 在运行时判断任意一个对象所属的类
  * 在运行时构造任意一个类的对象
  * 在运行时判断任意一个类所具有的成员变量和方法
  * 在运行时获取泛型信息
  * 在运行时调用任意一个对象的成员变量和方法
  * 在运行时处理注解
  * 生成动态代理

* 反射的优点和缺点：

  * 可以实现动态创建对象和编译，很灵活
  * 对性能有影响，反射基本上是一种解释操作

## 理解Class类并获取Class实例

* 发射相关的主要API

  * java.lang.Class：代表一个类
  * java.lang.reflect.Method：代表类的方法
  * java.lang.reflect.Field：代表类的成员变量
  * java.lang.reflect.Constructor：代表类的构造器

* 例子：

  ```java
  class User{
      private String name;
      private int id;
      private itn age;
      public User(){}
      public User(String name,int id,int age){
          this.name = name;
          this.id = id;
          this.age = age;
      }
      //省略get,set方法，toString()方法
  }
  
  public class Test{
      public static void main(String[] args){
          Class c1 = Class.forName("com.jack.User");
          c1.get
      }
  }
  ```

* Class类是Java反射的源头。所谓反射实际就是：可以通过对象反射求出类的名称

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201218162814.png" style="zoom: 80%; margin-left:0px"/>

## 类的加载与ClassCloader

* 对象照镜子后可以得到的信息：某个类的属性，方法和构造器，某个类实现了哪些接口。

* 对于一个类而言，JRE都为其保留一个不变的Class类型的对象，一个Class对象包含了特定某个结构的有关信息。

  * Class本身也是一个类
  * Class对象只能由系统建立对象
  * 一个加载的类在JVM中只会有一个Class实例
  * 一个Class对象对应的是一个加载到JVM中的一个.class文件
  * 每个类的实例都会记得自己是由哪个Class实例所生成
  * 通过Class可以完整地得到一个类中的所有被加载的结构
  * Class类是Reflection地根源，针对任何逆向动态加载，运行的类，唯有先获得相应的Class对象

  ```java
  //Class类的常用方法
  static Class forName(String name);
  Object newInstance();
  getName();
  Class getSuperClass();
  Class[] getinterfaces();
  ClassLoader getClassLoader();
  Constructor[] getConstructor();
  Method getMethod(String name,Class T);
  Field[] getDeclaredFields();
  ```

  ```java
  //获取Class类的实例
  //通过类的clas属性获取（多用于传参）
  Class c1 = Person.class;
  //用类的实例的getClass获取Class对象（多用于对象获取字节码）
  Class c2 = person.getClass();
  //用Class类的静态方法forName通过一个类的全类名获取Class对象（多用于读取配置文件）
  Class c3 = Class.forName("jack.Person");
  //内置基本数据类型的包装类可以直接用类名.Type
  //还可以利用ClassLoader
  ```

## 类加载内存分析

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219101125.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219101155.png)

## 类初始化的时机

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219101511.png)

## 类加载器ClassLoader

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219102507.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219113843.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219113851.png)

```java
//获取系统类加载器可以加载的路径
System.out.println(System.getProperty("java.class.path"));
```

### 双亲委派机制

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219114522.png)

## 创建运行时类的对象

* 通过反射获取运行时类的完整结构
  * Field：全部的属性
  * Method：全部的方法
  * Constructor：全部的构造器
  * Superclass：所继承的父类
  * Interface：实现的全部接口
  * Annotation：注解

```java
public class Test01(){
    public static void main(String[] args) throws ClassNotFoundException{
        Class c1 = Class.forName("com.jack.User");
        System.out.println(c1.getName());
        System.out.println(c1.getSimpleName());
        System.out.println(c1.getFields());
        System.out.println(c1.getDeclaredFields());
        System.out.println(c1.getFields("name"));
        System.out.println(c1.getDeclaredField("age"));
        System.out.println(c1.getMethods());
        System.out.println(c1.getDeclaredMethods());
        System.out.println(c1.getMethod("getName",null));
        System.out.println(c1.getMethod("setName",String.class));
        System.out.println(c1.getDeclaredMethod("getName",null));
        System.out.println(c1.getConstructors());
        System.out.println(c1.getDeclaredConstructors());
        System.out.println(c1.getConstructor(String.class,int.class,int.class));
    }
}
```

## 动态创建对象执行方法

```java
public class Test02(){
    public static void main(String[] args) throws ClassNotFoundException{
        Class c1 = Class.forName("com.jack.User");
        User user1 = (User)c1.newInstance();//本质上调用无参构造器
        //通过构造器创建对象
        Constructor constructor = c1.getDeclaredConstructor(String.class,int.class,int.class);
        User user2 = constructor.newInstance("Jack",001,18);
        //通过反射调用方法
        User user3 = c1.newInstance();
        Method setName = c1.getDeclaredMethod("setName",String,class);
        setName.invoke(user3,"Sam");
        //通过反射操作属性
        User user4 = c1.newInstance();
        Field name = c1.getDeclaredField("name");
        name.setAccessible(true);//暴力反射：不能直接访问私有属性，我们要关闭程序的安全检测。这里如果设置为false则指示反射的对象实施Java语言的语言访问检查
        name.set(user4,"Mariah");
    }
}
```

* 反复执行类中获取名字的方法的三种方式的效率对比：

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201219122112.png" style="zoom: 80%;" />

## 通过反射操作泛型

* Java采用的泛型擦除机制来引入泛型，Java中的泛型仅仅是给编译器javac使用的，确保数据的安全性和免去强制类型转换问题。但是，一旦编译完成，所有和泛型相关的类型会全部擦除。
* 为了通过反射来操作这些类型，Java新增了Parameterized，GenericArrayType，TypeVariable和WildcaredType几种类型来代表**不能被归一到Class类中的类型**但是又和原始类型齐名的类型
* ParameterizedType：表示一种参数化类型，比如`Collection<String>`
* GenericArrayType：表示一种元素类型是参数化类型或者类型变量的数组类型
* TypeVaraible：是各种类型变量的公共父接口
* WildcaredType：代表一种通配符类型表达式

```java
public class Demo{  
    //定义两个带泛型的方法
    public void test01(Map<String,Person> map,List<Person> list){
        System.out.println("Demo.test01()");
    }   
    public Map<Integer,Person> test02(){
        System.out.println("Demo.test02()");
        return null;
    }   


    public static void main(String[] args) {
        try {           
            //获得指定方法参数泛型信息
            Method m = Demo.class.getMethod("test01", Map.class,List.class);
            Type[] t = m.getGenericParameterTypes();

            for (Type paramType : t) {
                System.out.println("#"+paramType);
                if(paramType instanceof ParameterizedType){
                    //获取泛型中的具体信息
                    Type[] genericTypes = ((ParameterizedType) paramType).getActualTypeArguments();
                    for (Type genericType : genericTypes) {
                        System.out.println("泛型类型："+genericType);
                    }
                }
            }   

            //获得指定方法返回值泛型信息
            Method m2 = Demo.class.getMethod("test02", null);
            Type returnType = m2.getGenericReturnType();
            if(returnType instanceof ParameterizedType){
                    Type[] genericTypes = ((ParameterizedType) returnType).getActualTypeArguments();

                    for (Type genericType : genericTypes) {
                        System.out.println("返回值，泛型类型："+genericType);
                    }                   
            }       

        } catch (Exception e) {
            e.printStackTrace();
        }   
    }
}
```

## 通过注解反射操作注解

**小例子：**通过注解和反射完成类和表结构的映射关系

ORM：Object Relationship Mapping——》对象关系映射

* 类和表结构对应
* 睡醒和字段对应
* 对象和记录对应

```java
public class test12 {
    
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        Class c1 = Class.forName("dome_05_注解.Student2");
        //通过反射 获得注解
        Annotation[] annotations = c1.getAnnotations();
        for (Annotation annotation : annotations) {
            System.out.println(annotation);
        }

        // 获得 注解 value 的值
        Tablekuang annotation = (Tablekuang) c1.getAnnotation(Tablekuang.class);
        System.out.println(annotation.value());

        // 获得类指定的注解
        Field name = c1.getDeclaredField("name");
        Fieldkuang annotation2 = name.getAnnotation(Fieldkuang.class);

        System.out.println(annotation2.Columname());
        System.out.println(annotation2.type());
        System.out.println(annotation2.length());

    }
}

// 类名的注解
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@interface Tablekuang {
    String value();
}

// 属性的注解
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@interface Fieldkuang {
    String Columname();
    String type();
    int length();
}

class Student2 {
    private int id;
    private int age;
    private String name;

    public Student2() {    }
    public Student2(int id, int age, String name) {
        this.id = id;
        this.age = age;
        this.name = name;
    }
    public int getId() {    return id;    }
    public void setId(int id) {    this.id = id;    }
    public int getAge() {    return age;    }
    public void setAge(int age) {    this.age = age;    }
    public String getName() {    return name;    }
    public void setName(String name) {    this.name = name;    }
}
```
