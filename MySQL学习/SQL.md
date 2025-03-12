## 存储引擎

Inoodb And ISSM And Mermenr And Merge



## 两阶段提交

binlog   redolog

## 执行

从你发送命令，到MySQL把数据发送回来，这期间发生了什么？

## 范式

第一范式：每一列都是不可再分割的。遵循原子性，表中字段的数据不可再分割

第二范式：在遵循第一范式的情况下也就是原子性的情况下，再遵循唯一性，消除部份依赖。

​					表中任意一个主键或者联合主键能够确定除了该主键外其他所有非主键的值，也就是一个表讲述一件事

第三范式： 在满足第二范式的情况下，消除依赖传递。就是在满足任意主键能够确定除此主键以外其他值的情况下，其他任意非主键不					能确定另外一个非主键的值（也就是在第二范式上加了一个，不能通过非主键获取另外一个非主键的值）

## 多表查询

#### 内连接

* 隐式内连接

表结构：emp，dept

连接条件：emp,dept_id = dept.id

```sql
-- 1
select * from emp , dept where emp.dept_id = dept.id
-- 2
select emp.name, dept.name from emp , dept where emp.dept_id = dept.id
-- 3 起个别名
select e.name, d.name from emp e , dept d where e.dept_id = d.id
-- 4 起个别名 就不能使用表名来限定字段了  因为先执行的是from 后where
select e.name, d.name from emp e , dept d where emp.dept_id = dept.id
```

* 显示内连接

... inner join ... on ...

表结构：emp，dept

连接条件：emp,dept_id = dept.id

```sql
-- 1
select e.name , d.name from emp e inner join dept d on e.dept_id = d.id
-- 2 inner可以省略
select e.name , d.name from emp e join dept d on e.dept_id = d.id
```

![image-20241211143747099](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111437217.png)

#### 外连接

![image-20241211143931974](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111439043.png)

* 左外连接

表结构：emp，dept

连接条件：emp,dept_id = dept.id

查询emp的所有数据和dept的交集

```sql
-- 这条语句会把emp和dept的所有列全部拿过来  有两列id
select * from emp e left outer join dept d on e.dept_id = d.id
-- 筛选一下只要empt的所有和dept的name
select e.*, d.name from emp e left outer join dept d on e.dept_id = d.id
```

* 右外连接

查询dept表的所有信息和对应的员工信息

```sql
select d.*, e.* from emp e right outer join dept d on e.dept_id = d.id
```

改成左外

```sql
select d.*, e.* from dept d left outer join emp e on e.dept_id = d.id
```

#### 自连接

![image-20241211151504688](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111515739.png)

查询员工信息和所属领导信息

```sql
-- 用内连接是查的交集   如果有的人没有领导  交集里面就没有他
-- 隐式
select a.*, b.name from emp a, emp b  where a.managerid = b.id
-- 显式
select e.*, b.name from emp e join emp b on e.managerid = b.id
```

```sql
-- 用外连接  这样就可以包含所有的员工了  即使他没有领导  
select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid = b.id
```

### 联合查询

![image-20241211153043942](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111530015.png)

```sql
-- 查询薪资低于5000的员工 和年龄大于50的员工

-- 直接合并了
select * from emp where salary < 5000
union all
select * from emp where age > 50

-- 去重  union是去重  union all是直接合并
select * from emp where salary < 5000
union
select * from emp where age > 50
```

![image-20241211154002967](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111540021.png)

### 子查询

![image-20241211162953044](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111629101.png)

![image-20241211164411019](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111644127.png)

```sql
-- 2 4
select id from dept where name = '销售部' or name = '市场部'
select * from emp where dept_id in (2,4)

select * from emp where dept_id in (select id from dept where name = '销售部' or name = '市场部')
```

![image-20241211164950136](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111649189.png)

all 是必须子查询返回来的所有值都必须满足这个条件

![image-20241211165216764](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111652827.png)

