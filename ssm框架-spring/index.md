# SSM框架-Spring


## Spring简介

* Spring前身：EJB框架

### Spring优点

1. 方便解耦，简化开发：IOC容器使对象的依赖关系由Spring控制，编程者更专注于上层的应用
2. AOP编程的支持：方便面向切面编程
3. 声明式事务的控制：配置的方式简化开发
4. Junit：方便程序测试
5. 方便集成各种优秀的框架：Mybatis等
6. 降低JavaEE API的使用难度：提供了一些工具的封装类
7. 开源：方便学习

![img](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230249.png)

### Spring程序大致步骤

![image-20210312105112356](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230259.png)

1. 导入Spring开发的maven依赖
2. 编写Dao接口和实现类
3. 创建Spring核心配置文件applicationContext.xml
4. 在Spring配置文件中配置实现类
5. 使用Spring的API获得Bean实例

## Spring核心配置文件

### bean标签

1. **id：**唯一性标识，通过其获得对象
2. **class：**bean的全限定名称
3. **scope：**singleton(默认)，prototype等5个
   * scope：bean对象在核心配置文件加载时就创建，只有一个，spring容器销毁时才会销毁bean
   * prototype：bean对象在每次getBean时创建一个，对象长时间不用会被GC垃圾回收机制回收
4. **init-method：**指定类中初始化方法名称，对象创建后执行
5. **destory-method：**指定类中销毁方法名称，对象销毁前执行
6. **bean实例化的三种方式：**
   1. 无参构造实例化：默认
   2. 工厂静态方法实例化：factory-method=“工厂方法”，class中写工厂类
   3. 工厂实例方法实例化：需要先bean一个工厂对象，然后factory-bean=“工厂实例”，factory-method=“工厂方法”

### 依赖注入

* 依赖注入是Spring框架核心IOC的具体体现
* 通过控制反转，把对象的控制权交给Spring容器。IOC解耦可以降低Service业务层和Dao持久层之间的关系，此时依赖关系就由Spring来维护了，Spring容器会自己把持久层对象传入业务层。
* 如何把UserDao依赖注入到UserService中？


```java
/**
构造方法
**/
public class UserServiceImpl implements UserService{
    private UserDao userDao;
    public UserServiceImpl(UserDao userDao){
        this.userDao = userDao;
    }
    public userServiceImpl(){
        
    }
    public void save(){
        usreDao.save();
    }
}

//name指的构造函数的参数名
<bean id="userDao" class="link.jack1024.dao.impl.UserDaoImpl"></bean>
<bean id="userService" class="link.jack1024.service.impl.UserServiceImpl">
	<constructor-arg name="userDao" ref="userDao"></constructor-arg>
</bean>
```

```java
/**
set方法
**/
public class UserServiceImpl implements UserService{
    private UserDao userDao;
    public void setUserDao(UserDao userDao){
        this.userDao = userDao;
    }
    public void save(){
        usreDao.save();
    }
}

//name指的set的方法除去set后首字母小写后的
<bean id="userDao" class="link.jack1024.dao.impl.UserDaoImpl"></bean>
<bean id="userService" class="link.jack1024.service.impl.UserServiceImpl">
	<property name="userDao" ref="userDao"/>
</bean>

//也可以使用p命名空间注入: xmlns="http://www.springframework.org/schema/p"
<bean id="userService" class="link.jack1024.service.impl.UserServiceImpl" p:userDao-ref="userDao"></bean>
```

* 依赖注入的数据类型：
  1. 普通数据类型
  2. 集合数据类型
  3. 引用数据类型

