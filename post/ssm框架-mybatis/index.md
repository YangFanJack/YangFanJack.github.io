
## 开始

### 原始jdbc操作的分析

原始jdbc开发**存在的问题**如下：

1. 数据库连接创建、释放频繁造成系统资源浪费从而影响系统性能
2. sql 语句在代码中硬编码，造成代码不易维护，实际应用 sql 变化的可能较大，sql 变动需要改变java代码。
3. 查询操作时，需要手动将结果集中的数据手动封装到实体中。插入操作时，需要手动将实体的数据设置到sql语句的占位符位置

应对上述问题给出的**解决方案**：

1. 使用数据库连接池初始化连接资源
2. 将sql语句抽取到xml配置文件中
3. 使用反射、内省等底层技术，自动将实体与表进行属性与字段的自动映射

### 什么是Mybatis

1. mybatis 是一个优秀的基于java的持久层框架，它内部封装了jdbc，使开发者只需要关注sql语句本身，而不需要花费精力去处理加载驱动、创建连接、创建statement等繁杂的过程。
2. mybatis通过xml或注解的方式将要执行的各种 statement配置起来，并通过java对象和statement中sql的动态参数进行映射生成最终执行的sql语句。
3. 最后mybatis框架执行sql并将结果映射为java对象并返回。采用ORM（对象关系映射）思想解决了**实体和数据库映射**的问题，对jdbc 进行了封装，**屏蔽了jdbc api 底层访问细节**，使我们不用与jdbc api 打交道，就可以完成对数据库的持久化操作。

### 开发步骤

**MyBatis开发步骤：**

1. 添加MyBatis的坐标
2. 创建user数据表
3. 编写User实体类 
4. 编写映射文件UserMapper.xml
5. 编写核心文件SqlMapConfig.xml
6. 编写测试类

```xml
<!--mybatis坐标-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.5</version>
</dependency>
<!--mysql驱动坐标-->
<dependency>    
    <groupId>mysql</groupId>   
    <artifactId>mysql-connector-java</artifactId>    
    <version>5.1.6</version>    
    <scope>runtime</scope>
</dependency>
<!--单元测试坐标-->
<dependency>    
    <groupId>junit</groupId>    
    <artifactId>junit</artifactId>    
    <version>4.12</version>    
    <scope>test</scope>
</dependency>
<!--日志坐标-->
<dependency>    
    <groupId>log4j</groupId>    
    <artifactId>log4j</artifactId>    
    <version>1.2.12</version>
</dependency>
```

```java
public class User {    
	private int id;    
	private String username;    
	private String password;
    //省略get个set方法
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper        
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"        
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="userMapper">    
	<select id="findAll" resultType="com.itheima.domain.User">        
		select * from User
	</select>
</mapper>
```

```xml
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN“ "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>    
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC"/>
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver"/>
				<property name="url" value="jdbc:mysql:///test"/>
				<property name="username" value="root"/>
				<property name="password" value="root"/>
			</dataSource>
		</environment>
	</environments>
	<mappers>
		<mapper resource="com/itheima/mapper/UserMapper.xml"/> 
    </mappers>
</configuration>
```

```java
//加载核心配置文件
InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
//获得sqlSession工厂对象
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
//获得sqlSession对象
SqlSession sqlSession = sqlSessionFactory.openSession();
//执行sql语句
List<User> userList = sqlSession.selectList("userMapper.findAll");
//打印结果
System.out.println(userList);
//释放资源
sqlSession.close();
```

## Mybatis的增删改查操作

### 查询操作

上面那个例子就是查询操作

### 插入操作

```xml
<mapper namespace="userMapper">    
	<insert id="add" parameterType="com.itheima.domain.User">        
		insert into user values(#{id},#{username},#{password})    
	</insert>
</mapper>
```

```java
InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
SqlSession sqlSession = sqlSessionFactory.openSession();
int insert = sqlSession.insert("userMapper.add", user);
System.out.println(insert);
//提交事务（JDBC事务默认提交，Mybatis事务默认不提交）
sqlSession.commit();
sqlSession.close();
```

**需要注意：**

