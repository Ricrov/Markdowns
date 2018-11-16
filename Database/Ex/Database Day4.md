# 表之间的关系
- 一对一:很少用
- 一对多的关系:一个对象可以有很多其他信息,比如一个人可以拥有多辆汽车,==可以通过添加外键的形式实现==
- 多对多的关系:比如:学生选课,一个学生可以选多门课程,每门课程可供多个学生选择;一个学生可以有多个老师,而一个老师也可以有多个学生
  * 例如:创建老师表
  * 创建学生表
  * 创建老师与学生的关系表
  * 添加外键:通过修改表的方式添加
## 创建多对多表关系
- 创建老师表:creat table teacher(tid int primary key, name varchar(50));
- 创建学生表:creat table student(sid int primary key, name varchar(50));
- 创建老师和学生的关系表:creat table tea_sut_rel(tid int, sid int);
- 添加外键:alter table tea_sut_rel add constraint foreign key(tid) references teacher(tid);
- alter table tea_sut_rel add constraint foreign key(sid) references student(sid);
## 为什么拆分表
- 为了避免大量冗余数据的出现
## 多表查询(查询数据时不需要建立主外键关系)
- 合并结果集
  * 就是把两个select语句的查询结果合并在一起
  * 合并结果集的两种方式
    * union :合并时去除重复记录
    * union all: 合并时不去除重复记录
  * 格式:在两个select语句中间用union或union all链接在一起
    * union:
      * select*from 表1 union select * from 表2;
      * select*from 表1 union all select* from 表2;
  * 注意事项:被合并的两个结果:列数,列类型必须相同
- 链接查询
  * 它可以叫跨表查询,需要关联多个表进行查询
  * 什么是笛卡尔积:
    * 假设集合A={a,b}, 集合B={0,1,2}
    * 则两个集合的笛卡尔积为{(a,0),(a,1),(a,2),(b,0),(b,1),(b,2)}
    * 它可以拓展的多个集合的情况
  * 使用一条查询语句,同时查询两个表,会出现笛卡尔积的情况
  * 查询表时给表起别名:select*from 表1 别名,表2 别名;
  * ==如何保证多表联查的数据正确性==
    * 在查询时要把主键和外键保持一致
      * select*from 表名1 别名1,表名2 别名2 where 别名1.被关联的字段名 = 别名2.关联的字段名
    * 主表当中的数据参照子表中的数据
  * 根据链接方式分类
   * 内连接 INNER JOIN ...ON 其中INNER可以省略
     * 等值链接:
       * 两个表同时出现的值才显示
       * select*from 表1 ==inner join== 表2 ==on== 表1.被关联的字段=表2.关联的字段 where 条件1 and 条件2 ;==其中inner可以省略不写==
       * on后面只写主外键(条件)
       * 如果还有条件直接在后面写where
       * 多表联查后面还有条件就直接写and
     * 多表连接(多对多关系):
       * 99查询:select*from 表1,表2 where 条件1 and条件2
       * 内连接查询:select*from 表1 INNER JOIN 表2 ON 条件1 JOIN 表3 ON 条件2;先连接两个然后再连接下一个
     * 非等值链接
       * 使用方法同等值查询,只需要改变等值查询中的等号为其他符号即可,如大于号,小于号等等
     * 自链接
   * 外链接
     * 左链接(左外链接): 两个表满足条件都查出来,把不满足条件的左边的表的数据也查出来,用法跟内连接查询一样,只是把INNER JOIN 关键字改为==LEFT OUTER JOIN==即可,其中OUTER可以省略
     * 右链接(右外链接):用法同左外链接,只把关键字改为RIGHT OUTER JOIN即可,其中OUTER可以省略
   * 自然链接
     * 无需给出主外键等式,它会自动找到这一等式
     * 也就是说不用去写条件
     * 要求:
       * 两张连接的表中列名称和类型完全一致的列作为条件
       * ==它会去除相同的列,如果同时存在多个列相同,那么两个表中的相同的列必须完全相同,才能查询出来==
       * 它会去除笛卡尔积现象
       * 语法:
         * select*from 表1 natural JOIN 表2;
- 子查询
- 自链接
