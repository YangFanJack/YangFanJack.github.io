# 玩儿转MySQL


数据库的特点：

1. 持久化存储数据，数据库就是一个文件系统
2. 方便存储并管理数据
3. 提供了方式统一操作数据

数据模型示意图：客户端 -> DBMS -> 数据库 -> 表
注意：DBMS可以操作多个数据库，单个数据库可以存在多个表

常见的数据库管理系统：
  1. Oracle
  2. MySQL
  3. Microsoft SQL Server
  4. DB2
  5. MongoDB
  6. SQLite
  7. Redis

## MySQL准备

### 安装：推荐5.7版本

* Windows直接官网下载安装包，然后根据提示选择并下一步，如果要删除除了在控制面板卸载，还需要在C盘的ProgramData的目录下的mysql目录卸载

* Linux下的安装/卸载参考博客另一篇博文《我的linux工作平台搭建》

### 启动

```bash
#本地启动
mysql -uroot -proot
#远程连接
mysql -h127.0.0.1 -uroot -proot
#详细写
mysql --host=127.0.0.1 --user=root --password=root

#退出
exit or quit
```

## MySQL执行流程
1. ![MySQL内部架构各个功能模块](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/20230907143925.png)
   1. Server层负责建立连接，分析和执行SQL。MySQL 大多数的核心功能模块（连接器，查询缓存、解析器、预处理器、优化器、执行器等）都在这实现。另外，所有的内置函数（如日期、时间、数学和加密函数等）和所有跨存储引擎的功能（如存储过程、触发器、视图等。）都在 Server 层实现。
   2. 存储引擎层负责数据的存储和提取。不同的存储引擎共用一个 Server 层。现在最常用的存储引擎是 InnoDB，从 MySQL 5.5 版本开始， InnoDB 成为了 MySQL 的默认存储引擎。我们常说的索引数据结构，就是由存储引擎层实现的，不同的存储引擎支持的索引类型也不相同，比如 InnoDB 支持索引类型是 B+树。
2. 第一步：连接器
   1. 与客户端进行TCP三次握手建立连接
   2. 校验客户端的用户名和密码，如果用户名和密码不对，报错
   3. 如果校验通过，读取用户权限，之后sql语句的权限逻辑判断都基于此时读取的权限
3. 第二步：查询缓存
   1. 以key（查询语句）-value（查询结果）的形式存在，理论上能加速查询速度
   2. 查询缓存挺鸡肋，对于更新频繁的表，缓存的命中率很低（只要一个表有更新操作，那么这个表的查询缓存就会被清空）
   3. MySQL 8.0 版本直接将查询缓存删掉了，对于 MySQL 8.0 之前的版本，如果想关闭查询缓存，我们可以通过将参数 query_cache_type 设置成 DEMAND
4. 第三步：解析SQL
   1. 词法分析：把SQL字符串识别出一个个的关键字，非关键字
   2. 语法分析：根据词法分析的结果，语法解析器会根据语法规则判断SQL语句是否满足MySQL语法，没有问题就会构建出SQL语法树
      * ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/20230907152512.png)
      * 注意，表不存在或者字段不存在，MySQL5.7中是在此时进行。但是MySQL8中并不是在解析器里做的，是在执行的时候做的
