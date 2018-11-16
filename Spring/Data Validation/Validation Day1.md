


## 数据校验#1

- 数据校验的目的
数据的校验是交互式网站一个不可或缺的功能，前端的js校验可以涵盖大部分的校验职责，为了避免用户绕过浏览器，使用http工具直接向后端请求一些违法数据，服务端的数据校验也是必要的，可以防止脏数据落到数据库中，Java中使用validation来对数据进行校验。


- 数据校的过程:
    1. 需要人为的判断哪些属性需要校验
    2. 这些属性各自需要什么样的校验规则
    3. 若校验不通过, 需要提示什么信息
    4. 此信息如何发送给用户

#### 依赖包

```xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>5.4.1.Final</version>
</dependency>
```


#### web.xml 配置

```xml
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:application-context.xml</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

#### application-context.xml 配置
- 配置校验器
- 驱动添加 validator 属性 `validator="validatorFactoryBean"`

> 由于 servlet 设置了针对 '/' 进行匹配, 故静态页面皆无法访问. 因此需要配置默认的 Servlet 以访问静态资源
`<mvc:default-servlet-handler/>`


```xml
    <context:component-scan base-package="com.ric.demo"/>

    <!-- 配置校验器的同时需要增加 validator 属性 -->
    <mvc:annotation-driven validator="validatorFactoryBean" />

    <!-- 配置校验器 -->
    <bean id="validatorFactoryBean" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
        <property name="providerClass" value="org.hibernate.validator.HibernateValidator"/>
    </bean>


    <!-- 使用默认的 Servlet 去访问静态资源 -->
    <mvc:default-servlet-handler/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

```

#### 实体类 bean 数据校验管理
注解名  | 注解的含义 | 应用 | 说明
---|--- | --- | ---
@NotNull | 输入的内容不能为空 |  @NotNull(message = "xxx") | message为出现错误提示的信息
@Min | 可以输入的最小值是多少 | @Min(value = 0,message = "价格必须大于0") | --
@Max | 可以输入的最大值是多少 | @Max(value = 1000 ) | --
@Digits | 规定小数的位数 |  @Digits(integer = a,fraction = b,message = "xxx") | 整数位<=a,小数位<=b
@Past | 代表这个时间必须是过去的时间 |  @Past(message = "时间必须是过去的时间") | --
@Future | 代表这个时间必须是将来的时间 |  @Future(message = "时间必须是将来的时间") | --
@AssertTrue | 元素必须为true | @AssertTrue(message="填入的值必须为true") |--
@AssertFalse | 元素必须为false | @AssertFalse(message="填入的值必须为false") |--
@Size() | 输入的值必须在指定的范围内 | @Size(max = 1000, min = 10, message) | --
@Pattern() | 输入的圆度必须符合指定的正则表达式 | @Pattern(regex=,flag=) |--
@NotBlank | 输入的内容不能为空,且长度不能为0 |  @NotBlank(message = "xxx") |    --
@Email() | 输入的内容必须是邮箱的地址 | @Email(message = "xxx") | --
@Length() | 输入的字符串长度必须在指定的范围内 | @Length(min=,max=) | --
@NotEmpty | 输入的字符串必须非空 | @NotEmpty(message = "xxx") | --
@Range | 输入的内容必须在合适的范围内 | @Range(min=,max=,message="xxx") | --

```java
public class BookBean {

    // 校验各个变量
    @NotBlank(message = "书名不能为空")
    private String bookName;

    @NotBlank(message = "作者不能为空")
    private String author;

    @NotNull(message = "价钱不能为空")
    @Min(value = 0, message = "价格必须大于0")
    // 整数部分3位数以内, 小数部分2位数以内
    @Digits(integer = 3, fraction = 2, message = "设置的价格不合理")
    private Float price;

    // @Past 代表时间必须是过去的时间
    // @Future 代表时间必须是未来的时间
    // @Pattern 正则校验
    @NotNull(message = "时间不能为空")
    @Past(message = "时间必须是过去的时间")
    private Date publishDate;

}
```

#### controller 层数据校验
 - Bookbean是个实体类,参数Bookbean前需要加上@Validated注解,表明需要spring对其进行校验,而校验的结果和信息会存放到其后的BindingResult对象中.注意,这两者必须相邻.如果还有多个参数需要验证,那么每个参数后面都必须紧邻着一个BindingResult对象.即一个校验类对应一个校验结果
 ![image](https://s1.ax1x.com/2018/09/20/inpsFH.png)
```java
@Controller
@RequestMapping("/books")
public class BookController {


    // 此时前端传入任何值都能接收, 因此需要数据校验
    @RequestMapping("/add")
    // @Validated 注解写到想要校验的参数位置
    // BindingResult 用来获取校验不通过时的信息, 必须紧跟在被校验的实体类后面
    public String newBook(@Validated BookBean book, BindingResult result, Model model) {

        // .hasErrors 判断是否存在校验不通过的属性
        System.out.println(book);
        if (result.hasErrors()) {
            // 针对某个属性获取它的校验信息
            FieldError bookName = result.getFieldError("bookName");

            // 针对某个属性获取它的所有校验信息
            List<FieldError> priceErrors = result.getFieldErrors("price");

            // 获取所有属性的所有校验信息
            List<FieldError> allErrors = result.getFieldErrors();

            // 若出错, 将错误集合放到 Model 中
            model.addAttribute("errors", allErrors);

            for (FieldError error : allErrors) {
                // 遍历获取错误信息
                String errMsg = error.getDefaultMessage();
                System.out.println(errMsg);
            }

            return "error";
        } else {

            return "success";
        }
    }


    // 更新书本方法
    @RequestMapping("/update")
    // 给予标记, @Validated 注解中有 value 属性, 对应标记的类组
    public String updateBook(@Validated(value = {BookUpdateValidator.class}) BookBean book, BindingResult result, Model model) {
        // 该方法与新增书本的方法使用的都是同一个实体类对象
        // 但在不同的方法中的校验规则是不同的
        System.out.println(book);

        return newBook(book, result, model);
    }
}
```


#### 分组校验
- 对于一个实体类中需进行不同校验的情况
- 这条属性在更新书本的方法中是需要校验的, 在新增书本方法中不需校验.
- 解决办法: 给注解一个标记, 校验时给予一个同样的标记, 一致则生效.
- 建立空接口 BookUpdateValidator
```java
// 标记书本更新验证
public interface BookUpdateValidator {
}
```
- 至此, 接下来在实体类中将需要标记的成员变量上添加校验注解
- group: 校验组, 一个 Class 类型的数组, 放入标记的类
- 由于添加了分组groups,那么Controller层对应的@Validated注解也要进行改写,需要添加一个value属性指定相同的接口类
```java
public class BookBean {
    @NotNull(message = "ID不能为空", groups = {BookUpdateValidator.class})
    private Integer id;
}
```

- 如果同一条属性的同一校验规则在不同的场景下都需要进行校验,groups属性之间多个接口类使用`,`分割
```
     @NotNull(message = "id不能为空",groups = {a.class,b.class,c.class ...})
     private Integer id;
```





#### 附: 数据校验的项目结构
![image](https://s1.ax1x.com/2018/09/20/inpeWn.png)
