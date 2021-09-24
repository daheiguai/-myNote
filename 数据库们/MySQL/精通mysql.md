# 精通mysql

## 一、mysql体系结构

### 1.结构分层与讲解

MySql Server层

- 连接层
  - 通信协议
  - 线程处理
  - 用户名密码认证
- SQL层
  - 权限判断
  - 解析器
  - 预处理
  - 查询优化器
  - 缓存
  - 执行计划

储存引擎层

### 2.关闭Query Cache

Query Cache

- 智能缓存静态数据，数据发生变化经常读写就没有太大用处了
- 5.6之前默认开启，之后默认关闭
- query_cache_size设置成0也会影响性能
- query_cache_type设置成0才是真正关闭，而且启动时就要关闭，启动后再关闭也会影响性能

### 3.InnoDB结构

## 二、储存引擎

### 1.引擎对比总览

InnoDB

- 支持事务ACID
- 支持行级锁。(行级锁最大程度支持并发)
- 为了处理巨大数据的最大性能设计
- 数据和索引文件都在.Idb文件中，并且缓存在内存中
- 支持B-TREE、FULL-TEXT等索引，不支持 Hash 索引；
- select count(*)   扫码全表统计
- 支持外键完整性约束
- 支持崩溃数据自修复

MyISAM

- 不支持事务
- 锁是表级锁。锁的实现成本小，但是并发性能降低
- 索引在.MYI文件，数据在.MYD文件，只会缓存索引，不会缓存数据，减少磁盘I/O
- 支持 B-tree、Full-text 等索引，不支持 Hash 索引；
- select count(*)   从计数器取出保存的行数

MEMORY

- 使用储存在内存中的内容来创建表，数据也储存在内存中
- 每个表对应一个frm文件，该文件储存表的结构，数据在内存中
- 默认使用哈希索引，速度比B树型索引快，创建时可以指定使用B树索引
- 支持 B-tree、Hash 等索引，不支持 Full-text 索引；

NDB

- 支持事务
- 行级锁
- 支持 Hash 索引，不支持 B-tree、Full-text 等索引

Archive 

-  不支持事务
- 表级别锁定
- 不支持 B-tree、Hash、Full-text 等索引；

## 三、事务

### 1.ACID

- 原子性（Atomicity）  		要么都成功，要么都失败

- 一致性（Consistency）      事务前后的数据完整性要保持一致
- 隔离性    (Isolation)              事务产生多并发时，互不干扰

- 持久性（Durability）          事务一旦提交就不可逆转，被持久化到数据库中

### 2.事务隔离

脏读

- 指一个事务读取了另外一个事务未提交的数据。

不可重复读

- 在一个事务内读取表中的某一行数据，多次读取结果不同。（这个不一定是错误，只是某些场合不对）

虚读(幻读)

- 是指在一个事务内读取到了别的事务插入的数据，导致前后读取不一致。
  （一般是行影响，多了一行）

| 事务隔离级别                    | 脏读 | 不可重复读 | 幻读 |
| ------------------------------- | ---- | ---------- | ---- |
| 读未提交（read-uncommitted）    | 是   | 是         | 是   |
| 读已提交（read-committed）      | 否   | 是         | 是   |
| 可重复读（repeatable-read）默认 | 否   | 否         | 是   |
| 串行化（serializable）          | 否   | 否         | 否   |

### 实际操作大全

执行事务

```mysql
-- mysql 自动开启事务提交
SET autocommit=0 -- 关闭
SET autocommit=1 -- 开启（默认的）

-- 手动处理事务
SET autocommit =0 -- 关闭自动提交

-- 事务开启

START TRANSACTION -- 标记一个事务的开始，从这个之后的SQP都在同一个事务内

INSERT XX
INSERT XX

-- 提交 ： 持久化(成功)
COMMIT 
-- 回滚：  回到原来的样子（失败）
ROLLBACK
-- 事务结束
SET autocommit = 1 -- 开启自动提交
-- 了解
SAVEPOINT 保存点名称 -- 设置一个事务的保存点
ROLLBACK TO SAVEPOINT 保存点名 -- 回滚到保存点
RELEASE SAVEPOINT 保存点 -- 删除保存点
```

## 四、索引

### 聚集索引和辅助索引

数据库中的B+树索引可以分为聚集索引（clustered index）和辅助索引（secondary index）

聚集索引