5. 第四步：执行SQL
   1. prepare预处理阶段：
      1. 检查SQL语句中的表，字段是否存在
      2. 将 select * 中的 * 符号，扩展为表上的所有列
   2. optimize优化阶段：
      1. 优化器主要负责将 SQL 查询语句的执行方案确定下来，比如在表里面有多个索引的时候，优化器会基于查询成本的考虑，选择一个查询成本最小的执行计划
      2. 要想知道优化器选择了哪个索引，我们可以在查询语句最前面加个 explain 命令，这样就会输出这条 SQL 语句的执行计划。
      3. 执行计划中的 key 就表示执行过程中使用了哪个索引
      4. 如果查询语句的执行计划里的 key 为 null 说明没有使用索引，那就会全表扫描（type = ALL），这种查询扫描的方式是效率最低的
   3. execute执行阶段：
      1. 优化器的输出就是执行计划，执行器按照执行计划和存储引擎交互，交互是以记录为单位的
      2. 主键索引查询の执行：
         ```sql
            select * from product where id = 1;
            -- 执行器第一次查询，会调用 read_first_record 函数指针指向的函数，因为优化器选择的访问类型为 const，这个函数指针被指向为 InnoDB 引擎索引查询的接口，把条件 id = 1 交给存储引擎，让存储引擎定位符合条件的第一条记录
            -- 存储引擎通过主键索引的 B+ 树结构定位到 id = 1的第一条记录，如果记录是不存在的，就会向执行器上报记录找不到的错误，然后查询结束。如果记录是存在的，就会将记录返回给执行器；
            -- 执行器从存储引擎读到记录后，接着判断记录是否符合查询条件，如果符合则发送给客户端，如果不符合则跳过该记录。
            -- 执行器查询的过程是一个 while 循环，所以还会再查一次，但是这次因为不是第一次查询了，所以会调用 read_record 函数指针指向的函数，因为优化器选择的访问类型为 const，这个函数指针被指向为一个永远返回 -1 的函数，所以当调用该函数的时候，执行器就退出循环，也就是结束查询了
         ```
      3. 全表扫描の执行：
         ```sql
            select * from product where name = 'iphone';
            -- 执行器第一次查询，会调用 read_first_record 函数指针指向的函数，因为优化器选择的访问类型为 all，这个函数指针被指向为 InnoDB 引擎全扫描的接口，让存储引擎读取表中的第一条记录
            -- 执行器会判断读到的这条记录的 name 是不是 iphone，如果不是则跳过；如果是则将记录发给客户的（是的没错，Server 层每从存储引擎读到一条记录就会发送给客户端，之所以客户端显示的时候是直接显示所有记录的，是因为客户端是等查询语句查询完成后，才会显示出所有的记录）。
            -- 执行器查询的过程是一个 while 循环，所以还会再查一次，会调用 read_record 函数指针指向的函数，因为优化器选择的访问类型为 all，read_record 函数指针指向的还是 InnoDB 引擎全扫描的接口，所以接着向存储引擎层要求继续读刚才那条记录的下一条记录，存储引擎把下一条记录取出后就将其返回给执行器（Server层），执行器继续判断条件，不符合查询条件即跳过该记录，否则发送到客户端；
            --一直重复上述过程，直到存储引擎把表中的所有记录读完，然后向执行器（Server层） 返回了读取完毕的信息；
            -- 执行器收到存储引擎报告的查询完毕的信息，退出循环，停止查询。
         ```
      4. 索引下推の执行：
         ```sql
            -- 索引下推能够减少二级索引在查询时的回表操作，提高查询的效率，因为它将 Server 层部分负责的事情，交给存储引擎层去处理了
            select * from t_user  where age > 20 and reward = 100000;
            -- 联合索引当遇到范围查询 (>、<) 就会停止匹配，也就是 age 字段能用到联合索引，但是 reward 字段则无法利用到索引
            -- 不使用索引下推（MySQL 5.6 之前的版本）时，执行器与存储引擎的执行流程：先由存储引擎定位到age>20的第一条记录，回表将完整记录返回给server层，并由server判断reward是否=100000以及其他非索引条件，以此往复
            -- 用索引下推后，判断记录的 reward 是否等于 100000 的工作交给了存储引擎层，过程：先由存储引擎定位到age>20的第一条记录，判断reward是否=100000，成立才回表把完整数据返回Server层判断其他非索引条件是否满足，以此往复
            -- 充分利用索引中的判断条件来减少回表次数
            -- 当你发现执行计划里的 Extr 部分显示了 “Using index condition”，说明使用了索引下推
         ```
