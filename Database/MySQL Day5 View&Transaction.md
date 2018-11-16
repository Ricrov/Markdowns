
# 视图
- 它是一张虚拟表,其内容有查询定义
- 它有select结果集所组成的表
### 视图的特性
- 不存储具体的数据
- 可以对它进行增加,删除,改
### 视图的作用
- 安全性:可以以视图的形式,把内容分享给其他用户
- 查询性能提高
- 提高了数据的独立性
### 视图机制(创建,修改,删除)
- 创建视图: creat view 视图名 as 查询的结果集
- 查询视图信息: select*from 视图名 where 条件
- 视图的修改操作同DML
- 删除视图:drop view 视图名
### 视图机制
- 替换式
- 巨化式
### 视图不可更新部分
- 只要视图当中的数据不来源于基类,就不能够直接更改

## 视图
- 它就是一张临时表
- 相当于新建了一张临时表,储存在内存中
```
create view 视图名字 as '查询语句'
```

## 设置变量
- set @num := 初始值;
- select @num := @num + 1;



# 事务
- 事务是一系列操作,要么这一系列操作全部完成,要么全不完成
### 事务的特性:ACID
- 原子性:(atomicity)
  * 一个事务必须视为一个不可分割的最小工作单元，整个事务中的所有操作要么全部提交成功，要么全部失败回滚
- 一致性:(consistency)
  * 数据库总数从一个一致性的状态转换到另一个一致性的状态
- 隔离性:(isolation)
  * 一个事务所做的修改在最终提交以前，对其他事务是不可见的
- 持久性:(durability)
  * 一旦事务提交，则其所做的修改就会永久保存到数据库中。此时即使系统崩溃，修改的数据也不会丢失
### 开启事务
- begin
- start transaction
### 提交数据
- commit
### 回滚事务
- rollback: ==回滚之后,需要重写开启事务==
### 默认情况下
- 每一条SQL语句都是一个事务
### 设置SQL语句是否自动提交
- set autocommit=0/1: 设置提交时自动或手动
### 设置保存点
- savepoint 保存点名字
### 回滚到指定保存点
- rollback to savepoint 保存点名字

## MySQL实现行转列
一.
```
select stu.stu_id, stu.stu_name,
ifnull(max(case cou.course_name when '语文' then sc.score end) ,0) '语文成绩',
ifnull(max(case cou.course_name when '数学' then sc.score end) ,0) '数学成绩',
ifnull(max(case cou.course_name when '英语' then sc.score end) ,0) '英语成绩'
from t_students stu
left join t_score sc
on stu.stu_id=sc.stu_id
left join t_courses cou
on cou.course_id=sc.course_id
group by stu.stu_id;
```

二.
```
select stu.stu_id, stu.stu_name,
ifnull(max(case cou.course_name when '语文' then sc.score end) ,0) '语文成绩',
ifnull(max(case cou.course_name when '数学' then sc.score end) ,0) '数学成绩',
ifnull(max(case cou.course_name when '英语' then sc.score end) ,0) '英语成绩'
from t_students stu
cross join t_courses cou
left join t_score sc
on stu.stu_id=sc.stu_id
and cou.course_id=sc.course_id
group by stu.stu_id;
```
