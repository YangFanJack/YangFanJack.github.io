# javaWeb核心:servlet细节


## HTTP协议：

* 概念：**Hyper Text Transfer Protocol**
* 特点：
  1. 基于TCP/IP的应用层协议
  2. 默认端口是80
  3. 基于请求响应模型，一次请求一次响应
  4. 无状态的；每次请求之间相互独立，不能交互数据
* 历史版本：
  1. 1.0版本：每次请求都会建立新的连接
  2. 1.1版本：每次连接建立后会等一会儿，如果有新的请求会复用

### 请求消息数据格式

1. **请求行**

   ```markdown
   请求方式 请求url 请求协议/版本
   GET /login.html HTTP/1.1
   ```

   HTTP协议有7中请求方式，常用的就是GET和POST

   * GET：请求参数在请求行中；请求url长度有限制；相对不安全
   * POST：请求参数在请求体中；请求url长度没有限制；相对安全

2. **请求头**

   ```markdown
   请求头名称: 请求头值
   ```

   常见请求头：

   * **User-Agent：** 浏览器告诉服务器浏览器的版本信息
   * **Accept：** 浏览器告诉服务器可以给我响应什么格式的信息
   * **Referer：** 浏览器告诉服务器我从哪里来（防盗链 & 统计）
   * **Connection：** 这个请求连接此时的状态（1.0和1.1有区别）

3. **请求空行**

   用来分隔请求头和请求体

4. **请求体**

   只有POST请求方式有请求体，

   ```markdown
   请求参数=请求参数值
   username=zhangsan
   ```

   

### 响应消息数据格式

1. 响应行

   ```markdown
   协议/版本 响应状态码 状态码描述
   HTTP/1.1 200 OK
   ```

   **响应状态码：服务器告诉客户端本次请求和响应的一个状态**

   1. 1xx：服务端正在接收客户端信息，没有接收完全，等待一段时间后，发送1xx状态码
   2. 2xx：成功
   3. 3xx：302重定向，304访问本地缓存
   4. 4xx：404客户端错误（请求路径没有对应的资源）；405请求方式没有对应的doXXX方法
   5. 5xx：500服务器端错误（服务器内部出现异常，控制台和网页一般都会报错）

2. 响应头

   ```markdown
   响应头名: 响应头值
   ```

   常见的响应头：

   1. Content-type：服务器告诉浏览器本次响应体数据格式以及编码格式
   2. Content-disposition：服务器告诉客户端以什么格式打开响应体数据
      * 默认：in-line
      * attachment;filename=xxx：以附件形式打开响应体（文件下载）

3. 响应空行

   用来分隔响应头和响应体

4. 响应体

   网页文件 & 其他打回浏览器的数据（图片，视频等）

   ```html
   <html>
       <head>
           <title></title>
       </head>
       <body>
           hello,world!
       </body>
   </html>
   ```

   

**HTTPS和HTTP的区别主要如下：**

1. https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。
2. http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。
3. http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
4. http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。



## Request对象

* request & response 原理图

![image-20210314192721654](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224051.png)

* request对象继承体系结构

  ```markdown
  ServletRequest  --接口
        |
  HttpServletRequest  --接口
        |
  org.apache.catalina.connector.RequestFacade 类（tomcat）
  ```

* request功能

  1. 获取请求行数据

     ```markdown
     请求方式 请求url 请求协议/版本
     GET /bbs/login.html?username=jack HTTP/1.1
     ```

     ```java
     String getMethod();//请求方式GET
     String getContextPath();//虚拟目录/bbs
     String getServletPath();//srevlet资源路径 /login.html
     String getQueryString();//获得请求参数 username=jack
     String getRequestURI();//获取请求uri /bbs/login.html
     String getRequestURL();//获取请求url http://localhost:8080/bbs/login.html
     String getProtocol();//获取协议，版本 HTTP/1.1
     String getRemoteAddr();//获取客户端ip地址
     ```

  2. 获取请求头数据

     ```java
     String getHeader(String name);//根据请求头名称获得请求头值
     Enumeration<String> getHeaderName();//获取所有请求头名称
     ```

  3. 获取请求体数据

     ```java
     BufferedReader getReader();//获取字符输入流，只能操作字符数据
     ServletInputStream getInputStream();//获取字节输入流，可以操作所有类型数据
     ```

  4. 其他功能

     ```java
     //获取请求参数通用方式（get和post通用）
     String getParameter(String name);//根据参数名获得参数值
     String[] getParameterValues(String name);//根据参数名称获得参数值的数组
     String getParameterNames();//获取所有请求的参数名称
     Map<String,String[]> getParameterMap();//获取所有参数的map集合，使用BeanUtils工具类的populate方法能把map直接转化为javabean对象
     //BeanUtils工具类操作的是属性，不是成员变量
     
     //请求转发：服务器内部的资源跳转方式
     RequestDispatcher getRequestDispatcher(String path);//获得请求转发器对象
     void forward(ServletRequest request, ServletResponse response);//请求转发,dispatcher类的方法
     
     //共享数据：request域代表一次请求的范围，一般用于请求转发的多个资源共享数据
void setAttribute(String name,Object obj);
     Object getAttribute(String name);
     void removeAttribute(String name);
     
     //获取servletContext
     ServletContext getServletContext();//返回servletContext对象
     ```
     
     **请求转发的特点：**
     
     1. 浏览器地址栏不发生变化
     2. 转发是在web服务器内部进行的，不能跨域访问
     3. 转发只有一次请求
     