## MySQL行记录存储机制
> 1. MySQL的NULL值是如何存放的？
> 2. MySQL 的 NULL 值会占用空间吗？
> 3. MySQL 怎么知道 varchar(n) 实际占用数据的大小？
> 4. varchar(n) 中 n 最大取值为多少？
> 5. 行溢出后，MySQL 是怎么处理的？

1. MySQL数据存放在哪个文件？
   1. `SHOW VARIABLES LIKE 'datadir';`：结果：/var/lib/mysql/，我们每创建一个 database（数据库） 都会在 /var/lib/mysql/ 目录里面创建一个以 database 为名的目录，然后保存表结构和表数据的文件都会存放在这个目录里。
   2. opt文件：存储当前数据库默认字符集和字符校验规则，一个数据库一个
   3. frm文件：存储表结构定义，一张表一个
   4. ibd文件：存储表数据，一张表一个。表数据既可以存在共享表空间文件（文件名：ibdata1）里，也可以存放在独占表空间文件（文件名：表名字.ibd）。主键索引放在ibd文件中，二级索引放在ibdata1中
   5. MySQL的存储引擎主要就是以这些库文件的形式存在,存储在MySQL的数据目录内
2. 表空间文件的结构？
   1. 表空间由段segment，区extent，页page，行row组成
   2. row：
      1. 数据库表中的记录都是按行（row）进行存放的，每行记录根据不同的行格式，有不同的存储结构。
   3. page：
      1. 记录是按照行来存储的，是按「页」为单位来读写的
      2. 页是 InnoDB 存储引擎磁盘管理的最小单元，默认每个页的大小为 16KB，也就是最多能保证 16KB 的连续存储空间
      3. 页的类型有很多，常见的有数据页、undo 日志页、溢出页等等
   4. extent：
      1. InnoDB 存储引擎是用 B+ 树来组织数据的，B+ 树中每一层都是通过双向链表连接起来的，如果是以页为单位来分配存储空间，那么链表中相邻的两个页之间的物理位置并不是连续的，可能离得非常远，那么磁盘查询时就会有大量的随机I/O，随机 I/O 是非常慢的
      2. 在表中数据量大的时候，为某个索引分配空间的时候就不再按照页为单位分配了，而是按照区（extent）为单位分配。每个区的大小为 1MB，对于 16KB 的页来说，连续的 64 个页会被划为一个区，这样就使得链表中相邻的页的物理位置也相邻，就能使用顺序 I/O 了
   5. segment：
      1. 表空间是由各个段（segment）组成的，段是由多个区（extent）组成的。段一般分为数据段、索引段和回滚段等
      2. 索引段：存放 B + 树的非叶子节点的区的集合；
      3. 数据段：存放 B + 树的叶子节点的区的集合；
      4. 回滚段：存放的是回滚数据的区的集合，之前讲事务隔离 (opens new window)的时候就介绍到了 MVCC 利用了回滚段实现了多版本查询数据。
3. InnoDB行格式：
   1. 四种：Redundant、Compact、Dynamic、Compressed