1. 插入语句使用insert标签
2. 在映射文件中使用parameterType属性指定要插入的数据类型
3. Sql语句中使用#{实体属性名}方式引用实体中的属性值
4. 插入操作使用的API是sqlSession.insert(“命名空间.id”,实体对象);
5. 插入操作涉及数据库数据变化，所以要使用sqlSession对象显式的提交事务，即sqlSession.commit() 

### 修改操作

```xml
<mapper namespace="userMapper">
    <update id="update" parameterType="com.itheima.domain.User">
        update user set username=#{username},password=#{password} where id=#{id}
    </update>
</mapper>
```

```java
InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
SqlSession sqlSession = sqlSessionFactory.openSession();
int update = sqlSession.update("userMapper.update", user);
System.out.println(update);
sqlSession.commit();
sqlSession.close();
```

**需要注意：**

1. 修改语句使用update标签
2. 修改操作使用的API是sqlSession.update(“命名空间.id”,实体对象);

### 删除操作

```xml
<mapper namespace="userMapper">
    <delete id="delete" parameterType="java.lang.Integer">
        delete from user where id=#{id}
    </delete>
</mapper>
```

```java
InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
SqlSession sqlSession = sqlSessionFactory.openSession();
int delete = sqlSession.delete("userMapper.delete",3);
System.out.println(delete);
sqlSession.commit();
sqlSession.close();
```

**需要注意：**

1. 删除语句使用delete标签
2. Sql语句中使用#{任意字符串}方式引用传递的单个参数
3. 删除操作使用的API是sqlSession.delete(“命名空间.id”,Object);

## Mybatis核心配置文件

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220522094845.png" alt="image-20210321155721489" style="zoom:67%;" />

### environments标签

数据库环境的配置，支持多环境配置

![image-20210321160006282](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225805.png)

其中，事务管理器（transactionManager）类型有两种：

1. **JDBC：**这个配置就是直接使用了JDBC 的提交和回滚设置，它依赖于从数据源得到的连接来管理事务作用域。
2. **MANAGED：**这个配置几乎没做什么。它从来不提交或回滚一个连接，而是让容器来管理事务的整个生命周期（比如 JEE 应用服务器的上下文）。 默认情况下它会关闭连接，然而一些容器并不希望这样，因此需要将 closeConnection 属性设置为 false 来阻止它默认的关闭行为。

其中，数据源（dataSource）类型有三种：

1. **UNPOOLED：**这个数据源的实现只是每次被请求时打开和关闭连接。
2. **POOLED：**这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来。
3. **JNDI：**这个数据源的实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的引用。

### mapper标签

该标签的作用是加载映射的，加载方式有如下几种：

1. 使用相对于类路径的资源引用，例如：

`<mapper resource="org/mybatis/builder/AuthorMapper.xml"/>`

2. 使用完全限定资源定位符（URL），例如：

`<mapper url="file:///var/mappers/AuthorMapper.xml"/>`

3. 使用映射器接口实现类的完全限定类名，例如：

`<mapper class="org.mybatis.builder.AuthorMapper"/>`

4. 将包内的映射器接口实现全部注册为映射器，例如：

`<package name="org.mybatis.builder"/>`

### Properties标签

实际开发中，习惯将数据源的配置信息单独抽取成一个properties文件，该标签可以加载额外配置的properties文件

![image-20210321160935425](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225815.png)

### typeAliases标签

类型别名是为Java 类型设置一个短的名字。原来的类型名称配置如下

![image-20210321161007745](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225826.png)

配置typeAliases，为com.itheima.domain.User定义别名为user

![image-20210321161023078](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225931.png)

上面我们是自定义的别名，mybatis框架已经为我们设置好的一些常用的类型的别名

![image-20210321161220049](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225938.png)



**注意：Mybatis的核心配置文件的标签是有顺序的，必须按照顺序来书写，否则将报错**



## Mybatis相应API

### SqlSession工厂构建器SqlSessionFactoryBuilder

常用API：SqlSessionFactory  build(InputStream inputStream)

