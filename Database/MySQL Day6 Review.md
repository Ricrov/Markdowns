# MySQL复习
## 数据库
- 创建数据库
```
create database 数据库名字;
```
- 删除数据库
```
drop database 数据库名字;
```
- 选择数据库
```
use database;
```
- 查看所有数据库
```
show databases;
```
## 表
1. 查看所有表
```
show tables;
```
2. 建表
```
create table 表名(列名1 数据类型 约束 [default 默认值] [comment 注释],
                 列名2 数据类型 约束 [default 默认值] [comment 注释],
                 列名3 数据类型 约束 [default 默认值] [comment 注释],
                 ...
                 列名n 数据类型 约束 [default 默认值] [comment 注释])
-- 约束类型
-- unique
--- not null
-- primary key
--- auto_increment 自增长

-- 数据类型
-- BIT
-- TINYINT SMALLINT MEDIUMINT INT BIGINT
-- 整数: INT [(M)] [unsigned] [zerofill]
-- BOOLEAN/BOOL
-- FLOAT DOUBLE DECIMAL
-- CHAR
-- VARCHAR(M)
-- BOLB 存储二进制的,可以存文件
-- BINARY
-- DATE TIME DATETIME TIMESTAMP YEAR
```
3. 修改表
- 添加列
```
alter table 表名 add 列名 列类型
```
- 删除列
```
alter table 表名 drop 列名
```
- 修改列的类型
```
alter table 表名 modify 列名 列类型
```
- 修改列的名称和类型
```
alter table 表名 change 旧列名 新列名 新列类型
```
4. 表的重命名
```
-- 重命名表
rename table 旧表名 to 新表名

-- 移动表到新的数据库
rename table 旧数据库.旧表名 to 新数据库.新表名
```
5. 删除表
```
drop table 表名;
```
## 插入数据
1. 根据列的顺序插入一条数据
```
-- 值得顺序和数量需要与列的定义保持一致
insert into 表名
value (值1, 值2, 值3...值n);
```
2. 向指定的列中插入一条数据
```
insert into 表名(列1,列2,...列n)
value(值1, 值2,...值n);
```
3. 根据列的顺序插入多条数据
```
insert into 表名
values (值1, 值2,...值n),(值1,值2,...值n),...(值1, 值2,...值n);
```
4. 向指定列中插入多条数据
```
insert into 表名(列1, 列2,...列n)
values (值1, 值2,...值n),(值1,值2,...值n),...(值1, 值2,...值n);
```
## 删除数据
1. 根据条件删除数据
```
delete from 表名 where 条件;
```
2. 删除某个表的所有数据
```
delete from 表名;
```
3. 清空某个表
```
truncate table 表名;
-- 清空表的时候可以把自增长的数据也重置了
```
## 修改数据
```
update 表名 set 列1=新值... where 条件;
```
## 查询数据

```
-- 查询某个表的所有列所有数据
select * from 表名;
-- 查询某个表的指定列的数据
select 列1 as 别名1,列2 as 别名2,...列n as 别名n from 表名 where 条件;
```
## 条件
1. 比较运算符: > >= < <= !=
2. 是否在某个集合中: in
3. 是否不在某个集合中: not in
4. 在两个值之间: between min and max
5. 不在两个值之间: not between min and max
6. 逻辑运算符: and or !
7. 判断是否为null: is null
8. 判断是否不为null: is not null
9. 判断字符串符合某个格式: like
    - '%str' : 以str结尾的
    - 'str%' : 以str开头的
    - '%str%': 包含str的
## 分组
    'group by... 列1,列2,...列n having 条件'

    '分组一般会跟着分组函数/聚合函数一起使用'
    - count
    - sum
    - avg
    - max
    - min
    - group_concate: 把分组之后的数据连接到一起
## 分页
    'limit m, n'
     从第m条数据开始,取n条数据
## 函数
1. if(con,value1,value2)
- 相当于: con?value1:value2;

2.ifnull(列,值)
- 当列的值为空时,结果取值

3. case...when ... then ...else... end
    - 一共有两种写法
    - 一种相当于java中的switch
    - 一种相当于if...else if...
    - switch 用法
    ```
    - 有一列地址(address),取值有如下形式:
    --- 10001: 户籍地址
    --- 10002:家庭地址
    --- 10003:公司地址
    case address when 10001 then '户籍地址'
    when 10002 then '家庭地址'
    when 10003 then '公司地址'
    else '其他地址'
    end;
    - if...else if ...else...
    - 分数(score)
    -- 大于等于80分优秀
    -- 大于等于60分及格
    -- 小于60分不及格
    case when score>=80 then '优秀'
    when score>=60 then '及格'
    else '不及格'
    end;
    ```
## 外键
1. 添加外键
```
alter table 表名 add
[constraint 外键名]
foreign key (需要添加外键的列)
references 关联的表(关联的列)
[on update 约束类型]
[on delete 约束类型]
--- 外键约束:约束的是对父表的数据进行删除和更新时,对子表的影响
-- 约束类型
--- restrict 限制约束: 父表的数据不能动
--- cascade 级联约束: 更新或删除父表数据的时候,子表也做同样的操作
--- set null 置空 子表的外键列置为kong
--- no action 标准的限制约束
--- set default MySQL中没有
--- 外键名字命名规范
--- 源表_目标表_外键列_fk
```
2. 删除外键
```
alter table 表名 drop foreign key '外键名字';
-- '删除主键';
--- 前提是主键没有自增,并且没有外键关联
alter table 表名 drop primary key;
-- 删除unique键,删除索引
alter table 表名 drop index 唯一键名;
```