```java
public class User{
    private String name;
    private String email;
    //Getter & Setter 省略
}
public class UserDaoImpl implements UserDao{
    private String username;
    private int age;
    private List<String> list;
    private Map<String,User> userMap;
    //Setter 省略
}

<bean id="userDao" class="link.jack1024.dao.impl.UserDaoImpl">
	<property name="username" value="Sam Smith"/>
    <property name="age" value="26"/>
    <property name="list">
    	<list>
        	<value>aaa</value>
            <value>bbb</value>
            <value>ccc</value>
        </list>
    </property>
    <property name="userMap">
    	<map>
        	<entry key="firstUser" value-ref="user1"></entry>
            <entry key="secondUser" value-ref="user2"></entry>
        </map>
    </property>
</bean>
<bean id="user1" class="link.jack1024.domain.User">
	<property name="name" value="Jobs"/>
    <property name="email" value="112233@qq.com"/>
</bean>
<bean id="user2" class="link.jack1024.domain.User">
	<property name="name" value="Marks"/>
    <property name="email" value="445566@qq.com"/>
</bean>
```

### Sprng配置文件拆解合并

* 如果所有Spring容器的配置文件都放在applicationContext.xml核心配置文件中，那将会十分庞大，所以可以按照程序分层Controller，Service，Dao将配置文件也拆分为几个xml文件，最后在applicationContext.xml核心配置文件中import即可

```xml
<import resource="spring-mvc.xml"/>
<import resource="spring-sevice.xml"/>
<import resource="spring-dao.xml"/>
```

## Spring相关API

```java
ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
UserDao userDao1 = (UserDao) ac.getBean("userDao");
```

### ApplicationContext继承体系

* ApplicationContext的3个常用实现类
  1. ClassPathXmlApplicationContext：从类的根路径下加载配置文件
  2. FileSystemXmlApplicationContext：从磁盘路径下加载配置文件
  3. AnnotationConfigApplicationContext：当使用你注解配置容器对象时，需要使用此类创建spring容器。它用来读取注解

### getBean方法

1. getBean(bean的id)：配置文件中可以有多个同类型的bean
2. getBean(bean对应类.class)：配置文件中一个类型只能有一个bean，否则无法识别

## Spring配置数据源（连接池）

**常见数据源：**DBCP，C3P0，BnoeCP，Druid

```xml
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.32</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.mchange/c3p0 -->
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.1.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
<!-- https://mvnrepository.com/artifact/junit/junit -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-web -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.12.RELEASE</version>
</dependency>
```

**之前创建数据库连接池都是通过代码手动创建的，通过Spring完全可以将数据库连接池的控制反转给Spring容器，将数据库信息注入给数据库连接池对象。**

```xml
<!--applicationContext.xml-->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"/>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"/>
    <property name="user" value="root"/>
    <property name="password" value="root"/>
</bean>
```

```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
DataSource dataSource = (DataSource) applicationContext.getBean("dataSource");
Connection connection = dataSource.getConnection();
System.out.println(connection);

```

继续解耦，把数据库信息解到properties文件中

```xml
<!--
applicationContext.xml加载jdbc.properties配置文件获得连接信息。

首先，需要引入context命名空间和约束路径：
命名空间：xmlns:context="http://www.springframework.org/schema/context"
约束路径：
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd
-->
<context:property-placeholder location="classpath:c3p0.properties"/>

<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driver}"/>
    <property name="jdbcUrl" value="${jdbc.url}"/>
    <property name="user" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>

```

## Spring注解开发

* Spring是轻代码而重配置的框架，配置比较繁重，影响开发效率，所以**注解开发是一种趋势**，注解代替xml配置文件可以简化配置，提高开发效率。 

### 原始注解

* Spring原始注解主要是替代`<Bean>`的配置

![image-20210319092521680](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230317.png)

```xml
<!--
使用注解进行开发时，需要在applicationContext.xml中配置组件扫描，作用是指定哪个包及其子包下的Bean需要进行扫描以便识别使用注解配置的类、字段和方法。
-->
<!--注解的组件扫描-->
<context:component-scan base-package="com.jack1024"></context:component-scan>
```

```java
//@Component("userDao")
@Repository("userDao")
public class UserDaoImpl implements UserDao {
    @Override
    public void save() {
        System.out.println("save running... ...");
    }
}
//@Component("userService")
@Service("userService")
public class UserServiceImpl implements UserService {
    /*@Autowired
    @Qualifier("userDao")*/
    @Resource(name="userDao")
    private UserDao userDao;
    @Override
    public void save() {
        userDao.save();
    }
}
```