* 表子查询

![image-20241211174806082](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111748196.png)

子查询用作临时表

![image-20241211175011453](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111750566.png)

去重关键字 **distinct**

![image-20241211175707909](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202412111757947.png)

## sql性能

```sql

-- 查看全局状态  各个语句的执行次数
show GLOBAL STATUS LIKE 'Com_______';

SELECT * from question;

-- 慢查询日志

-- 查看慢查询日志是否开启
SHOW VARIABLES like 'slow_query_log';

-- # 开启MySQL慢日志查询开关 
-- slow_query_log=1
-- # 设置慢日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志 
-- long_query_time=2
-- slow_query_log_file = "D:/MySQL/mysql_slow_query.log"


-- PROFILE
-- 查看数据库是否支持profile
select @@have_profiling; 
-- 开启profile
SET profiling=1;

-- 执行一些操作使得profiling可以追踪
select COUNT(*) FROM question;

SHOW PROFILES;
-- 从里面可以得到根据name查要比根据id慢好多，是因为，id是聚合索引，name是二级索引，需要回表查询。


-- 查询指定id的sql语句耗时
show PROFILE for QUERY 39;


-- EXPLAIN执行计划
-- 可以在任何select前面加上explain,查看此sql的执行计划
EXPLAIN SELECT * FROM question WHERE id = 1;

```

> 问题：了解过索引嘛，什么是索引
>
> 回答：
>
> 1. 索引是帮助mysql高效获取数据的数据结构（有序）。数据库维护着一个满足特定查找算法的树结构（B+），这些数据结构指向数据，这种数据结构就是索引。
> 2. 提高检索的效率，降低IO成本
> 3. 通过索引进行排序，降低了排序的成本，降低了CPU的消耗
>
> 
>
> 问题： 为什么用B+
>
> 回答：多路平衡查找树，避免了二叉树层数太深的情况。
>
> （其实就是阶数更多，路径更短，存储的节点更多）
>
> 二叉树存在最坏二叉树的情况，就是形成一个链表，虽然有红黑树可以平衡，但是还是二路树，节点多了，太高，B+树的叶子节点是双向链表，非叶子节点但只存储指针，所有的数据都在叶子节点。
>
> 对比与B树：
>
> 1. 磁盘读写代价低，因为B树非叶子节点也有数据，查的时候非叶子节点的数据也得查出来。B+就不用，非叶子节点只导航，数据在叶子节点获取。
> 2. 查找更稳定：数据都在叶子上，每一次都要从跟开始查找，查找路径差不多，所以更稳定
> 3. B+树便于扫库和区间查询，因为叶子数据是双向指针

## 索引

### 索引分类

* 按照数据结构分类：B+树索引、HASH索引
* 按照物理存储分类：聚簇索引、二级索引
* 按照字段特性分类：主键索引、唯一索引、普通索引、前缀索引
* 按照字段个数分类：单列索引、联合索引

> 什么是聚簇索引和非聚簇索引？

> **聚簇索引默认是主键索引，如果没有这个主键索引，就会去找第一个唯一索引，都没有就会生成一个默认的row_id作为聚集索引， 聚簇索引（聚集索引）叶子节点存储整行的数据，非聚簇索引叶子节点存储行id**



> 什么是回表

比如：select * from tb_user where name = Arm

我们在ame字段上建立非聚簇索引，通过Arm走一遍索引，拿到主键值，应为是要*，所以非聚簇索引不够用，用id再去走一遍聚簇索引，这就叫回表查询

![image-20250218224724158](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202502182247524.png)

> 什么是覆盖索引

覆盖索引指的是查询使用了索引，并且需要返回的数据在该索引树上已经可以全部找到。

### 索引使用

