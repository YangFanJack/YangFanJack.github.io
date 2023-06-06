
Java的常用web框架中会有很多配置文件，这些配置文件都是为了降低代码耦合性而从代码中剥离出来的。为了在程序运行时动态地加载这些文件进内存，需要加载这些配置文件，学习过程中发现有很多种方法都能将这些配置文件加载进内存，需要总结下方便记忆。



## 基于ClassLoader加载配置文件

```java
Properties properties = new Properties();
InputStream in = 当前类名.class.getClassLoader().getResourceAsStream("配置文件相对路径");
properties.load(in);
System.out.println(properties.getProperty("username"));
System.out.println(properties.getProperty("password"));
```

## 基于Resources读取配置文件

```java
InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
SqlSession sqlSession = sqlSessionFactory.openSession();
//获得MyBatis框架生成的UserMapper接口的实现类
UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
User user = userMapper.findById(1);
System.out.println(user);
sqlSession.close();
```



## 基于ResourceBundle读取配置文件

```java
ResourceBundle resourceBundle = ResourceBundle.getBundle("配置文件相对路径");
System.out.println(resourceBundle.getString("username"));

InputStream in3 = new FileInputStream(new File(filePath));
ResourceBundle resourceBundle2 = new PropertyResourceBundle(in3);
System.out.println(resourceBundle2.getString("username"));
```