- 每张表的主键构造一棵B+树，同时叶子结点存放的即为整张表的行记录数据
- 聚集索引的叶子结点称为数据页。
- 聚集索引的这个特性决定了索引组织表中数据也是索引的一部分。同B+树数据结构一样，每个数据页都通过一个双向链表来进行链接。
  - 如果未定义主键，MySQL取第一个唯一索引而且只含非空列作为主键，InnoDB使用它作为聚簇索引。
  - 如果没有这样的列，InnoDB就自己产生一个这样的ID值，它有六个字节，而且是隐藏的，使其作为聚簇索引。
  - 由于实际的数据页只能按照一棵B+树进行排序，因此每张表只能拥有一个聚集索引。

辅助索引

- 辅助索引的叶子节点不包含行记录的全部数据。
- 叶子节点除了包含键值以外，每个叶子节点中的索引行中还包含一个书签（bookmark）。该书签用来告诉InnoDB存储引擎去哪里可以找到与索引相对应的行数据。
- 由于InnoDB存储引擎是索引组织表，因此InnoDB存储引擎的辅助索引的书签就是相应行数据的聚集索引键。
- 举例来说，如果在一棵高度为3的辅助索引树种查找数据，那需要对这个辅助索引树遍历3次找到指定主键，如果聚集索引树的高度同样为3，那么还需要对聚集索引树进行3次查找，最终找到一个完整的行数据所在的页，因此一共需要6次逻辑IO访问才能得到最终的一个数据页。
  - 辅助索引的存在并不影响数据在聚集索引中的组织，因此每张表上可以有多个辅助索引，但只能有一个聚集索引

### 普通索引



### 唯一索引

唯一索引和主键索引的区别

- 唯一性约束所在的列允许空值，但是主键约束所在的列不允许空值。

- 可以把唯一性约束放在一个或者多个列上，这些列或列的组合必须有唯一的。但是，唯一性约束所在的列并不是表的主键列。

- 唯一性约束强制在指定的列上创建一个唯一性索引。在默认情况下，创建唯一性的非聚簇索引，但是，也可以指定所创建的索引是聚簇索引。

- 一个表最多只有一个主键，但可以有很多唯一键

### 联合索引

多个字段一起创建索引

### FULLTEXT全文索引

- 只有类型为char、vchar、text的列能建立全文索引。
- 有MyISAM引、InnoDB支持全文索引

介绍

- 一般情况，对于模糊查询的情况最容易想到的就是 where ... like %_... 这样。确实，like 关键字在大都数情况下都能完成需求，但是在列的内容十分大的时候，like的性能就不能令人满意了，**因为这个关键字并没有保证每次查询都能用上索引。**除了性能上的提高，全文索引提供了更灵活的服务，比如：
  - like 只是进行模式匹配，全文索引却提供了一些语法语义的查询功能，会将要查的字符串进行分词操作，这决定于MySQL的词库。
  - 全文索引可以自己设置词语的最小、最大长度，要忽略的词，这些都是可以设置的。
  -  用全文索引去某个列查一个字符串，会返回匹配度，可以理解为匹配的关键字个数，是个浮点数。

全文索引带来的负面影响：

- 占有存储空间更大，如果内存一次装不下全部索引，性能会非常差。
- 增删改代价更大，修改文本中10个单词，则要操作维护索引10次，而不是普通索引的一次。
- 如果一个列上有全文索引则一定会用上，即使有性能更好的其他索引也不会用上。由于只是存储文档指针，也就用不上索引覆盖。



### 空间索引

### ICP、MRR、BKA

### BTREE、HASH

### 覆盖索引

### 前缀索引

### 索引失效

- like 以%开头，索引无效；中间和后面有%，索引有效。

- or语句前后没有同时使用索引。当or左右查询字段只有一个是索引，该索引失效，只有当or左右查询字段均为索引时，才会生效

- 组合索引，不是使用第一列索引，索引失效

- 数据类型出现隐式转化。如varchar不加单引号的话可能会自动转换为int型，使索引无效，产生全表扫描。

- ```
  select * from  xxx where  aa = '123';√
  select * from  xxx where  aa = '123';×
  ```

- 在索引列上使用 IS NULL 或 IS NOT NULL操作。

  - 这种情况有时候使用有时候不使用索引
  - mysql根据成本判断什么时候用索引
    要扫描的二级索引记录条数越多，那么需要执行的回表操作的次数也就越多，达到了某个比例时，使用二级索引执行查询的成本也就超过了全表扫描的成本（举一个极端的例子，比方说要扫描的全部的二级索引记录，那就要对每条记录执行一遍回表操作，自然不如直接扫描聚簇索引来的快）。