4. Compact行格式
   1. ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/20230908105022.png)
   2. 记录的额外信息：变长字段长度列表、NULL 值列表、记录头信息
      1. 变长字段长度列表：
         * 在存储varchar(n)数据的时候，也要把数据占用的大小存起来，存到「变长字段长度列表」里面，读取数据的时候才能根据这个「变长字段长度列表」去读取对应长度的数据。其他 TEXT、BLOB 等变长字段也是这么实现的
         * 「记录头信息」中指向下一个记录的指针，指向的是下一条记录的「记录头信息」和「真实数据」之间的位置，这样的好处是向左读就是记录头信息，向右读就是真实数据，比较方便。
         * 这些变长字段的真实数据占用的字节数会按照列的顺序逆序存放，是因为这样可以使得位置靠前的记录的真实数据和数据对应的字段长度信息可以同时在一个 CPU Cache Line 中，这样就可以提高 CPU Cache 的命中率
      2. NULL值列表：
         * NULL 是不会存放在行格式中记录的真实数据部分里的，如果存在允许 NULL 值的列，则每个列对应一个二进制位（bit），二进制位按照列的顺序逆序排列
         * NULL值列表会占用 1 字节空间，当表中所有字段都定义成 NOT NULL，行格式中就不会有 NULL值列表，这样可节省 1 字节的空间
      3. 记录头信息：
         * delete_mask ：标识此条数据是否被删除。执行 detele 删除记录的时候，并不会真正的删除记录，只是将这个记录的 delete_mask 标记为 1。
         * next_record：下一条记录的位置。指向的是下一条记录的「记录头信息」和「真实数据」之间的位置，这样的好处是向左读就是记录头信息，向右读就是真实数据，比较方便。
         * record_type：表示当前记录的类型，0表示普通记录，1表示B+树非叶子节点记录，2表示最小记录，3表示最大记录
   3. 记录的真实信息：
      1. row_id：如果我们建表的时候指定了主键或者唯一约束列，那么就没有 row_id 隐藏字段了。如果既没有指定主键，又没有唯一约束，那么 InnoDB 就会为记录添加 row_id 隐藏字段。row_id不是必需的，占用 6 个字节。
      2. trx_id：事务id，表示这个数据是由哪个事务生成的。 trx_id是必需的，占用 6 个字节
      3. roll_pointer：这条记录上一个版本的指针。roll_pointer 是必需的，占用 7 个字节。
      4. trx_id 和 roll_pointer 和 MVCC机制的实现有关
5. VARCHAR(n)中n最大取值为多少？
   1. MySQL 规定除了 TEXT、BLOBs 这种大对象类型之外，其他所有的列（不包括隐藏列和记录头信息）占用的字节长度加起来不能超过 65535 个字节
   2. 一行数据的最大字节数 65535，其实是包含「变长字段长度列表」和 「NULL 值列表」所占用的字节数的
6. 行溢出后，MYSQL如何处理？
   1. MySQL 中磁盘和内存交互的基本单位是页，一个页的大小一般是 16KB，也就是 16384字节，而一个 varchar(n) 类型的列最多可以存储 65532字节，一些大对象如 TEXT、BLOB 可能存储更多的数据，这时一个页可能就存不了一条记录。这个时候就会发生行溢出，多的数据就会存到另外的「溢出页」中
   2. Compact 行格式在发生行溢出后的处理：
      ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/20230908110816.png)
   3. Compressed 和 Dynamic 这两种格式采用完全的行溢出方式(记录的真实数据处不会存储该列的一部分数据，只存储 20 个字节的指针来指向溢出页。而实际的数据都存储在溢出页中)
      ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/20230908110913.png)

## SQL语句

* **SQL：** Structured Qurey Language，结构化查询语言实际上就是定义了操作所有关系型数据库的规则

```mysql
-- SQL语句注意事项：
# SQL语句特有的注释
/**
	SQL语句可以单行或者多行书写，以分号结尾
	SQL语句不区分大小写，关键字建议使用大写
**/

-- 查看该操作系统下的mysql各文件的存储位置
show variables like '%dir%';
-- 查看该操作系统下的编码情况
show variables like '%character%';
```

### SQL语句分类
1. DDL	Data Definition Language	数据定义语言，用来定义数据库对象（数据库，表，字段）
2. DML	Data Manipulation Language	数据操作语言，用来对数据库表中的数据进行增删改
3. DQL	Data Query Language	数据查询语言，用来查询数据库中表的记录
4. DCL	Data Control Language	数据控制语言，用来创建数据库用户，控制数据库的访问权限

#### DDL（操作数据库、表）

* 数据定义语言
* drop，create，alter

**Create**

create数据库:

```mysql
create database if not exists db1;
create database db2 character set gbk;
```

数据库类型：

1. int(位数)：整数类型
2. double(位数)：小数类型
3. date：日期（yyyy-MM-dd）
4. datetime：日期(1000-01-01 00:00:00 至 9999-12-31 23:59:59)
5. timestamp：时间戳(1970-01-01 00:00:01 至 2038-01-19 03:14:07)
6. varchar(位数)：字符串类型
7. bolb：二进制长文本
8. text：长文本

create表:

```mysql
create table 表名{
	列名1 数据类型1,
	列名2 数据类型2,
	......
	列名n 数据类型n
}
# 复制表
create table 表名1 like 表名2;
```

**Retrieve**

show数据库:

```mysql
# 查询mysql目前所有数据库
show databases;
# 查询当前数据库
select database();
# 使用数据库
use db1;
# 查看数据库字符集
show create database mysql;
```

show表:

```mysql
# 查询当前数据库中所有表
show tables;
# 查询表结构
desc 表名;
```

**Alter**

alter数据库

```mysql
# 修改数据库字符集
alter database db2 character set utf8;
```

alter表

```mysql
# 修改表名 
alter table 表名 rename to 新表名;
# 修改字符集
show create table 表名;
alter table 表名 character set utf8;
# 添加一列
alter table 表名 add 列名 数据类型;
# 修改列名 类型
alter table 表名 change 列名 新列名 数据类型;#列名数据类型一起改
alter table 表名 modify 列名 数据类型;#只改类型
# 删除列
alter table 表名 drop 列名;
```

**Drop**

drop数据库

```mysql
drop database if exists db2 ;
```

drop表

```mysql
drop table 表名;
drop table if exists 表名;
```

#### DML（增删改表中的数据）

* 数据操作语言
* insert，delete，update

**基础查询**

```mysql
select [distintct结果去重]
	字段列表[可四则运算，如果有null则结果都为null，用IFNULL解决]
	[as 别名]
from 
	表名列表
where
	条件列表[比较运算符，and or，between and，in(x,x...)]
	[null只能用is null来判断]
	[like 占位符：_，%]
group by
	分组字段
having
	分组后条件
order by
	排序字段 ASC/DESC
limit
	分页限定
```

**insert**

* 如果不写表名，表示所有字段

```mysql
insert into 表名(列名1,列名2...列名n) values(值1,值2...值n);
select * from 表名;
```

**delete**

```mysql
delete form 表名 where 条件;
# 删除所有记录
delete from 表名; #多少记录删除多少下
truncate table 表名; #先删除表，然后创建一张一样的表，效率更高
```

**update**

```mysql
update 表名 set 列名1=值1,列名2=值2 where 条件;
```



#### DQL（查询表中的数据）

* 数据查询语言
* select，where

**排序查询**

```mysql
select * from 表名 order by 排序字段1 DESC,排序字段2 ASC;
```

**聚合函数**

* 将一列数据作为一个整体进行纵向计算
* count，max，min，sum，avg
* 聚合函数的计算会排除null值
  1. 解决方案1：选择非空列（主键 or *）
  2. 解决方案2：IFNULL函数

```mysql
select count(列名) from 表名;
```

**分组查询**

* 查询显示列只能是当前分组字段 或者 纵向处理后的聚合函数
* where是分组之前的条件限制，having是分组之后的条件限制
* where后筛选出条目—>group分组—>having分组后筛选
* where后不能跟聚合函数，having后可以跟聚合函数，因为聚合函数是对where后的结果进行计算的

```mysql
select 分组字段/聚合函数 from 表名 where 条件 group by 分组字段 having 条件;
```

**分页查询**

* 当前索引 = (当前页码 - 1) * 每页条数
* limit语句是mysql的“方言”

```mysql
select 列名 from 表名 limit 开始的索引,每页查询的条数;
```

#### DCL（授权）