通过加载mybatis的核心文件的输入流的形式构建一个SqlSessionFactory对象

 ```java
String resource = "org/mybatis/builder/mybatis-config.xml"; 
InputStream inputStream = Resources.getResourceAsStream(resource); 
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder(); 
SqlSessionFactory factory = builder.build(inputStream);
 ```

其中， Resources 工具类，这个类在 org.apache.ibatis.io 包中。Resources 类帮助你从类路径下、文件系统或一个 web URL 中加载资源文件。

### SqlSession工厂对象SqlSessionFactory

SqlSessionFactory 有多个个方法创建SqlSession 实例。常用的有如下两个：

![image-20210321162020518](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225947.png)

### SqlSession会话对象

SqlSession 实例在 MyBatis 中是非常强大的一个类。在这里你会看到所有执行语句、提交或回滚事务和获取映射器实例的方法。

执行语句的方法主要有：

```java
<T> T selectOne(String statement, Object parameter) 
<E> List<E> selectList(String statement, Object parameter) 
int insert(String statement, Object parameter) 
int update(String statement, Object parameter) 
int delete(String statement, Object parameter)

```

操作事务的方法主要有：

```java
void commit()  
void rollback() 
```

## Mybatis的Dao层实现

### 传统方式实现

```java
public interface UserDao {
    List<User> findAll() throws IOException;
}
```

```java
public class UserDaoImpl implements UserDao {
    public List<User> findAll() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        List<User> userList = sqlSession.selectList("userMapper.findAll");
        sqlSession.close();
        return userList;
    }
}
```

```java
@Test
public void testTraditionDao() throws IOException {
    UserDao userDao = new UserDaoImpl();
    List<User> all = userDao.findAll();
    System.out.println(all);
}

```

### 接口代理方式实现

**代理开发方式介绍**

采用 Mybatis 的代理开发方式实现 DAO 层的开发，这种方式是我们后面进入企业的主流。

Mapper 接口开发方法只需要程序员编写Mapper 接口（相当于Dao 接口），由Mybatis 框架根据接口定义**创建接口的动态代理对象**，代理对象的方法体同上边Dao接口实现类方法。

**Mapper 接口开发需要遵循以下规范：**

1. Mapper.xml文件中的namespace与mapper接口的全限定名相同
2. Mapper接口方法名和Mapper.xml中定义的每个statement的id相同
3. Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql的parameterType的类型相同
4. Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同

**编写UserMapper接口**

![image-20210321193412070](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225954.png)

**测试代理方式**

```java
@Test
public void testProxyDao() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //获得MyBatis框架生成的UserMapper接口的实现类
  	UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    User user = userMapper.findById(1);
    System.out.println(user);
    sqlSession.close();
}
```

## Mybatis映射文件深入

### 动态sql语句

**动态sql语句概述**

Mybatis 的映射文件中，前面我们的 SQL 都是比较简单的，有些时候业务逻辑复杂时，我们的 SQL是动态变化的，此时在前面的学习中我们的 SQL 就不能满足要求了。

**动态 SQL  之`<if>`** 

我们根据实体类的不同取值，使用不同的 SQL语句来进行查询。比如在 id如果不为空时可以根据id查询，如果username 不同空时还要加入用户名作为条件。这种情况在我们的多条件组合查询中经常会碰到。

```xml
<select id="findByCondition" parameterType="user" resultType="user">
    select * from User
    <where>
        <if test="id!=0">
            and id=#{id}
        </if>
        <if test="username!=null">
            and username=#{username}
        </if>
    </where>
</select>

```

当查询条件id和username都存在时，控制台打印的sql语句如下：

```java
//获得MyBatis框架生成的UserMapper接口的实现类
UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
User condition = new User();
condition.setId(1);
condition.setUsername("lucy");
User user = userMapper.findByCondition(condition);
```

当查询条件只有id存在时，控制台打印的sql语句如下：

```java
//获得MyBatis框架生成的UserMapper接口的实现类
UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
User condition = new User();
condition.setId(1);
User user = userMapper.findByCondition(condition);
```



**动态 SQL  之`<foreach>`**

循环执行sql的拼接操作，例如：SELECT * FROM USER WHERE id IN (1,2,5)。

 ```xml
<select id="findByIds" parameterType="list" resultType="user">
    select * from User
    <where>
        <foreach collection="array" open="id in(" close=")" item="id" separator=",">
            #{id}
        </foreach>
    </where>
</select>
 ```