**注意：**

1. @Autowired默认按照数据类型匹配注入
2. @Autowired结合@Qualifier会按照id匹配注入
3. @Resource相当于@Autowired+@resource
4. 注解方式@Autowired、@Qualifier，@resource来依赖注入，不需要写set方法了

```java
//@Value注入普通数据类型
@Repository("userDao")
public class UserDaoImpl implements UserDao {
    @Value("注入普通数据")//没多大意义
    private String str;
    @Value("${jdbc.driver}")//很有意义：Spring配置类中会用
    private String driver;
    @Override
    public void save() {
        System.out.println(str);
        System.out.println(driver);
        System.out.println("save running... ...");
    }
}

//Bean的创建形式
//@Scope("prototype")
@Scope("singleton")
public class UserDaoImpl implements UserDao {
   //此处省略代码
}

//使用@PostConstruct标注初始化方法，使用@PreDestroy标注销毁方法
@PostConstruct
public void init(){
    System.out.println("初始化方法....");
}
@PreDestroy
public void destroy(){
    System.out.println("销毁方法.....");
}

```

### 新注解

* 使用原始注解还不能全部替代xml配置文件，还需要使用注解替代的配置如下
  1. 非自定义的Bean的配置：`<bean>`（例如Datasource）
  2. 加载properties文件的配置：`<context:property-placeholder>`
  3. 组件扫描的配置：`<context:component-scan>`
  4. 引入其他文件：`<import>`

![image-20210319101631835](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230327.png)

```java
//Spring的配置类，使用class电梯xml，使用注解代替标签
@Configuration
@ComponentScan("com.jack1024")
@Import({DataSourceConfiguration.class})
public class SpringConfiguration {
    
}

@PropertySource("classpath:jdbc.properties")
public class DataSourceConfiguration {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;
    //Spring将当前方法的返回值以指定名称存在Spring容器
	@Bean(name="dataSource")
    public DataSource getDataSource() throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass(driver);
        dataSource.setJdbcUrl(url);
        dataSource.setUser(username);
        dataSource.setPassword(password);
        return dataSource;
    } 
}

//测试加载核心配置类创建Spring容器
@Test
public void testAnnoConfiguration() throws Exception {
    //加载核心配置类
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfiguration.class);
    UserService userService = (UserService)applicationContext.getBean("userService");
    userService.save();
    DataSource dataSource = (DataSource)applicationContext.getBean("dataSource");
    Connection connection = dataSource.getConnection();
    System.out.println(connection);
}
```

**注意：**这种全注解配置的方法在SSM不强制使用，但是在SpringBoot中很常用

## Spring整合Junit

```java
//测试类中很繁琐
ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
IAccountService as = ac.getBean("accountService",IAccountService.class);

```

**改进：**

1. 让SpringJunit负责创建Spring容器，但是需要将配置文件的名称告诉它
2. 将需要进行测试Bean直接在测试类中进行注入

**Spring继承Junit步骤：**

1. 导入spring集成Junit的maven依赖
2. 使用@Runwith注解替换原来的运行期
3. 使用@ContextConfiguration指定配置文件或配置类
4. 使用@Autowired注入需要测试的对象
5. 创建测试方法进行测试

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.0.2.RELEASE</version>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
//加载spring核心配置文件
//@ContextConfiguration(value = {"classpath:applicationContext.xml"})
//加载spring核心配置类
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJunitTest {
    @Autowired
    private UserService userService;
    @Test
    public void testUserService(){
        userService.save();
    }
}
```

## AOP

### 什么是AOP？

AOP 为 Aspect Oriented Programming 的缩写，意思为面向切面编程，是通过预编译方式和运行期**动态代理**实现程序功能的统一维护的一种技术。

AOP 是 OOP 的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。（举个栗子：A和B需要结合，但是是通过程序运行时的动态结合，可以很好地解耦）

**AOP作用一句话总结：**程序运行时在不修改源码情况下动态对代码增强功能

**AOP优势：**减少重复代码，提高开发效率，便于维护

### AOP底层原理：动态代理

* 实际上，AOP 的底层是通过 Spring 提供的的**动态代理**技术实现的。在运行期间，Spring通过动态代理技术动态的生成代理对象，代理对象方法执行时进行增强功能的介入，再去调用目标对象的方法，从而完成功能的增强。

JDK代理：基于接口的动态代理

cglib代理：基于父类的动态代理

![image-20210319145522857](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230334.png)

#### 基于JDK的动态代理

```java
public interface TargetInterface {
    public void method();
}
public class Target implements TargetInterface {
    @Override
    public void method() {
        System.out.println("Target running....");
    }
}