```sql
-- 索引使用
-- 最左前缀法则，使用的时候必须包含索引最左边的列

-- 在使用> < 范围查询的时候，右边的索引失效。尽量使用大于等于来规避索引失效的情况

-- 不要在索引列上进行运算  索引将会失效

-- 尾部模糊索引不会失效，头部模糊 索引失效

-- 用 or 左右都有索引的时候才会生效

-- 数据分布影响，查大部分数据走全表扫描，小部分数据走索引

-- SQL提示
-- use index(idx_name)     用哪个  只是建议 不一定使用
-- ignore index(idx_name)  不要用那个
-- force index (idx_name)  必须用某个

-- 覆盖索引
-- using index condition  使用了索引还要回表
-- using where using index  索引列就能找到
-- 覆盖索引就是可以在索引列中找到所需要的数据，不需要回表查询，所以尽量少使用select *, 因为它很容易出现回表查询
-- 查询的字段里面如果使用的索引或者联合索引找不到的话，就需要根据id去聚合索引中回表查找



```

![image-20241124173736396](SQL.assets/image-20241124173736396.png)

建立 id, username, password三个字段的联合索引，叶子节点挂的就是id，不需要回表查询。

------



```sql
-- 前缀索引  只将字符串一部分当作索引   解决长文本字段和大字符串的索引的时候  索引体积过大，浪费磁盘IO的情况
create index idx_xxx on table(column(n));
-- n为指定的字符数量


-- 单列索引和联合索引选择使用
-- 推荐使用联合索引   在使用两个单列索引的时候，mysql会之只使用一个索引，效率比较高的那个

```

![image-20241124175756923](SQL.assets/image-20241124175756923.png)

> 创建索引的原则

![image-20241124180039892](SQL.assets/image-20241124180039892.png)

![image-20250218230521408](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202502182305457.png)

> 索引失效的情况

用explain执行计划。

1. 违反了最左前缀法则
2. 范围查询没有等于
3. 不要在索引列上进行运算
4. 头部模糊查询失效

![image-20241124180746876](SQL.assets/image-20241124180746876.png)

![image-20241124181029090](SQL.assets/image-20241124181029090.png)

## SQL优化

> 问题：如果一个SQL查询很慢，如何分析呢？
>
> 使用sql的执行计划，分析慢的原因

### Explain

![image-20250212202736999](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202502122027455.png)

* possible_key 当前可能会用到的key
* key 当前sql实际命中的key
* key_len 索引的长度
* Extra 额外的优化建议

![image-20250212202910922](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202502122029005.png)

![image-20250212203115343](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202502122031399.png)

当使用index和all的时候就可能需要进行优化了

> 问题回答：
>
> 可以使用explain查看这条sql的执行计划
>
> 1. 通过key和key_len检查是否命中了索引
> 2. 通过type查看sql是否有进一步的优化空间，是否存在全索引扫描或者全盘扫描
> 3. 通过extra判断 是否出现了回表，如果出现，可以尝试添加索引，比如加上覆盖索引

### insert

* 批量插入
* 手动提交事务
* 主键顺序插入
* 大批量加载  load命令

### 主键

* 主键尽量短
* 顺序插入
* AUTO_INCREMENT	比UUID好，更短且顺序插入

### order by

* use index
* use filesort

使用use index 效率高

### limit

问题在于 limit 9000000 10，此时数据库需要排序前9000000 记录，然后返回9000001-9000010，查询代价太大

问题：超大分页：

![image-20250218225801825](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202502182258865.png)

```sql
-- 耗时太久
select * from tb_user limit 9000000,10;

-- 对其优化
-- 覆盖索引加子查询
-- 1. 拿到id
select id from tb_user order by id limit 9000000,10;
-- 2. 拿到数据，由于in后面不支持limit  
-- 不支持：select * from tb_user where user_id in (select id from tb_user order by id limit 9000000,10)
-- 把这句结果 select id from tb_user order by id limit 9000000,10; 当作一个表，通过多表联查。
select u.* form tb_user u, (select id from tb_user order by id limit 9000000,10) a where u.id=a.id;
-- 返回的时候只需要返回u的内容即可
-- 子查询是覆盖索引，主查询是聚集索引，避免了回表查询，这样实现了limit的优化

```

