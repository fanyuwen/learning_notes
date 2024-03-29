# 数据库事务的四大特性(ACID)
1. *原子性*(atomicity): 事务的最小工作单元(不可分割),要么全成功,要么全失败
2. *一致性*(consistency): 事务开始和结束后,数据库的完整性不会被破坏
3. *隔离性*(isolation): 不同事务之间互不影响,四种隔离级别为RU(read uncommitted读未提交)、RC(read committed读已提交)、RR(repeatable read可重复读)、SERIALIZABLE(串行化)
4. *持久性*(durability): 事务提交后,对数据的修改是永久性的,即使系统故障也不会丢失

# 事务隔离级别不同级别的划分区别
1. 读未提交 (read uncommitted/RU)
   又称为脏读,一个事务可以读取到另一个事务未提交的数据.这种隔离级别岁最不安全的一种,因为未提交的事务是存在回滚的情况
2. 读已提交 (read committed/RC)
   又称为不可重复读,一个事务因为读取到另一个事务已提交的修改数据,导致在当前事务的不同时间读取同一条数据获取的结果不一致
3. 可重复读 (repeatable read/RR)
   又称为幻读,一个事物读可以读取到其他事务提交的数据,但是在<u>_RR_</u>隔离级别下,当前读取此条数据只可读取一次,在当前事务中,不论读取多少次,数据仍然是第一次读取的值,不会因为在第一次读取之后,其他事务再修改提交此数据而产生改变.因此也成为幻读,因为读出来的数据并不一定就是最新的数据
4. 串行化 (SERIALIZABLE)
   所有的数据库的读或者写操作都为串行执行,当前隔离级别下只支持单个请求同时执行,所有的操作都需要队列执行.所以这种隔离级别下所有的数据是最稳定的,但是性能也是最差的.数据库的锁实现就是这种隔离级别的更小粒度版本

# mysql底层事务和mvcc原理
