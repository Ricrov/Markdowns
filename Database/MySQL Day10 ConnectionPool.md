## 常用的处理异常的方式
1. 在catch块中手动抛出异常

```java
try {
    Connection conn = DriverManager.getConnection(url, user, password);
    pool.add(conn);
} catch (SQLException e) {
    e.printStackTrace();
    throw new RuntimeException("数据库获取连接失败");
}
```
2. 返回一个空的对象回去

```java
public static <T> List<T> findAll(String sql, Class<T> clazz, Object... params) {
        try {
     Connection conn = cs.get();
     QueryRunner r = new QueryRunner();
     List<T> list = r.query(
                conn,
                sql,
                new BeanListHandler<>(clazz, new UnderLineProcessor()),
                    params);
            return list;
        } catch (SQLException e) {
            e.printStackTrace();
        }
        "为了防止其他人调用该方法会获取到空对象,返回一个0 size的集合";
        return new ArrayList<T>();
    }

```
## 数据库连接池
1. 应用程序直接获取链接连接数据库
#### 缺点
```
"用户每次请求都需要向数据库获得连接,
"而数据库创建连接通常需要消耗较大的资源"
"创建时间也较长,假设访问量过大,
"那么需要创建很多的连接,极大的浪费了数据库资源";
```
2. 应用程序使用连接池获取链接之后连接数据库
```
"数据库连接池是程序启动时建立足够的数据库连接";
"并将这些连接组成一个连接池";
"由程序动态的对池中的连接进行申请,使用和释放";
"创建数据库连接是一个很耗时的操作";
"也容易对数据库造成安全隐患";
"所以在程序初始化的时候,集中创建多个数据库连接";
"并把他们集中管理,供程序使用";
"可以保证较快的数据库读写速度,更加安全可靠";
```

## 数据库连接池的运行机制
1. 程序初始化时创建连接池
2. 使用时向连接池申请可用连接
3. 使用完毕,将连接返还给连接池
4. 程序退出时,断开所有连接,并释放资源

## 一些常用的获取连接池的jar包
## DBCP jar包
### 核心工具类 BasicDataSource
1. 需要在系统下增加两个jar包
    * Commons-dbcp.jar: 连接池的实现
    * Commons-pool.jar: 连接池实现依赖的jar包

2. 使用硬编码方式获取连接池

```java
"DBCP连接池核心类";
BasicDataSource dataSouce = new BasicDataSource();
"连接池参数配置：初始化连接数、最大连接数 / 连接字符串、驱动、用户、密码";
dataSouce.setUrl("jdbc:mysql:///jdbc_demo"); // 数据库连接字符串
dataSouce.setDriverClassName("com.mysql.jdbc.Driver");  //数据库驱动
dataSouce.setUsername("root");//数据库连接用户
dataSouce.setPassword("root");//数据库连接密码
dataSouce.setInitialSize(3);// 初始化连接
dataSouce.setMaxActive(6);// 最大连接
dataSouce.setMaxIdle(3000);// 最大空闲时间
```
3. 使用配置文件获取连接池

```java
"配置文件中存储的是键值对";
// 加载prop配置文件
Properties prop = new Properties();
// 获取文件流
InputStream inStream = App_DBCP.class.getResourceAsStream("db.properties");
// 加载属性配置文件
prop.load(inStream);
prop.load(new FileReader("文件路径"))
// 根据prop配置，直接创建数据源对象
DataSource dataSouce = BasicDataSourceFactory.createDataSource(prop);

// 获取连接
Connection con = dataSouce.getConnection();

```
## C3P0数据源
### 核心工具类 ComboPooledDataSource
1. 使用硬编码方式获取连接池

```java
// 创建连接池核心工具类
ComboPooledDataSource dataSource = new ComboPooledDataSource();
// 设置连接参数：url、驱动、用户密码、初始连接数、最大连接数
dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/jdbc_demo");
dataSource.setDriverClass("com.mysql.jdbc.Driver");
dataSource.setUser("root");
dataSource.setPassword("root");
// 设置初始连接数
dataSource.setInitialPoolSize(3);
// 设置最多连接数
dataSource.setMaxPoolSize(6);
// 设置最大存活时间
dataSource.setMaxIdleTime(1000);

// ---> 从连接池对象中，获取连接对象
Connection con = dataSource.getConnection();
```
2. 使用xml文件获取连接池

```xml
"xml文件格式要求";
<c3p0-config>
     <default-config>
          <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbc_demo
</property>
         <property name="driverClass">com.mysql.jdbc.Driver</property>
            <property name="user">root</property>
            <property name="password">root</property>
            <property name="initialPoolSize">3</property>
            <property name="maxPoolSize">6</property>
            <property name="maxIdleTime">1000</property>
      </default-config>
</c3p0-config>
"创建c3p0连接池核心工具类";
"自动加载src下c3p0的配置文件【c3p0-config.xml】";
ComboPooledDataSource dataSource = new ComboPooledDataSource();// 使用默认的配置

// 获取连接
Connection con = dataSource.getConnection();

```