- ```
  select * from xxx where aaa is not null; 
  ```

- 对索引字段进行计算操作、字段上使用函数。（索引为 emp(ename,empno,sal)）

- 当全表扫描速度比索引速度快时，mysql会使用全表扫描，此时索引失效。

### 实际操作大全

存在唯一键冲突时，避免策略：

- insert ignore:

  - insert ignore会忽略数据库中已经存在的数据(根据主键或者唯一索引判断)，如果数据库没有数据，就插入新的数据，如果有数据的话就跳过这条数据.

  - ```
    insert ignore into sc (name,class,score) values ('吕布','二年二班',77)
    ```

  - 执行上面的语句，会发现并没有报错，但是主键还是自动增长了。

---

建表时创建：

```
CREATE TABLE 表名(

字段名 数据类型 [完整性约束条件],

[UNIQUE | FULLTEXT | SPATIAL] INDEX | KEY

[索引名] (字段名1 [(长度)] [ASC | DESC]) [USING 索引方法]

);
```

说明：

- UNIQUE:可选。表示索引为唯一性索引。
- FULLTEXT:可选。表示索引为全文索引。
- SPATIAL:可选。表示索引为空间索引。
- INDEX和KEY:用于指定字段为索引，两者选择其中之一就可以了，作用是    一样的。
- 索引名:可选。给创建的索引取一个新名称。
- 字段名1:指定索引对应的字段的名称，该字段必须是前面定义好的字段。
- 长度:可选。指索引的长度，必须是字符串类型才可以使用。
- ASC:可选。表示升序排列。
- DESC:可选。表示降序排列。

普通索引

```mysql
CREATE TABLE projectfile (
	id INT AUTO_INCREMENT COMMENT '附件id',
	fileuploadercode VARCHAR(128) COMMENT '附件上传者code',
	projectid INT COMMENT '项目id;此列受project表中的id列约束',
	filename VARCHAR (512) COMMENT '附件名',
	fileurl VARCHAR (512) COMMENT '附件下载地址',
	filesize BIGINT COMMENT '附件大小，单位Byte',
	-- 主键本身也是一种索引（注:也可以在上面的创建字段时使该字段主键自增）
        PRIMARY KEY (id),
	-- 主外键约束（注:project表中的id字段约束了此表中的projectid字段）
	FOREIGN KEY (projectid) REFERENCES project (id),
	-- 给projectid字段创建了唯一索引(注:也可以在上面的创建字段时使用unique来创建唯一索引)
	UNIQUE INDEX (projectid),
	-- 给fileuploadercode字段创建普通索引
	INDEX (fileuploadercode)
	-- 指定使用INNODB存储引擎(该引擎支持事务)、utf8字符编码
) ENGINE = INNODB DEFAULT CHARSET = utf8 COMMENT '项目附件表';
```

联合索引

```mysql
CREATE TABLE projectfile (
	id INT AUTO_INCREMENT COMMENT '附件id',
	fileuploadercode VARCHAR(128) COMMENT '附件上传者code',
	projectid INT COMMENT '项目id;此列受project表中的id列约束',
	filename VARCHAR (512) COMMENT '附件名',
	fileurl VARCHAR (512) COMMENT '附件下载地址',
	filesize BIGINT COMMENT '附件大小，单位Byte',
	-- 主键本身也是一种索引（注:也可以在上面的创建字段时使该字段主键自增）
        PRIMARY KEY (id),
        -- 创建组合索引
	INDEX (fileuploadercode,projectid)
	-- 指定使用INNODB存储引擎(该引擎支持事务)、utf8字符编码
) ENGINE = INNODB DEFAULT CHARSET = utf8 COMMENT '项目附件表';
```

---

建表后创建：

```mysql
ALTER TABLE 表名 ADD [UNIQUE | FULLTEXT | SPATIAL]  INDEX | KEY  [索引名] (字段名1 [(长度)] [ASC | DESC]) [USING 索引方法]；

或

CREATE  [UNIQUE | FULLTEXT | SPATIAL]  INDEX  索引名 ON  表名(字段名) [USING 索引方法]；
```

---

查看已创建的索引：

```mysql
show index from 表名;
```

---

删除索引

```mysql
DROP INDEX 索引名 ON 表名

或

ALTER TABLE 表名 DROP INDEX 索引名
```