### count

```sql
-- innodb 执行count(*) 时，会一行一行读，非常耗时
count(*)     查询总记录数   不会取值。服务层直接按行累加
count(主键)   查询总记录数  取到每一行的主键值， 直接按行累加
count(字段)  返回这个字段不为null的条数   首先看看此字段有没有not null约束，如果有，引擎会把每一行的字段值拿出来，直接按行累加，如果没有，拿出来后就需要判断是否为null，累加不为null的 
count(1)     返回的每一条记录都放会一个1进去，对这个1累加  引擎不取值，但是服务层会对返回的每一行放一个1进去，进行累加

count(字段) < count(主键) < count(1) ~ count(*) 
```

### update

```sql
-- innodb 三大特性  事务 外键 行级锁
-- update语句的时候，如果where后面跟的不是索引，就会把把整个表锁住。没有索引相当于扫面整张表，行锁升级为表锁  导致并发性能降低
update tb_user set name = "JAVA" where name = "JAVAEE";
-- 所以更新字段的时候尽量使用索引更新

-- 可以先为name创建索引
create index idx_user_name on tb_user(name);
-- 再执行一样的语句  此时就是行锁
update tb_user set name = "JAVA" where name = "JAVAEE";
```

### 表的设计优化

![image-20250218232027635](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202502182320692.png)

## 锁

* 全局锁：所有表。对整个数据库实例加锁，处于只读状态  对数据库进行备份的时候，加全局锁，不加的话可能出现数据不一致性。

  缺点是，一旦上锁，全局停摆。

* 表级锁：一张表。

  * 表锁

    * 表共享读锁（readLock）：不会阻塞其他的读，但是阻塞其他的写

      ```sql
      lock tables tb_user read; 
      unlock tables;
      ```

    * 表独占写锁（writeLock）：只对当前客户端能读写，其他客户端全部不允许

      ```sql
      lock tables tb_user write;
      unlock tables;
      ```

  * 元数据锁：自动加锁，维护表结构

  * 意向锁：在更新的时候会加上行锁，再加上一个意向锁，客户端2来加表锁的时候，直接看意向锁兼容不兼容，不兼容就阻塞，兼容就加锁。这样加表锁的时候不用逐行检查行锁了。（他是针对行锁和表锁的，避免表锁和行锁的冲突，他是自动加的）

    * 意向共享锁（IS）：与表锁共享读锁兼容，与表锁排他写锁互斥

    * 意象排他锁（ES）：与表锁共享读锁表锁排他写锁都互斥

* 行级锁：某一行，锁的粒度最小。并发度最高

  > innodb的数据是基于索引组织的，聚集索引挂的是行数据，二级索引是ID。

  * 行锁：锁住单个行记录。**innodb中的行锁是针对索引列加锁，如果查询的条件不是索引，会升级为表锁**，会成为全表扫描

    * 共享锁
    * 排他锁

    ```sql
    select -- 不加任何锁
    select ... Lock in share mode   -- 加共享锁
    update -- 排他锁
    insert -- 排他锁
    delete -- 排他锁
    ```

  * 间隙锁：锁住索引间隙，在InnoDB中，**间隙锁**（Gap Lock）是为了防止**幻读**（Phantom Read）现象而引入的锁机制

    * 具体来说，间隙锁并不是锁定已存在的行，而是锁定行之间的“空隙”，即：

      如果查询条件为某个范围（例如 `SELECT * FROM table WHERE column > 10 AND column < 50`），那么间隙锁会锁定从 `column = 10` 到 `column = 50` 之间的“间隙”区域，防止其他事务在这个间隙中插入新的行。

    * 间隙锁的唯一目的就是锁住此记录之前的间隙，防止别的事务进行修改，防止幻读

  * 临键锁：行锁和间隙锁的组合

