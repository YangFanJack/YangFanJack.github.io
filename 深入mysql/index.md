# 深入MySQL


# 存储引擎
1. 定义：存储数据，建立索引，CRUD数据等技术的实现方式；存储引擎也被成为表类型
2. 存储引擎是基于表的，不是基于库的，不同表可以有不同存储引擎
3. 存储引擎的定义：
   ```mysql
    create table Name (
        ...
    )engine = InnoDB;
    # MySQL5.5后默认使用InnoDB
    show create table 表名;# 查看表的存储引擎
    show engines;# 查看改数据库支持的所有存储引擎
   ```
4. 几个存储引擎介绍：
   1. InnoDB
      * 遵循ACID，支持事务；行级锁，并发性好；支持Foreign Key约束
      * 表结构一个ibdata文件，数据索引一个ibd文件MyISAM
   2. MyISAM
      * MySQL早期版本默认引擎
      * 不支持事务，不支持外键
      * 支持表锁，不支持行锁
      * 增查快，删改慢
      * 表结构一个sdi文件，数据一个MYD文件，索引一个MYI文件
   3. Memory
      * 数据存在内存，只能作为临时表或缓存使用
      * 支持hash索引
      * 表结构一个sdi文件
# 索引
常见索引和数据结构，二叉树，红黑树，B树，B+树，Hash索引，聚集索引&非聚集索引，二级索引，覆盖索引，索引下推，单列索引&组合索引，索引优缺点
1. 定义：帮助MySQL高效获得数据的数据结构
2. 优点：提高数据检索效率，降低IO成本；提高排序效率，降低CPU成本
3. 缺点：索引占用空间；降低更新表（增删改）的速度
4. 分类：
   1. 按「数据结构」分类：B+tree索引（InnoDB，MyISAM，Memory）、Hash索引（Memory）、Full-text索引（InnoDB，MyISAM）
   2. 按「物理存储」分类：聚簇索引（主键索引）、二级索引（辅助索引）
   3. 按「字段特性」分类：主键索引、唯一索引、普通索引、前缀索引
   4. 按「字段个数」分类：单列索引、联合索引
> InnoDB为什么采用B+Tree索引结构？
> 相比二叉树，层数少了很多；相比B-Tree，数据都集中在叶子节点，内部节点仅包含键值，每页索引更密集，层数降低，并且叶子节点的双向链表方便范围查询；相比Hash索引支持范围查询
1. B+Tree索引
   1. B+树：
      1. 非叶子节点用于快速定位数据位置，所有的数据元素只会出现在叶子节点
      2. 叶节点之间用单项链接相连，形成链表类型
   2. MYSQL中的B+Tree索引在B+Tree结构上对叶节点进行了改造：在B+Tree结构上增加了一个指向相邻叶子节点的链表指针，形成了带有顺序指针的B+Tree，提高区间访问性能
2. Hash索引
   1. 哈希索引采用hash算法，把键值换算成新的hash值，映射到对应的槽位上，然后存储在hash表中
   2. 如果两个或多个键值对映射到同一个相同的槽位上，采用链表的方法来解决
   3. 查询效率比B+Tree还高，但是有两个问题：一个是hash冲突导致的长链表；一个是不支持范围查询和排序
3. 聚集索引&非聚集索引
   1. InnoDB存储引擎中，主键索引是聚集索引，非主键索引都是非聚集索引（二级索引）
   2. 聚集索引：数据存储和索引在一起，索引结构的叶子节点保存了数据
   3. 二级索引：数据和索引分文件存储，索引结构的叶子节点指向对应的主键索引（回表）
   4. InnoDB引擎默认使用主键索引作为聚集索引；如果不存在主键，使用第一个非空唯一索引作为主键也就是聚集索引；如果都没有，自动生成一个rowid作为隐藏的主键聚集索引
## 索引语法
    ```sql
    # 创建索引
    -- UNIQUE表示唯一索引 FULLTEXT表示全文索引
    -- (index_col_name,... ) 表示可以联合索引(左前缀！)
    CREATE [ UNIQUE | FULLTEXT ] INDEX index_name ON table_name (index_col_name,... ) ;

    # 查看索引
    SHOW INDEX FROM table_namne;

    # 删除索引
    DROP INDEX index_name ON table_name;
    ```
## SQL性能分析
1. 语句执行频率：
   ```sql
    SHOW GLOBAL STATUS LIKE 'Com_______';
   ```
