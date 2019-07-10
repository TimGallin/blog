##Transaction##
>事务就是一组原子性的独立的工作单元。mysql中默认所有操作都是事务

**ACID**
- **atomicity**原子性：事务要么全部成功要么全部失败
- **consistency**一致性：数据库总是从一个一致性状态转换到另一个一致性状态
- **isolation**隔离性：事务在提交前对于其他事务来说是不可见的（和数据库的事务隔离等级相关）
- **durability**持久性：一旦事务提交，其修改将永久的保留在数据库中

MYSQL默认采用自动提交，即每一个查询默认都作为事务进行。


**Isolation Level 隔离等级**
1. **READ UNCOMMIT**
   事务中的修改即使未提交，对于其他事务也是可见的。这导致在同一次事务中，执行两次查询可能得到不同的查询结果，因为有可能在这两次之间有事务做了插入操作，此时读取就会得到该事务做了修改却还未提交的数据，即为**Dirty Read**脏读。
2. **READ COMMIT**
   事务中的修改只有在提交后才对于其他事务是可见的。这避免了脏读问题。也称为 nonrepeatable read。因为在同一事务中执行两次相同的查询，中间可能由于另外的事务提交了修改操作从而令两次查询得到不同的结果。
3. **REPEATABLE READ**
   该级别保证了在同一事务中多次读取同样的记录，解决了脏读问题，但是还是无法解决**Phantom Read**幻读问题。同一事务中执行两次查询，中间可能由于其他事务提交了插入数据操作，导致两次查询读取到了多余的记录，称为幻行。MYSQL通过**MVCC**解决了幻读问题。该等级也是MYSQL的默认事务隔离级别。
4. **SERILIZABLE**
   最高的隔离等级，它强制事务串行执行，在每一行数据上加锁。

##READ PHENOMENA##
**Dirty READ:**
A dirty read occurs when a transaction is allowed to read data from a row that has been modified by another running transaction and not yet committed.
```
//Query 1(suppose to read 20)
SELECT age FROM usres WHERE id=1;

//Q2
UPDATE usres SET age=21 WHERE id=1;

//Q1 return. will read 21

//Q2 ROLLBACK 

//In this case no row exists that has an id of 1 and age of 21
```

**Non-Repeatable READS**
A non-repeatable read occurs,when during the course of a transaction,a row is retrieved twice and the values within the row differ between reads
```
//Q1 (will read 20)
SELECT age FROM usres WHERE id=1;

//Q2
UPDATE  SET age=21 WHERE id=1;
COMMIT;

//Q1 will read 21.In this transaction,Q1 respectively gets two different results of 20 and 21 
SELECT age FROM usres WHERE id=1;   
COMMIT;

**Phantom READ**
A phantom read occurs when,in a course of transaction,new rows are inserted or removed by another transaction to the records being read.
```
//Q1 
SELECT * FROM users WHERE age BETWEEN 10 AND 30;

//Q2
INSERT INTO users(id,name,age) VALUES ( 3, 'Bob', 27 );
COMMIT;

//Q1
SELECT * FROM users
WHERE age BETWEEN 10 AND 30;
COMMIT;
   
**MVCC（Multi-Version Concurrency Control）**
多版本并发控制
为了解决在Repeatable read下幻读问题，mysql引用了MVCC的解决办法。MVCC可以视为行级锁的一个变种。以Innodb的实现为例，它在每一行后面增加了**行的创建时间和过期时间**两个隐藏字段，该字段的时间值为数据库系统的**版本号（System Version Number）**。每开始一个新事务，系统就将版本号增加1。事务开始时刻的系统版本号为作为该事务的版本号，事务中的查询操作只会查找小于或等于该事务版本的行数据，且行数据的删除版本号要么未定义要么大于当前事务版本号。这两条原则避免了事务读取到其他更新事务作了修改的数据或在本事务开始后被删除掉的数据。
每一个事务再未提交前所做出的的修改将被保留在**Undo Log**中，Undo Log中安更新次序保留了该条记录的更新顺序和对应的事务id。任何事务对于是数据所做出的的更改都会直接在当前的索引结构上作出操作，也就是索引结构中将保留最新版本的操作内容，同事保留其**ROLL Pointer**只想Undo Log。
##Benchmark##
>**基准测试**，一种为系统设计的压力测试。

##Profiling##
>性能剖析

##Schema与数据类型优化##

数据类型的选择原则
- 更小的通常更好。 空间优化
- 简单的通常更好。 整型优于字符
- 尽量避免NULL。  NULL不利于查询优化

##Data Access 数据访问类型##
###全表扫描 Full Table Scan###
又称为顺序扫描，由于其涉及到大量的I/O操作，通常是最慢的一种数据访问类型	。但这并不是绝对的，相对于索引扫描，通常还取决于索引是聚鏃索引（Cluster）还是非聚鏃索引（non-Cluster）以及扫描结果所涉及到行占总数的比例(https://docs.microsoft.com/en-us/previous-versions/sql/legacy/aa224773(v=sql.80)) 
通常在没有索引的情况下一个不带WHERE限制的查询会触发Full-Table-Scan\

###索引扫描 Index Scan###
通过索引树执行查询，通常在一个索引字段上执行范围查询时将触发Index-Scan
```
SELECT * FROM t_film WHERE id BETWEEN 10 AND 100;
```

###索引查询 Index Seek###


