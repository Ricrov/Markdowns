# 常用函数
- 事先提供好的一些功能可以直接使用
- 函数可以在select语句及其字句中
- 也可以用在update,delete语句当中
## 函数分类
- 字符串函数
- 数值函数
- 日期和时间函数
- 流程函数
- 其他函数
### 字符串函数
- concat(s1,s2,...sn) :将传入的字符串连接成一个字符串
- 任何字符串和null进行连接之后值永远为null
- insert(str,x,y,str1):将str从x位置开始,将长度为y的字符子串替换为str1
- lower(str),upper(str):转换大小写
- left(str,x)和right(str,x):返回从左/右边开始返回x长度的字符,如果没有x则什么也不返还
- lpad(str,x,pad)和rpad(str,x,pad): 表示从str左边或右边填充字符,新字符长度不能大于x的长度,pad为填充的字符
- ltrim(str)和rtrim(str):去掉左边或右边的空格
- trim(str):去掉空格
- repeat(str,count);把字符串str拼接count次
- replace(str,a,b):把字符串中的a替换为b
- substring(str,x,y):返回从x位置开始,长度为y的子串
### 数值函数
- abs(x):求绝对值
- ceil(x):向上取整数,如1.1,取整后为2
- floor(x):向下取整,如2.3,取整后为2
- mod(x,y):求x/y的余数:
- rand():产生[0,1]的随机小数
### 日期时间函数
- crudate()
- curtime()
- now()
- week()
- date_format(date,fmt)
- date_add(date,interval(固定) expr(数字) type(日期格式)):对日期相加减
- date_diff(date1, date2):计算两个日期之间的间隔
### 流程函数
- if(value,t,f):如果value的值为true则返回t否则返回f
- ifnull(value1,value2):如果不为空返回value1,否则返回value2
### 其他常用函数
- database():返回当前数据库名
- version():返回当前版本号
- user():返回当前用户
- password(str):加密
- MD5():加密
