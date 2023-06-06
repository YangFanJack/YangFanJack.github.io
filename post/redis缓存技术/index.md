
## Redis简介：

**概念：** redis是一款高性能的NOSQL（Not Only SQL）系列的菲关系型数据库

![image-20210316153023744](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220522094933.png)

**Redis特点：**

* Redis 是一个开源的使用 ANSI、C 语言编写、遵守 BSD 协议、支持网络、可基于内存、分布式、可选持久性的键值对(Key-Value)存储数据库，并提供多种语言的 API。

* Redis 通常被称为数据结构服务器，因为值（value）可以是字符串(String)、哈希(Hash)、列表(list)、集合(sets)和有序集合(sorted sets)等类型。

* 关系型数据库和非关系数据库是**互补的关系**，通常情况下使用关系型数据库，在适合的情况下使用NOSQL数据库能够堆关系型数据库的不足进行弥补从而提高网站的性能。

* 一般会将数据存储在关系型数据库中，在nosql数据库中备份关系型数据库的数据

**目前Redis支持的键值数据类型如下：**

1. 字符串类型：string
2. 哈希类型：hash
3. 列表类型：list
4. 集合类型：set
5. 有序集合类型：sortedset

**Redis的应用场景：**

1. 缓存（数据查询，短连接，新闻内容，商品内容）
2. 聊天室的在线好友列表
3. 任务队列（秒杀，抢购，12306抢票）
4. 应用排行榜
5. 网站访问统计
6. 数据过期处理（可以精确到毫秒级别）
7. 分布式集群架构中的session分离

## 安装

debian：`sudo apt install redis`

启动服务：执行`sudo systemctl start redis`

关闭服务：执行`sudo systemctl stop redis`

进入客户端：`redis-cli`

## 命令操作

1. 字符串类型：string

   ```bash
   set key value
   get key
   del key
   ```

2. 哈希类型：hash

   ```bash
   hset key field value
   hget key field
   hgetall key
   hdel key field
   ```

3. 列表类型：list

   ```bash
   lpush key value
   rpush key value
   lrange key start end
   lpop key
   rpop key
   ```

4. 集合类型：set

   ```bash
   sadd key value
   smembers key
   srem key value
   ```

5. 有序集合类型：sortedset

   ```bash
   zadd key score value
   zrange key start end [withscores]
   zrem key value
   ```

**通用命令：**

1. `keys *`：查询所有的键
2. `type key`：获取键对应的value的类型
3. `del key`：删除指定的key value

## 持久化：

* redis是一个内存数据库，当redis服务器重启，获取电脑重启，数据会丢失，我们可以将redis内存中的数据持久化保存到硬盘的文件中。

#### Redis的持久化机制

* **RDB：** 默认方式，不需要进行配置，默认就使用这种机制

  在一定的间隔时间中，检测key的变化情况，然后持久化数据

  ```markdown
  编辑/etc/redis/redis.conf文件
  # after 900 sec (15 min) if at least 1 key changed
  save 900 1
  # after 300 sec (5 min) if at least 10 keys changed
  save 300 10
  # after 60 sec if at least 10000 keys changed
  save 60 10000
  ```

  重新启动redis服务器，并指定redis.conf：`redis-server /etc/redis/redis.conf`

  **注意：** 在linux上通过apt安装的redis，关闭服务器后重启默认会重现之前数据，也就是默认使用了RDB的方式。配置文件在`/etc/redis/redis.conf`，rdb存储文件在`/var/lib/redis`目录下。

* **AOF：** 日志记录的方式，可以记录每一条命令的操作。可以每一次命令操作后，持久化数据

  编辑redis.windwos.conf文件		
  ```markdown
  appendonly no（关闭aof） --> appendonly yes （开启aof）
  
  # appendfsync always ： 每一次操作都进行持久化
  appendfsync everysec ： 每隔一秒进行一次持久化
  # appendfsync no	 ： 不进行持久化
  ```

## Redis的java客户端：Jedis

使用步骤：

1. **导入Jedis相关jar包**

2. **使用**

   ```java
   //1. 获取连接
   Jedis jedis = new Jedis("localhost",6379);
   //2. 操作
   jedis.set("username","zhangsan");
   //3. 关闭连接
   jedis.close();
   ```

