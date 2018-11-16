## Spring SpringMVC Mybatis

### 环境搭建
[DEMO](https://pan.baidu.com/s/1DXGV-J3IQUyPhLQdcUWxJg)

#### 依赖包列表 POM.xml
 - spring-webmvc <br>
 5.0.8.RELEASE
 - spring-test <br>
 5.0.8.RELEASE
 - spring-orm <br>
 5.0.8.RELEASE
 - mybatis-spring <br>
 1.3.2
 - jackson-databind <br>
 2.9.6
 - commons-fileupload <br>
 1.3.3
 - hibernate-validator <br>
 5.4.1.Final
 - aspectjweaver <br>
 1.9.1
 - javax.servlet-api <br>
 3.1.0
 - c3p0 <br>
 0.9.5.2
 - mysql-connector-java <br>
 5.1.47
 - mybatis <br>
 3.4.6
 - log4j <br>
 1.2.17

#### Spring 数据库连接配置文件
> 此配置文件配置后可于 spring-dao 层配置创建 c3p0 数据源对象时作为 value 引用 <br>
> 例: `<property name="driverClass" value="${db.driver}"/>`

- db.properties (path: src/main/resources/)

```properties
db.driver = com.mysql.jdbc.Driver
db.url = jdbc:mysql://localhost:3306/db_new_goods_store
db.username = root
db.password = root
```

#### Log4J 日志配置文件
> 此配置文件用于配置日志 Log 打印

- log4j.properties (path: src/main/resources/)

```properties
### 设置###
log4j.rootLogger = debug, stdout, D
### 输出信息到控制台 ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d %l - %m%n

### 输出到文件
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = E:/IdeaProjects/mybatis/logs/log.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = DEBUG
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = [%-5p] %d %l - %m%n
```

#### Mybatis 配置文件
> Mybatis 相关配置

- mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
        <!--打开二级缓存 并在 查询语句的 mapper.xml 下加入 <cache/> 标签 -->
        <setting name="cacheEnabled" value="true"/>

        <!-- 懒加载开关, 默认为 false. 若设置为 true, 关联的 SQL 会根据需要的时候才会进行查询 -->
        <setting name="lazyLoadingEnabled" value="true"/>

        <!--
        积极懒加载开关, 默认为 false. 若设置为 true, 则调用某个对象的任意方法时都会把它关联的其他 SQL 进行调用
        例如, 获取 goods 的名字属性时, 也会查询该商品的类别等信息
        -->
        <setting name="aggressiveLazyLoading" value="true"/>

        <!-- 下划线形式的列名与小驼峰形式的属性名自动映射, 默认为 false -->
        <!-- 自动映射适应类型: 单表查询, 一对一的多表查询, 多对一 -->
        <!-- 自动映射不适应类型: 一对多的多表查询 -->
        <setting name="mapUnderscoreToCamelCase" value="true"/>

        <setting name="autoMappingBehavior" value="FULL"/>

        <!-- 指定 MyBatis 所用日志的具体实现，未指定时将自动查找 -->
        <setting name="logImpl" value="LOG4J"/>

    </settings>

</configuration>
```

#### SpringMVC 配置文件

- spring-mvc.xml (path: src/main/resources/)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 自动扫描 -->
    <context:component-scan base-package="com.ric.demo.controller"/>

    <!-- 验证器配置 -->
    <bean class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean" id="validatorFactoryBean">
        <property name="providerClass" value="org.hibernate.validator.HibernateValidator"/>
    </bean>

    <!-- 加载注解驱动, 加载验证器 -->
    <mvc:annotation-driven validator="validatorFactoryBean"/>

    <!-- 静态页面 Servlet -->
    <mvc:default-servlet-handler/>

    <!-- ViewHandler -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- 文件上传配置 -->
    <bean class="org.springframework.web.multipart.commons.CommonsMultipartResolver" id="multipartResolver">
        <property name="maxUploadSize" value="1024000"/>
        <property name="defaultEncoding" value="UTF-8"/>
    </bean>

    <!-- TODO 统一异常处理 -->
    <!-- TODO 拦截器 -->
</beans>

```

#### web.xml 配置

- web.xml (path: src/main/webapp/WEB-INF/)

```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >
<web-app>
    <display-name>Archetype Created Web Application</display-name>

    <!-- 选择加载哪个 xml 文件 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-*.xml</param-value>
    </context-param>

    <!-- 过滤器配置 -->
    <filter>
        <filter-name>EncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>EncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!-- 监听器 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!-- 配置 Servlet -->
    <servlet>
        <servlet-name>SpringMvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

#### dao 层 Spring 配置

- spring-dao.xml (path: src/main/resources/)

```xml
<!-- dao 层配置 -->
<!-- 引入数据库的配置文件 -->
<context:property-placeholder location="classpath:db.properties"/>

<!-- 创建 c3p0 数据源对象 -->
<bean class="com.mchange.v2.c3p0.ComboPooledDataSource" id="dataSource">
    <property name="driverClass" value="${db.driver}"/>
    <property name="jdbcUrl" value="${db.url}"/>
    <property name="user" value="${db.username}"/>
    <property name="password" value="${db.password}"/>

    <!-- 数据库连接池配置 -->
    <property name="maxPoolSize" value="20"/>
    <property name="initialPoolSize" value="5"/>
</bean>

<!-- 创建 Mybatis 使用的 SqlSessionFactory -->
<bean class="org.mybatis.spring.SqlSessionFactoryBean" id="sessionFactory">
    <!-- 加载上方 c3p0 数据源 -->
    <property name="dataSource" ref="dataSource"/>
    <!-- 加载 Mybatis 的配置文件 -->
    <property name="configLocation" value="classpath:mybatis-config.xml"/>
    <!-- 配置别名包 -->
    <property name="typeAliasesPackage" value="com.ric.demo.entity"/>

</bean>

<!--
Mybatis 会根据 mapper.xml 文件使用动态代理的方式自动创建出 Mapper 接口的实现类.
下面这个配置实际上就是将这些实现类的对象放入到 Spring 容器中.
所以, 我们只需告知 Spring 这些 Mapper 接口的位置, 使用哪个 SqlSessionFactory 来创建这些接口的实现类.
-->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!-- 和上方 SqlSessionFactory 关联 -->
    <!-- 使用这个 SqlSessionFactory 把下面接口的实现类全部创建出来 -->
    <property name="sqlSessionFactoryBeanName" value="sessionFactory"/>
    <!-- Mapper 的接口所在的包 -->
    <property name="basePackage" value="com.ric.demo.mapper"/>
</bean>
```

#### service 层 spring 配置

- spring-service.xml (path: src/main/resources/)

```xml
<!-- 配置 service 层自动扫描 -->
<context:component-scan base-package="com.ric.demo.service"/>

<!-- 创建事务处理管理器对象 -->
<bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="transactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>

<!-- 开启切面的事务管理, 使用事务处理管理器 -->
<tx:annotation-driven transaction-manager="transactionManager"/>

<!-- 切面自动代理: 强制使用 cgLib 进行动态代理 -->
<aop:aspectj-autoproxy proxy-target-class="true"/>

```

<hr>

### JAVA

#### entity 层

- 实体类 GoodsCategory

```java
public class GoodsCategory {

    private Integer categoryId;
    private String categoryName;

    Setter & Getter;
    toString();
}
```

#### controller 层

- GoodsCategoryController

```java
// 返回 java 数据
@RestController
@RequestMapping("/category")
public class GoodsCategoryController {

    // 自动扫描 service 层对象
    @Autowired
    private IGoodsCategoryService goodsCategoryService;

    @RequestMapping("/findAll")
    public List<GoodsCategory> findAll() {
        // 调用 service 层方法
        return goodsCategoryService.findAll();
    }

}
```

#### service 层
> @Transactional 标记的方法会放入一个事务中,
    若标记的是类, 则此类中的所有方法都在各自的事务中. <br>
    readOnly 属性默认值为 false, 如果设置为 true, 那么被标记的方法不会放到事务里.

- IGoodsCategoryService

```java
@Service
public class GoodsCategoryService implements IGoodsCategoryService {

    @Autowired
    private GoodsCategoryMapper goodsCategoryMapper;

    /*
    @Transactional 标记的方法会放入一个事务中,
    若标记的是类, 则此类中的所有方法都在各自的事务中
    readOnly 属性默认值为 false, 如果设置为 true, 那么被标记的方法不会放到事务里
     */
    // 增加类别方法
    @Transactional(readOnly = false)
    @Override
    public void addCategory(GoodsCategory category) {
        goodsCategoryMapper.addOne(category);

    }

    // 查找所有类别方法
    @Override
    public List<GoodsCategory> findAll() {
        return goodsCategoryMapper.findAll();
    }
}
```

<hr>

### Mybatis 映射

#### SQL

- GoodsCategoryMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.ric.demo.mapper.GoodsCategoryMapper">

    <select id="findAll" resultType="goodsCategory">
        SELECT * FROM tb_goods_category
    </select>

    <insert id="addOne">
        INSERT INTO tb_goods_category VALUE (NULL, #{categoryName})
    </insert>

</mapper>
```

### 测试

- GoodsCategoryMapperTest (path: src/test/java/package/mapper/)

```java
@RunWith(SpringJUnit4ClassRunner.class)
// 两个配置文件: 数组形式{"", ""}
@ContextConfiguration({"classpath:spring-dao.xml", "classpath:spring-service.xml"})
public class GoodsCategoryMapperTest {

    @Autowired
    private IGoodsCategoryService goodsCategoryService;

    @Test
    public void findAll() {
        GoodsCategory goodsCategory = new GoodsCategory();
        goodsCategory.setCategoryName("零食");
        goodsCategoryService.addCategory(goodsCategory);
    }

}
```


#### 附: 结构图
![image](https://s1.ax1x.com/2018/09/29/ilan9P.png)