2. 慢查询日志：
   ```sql
    # 开启MYSQL慢日志查询开关
    slow_query_log = 1;
    # 设置慢查询日志的时间为2s，即SQL的语句执行时间超过2s就被记录到慢查询日志中
    long_query_time = 2;
   ```
3. profile
   profile可以存储我们之前的操作时长，帮助我们在SQL优化中了解时间损耗的具体项目并加以改善
   ```sql
    # 查看是否支持profile操作
    SELECT @@have_profiling;

    # 开启profile操作
    SET profiling = 1;

    # 查看每条SQL语句的耗时情况
    SHOW profile;

    # 查看指定query_id的SQL语句各个阶段的耗时情况（这里的id是SHOW profile的标号id）
    SHOW profile for query query_id;

    # 查看指定query_id的SQL语句CPU的使用情况（这里的id是SHOW profile的标号id）
    show profile cpu for query query_id;
   ```
### 4. explain执行计划：
1. 定义：获取 MySQL 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行过程中表如何连接和连接的顺序
   ```sql
    -- 直接在select语句之前加上关键字 explain
    EXPLAIN SELECT 字段列表 FROM 表名 WHERE 条件 ;
   ```
2. 作用
   1. 读取表的顺序
   2. 哪些索引能够和实际被使用
   3. 数据读取操作的操作类型
   4. 表之间的引用
   5. 每张表有多少行被物理查询
3. 各列信息：
   1. **id**：
      1. select查询的序列号，表示查询中执行select子句或者是操作表的顺序 (id相同，执行顺序从上到下；id不同，值越大，越先执行；对于union查询，会创建一个临时表，对应的id是null)
   2. **select_type**：
      1. 表示 SELECT 的类型，常见的取值有 SIMPLE（简单表，即不使用表连接 或者子查询）、PRIMARY（主查询，即外层的查询）、 UNION（UNION 中的第二个或者后面的查询语句）、 SUBQUERY（SELECT/WHERE之后包含了子查询）等
      2. simple：不包含子查询和union查询都是simple
      3. primary：查询中若包含任何复杂的子部分，最外层查询则被标记为primary
      4. subquery：在SELECT或WHERE列表中包含了子查询的语句
      5. derived：派生表的select(from子句的子查询)
      6. union：union中后面的select语句
      7. dependent union：出现在union或union all语句中，但是这个查询要受到外部查询的影响
      8. dependent subquery：包含子查询，且受到外部查询的影响
      9. union result：出现在union或union all中，表示的是一个结果集
   3. **table**：
      1. 查询的表名
   4. **partitions**：
      1. 查询匹配记录的分区，对于非分区表值为null
   5. **type**：
      1. 表示连接类型，性能由好到差的连接类型为NULL、system、const、 eq_ref、ref、range、 index、all
      2. system：表只有一行记录，一般是系统表，是一种特殊的const类型
      3. const：表最多只有一个匹配行
      4. eq_ref：一般出现在连接查询中，且连接的条件是主键索引或者唯一索引时(左表index)
      5. ref：和eq_ref相比，不同的就是关联表查询的字段不是唯一或者主键索引，就是匹配到了，还要继续匹配，可能有多条记录
      6. fulltex：全文索引的话，innodb不支持，随着各种搜索引擎的出现，一般出现需要全文索引的地方，都会用类似es擅长分词的存储
      7. ref_or_null：类似于ref，会额外搜索包含null的行
      8. index_merge：索引合并
      9. unique_subquery：用于where中的in形式子查询，子查询返回不重复值唯一值，可以完全替换子查询，效率更高
      10. index_subquery：类似于unique_subquery子查询，但是子查询返回的是非唯一索引
      11. range：索引范围扫描，一般条件使用了 >，<，between，in等运算符的查询
      12. index：只有索引树被扫描，这通常比全表扫快，因为索引文件通常比数据文件小
      13. all：没有索引，全表扫描
   6. **possible_key**：
      1. 显示可能应用在这张表上的索引，一个或多个
   7. **Key**：
      1. 实际使用的索引，如果为NULL，则没有使用索引
   8. **Key_len**：
      1. 表示索引中使用的字节数， 该值为索引字段最大可能长度，并非实际使用长 度，在不损失精确性的前提下， 长度越短越好
   9.  **rows**：
      1. MySQL认为必须要执行查询的行数，在innodb引擎的表中，是一个估计值，可能并不总是准确的
   10. **filtered**：
      1. 表示返回结果的行数占需读取行数的百分比， filtered 的值越大越好

