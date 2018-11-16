## DML：SQL插入数据
- 对表中的数据进行增、删、该
- select*from 表名：查询表中的所有数剧
### 插入操作
- insert into 表名（列名1，列名2...列名N）values(列值1，列值2...列值N)，（列值1，列值2...列值N）  values的值与列名相对应，如果想添加多条记录，在插入第一条值之后，用逗号隔开，然后继续写值即可
### 更新操作
- update 表名 set 列名1=列值1，列名2=列值2...where 列名=值，如果需要指定条件需要用到where
- 修改数据库密码
### 删除操作
- delete from 表名 where 列名=值； ：删除列名=值的数据，这个方法删除的数据可以找回
- truncate table 表名；只能删除所有数据，删除之后表中的数据不能找回，原来的表被删除之后，会创建一个新的表，它的执行速度比delete快
- delete from 表名；删除表中的所有数据，词语句执行后，表的结构还在，表中的数据可以找回


# DQL取出数据
- select*from 表名:查询所有列
- 结果集:
  * 数据库执行DQL语句不会对数据进行改变,而是让数据库发送结果集给客户端
  * 通过查询语句查询出来的数据已表的形式展示给我们称这个表为虚拟结果集,存放在内存中,下次进入的时候结果集会消失
  * 查询返回的结果是一张虚拟表,储存在内存中,不会对原表进行改变
- select 列名1,列名2...from 表名:查询指定的列
- 条件查询:就是在查询时给出where字句,在where字句中可以使用一些运算符及关键字
- select * from 表名 where字句
- 条件查询运算符及关键字:
  * = > < != (<>不等于) <= >=
  * between..and...;值在什么范围
  * IN(set);查询固定的范围值,set表示需要固定的范围值
  * is null; is not null
  * and: 与
  * or:或
  * not:非
- 模糊查询:根据指定关键字查询
- 模糊查询:使用Like关键字后跟通配符
- 模糊查询:select*from 表名 where 列名 like '通配符'(通配符写在单引号里面)
- 通配符有:
  * 下划线(_) :任意一个字母(单个字符)
  * 百分号% : 任意0到n个字母(0到任意多个字符)
## SQL字段控制查询
- 去除重复记录:select distinct 列名 from 表名:把列名中相同的元素名去重复
- 把字段结果进行运算,生成一个新的表:select *,age+score from 表名:把表中的age和score相加后的结果添加到原表中并展示出来,但原表不变,只展示结果集
- 如果某个字段为空,那么在进行运算时可以加个判断如:
- select*,ifnull(age,0) + ifnull(score,0) from 表名;
- 对查询结果取别名:如
- select*,ifnull(age,0) + ifnull(score,0) as 新生成的列名 from 表名
## 排序
- 关键字:order by
- 用法:select*from 表名 order by 列名 排序规则
- 排序规则:DESC降序,ASC升序(默认情况也是按升序排)
## SQL聚合函数
- 对查询结果进行统计计算
### 常用聚合函数:
- count():统计指定列不为null的记录行数
- select count(*) from 表名，括号里填写的是要统计的名称
- max():计算指定列的最大值,如果指定列为字符串类型,那么使用字符串排序运算
- select max（需要查询的字段） from 表名
- min():计算指定列的最小值,如果指定列为字符串类型,那么使用字符串排序运算
- select min(需要查询的字段) from 表名
- sum():计算指定列的数值和,如果指定列不是数值类型,那么结果为0
- select sum(需要求和的字段) from 表名
- avg():计算指定列的平均值,如果指定列不是数值类型,那么结果为0
- select avg（需要求平均数的字段） from 表名
# SQL分组查询
- 基本概念：将查询的结果按照1个或多个字段进行分组，字段相同的为一组
- 分组使用：
  * select gender（字段名称），group_concat(字段名称) from 表名 group by gender（字段名称）;表示根据gender分组，并且根据concat（）括号里面的字段把分组后的具体内容也列出来
  * 根据gender字段来分组，gender字段的全部值只有两个（男和女），所以分为两组
  * 当group by 单独使用时，只显示每组的第一条记录
  * 所以group by单独使用时意义不大
- 分组注意事项：select 后面跟的字段一般都会在group by 后面也会出现
- group by + group_concat():group_concat(字段名)可以作为一个输出字段来使用，表示分组之后，根据分组的结果，使用 它来设置每一组的某字段的值得集合，如
- select gender,group_concat(name) from employee group by gender;
- group by + 聚合函数，如
- select department,group_concat(salary),sum(salary) from employee group by department
### group by + having
- 用来分组查询后，指定一些条件来输出查询结果
- having作用和where一样，但having只适用于group by
- having用在group by后面，而where用在group by 前面
- select department,group_concat(salary),sum(salary) from employee group by department having sum(salary) > 9000
- ==having 与where 的区别==
- having是在分组后对条件进行过滤
- where是在分组之前对数据过滤
- having 后面可以使用一些聚合函数
- where 后面不可以使用聚合函数
### SQL查询语句书写顺序
- select from where group by having order by limit
- 执行顺序：
- from where group by having select order by limit
### SQL分页查询思路limit使用
- 从哪一行开始查，一共要查多少条
- select * from 表名 limit 0,3:表示从第0条开始，取三条，表中的数据是从0开始的
- limit 参数1 参数2，
- 参数1：从哪一行开始查
- 参数2：一共查几条
### 分页思路：