测试代码片段如下：

```java
//获得MyBatis框架生成的UserMapper接口的实现类
UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
int[] ids = new int[]{2,5};
List<User> userList = userMapper.findByIds(ids);
System.out.println(userList);
```



foreach标签的属性含义如下：

`<foreach>`标签用于遍历集合，它的属性：

1. collection：代表要遍历的集合元素，注意编写时不要写#{}
2. open：代表语句的开始部分
3. close：代表结束部分
4. item：代表遍历集合的每个元素，生成的变量名
5. sperator：代表分隔符



### SQL片段抽取

Sql 中可将重复的 sql 提取出来，使用时用 include 引用即可，最终达到 sql 重用的目的

```xml
<!--抽取sql片段简化编写-->
<sql id="selectUser">select * from User</sql>
<select id="findById" parameterType="int" resultType="user">
    <include refid="selectUser"></include> where id=#{id}
</select>
<select id="findByIds" parameterType="list" resultType="user">
    <include refid="selectUser"></include>
    <where>
        <foreach collection="array" open="id in(" close=")" item="id" separator=",">
            #{id}
        </foreach>
    </where>
</select>
```



## Mybatis核心配置文件深入

### typeHandlers标签

无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时， 都会用类型处理器将获取的值以合适的方式转换成 Java 类型。下表描述了一些默认的类型处理器（截取部分）。

![image-20210321170520947](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230002.png)

你可以重写类型处理器或创建你自己的类型处理器来处理不支持的或非标准的类型。具体做法为：实现 org.apache.ibatis.type.TypeHandler 接口， 或继承一个很便利的类 org.apache.ibatis.type.BaseTypeHandler， 然后可以选择性地将它映射到一个JDBC类型。例如需求：一个Java中的Date数据类型，我想将之存到数据库的时候存成一个1970年至今的毫秒数，取出来时转换成java的Date，即java的Date与数据库的varchar毫秒值之间转换。

开发步骤：

1. 定义转换类继承类`BaseTypeHandler<T>`
2. 覆盖4个未实现的方法，其中setNonNullParameter为java程序设置数据到数据库的回调方法，getNullableResult为查询时 mysql的字符串类型转换成 java的Type类型的方法
3. 在MyBatis核心配置文件中进行注册

测试转换是否正确

```java
public class MyDateTypeHandler extends BaseTypeHandler<Date> {
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, Date date, JdbcType type) {
        preparedStatement.setString(i,date.getTime()+"");
    }
    public Date getNullableResult(ResultSet resultSet, String s) throws SQLException {
        return new Date(resultSet.getLong(s));
    }
    public Date getNullableResult(ResultSet resultSet, int i) throws SQLException {
        return new Date(resultSet.getLong(i));
    }
    public Date getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        return callableStatement.getDate(i);
    }
}
```

```xml
<!--注册类型自定义转换器-->
<typeHandlers>
    <typeHandler handler="com.itheima.typeHandlers.MyDateTypeHandler"></typeHandler>
</typeHandlers>
```

测试添加操作：

```java
user.setBirthday(new Date());
userMapper.add2(user);
```

#### Java数据类型和MySql数据类型对应表

![这里写图片描述](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230009.png)

### plugins标签

MyBatis可以使用**第三方的插件来对功能进行扩展**，分页助手PageHelper是将分页的复杂操作进行封装，使用简单的方式即可获得分页的相关数据

开发步骤：

1. 导入通用PageHelper的坐标
2. 在mybatis核心配置文件中配置PageHelper插件
3. 测试分页数据获取

**导入通用PageHelper坐标**

```xml
<!-- 分页助手 -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>3.7.5</version>
</dependency>
<dependency>
    <groupId>com.github.jsqlparser</groupId>
    <artifactId>jsqlparser</artifactId>
    <version>0.9.1</version>
</dependency>
```

**在mybatis核心配置文件中配置PageHelper插件**

