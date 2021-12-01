# MYSQL

## 1. 存储引擎

- `MyISAM`：

  ​		不支持事务，不支持外键，使用非聚簇索引。访问速度较快，对事务完整性没有要求或者以select、insert为主的应用基本可以使用。

- `InnoDB`:

  ​	`InnoDB`是一个健壮的事务型存储引擎，这种存储引擎已经被很多互联网公司使用，为用户操作非常大的数据存储提供了一个强大的解决方案。MySQL 5.5开始，`InnoDB`就是作为默认的存储引擎。`InnoDB`还引入了行级锁定和外键约束，在以下场合下，使用`InnoDB`是最理想的选择：

  - 更新密集的表。`InnoDB`存储引擎特别适合处理多重并发的更新请求。
  - 事务。`InnoDB`存储引擎是支持事务的标准MySQL存储引擎。
  - 自动灾难恢复。与其它存储引擎不同，`InnoDB`表能够自动从灾难中恢复。
  - 外键约束。MySQL支持外键的存储引擎只有`InnoDB`。
  - 支持自动增加列AUTO_INCREMENT属性。

  一般来说，如果需要事务支持，并且有较高的并发读取频率，InnoDB是不错的选择。

## 2. 常见索引

- 主键索引和联合主键索引：

  ```sql
  create table 表名 (
  	id int not null auto_increment primary key,		-- 主键
  	name varchar(32) no null
  );
  
  create table 表名 (
  	id int not null auto_increment,		
  	name varchar(32) no null,
  	primary key(id)	   -- 主键
  );
  
  create table 表名 (
  	id int not null auto_increment,		
  	name varchar(32) no null,
  	...... ,
  	primary key(列1, 列2)	   -- 联合主键
  );
  
  alter table 表名 add primary key (列名);
  alter table 表名 drop primary key;
  ```

  `注意:删除索引时可能会报错，因为自增的列必须定义为键。删除自增列之前必须让其不自增。如果那一列不自增，就不会报错。`

  ```sql
  alter table 表名 change id id int not null;
  ```

- 唯一索引和联合唯一索引：

  ```sql
  create table 表名 (
  	id int not null auto_increment primary key,		
  	name varchar(32) not null,
  	email varchar(64) not null,
  	unique ix_name (name),
  	unique ix_email (email)    -- 唯一索引，格式为 索引名 (列名)
  );
  
  create table 表名 (
  	id int not null auto_increment primary key,		
  	name varchar(32) not null,
  	email varchar(64) not null,
  	unique (列1, 列2)   -- 联合唯一索引
  );
  
  create unique index 索引名 on 表名(列名);
  drop unique index 索引名 on 表名;
  ```

- （普通）索引和联合索引，仅仅用于查询提速：

  ```sql
  create table 表名 (
  	id int not null auto_increment primary key,		
  	name varchar(32) not null,
  	email varchar(64) not null,
  	index ix_name (name),
  	index ix_email (email)    -- 索引，格式为 索引名 (列名)
  );
  
  create table 表名 (
  	id int not null auto_increment primary key,		
  	name varchar(32) not null,
  	email varchar(64) not null,
  	index (列1, 列2)   -- 联合索引
  );
  
  create index 索引名 on 表名(列名);
  drop index 索引名 on 表名;
  ```



## 3. 事务

```sql
# 开启一个事务
START TRANSACTION;
# 多条 SQL 语句
SQL1,SQL2...
## 提交事务
COMMIT;

```

- 关系型数据库的ACID特性
  - **原子性**（`Atomicity`） ： 事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；
  - **一致性**（`Consistency`）： 执行事务前后，数据保持一致，例如转账业务中，无论事务是否成功，转账者和收款人的总额应该是不变的；
  - **隔离性**（`Isolation`）： 并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；
  - **持久性**（`Durability`）： 一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。

### （1） 并发事务带来的问题

- **脏读（Dirty read）**：一个事务在修改一条数据，但还没有提交给数据库，另一个事务读取数据是读的是修改之前的数据；
- **丢失修改（Lost to modify）**：两个事务同时访问一条数据，后提交的那个覆盖了先提交的那个，先提交的那个修改被丢失了；
- **不可重复读（Unrepeatable read）**：一个事务多次读同一数据，在事务还没有结束的时候，另一个事务修改了这个数据导致多次读取的情况不一样；
- **幻读（Phantom read）**：与不可重复读类似。一个事务在多次读取的时候另一个事务增加或删除了一些记录，好像发生了幻觉一样。

### （2） 事务的隔离级别

​		**SQL标准定义了四个隔离级别**，`mysql`的默认隔离级别是**可重复读**。

