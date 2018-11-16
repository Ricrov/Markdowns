# 事务
- 不可分割的操作:假设一个操作有abcd四个步骤组成,那么除非这四个步骤都完成,否则事务失败
  * 每条sql语句都是一个事务(默认情况下)
  * 事务只对DML语句有效,对DQL无效
### 事务的ACID(四大特性)
- 原子性:它指事务包含的所有操作要么成功要么失败
- 一致性:让数据保持一定上的合理
- 隔离性:一边事务没完成,如果没有提交事务,其他人访问不到修改后的数据
- 持久性:提交之后不能撤回
### 事务的使用
- 开启事务:start transaction:手动开启事务
- 回滚事务:撤回的过程
  * rollback:当遇到一些情况时,撤销已经执行的SQL语句
  * 还原所有操作
- 提交事务:commit;
### 事务当中的并发问题
- 多人同时做某一件事情
- 会发生的情况
  * 脏读:看到了没有提交的数据
    * read committed!读提交,可以解决这个问题
  * 不可重复读:两次查询结果返回不一样
  * 重复读:
  * 幻读:
  * 对应关系
### 事务隔离级别
- read uncommitted:事务没有提交也可以读
- read committed :事务提交后才可以读
- repeatable read:设置可以重复读
- serializable:可以解决幻读的情况;一般不会使用

# 子查询
- 一个select语句中包含另外一个完整的select语句
- 或者包含两个以上的select,那么这就叫做子查询语句了
## 子查询出现的位置
- where后面,把select查询出的结果当做另一个select的条件值
  * 例如:select name from emp where depton = (select deptno from emp where name = '项羽');
- from后面,把查询出的结果当作一个新表
  * select name from (select name,salary,deptno from emp where deptno = 30) where salary > 2000;
## 自连接
- 自己连接自己,通过起别名的方式实现
  * select e1.empno ,e1.ename, e2.empno, e2.ename from emp e1,emp e2 where e1.mgr = e2.empno and e1.empno=7369