### 概述

锁是在并发访问时，解决数据一致性和有效性问题。全局锁、表级锁、行级锁

## InnoDB存储引擎

![image-20241128201311616](SQL.assets/image-20241128201311616.png)

![image-20241128201247869](SQL.assets/image-20241128201247869.png)

### 架构

![image-20241128201425399](SQL.assets/image-20241128201425399.png)

#### 内存结构

![image-20241128201906601](SQL.assets/image-20241128201906601.png)

![image-20241128202125987](SQL.assets/image-20241128202125987.png)

**Change Buffer相当于缓冲区的缓冲区**

![image-20241128202228765](SQL.assets/image-20241128202228765.png)

**Innodb支持自适应hash**

![image-20241128202554298](SQL.assets/image-20241128202554298.png)

#### 磁盘结构

![image-20241128203115551](SQL.assets/image-20241128203115551.png)

#### 后台线程

![image-20241128203327165](SQL.assets/image-20241128203327165.png)

在操作数据的时候会现在缓冲区进行操作，如果缓冲区没有数据也会先加载进来，缓冲区内的数据也会以一定的频率刷新回去磁盘

### 事务原理

**事务是一组操作的集合，是不可分割的工作单位。事务会把所有的操作作为一个整体一起向系统提交或者撤销操作，要么同时成功，要么同时失败。**

**ACID**

![image-20241128204021308](SQL.assets/image-20241128204021308.png)

![image-20241128204253632](SQL.assets/image-20241128204253632.png)

![image-20241128204925594](SQL.assets/image-20241128204925594.png)

持久性：当我们进行**数据操纵语言**（DML，Data Manipulation Language）语句的时候，如果缓冲区没有我们操作的页，会通过后台线程从磁盘加载，然后我们对目标页进行操纵，完毕后这个页就变成了脏页，当我们回写的时候，如果出现事故，就会出现数据的持久性得不到保障的问题，此时需要redoLog来帮忙。他会记录操作页的变化**（记录的是物理日志，也就是变化的数据）**，记录到redoLog文件中，然后追加到磁盘中，脏页不着急回写，因为磁盘现在有它的重做日志。为什么不直接将脏页写回而时写回redoLog呢，因为我们的操作都大不是顺序的，随机的IO性能太差，而redoLog是追加，是顺序IO，性能较好。当我们的数据正常写入磁盘的时候，他也就没用了。

![image-20241128205844694](SQL.assets/image-20241128205844694.png)

> redolog和undolog有啥区别
>
> 答：redolog记录的是数据页的物理变化，服务宕机的时候可以用来同步数据。undolog记录的是逻辑日志，当事务回滚的时候，通过逆操作来进行恢复原来的模样。
>
> redolog保证了事务的持久性，undolog保证了事务的原子性和一致性

### 并发事务问题

![image-20250219212731220](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202502192127326.png)

如何解决并发事务问题：
答：对事物进行隔离

事务隔离级别：
![](https://shuaiyao85.oss-cn-qingdao.aliyuncs.com/img/202502192129178.png)

> 事务中的隔离性是由排他锁和MVCC来保证的

### MVCC

![image-20241128210047946](SQL.assets/image-20241128210047946.png)

![image-20241128210156255](SQL.assets/image-20241128210156255.png)

**MVCC，多版本并发控制，维护一个数据的多个版本，允许多个事务同时读同一行的数据，而不会彼此阻塞。**

![image-20241128210241450](SQL.assets/image-20241128210241450.png)

#### 隐藏字段

![image-20241128210617810](SQL.assets/image-20241128210617810.png)

![image-20241128212041652](SQL.assets/image-20241128212041652.png)

undoLog版本链表头部是最新的记录，尾部是最旧的记录

#### readview

![image-20241128212205833](SQL.assets/image-20241128212205833.png)

#### 版本链规则

![image-20241128212356160](SQL.assets/image-20241128212356160.png)
