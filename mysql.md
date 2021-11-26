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
- **读取已提交（read-committed）**：允许读取已提交的数据，**可以阻止脏读，还还会有幻读或不可重复读**；
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