* 数据控制语言
* GRANT，REVOKE

```mysql
# 添加用户
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';

# 删除用户
DROP USER '用户名'@'主机名';

# 修改用户
UPDATE USER SET PASSWORD = PASSWORD('新密码') WHERE USER = '用户名';
SET PASSWORD FOR '用户名'@'主机名' = PASSWORD('新密码');

# 查询用户
-- 1. 切换到mysql数据库
USE myql;
-- 2. 查询user表
SELECT * FROM USER;

# 忘记root密码？
1. 停止mysql服务
2. 使用无验证方式启动mysql服务：
mysqld --skip-grant-tables
3. 直接输入:use mysql
4. update user set password = password('你的新密码') where user = 'root';
5. 结束mysqld进程
6. 使用新密码登录
```

```mysql
1. 查询权限：
	-- 查询权限
	SHOW GRANTS FOR '用户名'@'主机名';

2. 授予权限：
	-- 授予权限
	grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';
	-- 给张三用户授予所有权限，在任意数据库任意表上		
	GRANT ALL ON *.* TO 'zhangsan'@'localhost';
	
3. 撤销权限：
	-- 撤销权限：
	revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';
	REVOKE UPDATE ON db3.`account` FROM 'lisi'@'%';
```



## 约束

* 对表中的数据进行限定，保证数据的正确性，有效性和完整性
* mysql中约束分类：
  1. 主键约束：primary key
     * 主键：非空 且 唯一
     * 一张表只能有一个字段是主键
     * 主键是表中每条记录的唯一标识
     * 自增长：如果某一列是数值类型，使用auto_increment可以来完成自动增长，一般配合主键一起使用
  2. 非空约束：not null
  3. 唯一约束：unique
     * mysql中多个null不算重复
  4. 外键约束：foreign key
     * 如果关系模式R1中的某属性集不是自己的主键，而是关系模式R2的主键，则该属性集称为是关系模式R1的外键
  5. 默认约束：DEFAULT，为字段设置默认值
  6. 检查约束：check，保证字段值满足某一条件

```mysql
# 1. 主键约束：primary key
create table stu(
	id int primary key auto_increment;
	name vachar(30);
);
alter table stu drop primary key;#删主键约束
alter table stu modify id int;#删自动增长（删不掉主键）
alter table stu modify name varchar(30) primary key auto_increment#添加主键约束 & 自动增长

# 2. 非空约束：not null
create table stu(
	id int;
	name vachar(30) not null;
);
alter table stu modify id varchar(30);#删非空约束
alter table stu modify id varchar(30) not null;#添加非空约束

# 3. 唯一约束：unique
create table stu(
	id int unique;
	name vachar(30);
);
alter table stu drop index id;#删唯一约束
alter table stu modify id int unique;#添加唯一约束

# 4. 外键约束：foreign key
create table 表名(
    ....
    外键列
    constraint 外键名称 foreign key (外键列名称) references 主表名称(主表唯一约束列名)
);
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称; #删除外键
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名称) REFERENCES 主表名称(主表列名称); #添加外键
# 外键级联操作（谨慎使用）
1. 级联更新：ON UPDATE CASCADE
2. 级联删除：ON DELETE CASCADE
```



## 多表之间的关系

### 一对一

* 人和身份证
* 可以在任意一方添加外键指向另一方的主键，然后将外键设置为unique

### 一对多

* 部门和员工
* 在多的一方建立外键，指向一的一方的主键

### 多对多

* 学生和课程
* 需要借助第三张中间表，中间表中的两个字段作为中间表两个外键，分别指向两个表的主键



## 范式

* 设计关系型数据库时需要遵循的一些规范

* 各种范式成递次规范，越高的范式数据库冗余越小

