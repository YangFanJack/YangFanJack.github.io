# JDBC


## JDBC

### JDBC本质：

其实是官方（sun公司）定义的一套操作所有关系型数据库的规则，即接口。各个数据库厂商去实现这套接口，提供数据库驱动jar包。我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动jar包中的实现类。

### 操作步骤

1. 导入驱动jar包 mysql-connector-java-5.1.37-bin.jar
2. 复制mysql-connector-java-5.1.37-bin.jar到项目的libs目录下
3. 右键-->Add As Library
4. 注册驱动
5. 获取数据库连接对象 Connection
6. 定义sql
7. 获取执行sql语句的对象 Statement
8. 执行sql，接受返回结果
9. 处理结果
10. 释放资源

```java
//1. 导入驱动jar包
//2.注册驱动
Class.forName("com.mysql.jdbc.Driver");
//3.获取数据库连接对象
//如果连接的是本机mysql服务器，并且mysql服务默认端口是3306，则url可以简写为：jdbc:mysql:///数据库名称
Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db3", "root", "root");
//4.定义sql语句
String sql = "update account set balance = 500 where id = 1";
//5.获取执行sql的对象 Statement
//prepareStatement(String sql)解决sql注入问题
Statement stmt = conn.createStatement();
//6.执行sql
int count = stmt.executeUpdate(sql);
//7.处理结果
System.out.println(count);
//8.释放资源
stmt.close();
conn.close();
```

### 几个重要对象

1. **DriverManager**

   ```java
   //通过查看源码发现：在com.mysql.jdbc.Driver类中存在静态代码块
   //mysql5之后的驱动jar包可以省略注册驱动的步骤。
   static {
       try {
           java.sql.DriverManager.registerDriver(new Driver());
       } catch (SQLException E) {
           throw new RuntimeException("Can't register driver!");
       }
   }
   ```

2. **Connection**

   ```java
   Statement createStatement();
   PreparedStatement prepareStatement(String sql);
   
   //事务管理
   //开启事务:调用该方法设置参数为false，即开启事务
   setAutoCommit(boolean autoCommit);
   //提交事务
   commit();
   //回滚事务
   rollback();
   ```

3. **Statement**

   ```java
   //可以执行任意的sql
   boolean execute(String sql);
       
   //执行DML（insert、update、delete）语句、DDL(create，alter、drop)语句
   //返回值：影响的行数，可以通过这个影响的行数判断DML语句是否执行成功 返回值>0的则执行成功，反之，则失败。
   int executeUpdate(String sql);
   
   //执行DQL（select)语句
   ResultSet executeQuery(String sql);
   ```

4. **Resultment**

   ```java
   //getXxx(参数):获取数据
   //Xxx：代表数据类型   如： int getInt() ,	String getString()
   //参数：
   //1. int：代表列的编号,从1开始   如： getString(1)
   //2. String：代表列名称。 如： getDouble("balance")
   
   //使用
   //循环判断游标是否是最后一行末尾。
   while(rs.next()){
       //获取数据
       //6.2 获取数据
       int id = rs.getInt(1);
       String name = rs.getString("name");
       double balance = rs.getDouble(3);
   
       System.out.println(id + "---" + name + "---" + balance);
   }
   ```

5. **PreparedStatement**

   ```java
   //SQL注入问题：在拼接sql时，有一些sql的特殊关键字参与字符串的拼接。会造成安全性问题
   //输入用户随便，输入密码：a' or 'a' = 'a
   select * from user where username = 'fhdsjkf' and password = 'a' or 'a' = 'a' 
   ```

### JDBC工具类

```java
public class JDBCUtils {
    private static String url;
    private static String user;
    private static String password;
    private static String driver;
    /**
     * 文件的读取，只需要读取一次即可拿到这些值。使用静态代码块
     */
    static{
        //读取资源文件，获取值。

        try {
            //1. 创建Properties集合类。
            Properties pro = new Properties();

            //获取src路径下的文件的方式--->ClassLoader 类加载器
            ClassLoader classLoader = JDBCUtils.class.getClassLoader();
            URL res  = classLoader.getResource("jdbc.properties");
            String path = res.getPath();
            System.out.println(path);///D:/IdeaProjects/itcast/out/production/day04_jdbc/jdbc.properties
            //2. 加载文件
            // pro.load(new FileReader("D:\\IdeaProjects\\itcast\\day04_jdbc\\src\\jdbc.properties"));
            pro.load(new FileReader(path));

            //3. 获取数据，赋值
            url = pro.getProperty("url");
            user = pro.getProperty("user");
            password = pro.getProperty("password");
            driver = pro.getProperty("driver");
            //4. 注册驱动
            Class.forName(driver);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
    
    /**
     * 获取连接
     * @return 连接对象
     */
    public static Connection getConnection() throws SQLException {

        return DriverManager.getConnection(url, user, password);
    }

    /**
     * 释放资源
     * @param stmt
     * @param conn
     */
    public static void close(Statement stmt,Connection conn){
        if( stmt != null){
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if( conn != null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    
    /**
     * 释放资源
     * @param stmt
     * @param conn
     */
    public static void close(ResultSet rs,Statement stmt, Connection conn){
        if( rs != null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if( stmt != null){
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if( conn != null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

}
```

## 数据库连接池

**概念：**其实就是一个容器(集合)，存放数据库连接的容器。当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器。

