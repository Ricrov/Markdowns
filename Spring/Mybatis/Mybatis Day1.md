## MyBatis

#### 依赖包
- MySQL Connector/J
- MyBatis
- Apache Log4j

```xml
<dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.47</version>
</dependency>

<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>

<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

#### Log4j
- resources 标记为 Resources Root
- new resources bundle -> log4j
- log4j.properties 写入配置文件

```
### 设置 ###
# 重要程度从高到低 error warn info debug
# 若写成 warn 则只打印出 error 和 warn, 忽略 info 和 debug
log4j.rootLogger = debug, stdout, D


### 输出信息到控制台 ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d %l - %m%n

### 输出到文件 ###
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = E:/IdeaProjects/mybatis/logs/log.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = DEBUG
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = [%-5p] %d %l - %m%n

# [%-5p] 代表此位置占5个字符, 不足用空白补齐, 可保证日志的可观性和可操作性
# %l 代表日志在第几行
# %m 代表 Message
# %n 代表换行符
```

- 创建测试文件夹
    - src.test.java.com.ric.demo.LoggerTest
- 测试日志
```java
@Test
    public void logTest() {
        Logger logger = Logger.getLogger(Logger.class);
        logger.error("Error 日志");
        logger.warn("Warn 日志");
        logger.info("Info 日志");
        logger.debug("Debug 日志");
    }
```

### 什么是 MyBatis ？
> MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。

#### resources 下配置 mybatis-config.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!-- default 默认加载的环境 -->
    <environments default="dev">
        <environment id="dev">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/db_mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>

    </environments>

    <mappers>
        <!-- 配置 PersonMapper.xml -->
        <mapper resource="PersonMapper.xml"/>
    </mappers>

</configuration>
```

#### resources 下配置映射文件
PersonMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- 设置命名空间 -->
<mapper namespace="person">
    <!-- 查 -->
    <!-- select 标签即是一个 statement -->
    <!-- resultType 代表查出的是什么类型的: <包名+类名> -->
    <select id="findAll" resultType="com.ric.demo.entity.Person">
        SELECT * FROM tb_person
    </select>
</mapper>
```


#### 测试类
- 注意: 项目中需使用单例或静态单例创建 session 工厂


```java
public class MybatisTest {

    private SqlSessionFactory sessionFactory;

    @Before
    public void init() throws IOException {
        // 获取 SqlSessionFactory 对象
        InputStream in = Resources.getResourceAsStream("mybatis-config.xml");
        sessionFactory = new SqlSessionFactoryBuilder().build(in);

    }

    @Test
    public void test() {
        // 从 session 工厂出取出 session
        SqlSession session = sessionFactory.openSession();
        // 执行在 Mapper 配置文件中定义的某个 statement <namespace.id>
        List<Person> persons = session.selectList("person.findAll");
        persons.forEach(System.out::println);

    }

}

```

- 创建实体类 Person
- 在 PersonMapper.xml 中注入 resultType
- 在 mybatis-config.xml 中映射 PersonMapper.xml


#### 传参

- 传参单条查询

    > parameterType 参数可以省略, 自动识别参数类型

    > 当向 parameter 传递参数时, 只能传入<一个>对象.
    当传递的对象为数据库的基本数据类型时, SQL 中的参数名字可以随便写.
    当传递的对象为 Map 或实体类时, SQL 中的参数名必须与 Map 的 key 或实体类的属性名保持一致

- 配置 xml

```xml
    <select id="findOne" resultType="com.ric.demo.entity.Person">
        SELECT * FROM tb_person WHERE id = #{id} AND name LIKE #{name}
    </select>
```

- 查询

```java
@Test
public void selectOne() {
    SqlSession session = sessionFactory.openSession();

    // 传入 Map
    Map<String, Object> params = new HashMap<>();
    params.put("id", 1);
    params.put("name", "曹操");

    // 传入实体类
    Person params = new Person();
    params.setId(2);
    params.setName("刘备");

    Person p = session.selectOne("person.findOne", params);
    System.out.println(p);
}
```

#### 列名和实体类名不吻合的情况
- Mapper 映射里可以书写 <resultMap> 配置来使之吻合

- PersonMapper 配置
    - 查询结果映射 (sql 列名和实体类名不符时)
    - id 特殊, 其他属性都可以使用 <result>

```xml
<resultMap id="personMap" type="com.ric.demo.entity.Person">
    <id column="person_id" property="id"/>
    <result column="person_name" property="name"/>
</resultMap>


<select id="findAll" resultMap="personMap">
    SELECT * FROM tb_person
</select>


<!-- #{多参数时此处必须和实体类属性名吻合} -->
<select id="findOne" resultMap="personMap">
    SELECT * FROM tb_person WHERE person_id = #{id} AND person_name LIKE #{name}