* 目前关系型数据库有6中范式：

  1. **第一范式（1NF）：**每一列都是不可分割的原子项

  ![image-20210313140809522](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgimage-20210313140809522.png)

  2. **第二范式（2NF）：**在1NF的基础上消除了非主属性对主码的部分函数依赖

  * ** 通过A可以 确定唯一B，则B依赖于A
  * **完全函数依赖：** 如果A是一个属性组，则B的确定需要依赖于A属性组中所有属性，此时B完全依赖于A属性组
  * **部分函数依赖：** B属性的确定只需要依赖于A属性组中某一些值，则B部分依赖于A属性组
  * **传递函数依赖：** A—>B，B—>C，C传递函数依赖于A
  * **码：** 一个表中，如果一个属性或属性组被其他所有属性所**完全依赖**，则这个属性或属性组为该表的码。（也称为候选码，同一张表中可以有多个候选码）
  * **主属性：** 候选码中的所有属性
  * **非主属性：** 一张表中，除了主属性以外的其他所有属性

  ![image-20210313140916162](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgimage-20210313140916162.png)

  3. **第三范式（3NF）：** 在2NF的基础上消除了传递依赖

  ![image-20210313141101564](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgimage-20210313141101564.png)

  4. 巴斯-科德范式（BCNF）：需要消除“表中的主属性部分依赖于某个候选码“这种情况，也就是**主属性内部不能有部分或传递依赖**

  5. 第四范式（4NF）

  6. 第五范式（5NF）



## 数据库备份和还原

* 也就是把目前的数据库所有状态写进sql文件中持久化保存

1. 命令行：
	
	* 语法：
		* 备份：
		
		```mysql
		mysqldump -u用户名 -p密码 数据库名称 > 保存的路径
		```
		
		* 还原：
			1. 登录数据库
			2. 创建数据库
			3. 使用数据库
			4. 执行备份文件
		
		```mysql
		source 文件路径
		```
	
2. 图形化工具备份



## 多表查询

```mysql
select
	列名列表
from
	表名列表
where
	表1,表2...
```

不同where加条件就会得到笛卡尔积，多表查询要做的就是消除笛卡尔积的无用内容

### 内连接查询

#### 隐式内连接

```mysql
-- 查询所有员工信息和对应的部门信息
SELECT * FROM emp,dept WHERE emp.`dept_id` = dept.`id`;

-- 查询员工表的名称，性别。部门表的名称
SELECT emp.name,emp.gender,dept.name FROM emp,dept WHERE emp.`dept_id` = dept.`id`;

-- 加上别名
SELECT 
	t1.name, -- 员工表的姓名
	t1.gender,-- 员工表的性别
	t2.name -- 部门表的名称
FROM
	emp t1,
	dept t2
WHERE 
	t1.`dept_id` = t2.`id`;
```

#### 显式内连接

```mysql
SELECT * FROM emp INNER JOIN dept ON emp.`dept_id` = dept.`id`;
SELECT * FROM emp JOIN dept ON emp.`dept_id` = dept.`id`;
```



### 外连接查询

#### 左外连接

* 查询的左表所有信息以及和右表的交集部分

```mysql
-- 查询所有员工信息，如果员工有部门，则查询部门名称，没有部门，则不显示部门名称
SELECT 	t1.*,t2.`name` FROM emp t1 LEFT JOIN dept t2 ON t1.`dept_id` = t2.`id`;
```

#### 右外连接

* 查询的右表所有信息以及和左表的交集部分

```mysql
SELECT * FROM dept t2 RIGHT JOIN emp t1 ON t1.`dept_id` = t2.`id`;
```

### 子查询

* 查询中嵌套查询，称嵌套查询为子查询。

```mysql
-- 查询工资最高的员工信息
-- 1 查询最高的工资是多少 9000
SELECT MAX(salary) FROM emp;

-- 2 查询员工信息，并且工资等于9000的
SELECT * FROM emp WHERE emp.`salary` = 9000;

-- 一条sql就完成这个操作。子查询
SELECT * FROM emp WHERE emp.`salary` = (SELECT MAX(salary) FROM emp);
```

