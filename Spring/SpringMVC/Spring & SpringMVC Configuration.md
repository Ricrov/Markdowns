#### 配置 pom.xml

- Spring 配置0: 准备部分

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!-- 习惯更改为 1.8 版本 -->
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>

<dependencies>
    <dependency>
        <!-- 习惯更改为 4.12 版本 -->
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <!-- 配置 servlet-api 3.1.0 -->
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <!-- 编译后不会部署在服务器上 -->
        <scope>provided</scope>
    </dependency>
    ...
</dependencies>
```

- Spring 配置1: spring-core 核心部分

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.0.8.RELEASE</version>
</dependency>
```

- Spring 配置2: spring-beans 实体类部分

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>5.0.8.RELEASE</version>
</dependency>
```

- Spring 配置3: spring-context 环境部分

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.0.8.RELEASE</version>
</dependency>
```

- Spring 配置4: spring-test 测试部分

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.0.8.RELEASE</version>
</dependency>
```

> ※ ==注意需要统一版本==

- Spring 配置5: 其他部分

```xml
<!-- Gson -->
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.0</version>
</dependency>
```

- ==SpringMVC 配置: spring-web 和 spring-webmvc==

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>5.0.8.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.0.8.RELEASE</version>
</dependency>
```

#### 关于 web.xml 配置

> **容器对于 web.xml 的加载过程是 context-param >> listener  >> filter  >> servlet**


- <context-param> 可配置声明应用范围(整个WEB项目)内的上下文初始化参数。
    - param-name 设定上下文的参数名称。必须是唯一名称
    - param-value 设定的参数名称的值

```xml
<web-app>
    <display-name>Archetype Created Web Application</display-name>

    <!-- 一般在这里加载 Spring (除了 mvc)的配置文件 -->
    <!-- 这里配置的参数会传递到 listener 中 -->
    <!-- 可以使用通配符 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-*.xml</param-value>
    </context-param>
```

- 过滤器配置

```xml
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
```

- 监听器配置
> 此监听器的作用: 在 web 程序启动时, 会把 Spring 容器对象存放在 ServletContext 这个最大的域对象中.这样在无法声明成员变量的时候, 此监听器可以获得容器的对象以用来调用 getBean 方法.

```xml
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
```
```java
@RequestMapping("/login")
public String login(HttpServletRequest req) {
    ServletContext servletContext = req.getServletContext();
    // 从 ServletContext 中取出容器对象 (只有配置了监听器才能取出容器对象)
    WebApplicationContext context = WebApplicationContextUtils.findWebApplicationContext(servletContext);
    // 此时 context 就是容器对象, 可以使用 getBean 方法
    Person p = (Person) context.getBean("xxx");

    return "login";
}
```

- Servlet 配置

```
    <!-- Servlet 配置 -->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <!-- 指定加载的 SpringMVC 的配置文件 -->
            <param-name>contextConfigLocation</param-name>
            <!-- 此位置通过逗号','可加载多个配置文件 -->
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

#### Spring / SpringMVC 配置文件

- spring-mvc.xml

```xml
<!-- 命名空间 -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd   http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 自动扫描, 可指定扫描具体的某层 (.service) -->
    <context:component-scan base-package="com.ric.demo"/>

    <!-- 加载注解的驱动 -->
    <mvc:annotation-driven/>

    <!-- ViewResolver 拼接地址 前缀'/' + xxx + 后缀'.jsp' -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>

```

#### 返回 json 数据

```java
@RequestMapping("/person")
// 返回 json 注解 @ResponseBody (注: 需引入 gson 依赖)
@ResponseBody
public Person getOne() {
    Person p = new Person();
    p.setId(1);
    p.setName("张三");
    return p;
}
```

#### 单元测试

包: test -> java -> Make Directory As Test Resources Root

- 注解 @Runwith(SpringJUnit4ClassRunner.class)
    - @Runwith 是一个运行器, 让测试运行于 Spring 的测试环境
- 注解 @ContextConfiguration("classpath:springmvc.xml")
    - Spring 整合 JUnit4 测试时, 使用此注解可引用(多个)配置文件

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-mvc.xml")
public class PersonTest {

    @Autowired
    private Person person;

    // 如何测试 spring
    @Test
    public void test() {

        System.out.println(person);

    }
}
```


#### RESTful (Representational State Transfer) 风格

- RESTful 是一种软件架构风格、设计风格，提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。


普通自定义规则 | RESTful 规则
---|---
/add_user    | /user -> put请求
/delete_user | /user -> delete请求
/update_user | /user -> post请求
/find_user   | /user -> get请求
/book?id=1   | /book/1 -> 查询某一本书


> @RestController 注解标记的 Controller
里面所有请求都自动加上了 @ResponseBody 注解

- @RequestParam 注解
    - value = "" : 若形参的名字与前端不符, 则可以使用 value 参数来指定该形参的代名以完成匹配
    - defaultValue = "" : 规定了若前端并未给此参数传递值时, 此参数则会有一个默认的值
    - required = true / false : 为 true 时, 则规定了前台必须给此参数传递值, 在一定意义上进行了一次简单的后台校验 (※ 当为 true 时, defaultValue 失效)

- @PathVariable 注解
    - 带占位符的 URL 是 Spring 3.0 的新功能, 该功能在SpringMVC 向 REST 目标挺进发展过程中具有里程碑的意义。
    - 通过 @PathVariable 可以将 URL 中占位符参数绑定到控制器处理方法的形参中。
    - 下例 URL 中的 {id} 和 {name} 占位符可以通过 @PathVariable() int id, @PathVariable() String name 绑定到方法的形参中。
    - 以此方法, 在今后可以代替 ModelAndView。


```java
@RestController
public class PersonController {
    /**
     * 查询所有用户
     * @return
     */
    @RequestMapping(value = "/person", method = RequestMethod.GET)
                                // 若前端没有给 page 值, 则它的默认值是 1, 后者参数默认值为 20, required: true 时必须由前端传递过来, 否则将报错, 不会和默认值同时用
    public List<Person> findAll(@RequestParam(value = "page", required = false, defaultValue = "1") int page, @RequestParam(value = "20") int count) {
        return new ArrayList<>();
    }

    // 此种风格就称之为 RESTful 风格
    @RequestMapping(value = "/person", method = RequestMethod.DELETE)
    public Person delete() {
        return new Person();
    }

    // 查询 /dlj0603/1001 的人
    // @PathVariable 注解
    @RequestMapping(value = "/person/{id}/{name}", method = RequestMethod.GET)
    public Person findOne(@PathVariable() int id, @PathVariable() String name) {
        Person p = new Person();
        p.setId(id);
        p.setName(name);
        return p;
    }

}

```

#### 附: 布局结构

![image](https://ws3.sinaimg.cn/large/006tNbRwly1fvctzmy19zj30ab0o2dh5.jpg)