3. **Jedis对于String，hash，list，set，linkedset的操**作

   ```java
   //1. 获取连接
   Jedis jedis = new Jedis();//如果使用空参构造，默认值 "localhost",6379端口
   //2. 操作
   //存储
   jedis.set("username","zhangsan");
   //获取
   String username = jedis.get("username");
   System.out.println(username);
   
   //可以使用setex()方法存储可以指定过期时间的 key value
   jedis.setex("activecode",20,"hehe");//将activecode：hehe键值对存入redis，并且20秒后自动删除该键值对
   
   //3. 关闭连接
   jedis.close();
   ```

   ```java
   //1. 获取连接
   Jedis jedis = new Jedis();//如果使用空参构造，默认值 "localhost",6379端口
   //2. 操作
   // 存储hash
   jedis.hset("user","name","lisi");
   jedis.hset("user","age","23");
   jedis.hset("user","gender","female");
   
   // 获取hash
   String name = jedis.hget("user", "name");
   System.out.println(name);
   
   // 获取hash的所有map中的数据
   Map<String, String> user = jedis.hgetAll("user");
   // keyset
   Set<String> keySet = user.keySet();
   for (String key : keySet) {
       //获取value
       String value = user.get(key);
       System.out.println(key + ":" + value);
   }
   //3. 关闭连接
   jedis.close();
   ```

   ```java
   //1. 获取连接
   Jedis jedis = new Jedis();//如果使用空参构造，默认值 "localhost",6379端口
   //2. 操作
   // list 存储
   jedis.lpush("mylist","a","b","c");//从左边存
   jedis.rpush("mylist","a","b","c");//从右边存
   
   // list 范围获取
   List<String> mylist = jedis.lrange("mylist", 0, -1);
   System.out.println(mylist);
   
   // list 弹出
   String element1 = jedis.lpop("mylist");//c
   System.out.println(element1);
   
   String element2 = jedis.rpop("mylist");//c
   System.out.println(element2);
   
   // list 范围获取
   List<String> mylist2 = jedis.lrange("mylist", 0, -1);
   System.out.println(mylist2);
   
   //3. 关闭连接
   jedis.close();
   ```

   ```java
   //1. 获取连接
   Jedis jedis = new Jedis();//如果使用空参构造，默认值 "localhost",6379端口
   //2. 操作
   // set 存储
   jedis.sadd("myset","java","php","c++");
   
   // set 获取
   Set<String> myset = jedis.smembers("myset");
   System.out.println(myset);
   
   //3. 关闭连接
   jedis.close();
   ```

   ```java
   //1. 获取连接
   Jedis jedis = new Jedis();//如果使用空参构造，默认值 "localhost",6379端口
   //2. 操作
   // sortedset 存储
   jedis.zadd("mysortedset",3,"亚瑟");
   jedis.zadd("mysortedset",30,"后裔");
   jedis.zadd("mysortedset",55,"孙悟空");
   
   // sortedset 获取
   Set<String> mysortedset = jedis.zrange("mysortedset", 0, -1);
   
   System.out.println(mysortedset);
   //3. 关闭连接
   jedis.close();
   ```

4. **Jedis连接池**

   ```java
   //0.创建一个配置对象
   JedisPoolConfig config = new JedisPoolConfig();
   config.setMaxTotal(50);
   config.setMaxIdle(10);
   
   //1.创建Jedis连接池对象
   JedisPool jedisPool = new JedisPool(config,"localhost",6379);
   
   //2.获取连接
   Jedis jedis = jedisPool.getResource();
   //3. 使用
   jedis.set("hehe","heihei");
   //4. 关闭 归还到连接池中
   jedis.close();
   ```

   连接池工具类：

   ```java
   public class JedisPoolUtils {
       private static JedisPool jedisPool;
       static{
           //读取配置文件
           InputStream is = JedisPoolUtils.class.getClassLoader().getResourceAsStream("jedis.properties");
           //创建Properties对象
           Properties pro = new Properties();
           //关联文件
           try {
               pro.load(is);
           } catch (IOException e) {
               e.printStackTrace();
           }
           //获取数据，设置到JedisPoolConfig中
           JedisPoolConfig config = new JedisPoolConfig();
           config.setMaxTotal(Integer.parseInt(pro.getProperty("maxTotal")));
           config.setMaxIdle(Integer.parseInt(pro.getProperty("maxIdle")));
           //初始化JedisPool
           jedisPool = new JedisPool(config,pro.getProperty("host"),Integer.parseInt(pro.getProperty("port")));
       }
       /**
   	* 获取连接方法
   	*/
       public static Jedis getJedis(){
           return jedisPool.getResource();
       }
   }
   ```

   