## Response对象

```java
//设置响应行
void setStatus(int sc);
//设置响应头
void setHeader(String name,String value);
//设置响应体
PrintWriter getWriter();//字符输出流
ServletOutputStream getOutputStream();//字节输出流
```

```java
//重定向1
response.setStatus(302);
response.setHeader("location","/jack/responseDemo2");
//重定向简单方式
response.sendRedirect("jack/responseDemo2");
```

* **重定向的特点：**
  1. 浏览器地址栏会发生变化
  2. 重定向可以访问其他站点的资源
  3. 重定向有两次请求

* 什么时候用重定向,什么时候用请求转发？

  1. 需要使用了request域的时候,必须*用请求转发*。
  2. 访问站外资源的*时候用重定向*。
  3. 其他随意

* 相对路径和绝对路径区别？

  1. 相对路径：不以/开头，以`./..`开头
  2. 绝对路径：以`http://localhost/虚拟路径/资源路径`或者简写为`/虚拟路径/资源路径`的形式

  

  * 路径从客户端浏览器发出请求，需要加虚拟目录
  * 从服务器发出，不需要加虚拟目录
  * 动态获得虚拟目录：`request.getContextPath();`

## web整个过程的中文编码问题解析

![image-20210315143354798](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224100.png)

```java
//post请求参数通过getParameter方式乱码，设置request的编码
request.setCharacterEncoding("utf-8");
//写会数据到浏览器的汉字编码问题解决
response.setCharacterEncoding("utf-8");
response.setHeader("content-type","text/html;charset=utf-8");
//简单形式设置ContentType
response.setContentType("text/html;charset=utf-8");
```



## ServletContext对象

* **代表整个web应用**，可以和程序的容器（tomcat）来通信
* 功能：
  1. 获得MIME类型：
  2. 域对象：共享数据
  3. 获取文件的真实路径

```java
//通过request对象获取
request.getServletContext();
//通过HttpServlet实现类中方法获取
this.getServletContext();

//获得MIME类型：互联网通信中一种定义的文件数据类型（text/html,text/css,text/jpeg...）,所有文件后缀和mime的对应关系都存在tomcat服务器的web.xml中
String getMimeType(String file);

//域对象：共享所有用户所有请求的数据
void setAttribute(String name,Object obj);
Object getAttribute(String name);
void removeAttribute(String name);

//获取文件的真实路径
String getRealPath(String path);
context.getRealPath("/a.html");//web目录下
context.getRealPath("/WEB-INF/b.html");//WEB-INF目录下
context.getRealPath("/WEB-INF/classes/c.java");//src目录下
```



## 会话技术

* **会话：** 浏览器第一次给服务器发送请求会话建立，直到一方断开连接会话结束。
* 一次会话包含到了多次请求&响应
* **功能：** 在一次会话范围内共享数据，弥补http协议无状态特点的不足。

### Cookie：客户端会话技术

* **作用：** 将数据保存在客户端

```java
//创建cookie对象，绑定数据
new Cookie(String name,String value);
//发送cookie对象
response.addCookie(Cookie cookie);
//获取cookie，拿到数据
Cookie[] cookies = request.getCookies();
```

* **Cookie原理：**

![image-20210315205705956](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224109.png)

* **Cookie的细节**

  1. **一次能否发送多个cookie？**

     能，创建多个Cookie对象，然后调用response.addCookie函数即可

  2. **cookie在浏览器中保存多长时间？**

     * 默认情况下，浏览器关闭就没了
     * 持久化存储：setMaxAge(int seconds)：正数代表存储秒数且**放进了硬盘**，负数默认，零删除cookie

  3. **cookie能否存中文？**

     tomcat8之后开始支持中文存储，tomcat8之前需要URL编码解码（URLEncoder.encode(String s,"utf-8") & URLDecoder.decode(String s,"utf-8")）

  4. **cookie获取范围多大？**

     同一个服务器的多个web项目能否共享cookie？

     * 默认情况下cookie不能共享
     * 通过setPath(String path)设置cookie获取范围，如果需要共享，可以设置为setPath("/")

     不同tomcat服务器间怎么共享cookie？

     * setDomain(String path)：如果设置一级域名相同，那么多个服务器间就可以共享了，例如setDomain(".baidu.com")则tieba.baidu.com和news.baidu.com间就可以共享cookie了

  5. **Cookie特点和作用？**

     特点：

     1. cookie存在客户端浏览器
     2. 浏览器对单个cookie的大小有限制 以及 对统一域名下的总cookie的数量也有限制

     作用：

     1. 一般用于少量不太敏感的数据
     2. 在不登录的情况下，完成服务端对客户端身份的识别