* 子查询结果的几种情况：
  1. **单行单列：**子查询结果可以作为条件（用比较运算符）
  2. **多行单列：**子查询结果可以作为集合条件（用 in）
  3. **多行多列：**子查询结果集可以作为一张虚拟表继续进行查询 或者 作为多字段条件in查询



## 事务

### 事务的基本介绍

* 如果一个包含多个步骤的业务操作，被食物管理，那么这些操作要么同时成功，要么同事失效

* 操作：

  1. 开启事务：`start transactoin`
  2. 回滚：`rollback`
  3. 提交：`commit`

* mysql数据库中事务默认自动提交

  ```mysql
  #查看默认事务提交方式
  select @@autocommit; #1自动，0手动
  set @@autocommit = 0;
  ```

### 事务的四大特征

1. 原子性：不可分割最小操作单位
2. 持久性：当事务提交或回滚后，数据库会持久化保存数据
3. 隔离性：多个事务之间应该相互独立
4. 一致性：事务 操作前后，数据总量保持不变

### 事务的隔离级别

* 概念：多个事务之间是隔离的，相互隔离的。但是如果多个事务（线程）操作**同一批数据，则会引发一些问题**，设置不同的隔离级别就可以解决这些问题。

* **脏读：** 一个事务读到另外一个事务还没有提交的数据

* **不可重复读（虚读）：** 一个事务先后读取同一条记录，但两次读取的数据不同

* **幻读：** 一个事务按照条件查询数据时，没有对应的数据行，但在插入数据时，又发现该数据已经存在

* 4种隔离级别：级别从小到大安全性越高，效率越低

  1. **read uncommitted：**读未提交

     产生的问题：脏读，虚读，幻读

  2. **read committed：**读已提交（Oracle默认）

     产生的问题：虚读，幻读

  3. **repeatable read：**可重复读（MySQL默认）

     产生的问题：幻读

  4. **Serializable：**串行化

     产生的问题：无

  ```mysql
  #查询隔离级别
  select @@tx_isolation
  #数据库设置级别
  set global transaction isolation level 级别字符串;
  ```



> 接下来是MySQL高级部分

## 索引

* 索引是帮助MySQL高效获取数据的数据结构

![image-20210313203919303](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/imgimage-20210313203919303.png)

* 优势：
  1. 快速查询，降低IO消耗
  2. 降低数据排序成本，降低CPU消耗
* 劣势
  1. 索引占用磁盘控件
  2. 降低了更新表速率

### 索引结构

* MySQL目前有以下4种索引
  1. BTREE索引
  2. HASH索引
  3. R-tree索引
  4. Full-text索引
* MySQL默认支持的InnoDB引擎主要就是支持BTREE索引，主要也是介绍BTREE引擎

### BTREE索引

* MySQL中的BTREE索引默认是用的B+树

## 锁
>  从一个错误引申出Mysql的锁
`Lock wait timeout exceeded; try restarting transaction`
* 原因：后提交的事务等待前面的事务释放锁，但是等待的时候超过了mysql锁等待时间
* 解决：
  * information_schema/performance_schema表保存了MySQL服务器所有数据库信息
  * mysql8之前的版本:
    ```bash
    innodb_trx：当前运行的所有事务
    innodb_locks：当前出现的锁
    innodb_lock_waits：锁等待的对应关系
    ```
  * mysql8之后的版本：
    ```bash
    innodb_trx：当前运行的所有事务
    innodb_locks：当前出现的锁
    innodb_lock_waits：锁等待的对应关系
    ```
  * 查询数据库中有的锁：
    ```sql
    select * from information_schema.innodb_trx;
    # trx_mysql_thread_id就是事务对应的线程id，直接kill掉即可
    ```
  * 显示正在运行的线程,辅助定位问题:
    ```bash
    show full processlist;
    ```


