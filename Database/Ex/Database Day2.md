# SQL功能划分
- 它是结构化查询语言
- 它是专门为数据库建立的操作命令集
- DDL：数据定义语言：用来定义数据库对象：创建库、表、列等等
- DML：数据操作语言：用来操作数据库表中的记录
- DQL：数据查询语言：用来查询数据
- DCL：数据控制语言：用来定义访问权限和安全级别
# SQL中的数据类型
- 定义的类型对数据库的优化是非常重要的
- 数据类型大致分为三类
- 数值型
- 字符串类型
- 日期和时间类型
- 数值型：
- YINYINT 小整数型
- INT 或INTEGER
- BIGINT
- FLOAT
- DOUBLE
- DECIMAL
- 字符串类型:
- CHAR
- VARCHAR
- TINYBLOB
- TINYTEXT
- BLOB
- TEXT
- LONGTEXT
- LONGBLOB等等
- 日期和时间类型
- DATE
- TIME
- YEAR
- DETETIME
- TIMESTAMP
# SQL常用数据类型
- double:浮点型 double（5，2）表示最多有5位，并且必须有两位小数，如999.99
- char 固定长度字符串类型:char(10)表示字符长度一定为10
- varchar:可变字符串类型：varchar(10)表示允许最多为10个字符
- text:字符串类型，存大量文本时使用
- blob：存二进制数据，很少用
- date：日期类型，格式为yyyy-MM-dd
- time:时间类型：格式为 hh:mm:ss
- datetime:日期时间类型:yyyy-MM-dd hh:mm:ss
### 在MySQL中，字符串类型和日期类型都要用单引号括起来，如''mysql' '2018-07-17'
# DDL：数据定义语言
- 创建数据库：creat  database 数据库名称 character set utf8
- 显示所有数据库：show database
- 使用数据库：use 数据库名称
- 创建表：必须先使用数据库creat table 表明（
-  列名1 数据类型 [约束]，
-  列名2 数据类型 [约束]，
-  。。。。
-  列名n 数据类型 [约束]
- ）==注意：最后一行没有逗号，不同的列之间用逗号隔开==
- 查看表：show tables;
- 在表中添加一列：alter table 表名 add 列名 数据类型
- 查看表的结构：desc 表名，查看表的结构
- 修改一个表的字段类型：alter table 表名 modify 字段 数据类型
- 删除表中的一列：alter table 表名 drop 列名（字段）；
- 修改表明：rename table 原始表明 to 要修改成为的表明
- 查看表的创建细节:show creat table 表名；表示查看表名的创建细节
- 修改字符集：alter table 表名 character set 新的字符集类型
- 修改表的列名：alter table 表名 change 原始列名名称 新列名名称 数据类型
- 删除表：drop table 表名