---

索引使用

联合索引

- 使用语句：

```
where match(列名...) against(‘ 词语1 词语2 ’); //匹配词语1 词语2
```

- 如果要使用布尔查询或短语查询，如果使用短语的话，速度会慢很多，因为全文索引无法判断是否精确匹配了短语，得回表过滤。：

```
where match(列名...) against(‘ “短语1” ’);  //在单引号里用双引号包裹一个短语，让返回结果精确匹配指定的短语
where match(列名...) against(‘ +词语1 -词语2 ’ IN BOOLEAN MODE);  //返回结果必须含有词语1，但不能有词语2
where match(列名...) against(‘ >词语1 <词语2 ’ IN BOOLEAN MODE);  //含有词语1的话优先级升高，含有词语2的话优先级降低
```

- 也可以把全文匹配的结果返回，这是一个浮点数，表示这一行关于这些词语的匹配度：

```
select id,match(列名...) against(' 词语1 ') as factor from 表名 where....    //返回每行匹配词语1的匹配度
```

## 五、锁

### 1.共享锁与排他锁

- 共享锁（读锁）：其他事务可以读，但不能写。
- 排他锁（写锁） ：其他事务不能读取，也不能写。

### 2.粒度锁

- MyISAM 和 MEMORY 存储引擎采用的是表级锁（table-level locking）
- BDB 存储引擎采用的是页面锁（page-level locking），但也支持表级锁
- InnoDB 存储引擎既支持行级锁（row-level locking），也支持表级锁，但默认情况下是采用行级锁。

> 默认情况下，表锁和行锁都是自动获得的， 不需要额外的命令。
>
> 
>
> 但是在有的情况下， 用户需要明确地进行锁表或者进行事务的控制， 以便确保整个事务的完整性，这样就需要使用事务控制和锁定语句来完成。

不同粒度锁的比较：

- 表级锁：开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低。

- - 这些存储引擎通过总是一次性同时获取所有需要的锁以及总是按相同的顺序获取表锁来避免死锁。
  - 表级锁更适合于以查询为主，并发用户少，只有少量按索引条件更新数据的应用，如Web 应用

- 行级锁：开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高。

- - 最大程度的支持并发，同时也带来了最大的锁开销。
  - 在 InnoDB 中，除单个 SQL 组成的事务外，
    锁是逐步获得的，这就决定了在 InnoDB 中发生死锁是可能的。
  - 行级锁只在存储引擎层实现，而Mysql服务器层没有实现。 行级锁更适合于有大量按索引条件并发更新少量不同数据，同时又有并发查询的应用，如一些在线事务处理（OLTP）系统

- 页面锁：开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之间，并发度一般。

### 3.MyISAM表锁

- 表共享读锁 （Table Read Lock）：不会阻塞其他用户对同一表的读请求，但会阻塞对同一表的写请求；
- 表独占写锁 （Table Write Lock）：会阻塞其他用户对同一表的读和写操作；

MyISAM 表的读操作与写操作之间，以及写操作之间是串行的。当一个线程获得对一个表的写锁后， 只有持有锁的线程可以对表进行更新操作。 其他线程的读、 写操作都会等待，直到锁被释放为止。

默认情况下，写锁比读锁具有更高的优先级：当一个锁释放时，这个锁会优先给写锁队列中等候的获取锁请求，然后再给读锁队列中等候的获取锁请求。 

## 六、慢查询优化

### 1.查服务器状态

列出MySQL服务器运行各种状态值：

```
 show global status;
```

查询MySQL服务器配置信息语句：

```
show variables;
```

慢查询

```mysql
　mysql> show variables like '%slow%';
    　　+------------------+-------+
    　　| Variable_name | Value |
    　　+------------------+-------+
    　　| log_slow_queries | ON |
    　　| slow_launch_time | 2 |
    　　+------------------+-------+
 mysql> show global status like '%slow%';
    　　+---------------------+-------+
    　　| Variable_name | Value |
    　　+---------------------+-------+
    　　| Slow_launch_threads | 0 |
    　　| Slow_queries | 4148 |
    　　+---------------------+-------+　
```

连接数

```
mysql> show variables like 'max_connections';
  　　+-----------------+-------+
  　　| Variable_name | Value |
  　　+-----------------+-------+
  　　| max_connections | 256 |
  　　+-----------------+-------+　　

​   
```