### 实现

 	1. 标准接口：`DataSource   javax.sql`包下的
 	  * 获取连接：`getConnection()`
 	  * 归还连接：`Connection.close()`。如果连接对象Connection是从连接池中获取的，那么调用Connection.close()方法，则不会再关闭连接了。而是归还连接

2. 一般我们不去实现它，有数据库厂商来实现
	1. C3P0：数据库连接池技术
	2. Druid：数据库连接池实现技术，由阿里巴巴提供的

```java
//C3P0
/**
1. 导入jar包 (两个) c3p0-0.9.5.2.jar mchange-commons-java-0.2.12.jar ，
	* 不要忘记导入数据库驱动jar包
2. 定义配置文件：
	* 名称： c3p0.properties 或者 c3p0-config.xml
		jdbc.driver=com.mysql.jdbc.Driver
		jdbc.url=jdbc:mysql://localhost:3306/mydb
		jdbc.username=root
		jdbc.password=root
	* 路径：直接将文件放在src目录下即可。

3. 创建核心对象 数据库连接池对象 ComboPooledDataSource
4. 获取连接： getConnection
**/
//1.创建数据库连接池对象
DataSource ds  = new ComboPooledDataSource();
//2. 获取连接对象
Connection conn = ds.getConnection();
```

```java
//Druid
/**
1. 导入jar包 druid-1.0.9.jar
2. 定义配置文件：
	* 是properties形式的
	* 可以叫任意名称，可以放在任意目录下
3. 加载配置文件。Properties
4. 获取数据库连接池对象：通过工厂来来获取  DruidDataSourceFactory
5. 获取连接：getConnection
**/
//3.加载配置文件
Properties pro = new Properties();
InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
pro.load(is);
//4.获取连接池对象
DataSource ds = DruidDataSourceFactory.createDataSource(pro);
//5.获取连接
Connection conn = ds.getConnection();
```

## JDBCTemplate

* Spring框架对JDBC的简单封装为Spring JDBC。提供了一个JDBCTemplate对象简化JDBC的开发

### 实现

1. 导入jar包

2. 创建JdbcTemplate对象。依赖于数据源DataSource

  * JdbcTemplate template = new JdbcTemplate(ds);

3. 调用JdbcTemplate的方法来完成CRUD的操作

   * **update():**执行DML语句。增、删、改语句

     ```java
     /**
     * 1. 修改1号数据的 salary 为 10000
     */
     @Test
     public void test1(){		
         //2. 定义sql
         String sql = "update emp set salary = 10000 where id = 1001";
         //3. 执行sql
         int count = template.update(sql);
         System.out.println(count);
     }
     /**
     * 2. 添加一条记录
     */
     @Test
     public void test2(){
         String sql = "insert into emp(id,ename,dept_id) values(?,?,?)";
         int count = template.update(sql, 1015, "郭靖", 10);
         System.out.println(count);
     
     }
     /**
     * 3.删除刚才添加的记录
      */
     @Test
     public void test3(){
         String sql = "delete from emp where id = ?";
         int count = template.update(sql, 1015);
         System.out.println(count);
     }
     ```

   * **queryForMap():**查询结果将结果集封装为map集合，将列名作key，将值作为value 将这条记录封装为一个map集合

     * 注意：这个方法查询的结果集长度只能是1

     ```java
     /**
     * 4.查询id为1001的记录，将其封装为Map集合
     * 注意：这个方法查询的结果集长度只能是1
     */
     @Test
     public void test4(){
         String sql = "select * from emp where id = ? or id = ?";
         Map<String, Object> map = template.queryForMap(sql, 1001,1002);
         System.out.println(map);
         //{id=1001, ename=孙悟空, job_id=4, mgr=1004, joindate=2000-12-17, salary=10000.00, bonus=null, dept_id=20}
     
     }
     ```

   * **queryForList():**查询结果将结果集封装为list集合

     * 注意：将每一条记录封装为一个Map集合，再将Map集合装载到List集合中

     ```java
     /**
     * 5. 查询所有记录，将其封装为List
      */
     @Test
     public void test5(){
         String sql = "select * from emp";
         List<Map<String, Object>> list = template.queryForList(sql);
     
         for (Map<String, Object> stringObjectMap : list) {
             System.out.println(stringObjectMap);
         }
     }
     ```

   * **query():**查询结果，将结果封装为JavaBean对象

     * query的参数：RowMapper
       * 一般我们使用BeanPropertyRowMapper实现类。可以完成数据到JavaBean的自动封装
       * new BeanPropertyRowMapper<类型>(类型.class)

     ```java
     /**
     * 6. 查询所有记录，将其封装为Emp对象的List集合
     */
     			
     @Test
     public void test6_2(){
         String sql = "select * from emp";
         List<Emp> list = template.query(sql, new BeanPropertyRowMapper<Emp>(Emp.class));
         for (Emp emp : list) {
             System.out.println(emp);
         }
     }
     ```

   * **queryForObject：**查询结果，将结果封装为一个对象

     * 一般用于聚合函数的查询

     ```java
     /**
     * 7. 查询总记录数
     */
     			
     @Test
     public void test7(){
         String sql = "select count(id) from emp";
         Long total = template.queryForObject(sql, Long.class);
         System.out.println(total);
     }
     ```

     
