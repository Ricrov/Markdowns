## JDBC四种驱动类型
## JDBC连接数据库步骤
### 1.加载驱动
```java
String driver="com.mysql.jdbc.Driver";
"第一种方式"
Class.forName(driver);
"第二种方式"
DriverManager.registerDriver(new Driver());
"第二种方式的缺点:
1.内存中存在两个驱动对象
2.程序耦合性增大了"
```
### 2.获取数据库连接
```java
String url = "jdbc:mysql://localhost:3306/数据库名称";
String user = "数据库用户名";
String password = "数据库密码";
Connection conn = DriverManager.getConnection(
String url, String user, String password);
"Connection中的常用方法:";
"用来设置事务是否自动提交,默认值为true 为true的情况下每条SQL语句都在一个事务中
    conn.setAutoCommit(false);
       提交事务
    conn.commit();
        回滚事务
    conn.rollback();
       设置事务保存点
    conn.setSavepoint();";
```
### 3.获取Statement对象
#### 获取Statement对象
```java
Statement sta = conn.createStatement(sql);
"有三种方法:它们的共同点都是执行SQL语句";
"不同点:";
"execute()方法:可以执行任何SQL语句,返回boolean类型,常用来执行DDL";
"executeUpdate()方法:只能用来执行DML语句,insert delete update,返回值为int类型,代表影响了多少条数据";
"executeQuery()方法:只能执行DQL语句,select,返回结果集对象:重点掌握";
```
#### 获取preparedStatement对象
```java
PreparedStatement ps = conn.preparedStatement(sql);
"它可以预编译SQL语句,条件参数最好用问号代替,有几个参数就写几个问号";
"如:String sql = "select * from t_students where stu_name like ?"";
"通过set方法给问号设置值,使用方法是:给第几个问号设置值为什么";
"有几个问号就必须给每个问号设置值";
"它可以防止用户注入,导致数据被异常处理等等";
```
### 4.获取结果集并处理
```java
"获取结果集";
ResultSet set = sta.executeQuery();
"获取到的结果集有一个游标的概念";
"游标的初始位置在第一行数据的最前面";
"可以使用set.next()方法获取下一行的数据";
"通过set.get...()方法可以获取到某一行的所对应的列的数据";
while(set.next()) {
    "方法中的参数为列的下标或列名字";
    int id = set.getInt(1);
    String name = set.getString(2);
    System.out.println(id + "  " + name);
        }
"结果集中的其他方法:";
"   获取当前行的前一行数据
    set.previous();
    定位到指定行
    set.absolute(int row);
    定位结果集中的最后一行
    set.afterLast();
    定位到结果集中的开始位置
    set.beforeFirst();";
```
### 5.关掉所有链接