## 索引的使用
1. 我们创建索引时需要遍历所有数据；但当我们创建索引后去查询数据，就会发现时间大大减少
2. 联合索引
   1. 最左前缀法则：查询从索引的最左列开始，并且不跳过索引中的列。如果跳跃某一列，后面的字段索引失效。因为后面的索引全局无序，局部相对有序
   2. 范围查询：我们如果使用 <,> 后面的索引将不再生效；但使用 <=,>=,between 则不受影响
   3. 把区分度大的字段排在前面
3. 索引失效的注意点：
   1. 索引列运算：我们不能在索引列上继续运算，使用函数，否则索引失效
   2. 字符串未加引号：字符串类型字段使用时，不加引号，索引将失效
      ```sql
        # 这里最后一部分status的0未加引号，数据仍旧可以输出，但不再通过索引查询
        explain select * from tb_user where profession = '软件工程' and age = 31 and status= 0;
      ```
   3. 模糊查询：索引中禁止头部出现模糊查询
      ```sql
        -- 索引有效
        explain select * from tb_user where profession like '软件%';
        -- 索引失效
        explain select * from tb_user where profession like '%工程';
      ```
   4. or查询条件：or条件各列必须同时命中索引才会使用索引,否则索引会失效
   5. 数据分布影响：如果MySQL评估使用索引比全表更慢，则不使用索引。假设我们所查询的数据占用该表的大多数数据（区分度过小），可能不采用索引而直接采用全表查询
4. SQL提示：优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的
   ```sql
    # 因为我们的SQL系统自动判定时可能不会采用最佳的运行方法

    -- 比如 我们有 profession索引 和 profession，age，status联合索引
    -- 当我们希望查询含有profession，age，status的数据时，系统却自动选择profession索引导致速度降低
    -- 因而我们需要手动设置SQL提示来提高整体运行速度

    # 推荐使用索引 use index
    explain select * from tb_user use index(idx_user_pro) where profession = '软件工程';

    # 拒绝使用索引 ignore index
    explain select * from tb_user ignore index(idx_user_pro) where profession = '软件工程';

    # 强迫使用索引 force index
    explain select * from tb_user force index(idx_user_pro) where profession = '软件工程';
   ```
5. 覆盖索引
   1. 定义：返回的所有列包含在索引中
   2. 使用覆盖索引，减少使用`select *`
6. 前缀查询
   1. 当字段类型为字符串（varchar，text，longtext等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO， 影响查询效率
   2. 可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率
7. 单列/联合索引选择：如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，减少回表次数
## 索引设计原则
1. 仅针对数据量较大，且查询比较频繁的表建立索引
2. 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高
4. 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率
6. 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率
7. 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询
## 索引优化
1. 前缀索引优化：减小索引字段大小，可以增加一个索引页中存储的索引值，有效提高索引的查询速度
   1. order by无法使用前缀索引
   2. 无法用于覆盖索引
2. 覆盖索引优化：从二级索引中查询得到记录，而不需要通过聚簇索引查询获得，可以避免回表的操作
3. 主键索引最好是自增的
   1. 如果我们使用非自增主键插入都是随机的，导致页分裂造成大量的内存碎片，导致索引结构不紧凑，从而影响查询效率
   2. 主键字段的长度不要太大，主键字段长度越小，二级索引的叶子节点越小（二级索引的叶子节点存放的数据是主键值），这样二级索引占用的空间也就越小
4. 索引最好设置为NOT NULL
   1. 索引列存在 NULL 就会导致优化器在做索引选择的时候更加复杂，更加难以优化
   2. NULL 值是一个没意义的值，但会占用物理空间带来的存储空间的问题，因为 InnoDB 存储记录的时候，如果表中存在允许为 NULL 的字段，那么行格式中至少会用 1 字节空间存储 NULL 值列表
5. 防止索引失效
# SQL优化
> SQL优化指的是手动优化以及配合索引的优化
## 插入数据优化
1. 一次**插入多条**数据以进行SQL优化（可以一次插入500~1000条数据）
2. MySQL在每条语句后都进行提交会影响整体性能，我们可以**手动提交事务**以减轻负担
   ```sql
      start transaction;
      insert into tb_test values (1,'TOM'),(2,'JERRY')...;
      insert into tb_test values (3,'TaM'),(4,'JyRRY')...;
      insert into tb_test values (5,'TeM'),(6,'JiRRY')...;
      commit;
   ```