### Session：服务器端会话技术

* **作用：** 将数据保存在服务器端

```java
//获得session
HttpSession session = request.getSession();
//操作共享数据
void setAttribute(String name,Object obj);
Object getAttribute(String name);
void removeAttribute(String name);
```

* 原理：（Session的实现依赖于Cookie）

![image-20210316082529471](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224122.png)

* 细节：

  1. 客户端关闭重启后，再次获取session是否是同一个？

     * 默认不是

     * ```java
       //手动设置session cookie可以延长session cookie存活时间，持久化
       Cookie c = new Cookie("JSESSIONID",session.getId());
       c.setMaxAge(60*60);
       reponse.addCookie(c);
       ```

  2. 服务器关闭重启后，再次获取session是否是同一个？

     * 肯定不一样，session都是重新创建的
     * 但是需要确保session不丢失
       1. session钝化：在服务器正常关闭后，将session对象序列化到硬盘上
       2. session活化：在服务器重启后，将session从硬盘转化为内存中您的session对象
       3. session钝化和sessoin活化：tomcat会自动帮我们做好，但是idea为每个项目创建的catalina_base地址不会活化。

  3. session什么时候被销毁：

     1. 服务器关闭
     2. session对象调用invalidate()
     3. session默认失效时间30min，可以再tomcat的web.xml和项目的web.xml的`<session-config><session-timeout></session-timeout></session-config>`中修改默认失效时间

* 特点：

  1. session用于存储一次会话的多次请求的数据，存在服务器端
  2. session可以存储任意类型，任意大小的数据

* 和cookie区别：

  1. session存在服务器端，cookie存在客户端
  2. session没有数据大小限制，cookie有
  3. session数据相对安全，cookie相对不安全

## JSP

* Java Server Pages：java服务器端页面。
* jsp本质就是一个servlet，可以查看IDEA每次运行都会生成的work目录，里面会有代码每次运行是生成的文件，就有jsp文件对应的java和class文件

![image-20210316075647096](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224129.png)

### 脚本

* `<% 代码%>`：定义的java代码在service方法中，service方法中定义什么，该脚本就定义什么
* `<%! 代码%>`：定义的java代码在jsp转化为serlvet后的成员变量位置
* `<%= 代码%>`：定义的java代码，会输出到页面上，输出语句定义什么，该脚本就定义什么。其实在serlvet中就是out.print()

### 指令

* 用于配置jsp页面 & 导入资源文件
* 格式：`<%@ 指令名称 属性名1=属性值1 属性名2=属性值2%>`

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions"%>
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<%@ include file="test.jsp"%>
```

* 分类：
  1. page：配置jsp页面
     * contentType：设置响应体的mime类型以及字符集
     * pageEncoding：当前jsp文件编码
     * language：目前只有java
     * improt：导入java的jar包
     * buffer：out对象输出到页面的缓冲区
     * errorPage：页面出错后自动跳转的页面
     * isErrorPage：标识当前页面是否是错误界面（当前jsp页面能使用exception对象）
  2. include：页面包含，导入页面的资源文件
  3. taglib：导入标签库
* **JSP中include指令和include动作区别**
  * `＜%@ include file=” .jsp”%＞`引入静态文本(html,jsp),在JSP页面被转化成servlet之前和它融和到一起
  * `＜jsp:include page=” .jsp” flush=”true” /＞`只是在运行时引入了要添加的jsp页面执行后所产生的应答

### 注释

1. **html注释：**`<!--html标签-->`（浏览器看得到）
2. **jsp注释：**`<%--Java代码/html标签--%>`（浏览器看不到）
3. 在jsp页面中建议都使用jsp注释



### EL表达式

* Expression Language：表达式语言

* 作用：替换jsp页面中java代码的编写

* 语法：`${表达式}`

* 注意：

  1. JSP中默认支持el表达式

     ```jsp
     <%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
     ```

* 使用：

  1. 运算：算数/比较/逻辑运算，empty运算符(判断字符串、集合、数组对象是否为null 或者 长度是否为0)
  2. 获取值：只能从域对象中获取值`${域名称.键名}`：
     * pageScope——>pageContext
     * requestScope——>request
     * sessionScope——>session
     * applicationScope——>application
     * 如果只写键名，表示依次从最小的域中查找是否有该建对应的值，直到找到为止
     * 如果域对象是对象，那么通过`${域名称.键名.属性名}`(Getter & Setter)获取
     * 如果域对象是List集合，那么通过`${域名称.键名[n]}`获取
     * 如果域对象是Map集合，那么通过`${域名称.键名.key名称}`或者`${域名称.键名["key名称"]}`获取
     * `${empty list}`,`${not empty list}`

* EL表达式中有11个隐式对象

  * **很重要：`${pageContext.request.contextPath}`：动态获取虚拟目录**

### JSTL标签

* JavaServer Pages Tag Library：JSP标准标签库
* 作用：简化和替换jsp页面上的java代码
* 使用步骤：
  1. 导入jstl相关jar包
  2. 引入taglib
  3. 使用`<c:XX>内容</c:XX>`

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions"%>
```

