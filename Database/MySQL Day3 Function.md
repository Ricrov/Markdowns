## 将sql文件导入到终端中
- 第一步: 进入mysql状态
- 第二步: 使用source + 文件地址
## 备份数据库
- 第一步: 在终端回到最初状态
- mysqldump -uroot -proot --all-databases > ~/Documents/all.sql
- mysqldump -uroot -proot --databases db1 db2 > /tmp/user.sql
- mysqldump -uroot -proot --databases db1 --tables a1 a2 > /tmp/db1.sql
# MySQL函数
## BIT FUNCTIONS 位运算符
- &: 与
- <<: 左移
- (>>): 右移
- ^: 与
- |: 或
- ~: 取反
## comparison operators 比较函数
- between A and B :在A和B之间,A一定比B小,包括A和B
- coalesce(null,1,2,3):返回第一个不为null的数
- greatest(1,2,3):返回最大的数
- least(1,2,3,4,5): 返回最小的数
- ISNULL(exp): 判断exp是不是NULL的函数
- is
- is null
- is not null
- <=>: 判断两个数是否相等,可以比较null
- =: 判断两个数是否相等,不可以比较null
- not between a and b:不在a和b之间
- not in
## control flow functions
流程控制函数
- case operator:相当于switch case
  * case既可以当做case用
  * case 列名 when 条件 then 满足条件时执行的结果 when 条件2 then 执行的结果 ... else 执行的结果 end;
- if函数:
- select if(1>2,2,3):括号里填三目运算符
- ifnull
  * select ifnull(1,0):返回1
  * select ifnull(null,1):返回1
  * 如果第一个数为null返回第二个数,否则返回第一个数
- date and time 日期和时间类函数
- now:返回当前时间,常用
  * select now()
- Encryption Functions 加密
- md5()
  * select md5
- Logical operators 逻辑函数
  * and
  * or
  * !
  * xor: 异或
- Numeric Functions 数学函数
  * round(10.34567,3):保留3位小数
- String Functions 字符串函数
  * CHAR_LENGTH("中国"):返回字符串长度
  * BIN(10):转换成二进制
  * HEX(10):转换成十六进制
  * OCT(10):转换成8进制
  * CONCAT('a','c','d','e','f'...):连接字符串
  * CONVERT():字符串翻转
  * TRIM():去掉前后空格
  * UPPER():转换大写
  * LOWER():转换小写
### ==GROUP BY 分组==
##### ==GROUP BY关键字可以将查询结果按照某个字段或多个字段进行分组。字段中值相等的为一组==
- 分组之后的字段中值相等的会合并在一起,比如同一个学生学习多门课程,那么可以通过分组把学生学过的课,和学生信息显示在同一行
- ==分组常和GROUP_CONCAT()函数一起使用==
- ==按照谁分组了,那么该列中只能有谁==
```
select
stu_id,count(course_id),round(avg(score),2)
from t_score
group by stu_id;
```
- 可以使用分组函数向分好组的表中加内容
- where stu.stu_id = sc.stu_id
group by sc.stu_id
having score_sum > 100
order by score_sum desc;
  * ==如果在分完组之后还有条件使用having==
- ==group_concat(列名): 把字符串分开显示==
- ==group_concat(distinct 列名): 去掉重复的部分==
- 也可以分多列
### 分组函数
- COUNT():计算数量
- AVG():算平均分
- MAX():求最大债
- MIN():求最小值
- SUM():求和
## ==SELECT语句基本格式==
#### select * from 表 where 条件 group by 列名 having 条件 order by 列名 limit num1, num2