3. 按照**主键顺序插入**会减轻SQL排序操作直接插入加快速度
4. 如果一次性插入超大量数据，insert语句的插入性能就太低了，因而我们采用**load方法**插入
   ```sql
      -- 客户端连接服务端时，加上参数 -–local-infile
      mysql –-local-infile -u root -p

      -- 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
      set global local_infile = 1;

      -- 执行load指令将准备好的数据，加载到表结构中
      load data local infile '/root/sql1.log' into table tb_user fieldsterminated by ',' lines terminated by '\n' ;
   ```
## 主键优化
> 在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的
1. 尽量降低主键的长度
2. 插入数据时，尽量选择顺序插入，或者选择使用AUTO_INCREMENT自增主键
3. 尽量不要使用UUID做主键或者是其他自然主键，compare负担重
4. 业务操作时，避免对主键的修改
5. 原理：页分裂和页合并；在InnoDB引擎中，数据行是记录在逻辑结构page页中的，每一个页的大小是固定的，默认16K。那也就意味着，一个页中所存储的优化行也是有限的，如果插入的数据行ow在该页存储不小，将会存储到下一个页中，页与页之间会通过指针连接。

## Order By优化
1. Using filesort
   * 通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sortbuffer中完成排序操作
   * 所有不是通过索引直接返回排序结果的排序都叫 FileSort 排序。
2. Using index
   * 通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index
   * 不需要额外排序，操作效率高。
3. 创建索引时可以带上ASC or DESC排序，默认均为ASC
4. 联合索引如果ASC，DESC都有，必须要完全吻合才能Using index
5. 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则
6. 尽量使用覆盖索引
7. 如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小sort_buffer_size(默认256k)

## Group By优化
1. 在分组操作时，可以通过索引来提高效率
2. 分组操作时，索引的使用也是满足最左前缀法则的
3. 最好建立联合索引

## Limit优化
1. 越往后，分页查询的效率越低
2. 通过覆盖查询 + 子查询 的形式优化
   ```sql
      -- 我们通过select只获得第9000000个后的十个数据的id
      -- 然后通过id对比来获得整行数据：
      explain select * from tb_sku t , (select id from tb_sku order by id limit 9000000,10) a where t.id = a.id;
   ```
## Count优化
1. 针对count操作，不同存储引擎有不同的处理方式：
   1. MyISAM：直接把表的总行数存储在磁盘中，当运行count（*）时直接输出
   2. InnoDB：需要一行一行读取数据，进行累加
2. 按照效率排序的话，count(字段) < count(主键 id) < count(1) ≈ count（*)，所以尽量使用 count( *)
3. count(1)和count(*)速度基本相近，均为最快速度
4. mysql的InnoDB存储引擎本身对count没有比较好的优化方式，如果要大幅度提升InnoDB的count效率，优化思路就是模拟MyISAM，自己计数
## Update优化
1. InnoDB的行锁是针对索引加的锁，不是针对记录加的锁 ,并且该索引不能失效，否则会从行锁升级为表锁
2. Update操作尽量采用索引，这样锁就会变成行锁，只控制这一行数据
3. 如果我们采用的Update的操作没有使用索引，那么就会采用表锁，导致整个表的数据都无法改变，影响其他人同步修改该表

# 视图/存储过程/触发器
## 视图
1. 定义：视图（View）是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的。通俗的讲，视图只保存了查询的SQL逻辑，不保存查询结果。所以我们在创建视图的时候，主要的工作就落在创建这条SQL查询语句上。
2. 相关语句
   ```sql
      # 创建
      CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [WITH [CASCADED | LOCAL] CHECK OPTION]

      # 查询
      SHOW CREATE VIEW 试图名称;
      SELECT * FROM 试图名称;

      # 修改
      ALTER VIEW 视图名称[(列名列表)] AS SELECT语句 [WITH [CASCADED | LOCAL] CHECK OPTION]

      # 删除
      DROP VIEW [IF EXISTS] 视图名称；
   ```
