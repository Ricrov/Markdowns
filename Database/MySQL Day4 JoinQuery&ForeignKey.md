## 去掉重复部分 distinct
- 在要查询的结果前面使用distinct 关键字可以去掉重复
```
select distinct stu1.stu_id, stu1.stu_name
from t_students stu1, t_score sc1
where stu1.stu_id=sc1.stu_id
and stu1.stu_id!=1
and sc1.course_id in(
	select sc.course_id
	from t_students stu, t_score sc
	where stu.stu_id=sc.stu_id
	and stu.stu_id=1
);
```
## ==向结果集中添加一列序列号==
### 设置变量
- @rowNO:= @rowNo+1 一定有@符号,等号一定是 := 名字可以随便起
- (select @rowNo:=0) as it :在查询表的后面添加一个这个表,用来设置序列号的初始值
### SET 设置变量的方式
- set @变量名=初始值
```
(select (@rowNO:= @rowNo+1) '排名',cou.course_name '语文成绩', sc.score
from t_courses cou, t_score sc,(select @rowNo:=0) as it
```
## 链接查询
### 交叉链接
- 其结果集中不会显示结果为null的数据
- cross join: 标准的sql语句
- MySQL中独有的的交叉连接: cross join on ... where...
```
select stu.stu_name,sc.score from t_students stu
cross join t_score sc on stu.stu_id=sc.stu_id where sc.course_id=2;
```

- on 跟的是连接条件,where 跟的筛选条件
### ==内连接: 常用==
- 其结果集中不会显示结果为null的数据
- inner join...on...join...on...
```
select stu.stu_name,sc.score from t_students stu
inner join t_score sc on stu.stu_id=sc.stu_id where sc.course_id=2;
```
### 外链接
#### 左外链接
```
select stu.stu_name,sc.score from t_students stu
left join t_score sc on stu.stu_id=sc.stu_id and sc.course_id=2;
```
- 表1 left join 表2...on ...and ...where left join 表3 ...on.......
  * 把left左边的表当做主表,主表中的信息最终都会显示在结果集中
  * 最终的结果集会根据on后面的条件进行筛选和显示
#### 右外链接
- 表1 right join 表2 ...on ...and ... where...right join 表3 on...
  * 把right右边的表当做主表,主表中的信息最终都会显示在结果集中
  * 最终的结果集会根据on后面的条件进行筛选和显示
## ==全链接==
- MySQL不支持全连接
- 但是可以把左链接和右链接的结果集取并集,其结果集就相当与全连接
```
select ch.child_id,ch.chile_name,fa.father_name
from t_children ch
left join t_father fa on ch.father_id=fa.father_id
union
select ch.child_id,ch.chile_name,fa.father_name
from t_children ch
right join t_father fa on ch.father_id=fa.father_id;
```

## ==union 和union all的区别==
- 它们都可以把两条sql语句链接在一起,显示成一个结果集
- union: 是把两个结果集的重复部分去掉,显示不同的部分
- union all: 把两个结果集中的数据都显示出来
## 自然连接
### 自然内连接
- 表1 natural join 表2;
### 自然做外链接
- 表1 natural left join 表2
### 自然右外链接
- 表1 natural right join 表2
- 自然链接会自动加上on后面的条件
- 但on后面的条件的表之间的列名必须相同,否则不能使用自然连接
### on where having的区别
- on: 在连接查询时的筛选条件关键字
- 使用的条件必须是真实存在的列
- where: 对查询结果进行条件筛选
- 使用的条件必须是真实存在的
- 在where之前的结果集是储存在mysql自己的内存中的
- where之后的结果集才真正存在于内存中
- having: 在内存中进行条件筛选,可以使用列的别名当做筛选条件
# ==子查询==
### 在一个SQL语句中使用另一个SQL语句
- 在子查询中使用in条件时,in后面的条件必须是一列
## 子查询的应用
### 可以用作条件
```
select stu.stu_name,cou.course_name,sc.score
from t_students stu
left join t_score sc on stu.stu_id=sc.stu_id
left join t_courses cou on cou.course_id=sc.course_id
where sc.score>(
	select
	score
from t_score where stu_id=4 and course_id=1);
```
### 可以当做一个临时表
- 这个临时表必须起别名
```
select stu.stu_name,temp.course_name,temp.score
from t_students stu
left join (
	select sc.stu_id,cou.course_name,sc.score
	from t_score sc
	left join t_courses cou
	on sc.course_id=cou.course_id
) temp on temp.stu_id=stu.stu_id;
```

# 外键
### 表和表之间的关系
- 一对一的关系:一个a对应一个b,一个b对应一个a
- 多对多的关系:a对应多个b,b对应多个a
- 一对多的关系:a对应多个b,b对应一个a
## 外键
- 在物理上表示表与表之间的关联关系的一个桥梁
### ==子表和父表==
- ==表中有外键的表叫子表==
- 关联的表叫父表
### 可以设置外键的前提
- 数据库引擎必须是InnoDB
- 子表的外键列与父表的主键列的数据类型统一
- 主键和外键数据类型必须一致
- 当前表中的数据是符合外键的关联关系的
### ==设置外键的的SQL语句==
- 本质上是在修改一张表
```
alter table 表名 add
[constraint '外键名字'](本行可以省略)
foreign key ('需要添加外键的列')
references '需要关联的表(关联的列)'
```
- ==外键名字的规范:==
  * 源表_目标表_列_fk
#### ==创建外键格式==
```
alter table t_courses  add
-- 给外键取名
constraint t_courses_t_teachers_teacher_id_fk
-- 给哪列添加外键
foreign key (teacher_id)
-- 关联哪个表的哪一列
references t_teachers(teacher_id);
# 外表取名规则
# 原表_目标表_列名_fk
```
## ==外键约束==
- ==它指的是修改或删除父表的某条数据时对子表产生不同的影响==
- 在设置外键的时候,可以分别给更新和删除设置约束
### 约束方式
#### ==restrict 限制约束==
- 不允许删除或更新父表中的外键关联的那列数据
- 主外键关联的情况下,不可以删除和更新主表中的主键中的数据
- 主外键关联的情况下,可以删除子表中的数据
### ==cascade 级联约束==
- 父表更新数据的同时,子表也跟着更新
### ==set null 置空==
- 删除或更新父表中的数据的时候,子表对应的外键列数据变为null
- ==使用set null 的前提是,子表的外键列可以为null==
### no action = reatrict 标准的默认约束
### set default MySQL中不可以使用
### ==外键约束:可以分别给更新和删除设置约束==
#### ==外键是不可以更改的,如果想改则需要删除现有的外键然后重新设置外键==
## ==设置外键约束的语法==
```
alter table 表名 add
[constraint '外键名字'](本行可以省略)
foreign key ('需要添加外键的列')
references '需要关联的表(关联的列)'
on delete 外键约束类型1
on update 外键约束类型2
```
### 比较常用的约束形式:
```
on delete set null
on update cascade
```
### ==删除外键==
- 外键不可以更改
```
alter table 表名 drop foreign key 外键名字;
```
### ==外键关联之后多表联查效率高==
