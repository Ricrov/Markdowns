# DML
## DELETE 删除数据
### ==and:与  or:或  !:非==
- delete from 表名 where 条件1 and 条件2
- 与: 用and表示
- 或: 用or表示
- 非: 用!表示
- = : 表示条件等于什么
- ==in: 表示删除在一个范围内的数据==
  * 如: ==delete from 表名 where id in (1,2,3,4,5)==
- ==like :表示删除的内容像什么样的==
- 'data%': 表示以data开头; '%data': 表示以data结尾; '%data%': 表示包含data
  * 如 delete from 表名 where username like 'class%';==百分号表示它的后面或前面还有内容==
## UPDATE 更新语句
### ==update 表名 set `列1`=`值1`,...`列n`=`值n` where 条件==
- 如: update 表名 set gender='男' where id=12;
### ==NULL 特殊的==
- ==当把null当做条件时:用 is null 表示==
## 事务
- begin;开启事务
- rollback;回滚事务
- commit;提交事务
## SELECT 查询语句
### select查询出来的是结果集,不会对原数据有影响,查出来的结果集不可以保存
- ==select 列名1,列名2,...列n from 表名 where 条件==
- select 列名1 as 新名字,列名2,... from 表名 as 新表名 where 条件
  * ==as关键字可以把结果集中的列名和表名重命名==
  * ==as可以省略==
## ==笛卡尔积==
- 在多表联查时出现的所有组合情况的集合
## 对表排序
### order by
- order by 条件(按什么排列):升序排列
- order by 条件 desc :降序排
- select t1.code_type_id, t1.code_type_name, t2.code_id, t2.code_name, t2.code_type_id from tb_code_type t1, tb_code t2 where t1.code_type_id = t2.code_type_id order by t1.code_type_id, t2.code_id desc
  * 先按t1表中的code_type_id生序排列,如果ti中的code_type_id有重复的,按t2中的code_id降序排
## 分页
### ==limit num1,num2==
- num1: 表示从第几条开始查询
- num2: 表示查询多少条数据

# DDL
## DDl 数据库定义语言
- 对数据结构进行操作
### ==ALTER: 修改表==
- alter table 表名 add 列定义:在表中新增加一列
- alter table 表名 drop 列名:删除表中的列名那一列
- alter table 表名 change 列名 新列定义:重新定义表中的某一列
- alter table 表名 modify 列名 列类型:改变表中的某一列的类型和约束
### ==rename:修改表名和移动表==
- rename table old-name to new-name:修改表名
- rename table 旧数据库.表名 to 新数据库.表名;
### truncate 清空表
- truncate table 表名
- ==当表被清空时,id会从新开始增长==
### ==主键 primary key==
- 它声明在列定义的后面
- 它有自增长功能
- primary key 主键的功能
- primary key auto_increment:主键自增长
### ==给指定列插入数据==
- insert into 表名 (列名1, 列名2) values (数据内容),(数据内容)...
### DELETE 删除语句
#### ==当删除一行时,如果id有自增长功能,那么再插入数据时,id的值会接着增长,不会从新开始==
- delete from 表名 where 条件;
## ==插入数据 insert==
- insert into 表名 value (值1,值2...):向表中插入一条数据
- insert into 表名 values (值1,值2), (值1,值2...)...:向表中插入多条数据
- insert into 表名(列1,列2...) value (值1,值2...):向指定列插入一条数据
- insert into 表名(列1,列2...) values (值1,值2...),(值1,值2...)...:向指定列插入多条数据
## ==删除数据 delete==
- delete from 表名 where 条件:删除指定条件的数据
- delete from 表名:清空表中的数据
### ==COMMENT 注释==
#### 在定义列的时候和表的时候可以加上注释
### ==DEFAULT 默认值==
- 如果在插入数据的时候某一列不插入数据,那么它的值就是定义的默认值
## MySQL存储引擎
#### InnoDB:删除,更新,插入,安全性高
#### MyISAM:不需要事务,空间小,以查询访问为主
#### ==在建表的后面可以指定储存引擎和字符集以及注释==
## 注释
- #: 表示单行注释
- /*   */: 多行注释
