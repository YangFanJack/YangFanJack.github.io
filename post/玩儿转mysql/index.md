
数据库的特点：

1. 持久化存储数据，数据库就是一个文件系统
2. 方便存储并管理数据
3. 提供了方式统一操作数据

常见的数据库软件：

* 数据库软件是对数据库的不同实现
* 常见的有：
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

#### DDL（操作数据库、表）

* 数据定义语言
* drop，create，alter

**Create**

数据库:

```mysql
create database if not exists db1;
create database db2 character set gbk;
```

数据库类型：

1. int(位数)：整数类型
2. double(位数)：小数类型
3. date：日期（yyyy-MM-dd）
4. datetime：日期（yyyy-MM-dd HH:mm:ss）
5. timestamp：时间戳类型，默认是当前系统的时间
6. varchar(位数)：字符串类型

表:

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

数据库:

```mysql
# 查询mysql目前所有数据库
show databases;
# 查看数据库字符集
show create database mysql;
```

表:

```mysql
# 查询当前数据库中所有表
show tables;
# 查询表结构
desc 表名;
```

**Update**

数据库

```mysql
# 修改数据库字符集
alter database db2 character set utf8;
```

表

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

**Delete**

数据库

```mysql
drop database if exists db2 ;
```

表

```mysql
drop table 表名;
drop table if exists 表名;
```

**使用数据库**

```mysql
# 查询当前正在使用您的数据库
select database();
# 使用数据库
use db1;
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

**add**

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

**modify**

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
  3. **多行多列：**子查询结果集可以作为一张虚拟表继续进行查询



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

* **脏读：** 读取到另一个事务没有提交的数据

* **不可重复读（虚读）：** 同一个事务中，两次读到的数据不一样

* **幻读：** 一个事务操作（DML）数据表中所有记录，另一个事务添加了一条数据，则第一个事务查询不到自己的修改

* 4中隔离级别：级别从小到大安全性越高，效率越低

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
  1. 快速查询
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