#### if

```jsp
<c:if test="表达式">
	标签体内容
</c:if>
```

* 如果表达式为true，则显示if标签体内容，如果为false，则不显示标签体内容
* 一般情况下，test属性值会结合el表达式一起使用，test后的表达式一般用的是el表达式接收域对象动态数据做判断

#### choose

```jsp
<c:choose>
    <c:when test="${number==1}">星期一</c:when>
    <c:when test="${number==2}">星期二</c:when>
    <c:when test="${number==3}">星期三</c:when>
    <c:when test="${number==4}">星期四</c:when>
    <c:when test="${number==5}">星期五</c:when>
    <c:when test="${number==6}">星期六</c:when>
    <c:when test="${number==7}">星期日</c:when>
    <c:otherwise>数字数据有误</c:otherwise>
</c:choose>
```

* 使用choose标签声明         			相当于switch声明
* 使用when标签做判断         			相当于case
* 使用otherwise标签做其他情况的声明    	相当于default

#### foreach

```jsp
<c:forEach items="${departmentPageBean.list}" var="keyword" varStatus="id">
    for循环体
</c:forEach>
```

* begin：开始值
* end：结束值
* var：临时变量
* step：步长

```jsp
<c:forEach begin="1" end="10" var="i" step="2">
    ${i}<br>
</c:forEach>

```

* items：容器对象
* var：容器中元素的临时变量
* varStatus：循环状态对象，属性index和count
  * index：从0开始（遍历容器的索引）
  * count：遍历次数

```jsp
<c:forEach items="${departmentPageBean.list}" var="keyword" varStatus="id">
    <td>${keyword.id}</td>
    <td>${keyword.name}</td>
    <td>${keyword.phone}</td>
</c:forEach>
```



### JSP中9大内置对象

* jsp页面中不需要获取和创建，可以直接使用的对象

| 内置对象        | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| request(域)     | 对应HttpServletRequest/ServletRequest对象                    |
| response        | 对应HttpServletRespons/ServletResponse对                     |
| session(域)     | 对应HttpSession对象                                          |
| application(域) | 对应ServletContext对象                                       |
| out             | 对应JspWriter对象，其内部关联一个PrintWriter对象             |
| page            | 后对应this                                                   |
| config          | 对应ServletConfig对象                                        |
| exception       | 对应Throwable对象，代表由其他JSP页面抛出的异常对象，只会出现于JSP错误页面(isErrorPage设置为true的JSP页面) |
| pageContext(域) | 对应PageContext对象，它提供了JSP页面资源的封装，并可设置页面范围，可以用来获取其他8个内置对象 |

*  response.getWriter()和out对象区别：response.getWriter()输出内容永远都会在out输出内容之前

## MVC开发模式

### jsp历史

1. servlet：response输出标签
2. jsp：简化了servlet开发，大项目难以维护
3. java的web开发借鉴了MVC开发模式，程序设计更加合理（model1—>model2）

### MVC：

1. M：模型Model
2. V：视图View（jsp中禁止写java代码了,利用el和jstl做展示工作）
3. C：控制器Controller

![image-20210316094511914](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224140.png)

### MVC优缺点：

* 优点：
  1. 耦合低，利于分工、维护
  2. 代码重用性高（C和M层代码）
* 缺点：
  1. 使得项目架构变得复杂，对开发人员要求高

## 三层架构

* 一种软件设计架构

### 分层

1. **view表现层：** 用于显示数据和接收用户输入的数据，为用户提供一种交互式操作的界面
2. **service业务逻辑层：** 实现业务的主要逻辑，是系统架构中体现核心价值的部分。
3. **Dao数据访问层：** 有时候也称为是持久层，其功能主要是负责数据库的访问。可以访问数据库、二进制文件、文本文件等。简单的说法就是实现对数据表的Select，Insert，Update，Delete的操作。如果要加入ORM的元素，那么就会包括对象和数据表之间的mapping，以及对象实体的持久化。

![image-20210316104928444](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224147.png)