3. 视图的CHECK检查操作
   1. 当使用WITH CHECK OPTION子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如 插入，更新，删除，以使其符合视图的定义
   2. CHECK操作：
      ```sql
         -- 下述视图设置CHECK
         CREATE VIEW table_view2 AS SELECT id,age FROM table WHERE age > 20 WITH CASCADED CHECK OPTION;
         -- 当我们对view1操作时,如果添加的数据没有大于20，不会执行成功
         INSERT INTO table_view2 values (1,18)；
      ```
   3. CASCADED操作
      ```sql
         -- CASCADED：不仅为当前视图检查条件，而且为当前视图的之前视图检查条件
         -- 假设我们有一个 原表table含有age属性
         -- 下述视图没有设置CHECK
         CREATE VIEW table_view1 AS SELECT id,age FROM table WHERE age > 20;
         -- 下述视图view2以view1为模板设置检查条件
         CREATE VIEW table_view2 AS SELECT id,age FROM table WHERE age < 25 WITH CASCADED CHECK OPTION;
         -- 这时，我们所添加的数据不仅需要满足当前条件age<25,并且需要满足上一视图条件age>20
         INSERT INTO table_view2 values (1,23)；
      ```
   4. LOCAL操作
      ```sql
         -- LOCAL：只为当前视图检查条件，不为之前视图设置条件
         -- 假设我们有一个 原表table含有age属性
         -- 下述视图没有设置CHECK
         CREATE VIEW table_view1 AS SELECT id,age FROM table WHERE age > 20;
         -- 下述视图view2以view1为模板设置检查条件
         CREATE VIEW table_view2 AS SELECT id,age FROM table WHERE age < 25 WITH LOCAL CHECK OPTION;
         -- 这时，我们所添加的数据只需要满足当前条件age<25即可
         INSERT INTO table_view2 values (1,10)；
      ```
4. 视图的更新：
   1. 视图的更新具有一定的严格性，要使视图可更新，视图中的行与基本表中的行之间必须存在一对一的关系
   2. 如果视图包含以下任意一项，则不可更新：
      1. 聚合函数或窗口函数（SUM()、 MIN()、 MAX()、 COUNT()等）
      2. DISTINCT
      3. GROUP BY
      4. HAVING
      5. UNION 或 UNION ALL
5. 视图存在的意义：
   1. 简单
      1. 视图不仅可以简化用户对数据的理解，也可以简化他们的操作。
      2. 那些被经常使用的查询可以被定义为视图，从而使得用户不必为以后的操作每次指定全部的条件。
   2. 安全
      1. 数据库可以授权，但不能授权到数据库特定行和特定的列上。通过视图用户只能查询和修改他们所能见到的数据
   3. 数据独立
      1. 视图可帮助用户屏蔽真实表结构变化带来的影响。
   4. 数据联合显示
      1. 可用于联合多表的数据展现在一起，方便阅读使用
## 存储过程&存储函数
### 存储过程
1. 存储过程思想上很简单，就是数据库 SQL 语言层面的代码封装与重用。
2. 存储过程具有以下三大特点：
   1. 封装，复用：可以把某一业务SQL封装在存储过程中，需要用到的时候直接调用即可
   2. 可以接收参数，也可以返回参数：在存储过程中，可以传递参数，也可以接收返回值
   3. 减少网络交互，效率提高：如果涉及到多条SQL，每执行一次都是一次网络传输。 而如果封装在存储过程中，我们只需要网络交互一次可能就可以了
3. 语法：
   ```sql
      # 创建
      CREATE PROCEDURE 存储过程名称([参数列表])
      BEGIN
         --SQL语句
      END;

      # 调用
      CALL 名称（[参数]）

      # 查看
      -- 查询指定数据库的存储过程及状态信息
      SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA = 'XXX';
      -- 查询某个存储过程的定义
      HOW CREATE PROCEDURE 存储过程名称 ;

      # 删除
      DROP PROCEDURE [IF EXISTS] 存储过程名称;
   ```