</select>

```
#### 插入语句
- 由于默认情况下自动提交被设置为 false, 所以插入语句需要手动 commit
- openSession() 方法的参数可以填写 true 以达成 Autocommit

```xml
<!-- 插入数据 -->
<insert id="addOne">
    INSERT INTO tb_person (person_name, age) VALUE (#{name}, #{age})
</insert>
```

```java
@Test
public void addOne() {
    // openSession() 参数为 true 时代表自动提交
    SqlSession session = sessionFactory.openSession(true);
    Person params = new Person();
    params.setName("郭嘉");
    params.setAge(19);
    int i = session.insert("person.addOne", params);
    System.out.println(i);
}
```

#### Mapper映射文件 - 接口
因需保持编译后接口和 xml 在同一目录下, 因此:
resources 下创建 com.ric.demo.mapper 文件夹, 将 PersonMapper.xml 放入其中

- 更改新 xml 的命名空间为
    - `<mapper namespace="com.ric.demo.mapper.PersonMapper">`

- mybatis-config 配置文件中配置新的 PersonMapper.xml 有两种方法:

    1. `<mapper resource="com/ric/demo/mapper/PersonMapper.xml"/>`
    2. `<mapper class="com.ric.demo.mapper.PersonMapper"/>`

- 解释:
    1. mapper 配置的是 resource 属性
       mybatis 框架会根据该配置文件的 namespace 寻找对应的接口.
    2. mapper 配置的是 class 属性
       mybatis 框架会根据该类所在的包寻找与该类同名的映射文件.

> ++==实际开发中, 我们会把接口和 mapper 文件放在同一文件夹下(部署后), 并保持名称一致.
  在 mapper 配置中, namespace 属性也会写成接口的全限定名.==++

> ==这样在部署时, mapper 映射文件和 Mapper 接口就可以生成在同一文件夹下
> (效果见底部布局图)==


- 创建 PersonMapper 接口测试类

```java

private SqlSessionFactory sessionFactory;

@Before
public void init() throws IOException {
    // 获取 SqlSessionFactory 对象
    InputStream in = Resources.getResourceAsStream("mybatis-config.xml");
    sessionFactory = new SqlSessionFactoryBuilder().build(in);
}


@Test
public void test() {
    SqlSession session = sessionFactory.openSession();
    // 动态代理创建实现类
    PersonMapper personMapper = session.getMapper(PersonMapper.class);
    List<Person> personList = personMapper.findAll();
    personList.forEach(System.out::println);
    session.close();
}
```


- 更新数据
```java
@Test
public void updateTest() {
    SqlSession session = sessionFactory.openSession();
    PersonMapper personMapper = session.getMapper(PersonMapper.class);
    Person p = new Person();
    p.setId(1);
    p.setName("新人");
    p.setAge(38);
    int i = personMapper.updateOne(p);
    session.close();
}
```





- 更新数据
```java
@Test
public void updateTest() {
    SqlSession session = sessionFactory.openSession();
    PersonMapper personMapper = session.getMapper(PersonMapper.class);
    Person p = new Person();
    p.setId(1);
    p.setName("新人");
    p.setAge(38);
    int i = personMapper.updateOne(p);
    session.close();
}
```


- 插入数据并获取到主键

```xml
<!-- 插入数据 -->
    <!-- useGeneratedKeys 属性: 使用生成的键 -->
    <!-- keyColumn 属性: 哪个是主键 -->
    <!-- keyProperty 属性: 赋值给实体类的哪个属性 -->
    <insert id="addOne" useGeneratedKeys="true" keyColumn="person_id" keyProperty="id">
        INSERT INTO tb_person (person_name, age) VALUE (#{name}, #{age})
    </insert>
```

```java
@Test
    public void insertTest() {
        SqlSession session = sessionFactory.openSession(true);
        PersonMapper personMapper = session.getMapper(PersonMapper.class);
        Person newPerson = new Person();
        newPerson.setName("国子监");
        newPerson.setAge(88);

        personMapper.addOne(newPerson);

        System.out.println(newPerson);

    }
```


- 也可以自动赋值多个属性 (顺序一一对应)
```xml
    <insert id="addOne" useGeneratedKeys="true" keyColumn="person_id, gender, crt_time" keyProperty="id, gender, crtTime">
```


![image](https://s1.ax1x.com/2018/09/26/iMsk8I.png)


 - 附: 静态工厂新写法

```java
public class Singleton {

    private static class SingletonHolder {
        private static final SqlSessionFactory sessionFactory = new SqlSessionFactory();
    }

    private Singleton (){}

    public static final Singleton getFactory() {
        return SingletonHolder.sessionFactory;
    }

}
```
