## SQL基本语句
- create database 数据库名:创建数据库
- show create database 数据库名字:查看数据库是怎么创建的 - show databases:查看所有数据库
- use 数据库名称:使用数据库:
- create table[if not exists] 表名(tb_persons 建表的时候最好加前缀):创建表格
- 创建表: create table 表名(列名1 数据类型 约束,列名2 数据类型 约束...)
  * unique :唯一约束
  * not null: 非空约束
- ==show create table 表名: 查看创建表的过程==
- ==desc 表名:查看表的结构==
- ==删除表:drop table[if exists] 表名;==
- ==删除数据库:drop database 数据库名;==
- help data types:查看所有数据类型
## SQL中的数据类型
- 数据类型的格式: 数据类型[(M)] 中括号的部分代表可以不写
- show tables;查看当前数据库有哪些表
- insert into 表名 values (值1,值2...) 括号里的内容根据列的数量按顺序写入值
- select*from 表名:查询表名
### 表中的数据是从第0行开始计算的
### 数值类型
- BIT[(M)] :M表示最大的'位'数,M的取值范围1~64;
- TINYINT 8位1个字节 相当于byte
- SMALLINT 16位两个字节 short
- MEDIUMINT 24位3个字节
- INT or INTEGER 4个字节 int
- BIGINT 8个字节 long
- 从TINYINT 到 BIGINT 也有[(M)] 如 INT[(M)] [UNSIGNED] [ZEROFILL]
  * UNSIGNED表示无符号,该列的数据只能是整数
  * ZEROFILL:如果插入的数据长度小于M那么其他位置会用0填充
  * ==如果指定了ZEROFILL那么会自动加上UNSIGNED==
  * ==M的意义与BIT中的不一样,只是决定显示的长度,不指定数的范围==
- BOOL or BOOLEAN 布尔类型
  * ==MySQL中的true就等于1==
  * ==MySQL中的false就等于0==
  * 但如果把一个值放在条件语句中(if语句),只要不填0,都是true,如果填0的话为false
- FLOAT[(M,D)] [UNSIGNED] [ZEROFILL]
  * M表示这个类型的最多位数,D表示小数位 unsigned 和zerofill和上面的一样
- DOUBLE 和float一样
- ==DECIMAL[(M[,D])] or DEC[(M[, D])],M为总位数, D为小数位 :存储的小数是按字符串存储的==
### 字符串类型
- CHAR[(M)], 0 ~ 255, 后面的空格不会被保留
- VARCHAR[(M)], 0 ~ 65535, 后面的空格会被保留
- BINARY, 0 ~ 255, 补默认字符
- VARBINARY
- TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT
- TYNYBLOB, BLOB, MEDIOMBLOB, LONGBLOB
  * BLOB可以存二进制
### 时间类
- DATE:存年月日的
- DATATIME:存年月日时分秒的
- TIMESTAMP:时间戳
- TIME:时间
- YEAR:年