```xml
<!-- 注意：分页助手的插件  配置在通用馆mapper之前 -->
<plugin interceptor="com.github.pagehelper.PageHelper">
    <!-- 指定方言 -->
    <property name="dialect" value="mysql"/>
</plugin>
```

**测试分页代码实现**

```java
@Test
public void testPageHelper(){
    //设置分页参数
    PageHelper.startPage(1,2);
    List<User> userList = userMapper2.select(null);
    for(User user : userList){
        System.out.println(user);
    }
}
```

**获得分页相关的其他参数**

```java
//其他分页的数据
PageInfo<User> pageInfo = new PageInfo<User>(userList);
System.out.println("总条数："+pageInfo.getTotal());
System.out.println("总页数："+pageInfo.getPages());
System.out.println("当前页："+pageInfo.getPageNum());
System.out.println("每页显示长度："+pageInfo.getPageSize());
System.out.println("上一页:"+pageInfo.getPrePage());
System.out.println("上一页:"+pageInfo.getNextPage());
System.out.println("是否第一页："+pageInfo.isIsFirstPage());
System.out.println("是否最后一页："+pageInfo.isIsLastPage());
```



## Mybatis多表操作

### xml配置

**一对一**

![image-20210321184552993](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230016.png)

![image-20210321184610801](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230021.png)

```xml
<mapper namespace="com.itheima.mapper.OrderMapper">
    <resultMap id="orderMap" type="com.itheima.domain.Order">
        <result column="uid" property="user.id"></result>
        <result column="username" property="user.username"></result>
        <result column="password" property="user.password"></result>
        <result column="birthday" property="user.birthday"></result>
    </resultMap>
    <select id="findAll" resultMap="orderMap">
    	select * from orders o,user u where o.uid=u.id
    </select>
</mapper>
```

或

```xml
<resultMap id="orderMap" type="com.itheima.domain.Order">
    <result property="id" column="id"></result>
    <result property="ordertime" column="ordertime"></result>
    <result property="total" column="total"></result>
    <association property="user" javaType="com.itheima.domain.User">
        <result column="uid" property="id"></result>
        <result column="username" property="username"></result>
        <result column="password" property="password"></result>
        <result column="birthday" property="birthday"></result>
    </association>
</resultMap>
```



**一对多**

![image-20210321190045301](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230029.png)

![image-20210321190058689](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230035.png)

![image-20210321190140515](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230042.png)



**多对多**

![image-20210321190302450](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230048.png)

![image-20210321190310051](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230055.png)

![image-20210321190446678](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230101.png)

### 注解配置

这几年来注解开发越来越流行，Mybatis也可以使用注解开发方式，这样我们就可以减少编写Mapper
映射文件了。我们先围绕一些基本的CRUD来学习，再学习复杂映射多表操作。

1. @Insert：实现新增
2. @Update：实现更新
3. @Delete：实现删除
4. @Select：实现查询
5. @Result：实现结果集封装
6. @Results：可以与@Result 一起使用，封装多个结果集
7. @One：实现一对一结果集封装
8. @Many：实现一对多结果集封装

#### 基础增删改查

```xml
<mappers>
    <!--扫描使用注解的类-->
    <mapper class="com.itheima.mapper.UserMapper"></mapper>
</mappers>
```

或

```xml
<mappers>
    <!--扫描使用注解的类所在的包-->
    <package name="com.itheima.mapper"></package>
</mappers>
```

```java
@Insert("insert into user values(#{id},#{username},#{password},#{birthday})")
public void save(User user);

@Update("update user set username=#{username},password=#{password} where id=#{id}")
public void update(User user);

@Delete("delete from user where id=#{id}")
public void delete(int id);

@Select("select * from user where id=#{id}")
public User findById(int id);
```



#### 复杂关系查询

实现复杂关系映射之前我们可以在映射文件中通过配置`<resultMap>`来实现，使用注解开发后，我们可以使用@Results注解，@Result注解，@One注解，@Many注解组合完成复杂关系的配置

![image-20210321195005295](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230112.png)

![image-20210321195021427](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230206.png)

**一对一**

![image-20210321195154358](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230218.png)

**一对多**

![image-20210321195240470](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230225.png)

**多对多**

![image-20210321195312897](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521230232.png)