4. 变量
   1. 系统变量：MySQL服务器层面提供，分为全局GLOBAL变量，会话SESSION变量（默认）
      ```sql
         -- 查看所有系统变量
         SHOW [SESSION | GLOBAL] VARIABLES;

         -- 可以通过LIKE模糊匹配查找变量
         SHOW [SESSION | GLOBAL] VARIABLES LIKE '...';

         -- 直接查看指定变量
         SELECT @@[SESSION | GLOBAL].系统变量名;

         -- 设置系统变量
         SET [SESSION | GLOBAL] 系统变量名 = 值;
         SET @@[SESSION | GLOBAL].系统变量名 = 值;
      ```
      * mysql服务重新启动之后，所设置的全局参数会失效，要想不失效，可以在 /etc/my.cnf 中配置
      * 会话变量针对于单个会话，在另外一个会话窗口就不生效了
   2. 用户自定义变量：用户变量不用提前声明，在用的时候直接用 "@变量名" 使用就可以。其作用域和session变量一样，都是一个连接
      ```sql
         -- 直接赋值
         SET @var_name = expr [, @var_name = expr] ... ;
         SET @var_name := expr [, @var_name := expr] ... ;
         SELECT @var_name := expr [, @var_name := expr] ... ;

         -- 从表中抽取数据进行赋值
         SELECT 字段名 INTO @var_name FROM 表名;

         --使用
         SELECT @var_name ;
      ```
   3. 局部变量：访问之前需要DECLARE声明。可用作存储过程内的局部变量和输入参数，局部变量的范围是在其内声明的BEGIN ... END块
      ```sql
         # 声明
         -- [DEFAULT ...]表示设置初始化值
         DECLARE 变量名 变量类型[DEFAULT ...]

         # 赋值
         SET 变量名 = 值 ;
         SET 变量名 := 值 ;
         SELECT 字段名 INTO 变量名 FROM 表名 ... ;
      ```
5. 7种结构
   1. if
      ```sql
         IF 条件1 THEN
         .....
         ELSEIF 条件2 THEN -- 可选
         .....
         ELSE -- 可选
         .....
         END IF;
      ```
   2. case
      ```sql
         -- case结构1：
         CASE case_value
            WHEN when_value1 THEN statement_list1
            [ WHEN when_value2 THEN statement_list2] ...
            [ ELSE statement_list ]
         END CASE;

         -- case结构2：
         CASE
            WHEN search_condition1 THEN statement_list1
            [WHEN search_condition2 THEN statement_list2] ...
            [ELSE statement_list]
         END CASE;
      ```
   3. while
      ```sql
         -- 先判定条件，如果条件为true，则执行逻辑，否则，不执行逻辑
         WHILE 条件 DO
            SQL逻辑...
         END WHILE;
      ```
   4. repeat
      ```sql
         -- 先执行一次逻辑，然后判定UNTIL条件是否满足，如果满足，则退出。如果不满足，则继续下一次循环
         REPEAT
            SQL逻辑...
            UNTIL 条件
         END REPEAT;
      ```
   5. loop
      ```sql
         [begin_label:] LOOP
            SQL逻辑...
         END LOOP [end_label];
      ```
   6. cursor
      ```sql
         # 声明游标
         DECLARE 游标名称 CURSOR FOR 查询语句 ;

         # 打开游标
         OPEN 游标名称;

         # 获得游标记录
         FETCH 游标名称 INTO 变量[,变量];

         # 关闭游标
         CLOSE 游标名称;
      ```
   7. handler
      ```sql
         -- 条件处理程序（Handler）可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤
         declare exit handler for SQLSTATE '02000' close u_cursor;
      ```
### 存储函数
1. 存储函数是有返回值的存储过程，存储函数的参数只能是IN类型的
2. 语句：
   ```sql
      # 存储函数创建结构
      CREATE FUNCTION 存储函数名称 ([ 参数列表 ])
      RETURNS type [characteristic ...]
      BEGIN
         -- SQL语句
         RETURN ...;
      END ;
      -- characteristics 参数主要是给优化器一些提示,让其更好地理解和优化存储函数的行为。正确设置这些参数可以改善函数和相关查询的性能
      -- characteristic  可以有以下三种情况：
      -- DETERMINISTIC：相同的输入参数总是产生相同的结果
      -- NO SQL ：不包含 SQL 语句。
      -- READS SQL DATA：包含读取数据的语句，但不包含写入数据的语句。
   ```