Target target = new Target(); //创建目标对象
//创建代理对象
TargetInterface proxy = (TargetInterface) Proxy.newProxyInstance(
    target.getClass().getClassLoader(),//目标对象的类加载器
    target.getClass().getInterfaces(),//目标对象相同的接口字节码对象数组
    new InvocationHandler() {//调用代理对象任何方法，实质都是执行invoke方法
            @Override
            public Object invoke(
                Object proxy,
                Method method,
                Object[] args) throws Throwable 
            {
                System.out.println("前置增强代码...");
                Object obj = method.invoke(target, args);
                System.out.println("后置增强代码...");
                return obj;
            }
        }
);

// 测试,当调用接口的任何方法时，代理对象的代码都无需修改
proxy.method();
```

#### 基于cglib的动态代理

* 虽然是第三方的实现，但是现在spring-core包已经将cglib拿进来了，无需手动导入

```java
public class Target {
    public void method() {
        System.out.println("Target running....");
    }
}

Target target = new Target(); //创建目标对象
Enhancer enhancer = new Enhancer();   //创建增强器
enhancer.setSuperclass(Target.class); //设置父类
enhancer.setCallback(new MethodInterceptor() { //设置回调
    @Override
    public Object intercept(
        Object o, 
        Method method, 
        Object[] args, 
    MethodProxy methodProxy) throws Throwable {
        System.out.println("前置代码增强....");
        Object obj = method.invoke(target, args);
        System.out.println("后置代码增强....");
        return obj;
    }
});
Target proxy = (Target) enhancer.create(); //创建代理对象

//测试,当调用接口的任何方法时，代理对象的代码都无需修改
proxy.method();
```

### AOP相关概念：

- **Target（目标对象）**：代理的目标对象
- **Proxy （代理）**：一个类被 AOP 织入增强后，就产生一个结果代理类
- **Joinpoint（连接点）**：所谓连接点是指那些被拦截到的点。在spring中,这些点指的是方法，因为spring只支持方法类型的连接点
- **Pointcut（切入点）**：所谓切入点是指我们要对哪些 Joinpoint 进行拦截的定义

**注意：**连接点是可以被增强的方法，切入点是真正被增强的方法。切入点是连接点的一部分。

- **Advice（通知/ 增强）**：所谓通知是指拦截到 Joinpoint 之后所要做的事情就是通知
- **Aspect（切面）**：是切入点和通知（引介）的结合
- **Weaving（织入）**：是指把增强应用到目标对象来创建新的代理对象的过程。spring**采用动态代理织入**，而AspectJ采用编译期织入和类装载期织入

### AOP开发需要明确的事项

1. **需要编写的内容**
   1. 编写核心业务代码（目标类的目标方法）
   2. 编写切面类，切面类中有通知(增强功能方法)
   3. 在配置文件中，配置织入关系，即将哪些Advice与哪些Pointcut进行结合

2. **AOP 技术实现的内容**

   Spring 框架监控切入点方法的执行。一旦监控到切入点方法被运行，使用代理机制，动态创建目标对象的代理对象，根据通知类别，在代理对象的对应位置，将通知对应的功能织入，完成完整的代码逻辑运行。

3. **AOP 底层使用哪种代理方式**

   在 spring 中，框架会根据目标类是否实现了接口来决定采用哪种动态代理的方式。

### XML 配置 AOP 详解

1. 导入 AOP 相关坐标
2. 创建目标接口和目标类（内部有切点）
3. 创建切面类（内部有增强方法）
4. 将目标类和切面类的对象创建权交给 spring
5. 在 applicationContext.xml 中配置织入关系
6. 测试代码

```xml
<!--导入spring的context坐标，context依赖aop-->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>5.0.5.RELEASE</version>
</dependency>
<!-- aspectj的织入 -->
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.8.13</version>
</dependency>
```

```java
//目标接口和目标类
public interface TargetInterface {
    public void method();
}