-  这台MySQL服务器最大连接数是256

- 然后查询一下服务器响应的最大连接数：

```
mysql> show global status like ‘Max_used_connections’;
```

- MySQL服务器过去的最大连接数是245，没有达到服务器连接数上限256，应该没有出现1040错误，比较理想的设置是：
  　　Max_used_connections / max_connections * 100% ≈ 85%
    　　最大连接数占上限连接数的85%左右，如果发现比例在10%以下，MySQL服务器连接数上限设置的过高了。

### 2.EXPALIN

```
id:选择标识符
select_type:表示查询的类型。
table:输出结果集的表
partitions:匹配的分区
type:表示表的连接类型
possible_keys:表示查询时，可能使用的索引
key:表示实际使用的索引
key_len:索引字段的长度
ref:列与索引的比较
rows:扫描出的行数(估算的行数)
filtered:按表条件过滤的行百分比
Extra:执行情况的描述和说明
```

各个属性详解

>
>
>### id字段
>
>##### 1. id相同
>
>```
>执行顺序从上至下
>例子：
>explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;
>读取顺序：subject > teacher > student_score
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458124)
>
>##### 2. id不同
>
>```
>如果是子查询，id的序号会递增，id的值越大优先级越高，越先被执行
>例子：
>explain select score.* from student_score as score where subject_id = (select id from subject where teacher_id = (select id from teacher where id = 2));
>读取顺序：teacher > subject > student_score
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458120)
>
>##### 3. id相同又不同
>
>```
>id如果相同，可以认为是一组，从上往下顺序执行
>在所有组中，id值越大，优先级越高，越先执行
>例子：
>explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id
> -> union 
> -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;
> 读取顺序：2.teacher > 2.subject > 1.subject > 1.teacher
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458126)
>
>### select_type字段
>
>##### 1. SIMPLE
>
>```
>简单查询，不包含子查询或Union查询
>例子：
>explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458121)
>
>##### 2. PRIMARY
>
>```
>查询中若包含任何复杂的子部分，最外层查询则被标记为主查询
>例子：
>explain select score.* from student_score as score where subject_id = (select id from subject where teacher_id = (select id from teacher where id = 2));
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458125)
>
>##### 3. SUBQUERY
>
>```
>在select或where中包含子查询
>例子：
>explain select score.* from student_score as score where subject_id = (select id from subject where teacher_id = (select id from teacher where id = 2));
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458123)
>
>##### 4. DERIVED
>
>```
>在FROM列表中包含的子查询被标记为DERIVED（衍生），MySQL
>会递归执行这些子查询，把结果放在临时表中
>备注：
>MySQL5.7+ 进行优化了，增加了derived_merge（派生合并），默认开启，可加快查询效率
>```
>
>##### 5. UNION
>
>```
>若第二个select出现在uion之后，则被标记为UNION
>例子：
>explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id
> -> union 
> -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458127)
>
>##### 6. UNION RESULT
>
>```
>从UNION表获取结果的select
>例子：
>explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id
> -> union 
> -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458128)
>
>### type字段
>
>```
>NULL>system>const>eq_ref>ref>fulltext>ref_or_null>index_merge>unique_subquery>index_subquery>range>index>ALL //最好到最差
>备注：掌握以下10种常见的即可
>NULL>system>const>eq_ref>ref>ref_or_null>index_merge>range>index>ALL
>```
>
>##### 1. NULL
>
>```
>MySQL能够在优化阶段分解查询语句，在执行阶段用不着再访问表或索引
>例子：
>explain select min(id) from subject;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458137)
>
>##### 2. system
>
>```
>表只有一行记录（等于系统表），这是const类型的特列，平时不大会出现，可以忽略
>```
>
>##### 3. const
>
>```
>表示通过索引一次就找到了，const用于比较primary key或uique索引，因为只匹配一行数据，所以很快，如主键置于where列表中，MySQL就能将该查询转换为一个常量
>例子：
>explain select * from teacher where teacher_no = 'T2010001';
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458138)
>
>##### 4. eq_ref
>
>```
>唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配，常见于主键或唯一索引扫描
>例子：
>explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458134)
>
>##### 5. ref
>
>```
>非唯一性索引扫描，返回匹配某个单独值的所有行
>本质上也是一种索引访问，返回所有匹配某个单独值的行
>然而可能会找到多个符合条件的行，应该属于查找和扫描的混合体
>例子：
>explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458135)
>
>##### 6. ref_or_null
>
>```
>类似ref，但是可以搜索值为NULL的行
>例子：
>explain select * from teacher where name = 'wangsi' or name is null;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458130)
>
>##### 7. index_merge
>
>```
>表示使用了索引合并的优化方法
>例子：
>explain select * from teacher where id = 1 or teacher_no = 'T2010001' .
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458129)
>
>##### 8. range
>
>```
>只检索给定范围的行，使用一个索引来选择行，key列显示使用了哪个索引
>一般就是在你的where语句中出现between、<>、in等的查询。
>例子：
>explain select * from subject where id between 1 and 3;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458132)
>
>##### 9. index
>
>```
>Full index Scan，Index与All区别：index只遍历索引树，通常比All快
>因为索引文件通常比数据文件小，也就是虽然all和index都是读全表，但index是从索引中读取的，而all是从硬盘读的。
>例子：
>explain select id from subject;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458131)
>
>##### 10. ALL
>
>```
>Full Table Scan，将遍历全表以找到匹配行
>例子：
>explain select * from subject;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458133)
>
>### table字段
>
>```
>数据来自哪张表
>```
>
>### possible_keys字段
>
>```
>显示可能应用在这张表中的索引，一个或多个
>查询涉及到的字段若存在索引，则该索引将被列出，但不一定被实际使用
>```
>
>### key字段
>
>```
> 实际使用到的索引，如果为NULL，则没有使用索引
>查询中若使用了覆盖索引（查询的列刚好是索引），则该索引仅出现在key列表
>```
>
>### key_len字段
>
>```
> 表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度
>在不损失精确度的情况下，长度越短越好
>key_len显示的值为索引字段最大的可能长度，并非实际使用长度
>即key_len是根据定义计算而得，不是通过表内检索出的
>```
>
>### ref字段
>
>```
>显示索引的哪一列被使用了，如果可能的话，是一个常数，哪些列或常量被用于查找索引列上的值
>```
>
>### rows字段
>
>```
>根据表统计信息及索引选用情况，大致估算出找到所需的记录所需读取的行数
>```
>
>### partitions字段
>
>```
>匹配的分区
>```
>
>### filtered字段
>
>```
>查询的表行占表的百分比
>```
>
>### Extra字段
>
>```
>包含不适合在其它列中显示但十分重要的额外信息
>```
>
>##### 1. Using filesort
>
>```
>说明MySQL会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取
>MySQL中无法利用索引完成的排序操作称为“文件排序”
>例子：
>explain select * from subject order by name;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458136)
>
>##### 2. Using temporary
>
>```
>使用了临时表保存中间结果，MySQL在对结果排序时使用临时表，常见于排序order by 和分组查询group by
>例子：
>explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id
> -> union 
> -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458141)
>
>##### 3. Using index
>
>```
>表示相应的select操作中使用了覆盖索引（Covering Index）,避免访问了表的数据行，效率不错！
>如果同时出现using where，表明索引被用来执行索引键值的查找
>如果没有同时出现using where，表明索引用来读取数据而非执行查找动作
>例子：
>explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;
>备注：
>覆盖索引：select的数据列只用从索引中就能够取得，不必读取数据行，MySQL可以利用索引返回select列表中的字段，而不必根据索引再次读取数据文件，即查询列要被所建的索引覆盖
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458145)
>
>##### 4. Using where
>
>```
>使用了where条件
>例子：
>explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458139)
>
>##### 5. Using join buffer
>
>```
>使用了连接缓存
>例子：
>explain select student.*,teacher.*,subject.* from student,teacher,subject;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458140)
>
>##### 6. impossible where
>
>```
>where子句的值总是false，不能用来获取任何元组
>例子：
>explain select * from teacher where name = 'wangsi' and name = 'lisi';
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458144)
>
>##### 7. distinct
>
>```
>一旦mysql找到了与行相联合匹配的行，就不再搜索了
>例子：
>explain select distinct teacher.name from teacher left join subject on teacher.id = subject.teacher_id;
>```
>
>![一张图搞定 explain](https://segmentfault.com/img/remote/1460000021458143)
>
>##### 8. Select tables optimized away
>
>```
>SELECT操作已经优化到不能再优化了（MySQL根本没有遍历表或索引就返回数据了）
>例子：
>explain select min(id) from subject;
>```

### 3.日志

### 4.分页分表

## 七、主从复制

## 八、缓存

## 九、集群



