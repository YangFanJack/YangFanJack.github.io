
## Servlet

* 见之前的两篇博客：《javaWeb核心-理解servlet》，《javaWeb核心-servlet细节》，讲的很详细

## Filter：过滤器

* 拦截请求：完成通用操作（登录验证，统一编码，敏感字符过滤）

```java
@WebFilter("/*")//访问所有资源之前，都会执行该过滤器
public class FilterDemo1 implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("filterDemo1被执行了....");
        //放行
        filterChain.doFilter(servletRequest,servletResponse);

    }

    @Override
    public void destroy() {

    }
}
```

* **web.xml配置**

```xml
<filter>
    <filter-name>demo1</filter-name>
    <filter-class>cn.itcast.web.filter.FilterDemo1</filter-class>
</filter>
<filter-mapping>
    <filter-name>demo1</filter-name>
    <!-- 拦截路径 -->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

* **过滤器执行流程**

  1. 执行放行前的代码
  2. 执行放行后的资源
  3. 回来执行过滤器放行代码下边的代码（一般是对response对象做增强）

* **过滤器生命周期方法**

  1. **init:**在服务器启动后，会创建Filter对象，然后调用init方法。只执行一次。用于加载资源
  2. **doFilter:**每一次请求被拦截资源时，会执行。执行多次
  3. **destroy:**在服务器关闭后，Filter对象被销毁。如果服务器是正常关闭，则会执行destroy方法。只执行一次。用于释放资源

* **过滤器配置详解**

  * 拦截路径配置：
  			1. 具体资源路径： /index.jsp   只有访问index.jsp资源时，过滤器才会被执行
  	 	2. 拦截目录： /user/*	访问/user下的所有资源时，过滤器都会被执行
  	 	3. 后缀名拦截： *.jsp		访问所有后缀名为jsp资源时，过滤器都会被执行
  	 	4. 拦截所有资源：/*		访问所有资源时，过滤器都会被执行
  * 拦截方式配置：**资源被访问的方式**
     * 注解配置：设置dispatcherTypes属性
       1. REQUEST：默认值。浏览器直接请求资源
       2. FORWARD：转发访问资源
       3. INCLUDE：包含访问资源
       4. ERROR：错误跳转资源
       5. ASYNC：异步访问资源
     * web.xml配置：设置`<dispatcher></dispatcher>`标签即可

* **过滤器链（配置多个过滤器）**

  * 执行顺序：如果有两个过滤器：过滤器1和过滤器2
  			1. 过滤器1
  	 	2. 过滤器2
  	 	3. 资源执行
  	 	4. 过滤器2
  	 	5. 过滤器1 
  * 过滤器先后顺序问题：
     1. 注解配置：按照类名的字符串比较规则比较，值小的先执行
     	* 如： AFilter 和 BFilter，AFilter就先执行了。
     2. web.xml配置： `<filter-mapping>`谁定义在上边，谁先执行

  ### 敏感词汇过滤

  * 在Filter中对request中的请求参数进行过滤操作，但是由于只有`getProperty()`这个方法，没有`setProperty()`这个方法，所以，我们需要对request对象的getParameter方法进行**增强**。产生一个新的request对象。

  * 增强对象功能，可以使用两种模式：装饰者模式和代理模式

    1. **装饰模式**

    2. **代理模式**

      * 概念：
      	1. 真实对象：被代理的对象
      	2. 代理对象：
      	3. 代理模式：代理对象代理真实对象，达到增强真实对象功能的目的

       	* 实现方式：
      	 	1
      	1. 静态代理：有一个类文件描述代理对象
      	
      	 	2. 动态代理：在内存中形成代理类
      			* 实现步骤：
      				1. 代理对象和真实对象实现相同的接口
      			2. 代理对象 = Proxy.newProxyInstance();
      			3. 使用代理对象调用方法。
      			4. 增强方法
      		* 增强方式：
      		     	1. 增强参数列表
      		      	2. 增强返回值类型
      		      	3. 增强方法体执行逻辑	

  ```java
  接口类 proxy = Proxy.newProxyInstance(
      // 参数1：真实对象的类加载器
      myBeanpostProcessor.class.getClassLoader(),
      //参数2：真实对象实现的所有的接口,接口是特殊的类，使用Class[]装载多个接口
      bean.getClass().getInterfaces(), 
      //参数3： 接口，传递一个匿名内部类对象
      new InvocationHandler() {
          //proxy 代理对象
          //method:代理的方法对象
          //args:方法调用时参数
          public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
              System.out.println("开启事务");
              Object obj=method.invoke(bean, args);//代理类，参数
              System.out.println("关闭事务");
              return obj;
          }
      }
  );
  ```

  

  

## Listener：监听器

### 事件监听机制

* **事件：** 一件事情
* **事件源：** 事件发生的地方
* **监听器：** 一个对象
* **注册监听：** 将事件，事件源绑定在一起。当事件源发生某个事件时，执行监听器代码

**说一个用来加载配置文件的监听器类**（web框架中这种监听器已经写好，我们只需要配置即可）

```java
ServletContextListener类 //监听ServletContext对象的创建和销毁
void contextDestroyed(ServletContextEvent sce) //服务器启动后自动调用
void contextInitialized(ServletContextEvent sce) //ServletContext对象创建后会调用该方法
```

步骤：

1. 定义一个类，实现ServletContextListener接口

2. 复写方法

3. 配置
   web.xml

   ```xml
   <listener>
       <listener-class>cn.itcast.web.listener.ContextLoaderListener</listener-class>
   </listener>
   <!--指定初始化参数-->
   <context-param>
   	<param-name>XXX</param-name>
       <param-value>XXX</param-value>
   </context-param>
   ```

   注解：

   ```java
   @WebListener
   ```

   

   

   ​	