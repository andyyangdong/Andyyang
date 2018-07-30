JAVA悲观锁与乐观锁
===
## 乐观锁
1）定义 ：事务更新不会频繁，冲突读情况下重新进行事务操作
  因为乐观锁不会锁定任何记录，所以当数据库的事务隔离级别设置为读已提交或者更低时，是不能避免不可重复读问题的，所以采用乐观锁的时候，系统应该容许不可重复读问题的出现
 2）实现方式
在数据库表中添加一个version字段来实现。先读 ，更新时候比较是否大于原先version

##  悲观锁 
定义：所有对于表操作的都是认为有冲突的 ；事务并发更新很频繁，并且事务失败了以后重来的开销很大，这样一来，我们就需要真正意义上的锁来进行实现
   2）实现方式： 悲观锁的实现，只能依靠数据库提供的锁机制  ；需要使用select  for update语句 当是id 则是行锁，普通字段类型：表锁




#  MySQL/InnoDB定义的4种事务隔离级别

##  **InnoDB默认是可重复读的（REPEATABLE READ）**

| 隔离级别 | 脏读（Dirty Read） | 不可重复读（NonRepeatable Read） | 幻读（Phantom Read） |
| :-- | :-- | :-- | :-- |
| 未提交读（Read uncommitted） | 可能 | 可能 | 可能 |
| 已提交读（Read committed） | 不可能 | 可能 | 可能 |
| 可重复读（Repeatable read） | 不可能 | 不可能 | 可能 |
| 可串行化（SERIALIZABLE） | 不可能 | 不可能 | 不可能 |

## 只需要在添加事务额注解上加上这样的代码即可提升事务的隔离级别：
@Transactional(rollbackFor = OrderProcException.class, isolation = Isolation.SERIALIZABLE)