public class Target implements TargetInterface {
    @Override
    public void method() {
        System.out.println("Target running....");
    }
}
//创建切面类
public class MyAspect {
    //前置增强方法
    public void before(){
        System.out.println("前置代码增强.....");
    }
}
```

```xml
<!--在 applicationContext.xml 中配置织入关系-->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <!--将目标类和切面类的对象创建权交给 spring-->
    <!--配置目标类-->
    <bean id="target" class="com.itheima.aop.Target"></bean>
    <!--配置切面类-->
    <bean id="myAspect" class="com.itheima.aop.MyAspect"></bean>
    
    <aop:config>
        <!--声明myAspect的Bean为切面对象-->
        <aop:aspect ref="myAspect">
            <!--配置Target的method方法执行时要进行myAspect的before方法前置增强-->
            <aop:before method="before" pointcut="execution(public void com.itheima.aop.Target.method())"></aop:before>
        </aop:aspect>
    </aop:config>
</beans>
```

```java
//测试代码
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class AopTest {
    @Autowired
    private TargetInterface target;
    @Test
    public void test1(){
        target.method();
    }
}
```

#### 切点表达式的写法

表达式语法：

```java
execution([修饰符] 返回值类型 包名.类名.方法名(参数))
```

- 访问修饰符可以省略

- 返回值类型、包名、类名、方法名可以使用星号*  代表任意

- 包名与类名之间一个点 . 代表当前包下的类，两个点 .. 表示当前包及其子包下的类

- 参数列表可以使用两个点 .. 表示任意个数，任意类型的参数列表

例如：

```xml
execution(public void com.itheima.aop.Target.method())	
execution(void com.itheima.aop.Target.*(..))
execution(* com.itheima.aop.*.*(..))
execution(* com.itheima.aop..*.*(..))
execution(* *..*.*(..))
```

#### 通知的类型

通知的配置语法：

```xml
<aop:通知类型 method=“切面类中方法名” pointcut=“切点表达式"></aop:通知类型>
```

![image-20210319185243993](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230344.png)

#### 切点表达式的抽取

当多个增强的切点表达式相同时，可以将切点表达式进行抽取，在增强中使用 pointcut-ref 属性代替 pointcut 属性来引用抽取后的切点表达式。

```xml
<aop:config>
    <!--引用myAspect的Bean为切面对象-->
    <aop:aspect ref="myAspect">
        <aop:pointcut id="myPointcut" expression="execution(* com.itheima.aop.*.*(..))"/>
        <aop:before method="before" pointcut-ref="myPointcut"/>
    </aop:aspect>
</aop:config>
```

### 注解配置 AOP 详解

1. 创建目标接口和目标类（内部有切点）
2. 创建切面类（内部有增强方法）
3. 将目标类和切面类的对象创建权交给 spring
4. 在切面类中使用注解配置织入关系
5. 在配置文件中开启组件扫描和 AOP 的自动代理
6. 测试

```java
public interface TargetInterface {
    public void method();
}

public class Target implements TargetInterface {
    @Override
    public void method() {
        System.out.println("Target running....");
    }
}

public class MyAspect {
    //前置增强方法
    public void before(){
        System.out.println("前置代码增强.....");
    }
}

@Component("target")
public class Target implements TargetInterface {
    @Override
    public void method() {
        System.out.println("Target running....");
    }
}
@Component("myAspect")
public class MyAspect {
    public void before(){
        System.out.println("前置代码增强.....");
    }
}