- **读取未提交（read-uncommitted）**：最低的隔离级别，允许读取尚未提交的数据变更，**可能导致脏读、幻读或不可重复读**；
- **读取已提交（read-committed）**：允许读取已提交的数据，**可以阻止脏读，还会有幻读或不可重复读**；
- **可重复读（repeatable-read）**：对同一字段的多次读取结果都是一致的，除非是由该事务本身修改的，**可阻止脏读和不可重复读，但还还可能出现幻读**；
- **可串行化（serializable）**：最高隔离级别，完全服从ACID。所有事务依次执行，完全不产生干扰，可以防止以上所有问题。



## 4. 锁

基于锁的属性分类：共享锁、排他锁；

基于粒度分类：行级锁（`InnoDB`用到）、表级锁（`InnoDB`、`MyISAM`）、页级锁（`BDB`）、记录锁、间隙锁、邻键锁；

基于锁的状态分类：意向共享锁、意向排他锁

- **共享锁**（Share Lock）

  > ​		又称读锁，简称 S 锁。一个事务对数据加S锁之后，其他事务只能对这个数据加S锁，不能加写锁。只有等释放之后才能加写锁。S锁的目的是支持并发读取数据，读取数据的时候不支持修改，避免不可重复读问题。

- **排他锁**（`eXclude` Lock）

  > ​       又称写锁，简称 X 锁。一个事务加上写锁后，其他事务不能加任何锁。目的是在数据修改时，不允许其他人同时修改、读取，避免出现脏数据和脏读。

- **表锁**

  > ​      对整个表加锁，最大的锁粒度。
  >
  > ​      特点：粒度最大，枷锁简单，容易冲突。

- **行锁**

  > ​      只对表中的某一行或多行数据进行加锁，其他事务访问这张表时可访问未加锁部分。
  >
  > ​      特点：粒度最小，加锁比较麻烦，不容易冲突，相比表锁并发度高。

- **记录锁**（Record Lock）

  > ​      属于行锁的一种，但是只有一条记录（行锁可以有多条）。需要精准的条件命中，命中的条件字段是唯一的索引。
  >
  > ​      避免数据在查询的时候被修改，即避免不可重复读问题。也避免了修改事务未提交的时候被其他事务读取造成的脏读。

- **页锁**

  > ​      粒度基于行锁和表锁之间，特点折中。一次锁定相邻的一组数据。

- **间隙锁**（Gap Lock）

  > ​      属于行级锁的一种，锁住表的某一个区间。当表的相邻ID之间出现空隙回形成一个区间，遵循左开右闭原则。加间隙锁后，无法往区间中插入数据，防止幻读问题。

- **临键锁**（Next-Key Lock）

  > ​      属于行锁的一种，它是`InnoDB`的行锁默认算法，总结来说是记录锁和间隙锁的组合。临键锁会把查询出来的记录锁住，同时也会把该范围查询内的所有间隙空间也锁住，再把它相邻的下一个区间也锁住。
  >
  > ​      触发条件：范围查询并命中，查询命中了索引。
  >
  > ​      结合了记录锁和间隙锁的特性，避免了脏读、不可重复读、幻读问题。再范围区间内数据不允许被修改和插入。

> ​		如果当事务A加锁成功后就设置一个状态告诉他人，已经有人往表的记录加了排他锁，其他人不能加锁了，那么后面的人只需要获取这个状态就知道能不能加锁了，避免了对整个索引树的每个节点扫描是否加锁，这个状态就是意向锁。

- **意向共享锁**：已经有人对整张表加了共享锁的标志位；
- **意向排他锁**：已经有人对整张表加了排他锁的标志位。



## 5. `InnoDB`事务实现原理

> ​		数据库的ACID四个特性，ACD是通过`Redo Log`和`Undo Log`实现的。而隔离性I是由锁实现的。

### （1） Redo Log 重做日志

​		重做日志用于实现事务的持久性，即D特性。它让数据库有了崩溃恢复的能力。由两部分组成：

- 内存中的重做日志缓存（Redo Log Buffer）
- 磁盘中的重做日志文件

​		`InnoDB`每次在事务提交时，将事务的所有日志写到redo log文件中，再等事务commit操作完成后才算整个事务操作完成。

**刷盘时机**

​		`InnoDB`为redo log刷盘策略提供了`innodb_flush_log_at_trx_commit`参数，它支持三种策略：

​	**0** ：设置为 0 的时候，表示每次事务提交时不进行刷盘操作

​	**1** ：设置为 1 的时候，表示每次事务提交时都将进行刷盘操作（默认值）

​	**2** ：设置为 2 的时候，表示每次事务提交时都只把 redo log buffer 内容写入 page cache

​		默认为1，就是说当事务提交的时候会调用`fsync`对redo log进行刷盘。

