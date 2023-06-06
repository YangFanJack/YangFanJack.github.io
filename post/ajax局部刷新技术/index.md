
## AJAX含义：

* Asynchromnous JavaScript And XML
* 异步的Javascript（编程语言）和XML（文件格式）
* AJAX不是一门新的编程语言，是一个多门已有技术组合后创新产生的新技术
* 核心点：
  * 核心编程语言是JS
  * 数据交换的工具是XML（现已被JOSN替代）
  * AJAX是一种异步请求方式（和同步请求对立）

## 同步&异步请求

### 同步请求：

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210106223642.png" style="zoom:67%;" />

* 浏览器发起的传统请求（表单，超链接，地址栏输入地址，location.href）
* 用户从发起请求开始，到服务器端相应结果完毕，这段时间内不能再操作页面，如果服务端相应结果耗时很长，用户在这段时间内一直等待体验很差。
* 特点：
  * 一定刷新页面
  * 地址栏一定改变
  * 发起同步请求，会阻塞用户的其他操作

### 异步请求：

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210106224315.png" style="zoom:67%;" />

* AJAX是一种浏览器端的异步请求技术
* 对于AJAX，服务端响应的不是一个完整页面，而是字符串（页面的数据部分），不会刷新页面
* 用户在JavaScript发起请求开始，到服务端响应结果完毕，不用等待，用户可以一直操作页面，用户体验好
* 特点：
  * 不刷新页面
  * 地址栏不改变
  * 异步请求不会阻塞用户的其他操作

## 原生AJAX操作

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210106225413.png" style="zoom:67%;" />

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210106225412.png" style="zoom:67%;" />

## AJAX异步请求通用模型

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210107105142.png" style="zoom:67%;" />

## JSON

* 异步请求下，Server端响应会Client的不是一个完整页面，而是字符串数据，当服务端响应的结果比较复杂时（对象，数组，集合），简单格式的字符串不能满足需求，在传递复杂的数据时，不能使用简单格式的字符串，要使用JSON格式的字符串。

* JSON：JavaScript Object Notation，它是JS对象字符串表示形式，定义了对象等复杂数据的特定字符串格式，独立于各个编程语言，是一种轻量级的数据交换格式。

* <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210107111226.png" style="zoom:67%;" />

* 按照java数据类型可分为以下两种JSON字符串转化方式：

  * 普通对象 & Map

    ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210107131028.png)

  * 数组 & List & Set

    ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210107131029.png)

### Jackson工具的使用

```xml
<!-- 引入依赖 -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.0</version>
</dependency>
```

```java
Student s = new Student(传入参数);
ObjectMapper objectMapper = new ObjectMapper();
String studentJson = objectMapper.writeValueAsString(s);
System.out.println(studentJson);
```

* 属性没有值，可以在对应实体类加上注解，就只会转化非null的数据：

  ```java
  @JsonInlcude(JsonInclude.Include.NON_NULL)
  ```

* 属性名定制，在实体类属性上加注解：

  ```java
  @JsonProperty("别名")
  ```

* 对象回环问题解决：

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20210107133621.png)

* 日期格式输出：

  日期格式默认转化为时间戳来显示的（1970年1月1日开始到现在的毫秒数）

  在实体类的日期属性上加上注解：

  ```java
  @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss",timezone = "GMT+8")
  ```

## 需要注意：

* POST和GET区别：
  * POST请求时必须带一个：content-type 指定参数的类型
  * POST的请求参数不能直接写在请求地址后
* 在SpringMVC中使用@responsebody的注解需要先引入jackson-core和jackson-databind两个依赖（我的惨痛教训，以后使用非传统功能事前，一定要先看看是否引入了外部依赖）
* 