## 触发器
1. 触发器是与表有关的数据库对象，指在insert/update/delete之前(BEFORE)或之后(AFTER)，触发并执行触发器中定义的SQL语句集合
2. 触发器的这种特性可以协助应用在数据库端确保数据的完整性, 日志记录 , 数据校验等操作 
3. 使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的
4. MySQL的触发器只支持行级触发，不支持语句级触发
5. 语句：
   ```sql
      -- 创建触发器
      CREATE TRIGGER trigger_name
      BEFORE/AFTER INSERT/UPDATE/DELETE
      ON tbl_name FOR EACH ROW 
      BEGIN
         trigger_stmt ;
         -- INSERT触发器	NEW 表示将要或者已经新增的数据
         -- UPDATE触发器	OLD 表示修改之前的数据 , NEW 表示将要或已经修改后的数据
         -- DELETE触发器	OLD 表示将要或者已经删除的数据
      END;
      
      -- BEFORE/AFTER 表示是在操作进行前/操作进行后触发
      -- INSERT/UPDATE/DELETE 表示操作类型
      -- FOR EACH ROW 表示行级触发器

      -- 查看触发器：
      SHOW TRIGGERS;

      -- 删除触发器：
      DROP TRIGGER trigger_name;
   ```
# 锁
> 在《玩儿转MySQL》中的4种MySQL事务隔离级别和MySQL的锁机制有着非常密切的关系：MySQL的事务隔离级别就是通过锁机制来实现的
1. MySQL锁和事务的联系：（事务的实现可通过无锁的读和加锁两种方式）
   1. READ UNCOMMITTED: 不使用任何锁,存在脏读、虚读、幻读等问题
   2. READ COMMITTED: 通过MVCC实现,避免脏读
   3. REPEATABLE READ: 通过MVCC实现，避免了脏读、不可重复读，但有幻读
   4. SERIALIZABLE: 通过MVCC，行锁，间隙锁和临键锁来禁止任何并发,完全串行执行事务
   5. 实际上MYSQL的RR隔离级别就已经能很大程度上避免幻读了
2. 锁是计算机协调多个进程或线程并发访问某一资源的机制。MySQL中的每个请求都可以看作是一个线程
3. MySQL锁按照粒度分为三类：
   1. 全局锁：锁定数据库中所有表
   2. 表级锁：锁定整张表
   3. 行级锁：锁住一行数据
## 全局锁
1. 全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语句都将被阻塞
2. 典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性
   ```sql
      -- 设置全局锁
      flush tables with read lock;
      -- 进行备份
      -- -u后加账号 -p后加密码 itcast为数据库名称 itcast.sql为本地地址
      mysqldump -uroot -p123456 itcast > itcaset.sql
      -- 关闭全局锁
      unlock tables;
   ```
3. 如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆
4. 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binlog），会导致主从延迟。
5. 因而为了防止全局业务停摆，MYSQL给出了一种新的备份方法：
   如果数据库的引擎支持的事务支持可重复读的隔离级别，那么在备份数据库之前先开启事务，会先创建 Read View，然后整个事务执行期间都在用这个 Read View，而且由于 MVCC 的支持，备份期间业务依然可以对数据进行更新操作
   ```sql
      -- 在InnoDB引擎中，我们可以在备份时加上参数 --single-transaction 参数来完成不加锁的一致性数据备份。
      mysqldump --single-transaction -uroot –p123456 itcast > itcast.sql
   ```
   这种方法只适用于支持「可重复读隔离级别的事务」的存储引擎
## 表级锁
1. 表锁：
   1. 表共享读锁：对于所有客户端均可以进行读操作，但均不可进行写操作
   2. 表独占写锁：仅对当前客户端可以进行读写操作，其他客户端读写均不可操作
   ```sql
      -- 加锁操作：
      lock tables 表名... read/write;
      -- 释放锁：
      unlock tables / 断开客户端连接
   ```
   3. 尽量避免在使用 InnoDB 引擎的表使用表锁，因为表锁的颗粒度太大，会影响并发性能，InnoDB 牛逼的地方在于实现了颗粒度更细的行级锁
2. 元数据锁（MDL）：MDL加锁过程是系统自动控制，无需显式使用，在访问一张表的时候会自动加上
   1. 当对一张表进行CRUD的时候，加MDL读锁
   2. 当对表结构进行变更操作的时候，加MDL写锁
   3. MDL 是在事务提交后才会释放，这意味着事务执行期间，MDL 是一直持有的
   4. MDL 是为了保证当用户对表执行 CRUD 操作时，防止其他线程对这个表结构做了变更。
   5. 对某张表申请 MDL 锁的操作会形成一个队列，队列中写锁获取优先级高于读锁，一旦出现 MDL 写锁等待，会阻塞后续该表的所有 CRUD 操作
