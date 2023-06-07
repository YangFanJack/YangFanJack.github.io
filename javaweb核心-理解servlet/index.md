# JavaWeb核心:理解servlet


Servlet定义

* **物理上是一个运行在服务器端的一个小程序（serve applet），可以理解为就是一个接口（规则），定义了服务器端的Java类被tomcat识别的规则**

* **我们需要自定义一个类，来实现servlet接口，复写接口的的方法，tomcat就可以识别这个类，我们就称这个类是个servlet**

**实际操作：**

1. 创建JavaEE项目
2. 定义一个类，实现servlet接口
3. 重写接口中的抽象方法
4. 在web.xml中配置servlet

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224023.png)

## Servlet的执行原理

servlet中的java类和javase中的普通java类是不一样的，它的运行依赖tomcat这个web容器，自己没有main方法。实现的servlet方法不需要自己调用，服务器会自己按照需要调用

1. 当服务器接收到客户端浏览器的请求后，会解析请求的URL路径，获取访问的servlet的资源路径
2. 查找web.xml文件，是否有对应的<url-pattern>标签体内容
3. 如果有，则会找到对应的<servlet-class>全类名
4. tomcat会将字节码文件内加载进内存，并且创建其对象
5. tomcat按照需要自己调用方法

![image-20210314153955729](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224030.png)

## Servlet中的生命周期

### sevlet中的方法

* init方法：servlet被创建时执行
* service方法：每一次servlet被访问时执行
* destroy方法：在服务器正常关闭时执行

上面三个方法对应servlet的三个生命周期

* servletConfig方法：获得servletconfig对象，就是servlet的配置对象
* getServletInfo方法：获得servlet的一些信息，版本，作者...

### Servlet被创建

* 默认情况下，第一次被访问时，servlet被创建，加载资源
* 非默认时，可以子啊web.xml中指定servlet的创建时机，配置<load-on-startup></load-on-startup>标签的值，**正数**表示在服务器**启动时创建**，**负数**表示**第一次被访问时创建**
* 一个servlet类在内存中只有一个对象，是单例模式。多用户同时访问时，可能不安全，但是加锁会严重影响性能，所有尽量不要在servlet中定义**成员变量线程公共资源**，定义局部变量就没有安全问题了，因为局部变量在栈中，线程私有。

### Servlet提供服务

* 每次访问都会执行service方法

### Servlet被销毁

* 服务器关闭时，Servlet被销毁，释放资源。但是只有服务器正常关闭，才会执行destroy方法。

## Servlet3.0

* 选择JavaEE时，选择servlet版本3.0以上，可以不选择web.xml了
* 可以通过注解配置：`@WebServlet`，不需要web.xml配置文件中配置了

## Servlet体系结构

* Servlet接口，GenericServlet抽象类，HttpServlet抽象类

* GenericServlet抽象类继承Servlet抽象类，已经空实现了除了service方法之外的所有抽象方法。所以，可以让自定义的servelt类继承GenricServlet类，减少工作量。
* HttpServlet抽象类也是继承于Servlet接口，并且由doPost和doGet方法，我们可以让自定义的Servlet类继承HttpServlet类复写doGet和doPost方法，但是service方法不用复写了，HttpServlet抽象类已经帮你写好了。用来根据post和get的请求方式做出不同判断。

## Servlet相关配置

* urlpartten：Servlet的访问路径
  * 一个Servlet可以定义多个访问路径：@webServlet({"d4","d44","d444"})
  * 路径定义规则：
    * /XXX
    * /XXX/XX：多层路径，目录结构
    * *.do

## Servlet和JSP？

* 先上一张图：

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224039.png)

* 要理解servlet和jsp的区别与联系，我们需要回顾javaWeb的发展历史：

  1. 最开始的web，用户使用浏览器向web服务器发送的请求都是静态的资源（html,css,javascript）。为了能够根据不同用户的不同请求来动态的处理并且返回资源，servlet就出现了。
  2. 前面提到了servlet是在服务器端的java代码，通过java这个高级语言就能动态的处理数据和资源，然后把经过动态处理后的静态资源再打回浏览器客户端。但是有一个问题，servlet类中需要写大量的冗余的out.write代码写html，这就导致后端人员都想骂娘，然后，jsp就诞生了。
  3. jsp是sun公司借鉴微软的asp后，推出的直接在原来的静态页面html中嵌入java代码的技术。但是，这样虽然可以避免冗余，但是前端人员必须要懂后端知识，并且在html中插入大量的java代码会是jsp文件代码看起来凌乱不堪，自己写完了都看不懂，更不要说多人协作了。所以再后来，基于MVC模式的JSP+Servlet+JavaBean就应运而生了（也就是上面图中所表达的）
  4. 所以，总结就是：
     * 一个java类必须是serevlet类才能被浏览器访问到，jsp本质上就是一个servlet，jsp简化servlet的繁琐写html代码的操作
     * servlet是在java代码中写html，jsp是在html中写java代码

  **注：关于MVC的具体以及Web发展后期框架的出现以及前后端分离的知识现在还在学习中，以后会陆续写出来。**