​		另外，`InnoDB` 存储引擎有一个后台线程，每隔`1` 秒，就会把 `redo log buffer` 中的内容写到文件系统缓存（`page cache`），然后调用 `fsync` 刷盘。也就是说，一个没有提交事务的redo log记录也可能会刷盘。

![image-20211127130518996](https://i.loli.net/2021/11/27/dG36Jks4lqL78MX.png)

​		`innodb_flush_log_at_trx_commit` = 1时，在提交事务的时候通知重做日志缓存强制刷盘。`innodb_flush_log_at_trx_commit` = 2，再提交事务的时候通知重做日志缓存将redo log buffer的内容写入page cache，刷盘由后台进程决定。

> **还有需要了解的是：**
>  		redo log buffer将内存中的log block刷新到磁盘是有一定的规则的：事务提交时(前面已经提到)、当log buffer中有一半的内存空间被使用时、log checkpoint时。



### （2） Undo Log

​		Undo Log的两个作用：实现事务回滚，实现MVCC；

​		undo log和redo log记录物理日志不一样，它是逻辑日志。可以认为当delete一条记录时，undo log中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的update记录。



## 6. MVCC实现

> ​		MVCC，多版本并发控制。指的是 “维持一个数据的多个版本，使得读写操作没有冲突” 这么一个概念。
>
> ​		MVCC的实现是基于**三个隐藏字段**、**Read View**、**Undo Log**。

### （1） 隐藏字段

- `DB_TRX_ID（6字节）`：表示最后一次插入或更新该行的事务 id。此外，`delete` 操作在内部被视为更新，只不过会在记录头 `Record header` 中的 `deleted_flag` 字段将其标记为已删除；

- `DB_ROLL_PTR（7字节）` 回滚指针，指向该行的 `undo log` 。如果该行未被更新，则为空；

- `DB_ROW_ID（6字节）`：如果没有设置主键且该表没有唯一非空索引时，`InnoDB` 会使用该 id 来生成聚簇索引；

### （2） `ReadView`

​		主要用于做可见性判断，里面保存了“当前对本事务不可见的其他活跃事务”，主要有以下的字段：

- `m_low_limit_id`：目前出现过的最大的事务 ID+1，即下一个将被分配的事务 ID。大于等于这个 ID 的数据版本均不可见；

- `m_up_limit_id`：活跃事务列表 `m_ids` 中最小的事务 ID，如果 `m_ids` 为空，则 	`m_up_limit_id`等于 `m_low_limit_id`。小于这个 ID 的数据版本均可见；`
- `m_ids`：`Read View` 创建时其他未提交的活跃事务 ID 列表。创建 `Read View`时，将当前未提交事务 ID 记录下来，后续即使它们修改了记录行的值，对于当前事务也是不可见的。`m_ids` 不包括当前事务自己和已提交的事务（正在内存中）；

- `m_creator_trx_id`：创建该 `Read View` 的事务 ID。

​	**事务可见性示意图**

![image-20211127151022924](https://i.loli.net/2021/11/27/j6icutfyqKm4pRW.png)

### （3）Undo Log

> ​		主要由两个作用：
>
> - 用于回滚事务，用于恢复数据
> - MVCC，当读取记录时，若该纪录被其他事务占用或当前版本对该事务不可见，则可以通过 `Undo Log`读取之前版本的数据，以此实现非锁定读。

​		

Undo Log主要分为两种：

- insert Undo Log：代表事务在`insert`的时候产生的，只有在事务回滚的时候需要，事务一旦提交就可以立即删除；
- update Undo Log：在`update，delete`的时候产生。不仅是在事务回滚时需要，在快照读的时候也需要，因此不能随便删除。只有在快速读或事务回滚不涉及该日志时，对应的日志会被**`purge`**线程统一清除。

> ​		**purge**
>
> - 从前面的分析可以看出，为了实现`InnoDB`的MVCC机制，更新或者删除操作都只是设置一下老记录的deleted_bit，并不真正将过时的记录删除。
> - 为了节省磁盘空间，`InnoDB`有专门的purge线程来清理deleted_bit为true的记录。为了不影响MVCC的正常工作，purge线程自己也维护了一个read view（这个read view相当于系统中最老活跃事务的read view）;如果某个记录的deleted_bit为true，并且DB_TRX_ID相对于purge线程的read view可见，那么这条记录一定是可以被安全清除的。

​		不同事务或者相同事务的对同一记录行的修改，会使该记录行的 `undo log` 成为一条链表，链首就是最新的记录，链尾就是最早的旧记录。



## 7. SQL语言执行过程

![image-20211127165436688](https://i.loli.net/2021/11/27/VZeRyja3tEmMSWH.png)

​		MySQL 主要分为 Server 层和引擎层，Server 层主要包括连接器、查询缓存、分析器、优化器、执行器，同时还有一个日志模块（`binlog`），这个日志模块所有执行引擎都可以共用，`redolog` 只有 `InnoDB` 有。

​		引擎层是插件式的，目前主要包括，`MyISAM`,`InnoDB`,`Memory` 等。

​		查询语句的执行流程如下：权限校验（如果命中缓存）--->查询缓存--->分析器--->优化器--->权限校验--->执行器--->引擎

​		更新语句执行流程如下：分析器---->权限校验---->执行器--->引擎---redo log(prepare 状态)--->`binlog`--->redo log(commit状态)



## 8. `binlog`归档日志

> ​		redo log是物理日志，记录了有个数据页上做了什么修改，属于`InnoDB`引擎。
>
> ​		`binlog`是逻辑日志，记录的内容是语句的原始逻辑，属于`Mysql Server`层。

​		不管是用什么存储引擎，只要发生了表的数据更新（增删改），都会产生`binlog`日志。`Mysql`数据库的**数据备份、主备、主主、主从**都离不开`binlog`，需要用它来进行数据同步，保持数据的一致性。

​		`binlog`会记录所有涉及到数据更新的逻辑操作，并且是顺序写。



**记录格式**

​		一共三种格式，通过`binlog_format`参数指定：

- statement：记录的是SQL语句原文，数据同步时执行SQL语句，这样简单，但是可能造成系统时间不一致导致的数据库恢复不一致。
- row：记录的不只是简单的SQL语句，还包含操作的具体信息，比如时间戳等。需要用到`mysqlbinlog`工具解析出来，这样数据更可靠，但是需要更多存储空间，解析也要花时间。
- mixed：前两者的混合。`Mysql`会先判断这条SQL语句是否可能引起数据的不一致，然后选择方法。



**写入机制**

​		事务执行过程中，先把日志写到`binlog cache`，事务**提交**的时候，再把`binlog cache`写到`binlog`中去。

​		因为事务的原子性，一个事务的`binlog`不能拆分，无论这个事务有多大，都必须确保一次性写入，所以系统会给每个线程分配一个**块内存**所谓`bonlog cache`。

​		我们可以通过`binlog_cache_size`参数控制单个线程 `binlog cache`大小，如果存储内容超过了这个参数，就要暂存到磁盘（`Swap`）。

![image-20211127192308770](https://i.loli.net/2021/11/27/gAV4enbNiYlpxwX.png)

​		上图的write是将日志写到page cache里，并没有把数据持久化到磁盘，所以速度比较快，`fsync`才是真正持久化操作。write和`fsync`的时机由参数`sync_binlog`控制，默认是0。

- **0**：每次提交的时候，表示每次提交事务都只write，由系统自行判断什么时候执行`fsync`。提升性能，但是一旦宕机就会造成缓存丢失。

- **1**：每次提交事务的时候都会调用`fsync`。

- **折中方式**：设置一个数字N，每次提交事务都只write，累计N个事务之后`fsync`。一旦宕机只会丢失N个记录。在IO瓶颈的场景中，将`sync_binlog`设置大一点可以提升性能。



**两阶段提交**

> `redo log`重做日志让`InnoDB`存储引擎拥有了崩溃恢复的能力；
>
> `binlog`归档日志保证了`Mysql`集群架构的数据一致性。
>
> ​		两者都是为了保证数据的一致性，但侧重点不同。在执行更新语句过程，会记录`redo log`与`binlog`两块日志，以基本的事务为单位，`redo log`在事务执行过程中可以不断写入，而`binlog`只有在提交事务时才写入，所以`redo log`与`binlog`的写入时机不一样。

![image-20211127193731656](https://i.loli.net/2021/11/27/kPjOWXv2fMEBszq.png)

​		**为什么会用提出两阶段提交**：为了解决`Redo Log`和`binlog`之间的记录不同的冲突，因为在commit之前，可能会宕机，此时`Redo Log`里存在修改，但是`binlog`没有。

​		原理很简单，将`redo log`的写入拆成了两个步骤`prepare`和`commit`，这就是**两阶段提交**。

![image-20211127195044860](https://i.loli.net/2021/11/27/hojqkm2pCWX7fwz.png)

​		使用**两阶段提交**后，写入`binlog`时发生异常也不会有影响，因为`MySQL`根据`redo log`日志恢复数据时，发现`redo log`还处于`prepare`阶段，并且没有对应`binlog`日志，就会回滚该事务。

> 再看一个场景，`redo log`设置`commit`阶段发生异常，那会不会回滚事务呢？
>
> ​		并不会回滚事务，它会执行上图框住的逻辑，虽然`redo log`是处于`prepare`阶段，但是能通过事务`id`找到对应的`binlog`日志，所以`MySQL`认为是完整的，就会提交事务恢复数据。



# Redis