@Component("myAspect")
@Aspect
public class MyAspect {
    @Before("execution(* com.itheima.aop.*.*(..))")
    public void before(){
        System.out.println("前置代码增强.....");
    }
}
```

```xml
<!--组件扫描-->
<context:component-scan base-package="com.itheima.aop"/>
<!--aop的自动代理，用于识别aop专属注解-->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

```java
//测试代码
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class AopTest {
    @Autowired
    private TargetInterface target;
    @Test
    public void test1(){
        target.method();
    }
}
```

#### 切点表达式的抽取

#### 注解通知的类型

通知的配置语法：@通知注解(“切点表达式")

![image-20210319193734452](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230353.png)

#### 切点表达式的抽取

同 xml配置
aop 一样，我们可以将切点表达式抽取。抽取方式是在切面内定义方法，在该方法上使用@Pointcut注解定义切点表达式，然后在在增强注解中进行引用。具体如下：

```java
@@Component("myAspect")
@Aspect
public class MyAspect {
    @Before("MyAspect.myPoint()")
    public void before(){
        System.out.println("前置代码增强.....");
    }
    @Pointcut("execution(* com.itheima.aop.*.*(..))")
    public void myPoint(){}
}
```

## Spring JDBCTemplate

1. 导入spring-jdbc和spring-tx坐标
2. 创建数据库表和实体
3. 创建JdbcTemplate对象
4. 执行数据库操作

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.0.5.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>5.0.5.RELEASE</version>
</dependency>
```

**JDBCTemplate的基本使用参考博客《JDBC》**

**我们可以将JdbcTemplate的创建权交给Spring，将数据源DataSource的创建权也交给Spring，在Spring容器内部将数据源DataSource注入到JdbcTemplate模版对象中,然后通过Spring容器获得JdbcTemplate对象来执行操作。**

```xml
<!--jdbc.properties-->
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=root

<!--applicationContext.xml-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
">

    <!--加载jdbc.properties-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--数据源对象-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!--jdbc模板对象-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

</beans>
```

```java
@Test
//测试Spring产生jdbcTemplate对象
public void test2() throws PropertyVetoException {
    ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
    JdbcTemplate jdbcTemplate = app.getBean(JdbcTemplate.class);
    int row = jdbcTemplate.update("insert into account values(?,?)", "lisi", 5000);
    System.out.println(row);
}
```

## Spring事务

### 编程式事务控制

![image-20210319210646503](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230403.png)

![image-20210319211610424](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230428.png)

![image-20210319220103243](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230433.png)

![image-20210319220336119](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230449.png)

![image-20210319220351682](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230456.png)

![image-20210319220537567](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230503.png)

### 基于XML的声明式事务控制

Spring 的声明式事务顾名思义就是采用声明的方式来处理事务。这里所说的声明，就是指在配置文件中声明，用在 Spring 配置文件中声明式的处理事务来代替代码式的处理事务。

**声明式事务处理的作用**

- 事务管理不侵入开发的组件。具体来说，业务逻辑对象就不会意识到正在事务管理之中，事实上也应该如此，因为事务管理是属于系统层面的服务，而不是业务逻辑的一部分，如果想要改变事务管理策划的话，也只需要在定义文件中重新配置即可

- 在不需要事务管理的时候，只要在设定文件上修改一下，即可移去事务管理服务，无需改变代码重新编译，这样维护起来极其方便

**注意：Spring 声明式事务控制底层就是AOP。**

#### 代码

1. **引入tx命名空间**

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx 
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">


```

2. **配置事务增强**

```xml
<!--平台事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>

<!--事务增强配置-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="*"/>
    </tx:attributes>
</tx:advice>
```

3. **配置事务 AOP 织入**

```xml
<!--事务的aop增强-->
<aop:config>
    <aop:pointcut id="myPointcut" expression="execution(* com.itheima.service.impl.*.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="myPointcut"></aop:advisor>
</aop:config>
```

4. **测试事务控制转账业务代码**

```java
@Override
public void transfer(String outMan, String inMan, double money) {
    accountDao.out(outMan,money);
    int i = 1/0;
    accountDao.in(inMan,money);
}
```

#### 切点方法的事务参数的配置

```xml
<!--事务增强配置-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!--设置事务的属性信息-->
    <tx:attributes>
        <tx:method name="*"/>
    </tx:attributes>
</tx:advice>
```

其中，<tx:method> 代表切点方法的事务参数的配置，例如：

```xml
<tx:method name="transfer" isolation="REPEATABLE_READ" propagation="REQUIRED" timeout="-1" read-only="false"/>
```

- name：切点方法名称

- isolation:事务的隔离级别

- propogation：事务的传播行为

- timeout：超时时间

- read-only：是否只读

### 基于注解的声明式事务控制

1. 编写 AccoutDao 

```java
@Repository("accountDao")
public class AccountDaoImpl implements AccountDao {
    @Autowired
    private JdbcTemplate jdbcTemplate;
    public void out(String outMan, double money) {
        jdbcTemplate.update("update account set money=money-? where name=?",money,outMan);
    }
    public void in(String inMan, double money) {
        jdbcTemplate.update("update account set money=money+? where name=?",money,inMan);
    }
}
```

2. 编写 AccoutService

```java
@Service("accountService")
@Transactional
public class AccountServiceImpl implements AccountService {
    @Autowired
    private AccountDao accountDao;
    @Transactional(isolation = Isolation.READ_COMMITTED,propagation = Propagation.REQUIRED)
    public void transfer(String outMan, String inMan, double money) {
        accountDao.out(outMan,money);
        int i = 1/0;
        accountDao.in(inMan,money);
    }
}
```

3. 编写 applicationContext.xml 配置文件

```xml
<!—之前省略datsSource、jdbcTemplate、平台事务管理器的配置-->
<!--组件扫描-->
<context:component-scan base-package="com.itheima"/>
<!--事务的注解驱动-->
<tx:annotation-driven/>
```

#### 注解配置声明式事务控制解析

1. 使用 @Transactional 在需要进行事务控制的类或是方法上修饰，注解可用的属性同 xml 配置方式，例如隔离级别、传播行为等。
2. 注解使用在类上，那么该类下的所有方法都使用同一套注解参数配置。
3. 使用在方法上，不同的方法可以采用不同的事务参数配置。
4. 要使用使用 @Transactional ，Xml配置文件中要开启事务的注解驱动<tx:annotation-driven />

## Spring集成web环境

### ApplicationContext应用上下文获取方式

应用上下文对象是通过new ClasspathXmlApplicationContext(spring配置文件) 方式获取的，但是每次从容器中获得Bean时都要编写new ClasspathXmlApplicationContext(spring配置文件) ，这样的弊端是配置文件加载多次，应用上下文对象创建多次。

在Web项目中，可以使用ServletContextListener监听Web应用的启动，我们可以在Web应用启动时，就加载Spring的配置文件，创建应用上下文对象ApplicationContext，在将其存储到最大的域servletContext域中，这样就可以在任意位置从域中获得应用上下文ApplicationContext对象了。

### Spring提供获取应用上下文的工具

上面的分析不用手动实现，Spring提供了一个监听器ContextLoaderListener就是对上述功能的封装，该监听器内部加载Spring配置文件，创建应用上下文对象，并存储到ServletContext域中，提供了一个客户端工具WebApplicationContextUtils供使用者获得应用上下文对象。

所以我们需要做的只有两件事：

①在web.xml中配置ContextLoaderListener监听器（导入spring-web坐标）

②使用WebApplicationContextUtils获得应用上下文对象ApplicationContext

### 导入Spring集成web的坐标

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>5.0.5.RELEASE</version>
</dependency>
```

### 配置ContextLoaderListener监听器

```xml
<!--全局参数-->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
<!--Spring的监听器-->
<listener>
	<listener-class>
       org.springframework.web.context.ContextLoaderListener
   </listener-class>
 </listener>
```

### 通过工具获得应用上下文对象

```java
ApplicationContext applicationContext = WebApplicationContextUtils.getWebApplicationContext(servletContext);
Object obj = applicationContext.getBean("id");
```