> MDL锁和表锁之间的关系
> 1. 表锁锁定表级数据，开销小，并发度低
> 2. MDL锁只锁定元数据，开销大，并发度高
> 3. MDL读锁兼容表级共享读锁，但不兼容表级独占写锁
> 4. MDL写锁不兼容任何表级锁
1. 意向锁：
   1. 在使用 InnoDB 引擎的表里对某些记录加上「共享锁」之前，需要先在表级别加上一个「意向共享锁」；
   2. 在使用 InnoDB 引擎的表里对某些纪录加上「独占锁」之前，需要先在表级别加上一个「意向独占锁」；
   3. 
   4. 也就是，当执行插入、更新、删除操作，需要先对表加上「意向独占锁」，然后对该记录加独占锁。而普通的 select 是不会加行级锁的，普通的 select 语句是利用 MVCC 实现一致性读，是无锁的。
   5. 意向共享锁和意向独占锁是表级锁，不会和行级的共享锁和独占锁发生冲突，而且意向锁之间也不会发生冲突，只会和共享表锁（lock tables ... read）和独占表锁（lock tables ... write）发生冲突。
   6. 意向锁的目的是为了快速判断表里是否有记录被加锁

## 行级锁
1. 行级锁，每次操作锁住对应的行数据。锁定粒度最小，发生锁冲突的概率最低，并发度最高。应用在InnoDB存储引擎中。
2. InnoDB的数据是基于索引组织的，行锁是通过对索引上的索引项加锁来实现的，而不是对记录加的锁
3. 共享锁（S锁）满足读读共享，读写互斥。独占锁（X锁）满足写写互斥、读写互
4. 对于行级锁，主要分为以下三类：
   1. 行锁（Record Lock）：
      1. 锁定单个行记录的锁，防止其他事务对此行进行update和delete
      2. 在RC、RR隔离级别下都支持
      ```sql
         -- 对读取的记录加共享锁(S型锁)
         select ... lock in share mode;
         -- 对读取的记录加独占锁(X型锁)
         select ... for update;
      ```
      3. 除了上面这两条锁定读语句会加行级锁之外，update 和 delete 操作都会加行级独占写锁(X型锁)
   2. 间隙锁（Gap Lock）：
      1. 锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事务在这个间隙进行insert，产生幻读
      2. 在RR隔离级别下都支持
   3. 临键锁（Next-Key Lock）：
      1. 行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隙Gap
      2. 在RR隔离级别下支持
5. 默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用next-key 锁进行搜索和索引扫描，以防止幻读。
6. 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。
7. InnoDB的行锁是针对于索引加的锁，不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，~~此时 就会升级为表锁~~。
8. 索引上的等值查询(唯一索引)，给不存在的记录加锁时, 优化为间隙锁 。
9. 索引上的等值查询(非唯一普通索引)，向右遍历时最后一个值不满足查询需求时，next-keylock 退化为间隙锁。
10. 索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

## MVCC
1. Multi-Version Concurrency Control，为了提高数据库并发性能，用更好的方式去处理**读-写冲突**，做到即使有读写冲突时，也能做到不加锁，非阻塞并发读
2. 当前读 & 快照读：
   1. 当前读：像 select lock in share mode (共享锁), select for update;update; insert; delete (排他锁)这些操作都是一种当前读，为什么叫当前读？就是它读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加锁
   2. 快照读：不加锁的 select 操作就是快照读，即不加锁的非阻塞读；快照读的前提是隔离级别不是串行级别，串行级别下的快照读会退化成当前读；之所以出现快照读的情况，是基于提高并发性能的考虑，快照读的实现是基于多版本并发控制，即 MVCC ,可以认为 MVCC 是行锁的一个变种，但它在很多情况下，避免了加锁操作，降低了开销；既然是基于多版本，即快照读可能读到的并不一定是数据的最新版本，而有可能是之前的历史版本
3. MVCC实现原理：
   1. 快照读就是 MySQL 实现 MVCC 理想模型的其中一个非阻塞读功能。要说的再细致一些，快照读本身也是一个抽象概念，再深入研究。MVCC 模型在 MySQL 中的具体实现则是由 3 个隐式字段，undo 日志 ，Read View 等去完成的


# InnoDB引擎



# MySQL管理


