# SpringMVC

![image](https://ws3.sinaimg.cn/large/006tNbRwly1fv7ovwnfdjj30cj05j74o.jpg)
### SpringMVC 原理
> **Step 1**:  客户端发送请求: URL + 数据

> **Step 2**: DispatcherServlet 接收数据, 将数据传递至 HandlerMapping, 由其封装成数据对象

```xml
<!-- 配置 HandlerMapping -->
    <bean id="simpleUrlHandlerMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
		<property name="mappings">
			<props>
				<prop key="/login.action">loginController</prop>
				<prop key="/success.action">successController</prop>
			</props>
		</property>
	</bean>
```
> **Step 3**: HandlerAdapter 接收数据, 对数据对象进行业务处理(即 Controller), 并封装成 ModelAndView 对象

```xml
<!-- 使用controller进行数据处理，生成ModelAndView -->
	<bean id="/login.action" class="org.springframework.web.servlet.mvc.ParameterizableViewController">
		<property name="viewName" value="login"></property>
	</bean>
	<bean id="/success.action" class="com.ric.springmvc.controller.LoginAction"></bean>
```
> **Step 4**: ViewResolver 接收 ModelAndView 并解析, 将 model 渲染到 view 中

```xml
<!--
	使用ViewResolver进行视图解析，生成对应的view
	前缀: /WEB-INF/jsp/
	后缀: .jsp
	组合为 /WEB-INF/jsp/login.jsp
-->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/jsp/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>
```

> **Step 0**: 简化版

```xml
<!-- 可将 Step 1, 2, 3 省略, 替换成此代码, 自动搜索 -->
<context:component-scan base-package="com.ric.springmvc.controller"></context:component-scan>
```

> **Step 5**: DispatcherServlet 最终将 view 响应给客户端




#### 配置框架
- Maven → Create from archetype → webapp
- GroupId: com.ric.springmvc.demo
- ArtifactId: springmvcDemo
- archetypeCatalog: Internal
- 创建 java 文件夹在 main 下, Make Directory as Source Root 以创建 Java 类
- 于 WEB-INF 下配置 springMVC-Servlet.xml (前提: 文件名与 web.xml 中的配置一致)
- to Step 0 or 1

#### 阻止地址栏输入直接访问

- WEB-INF

- web.xml 配置
    - 欢迎页

    ```xml
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
    ```

    - 配置 Servlet:
    >后台服务器通过 DispatcherServlet 进行调度
    对所有的 *.action 拦截, 进行文件处理

    ```xml
    <servlet>
        <servlet-name>springMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--
        通过参数 contextConfigLocation 可以配置 SpringMVC 配置文件的位置,
        若不进行配置, 默认会在 WEB-INF 下查找 "<servlet-name>"-servlet.xml
        (即 springMVC-servlet.xml)
        只要服务器接收到 *.action 文件, DispatcherServlet 首先会查找拦截器, 把请求的 URL 拦截到 SpringMVC 的配置文件里
        -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:</param-value>
        </init-param>
        </servlet>
        <servlet-mapping>
            <servlet-name>springMVC</servlet-name>
            <url-pattern>*.action</url-pattern>
        </servlet-mapping>
    </servlet>
    ```

- Controller
    - 函数的返回值
        >通常是 ModelAndView, 也可以写成 String, 若写成 String, 则容器会协助生成对应的 ModelAndView, 它的 viewName 就是 String 类型.

        - String 类型的返回值, 表示 viewName, 会通过 viewResolver 进行解析, 生成最后的 view

        - forward / redirect 表示请求转发和响应重定向:
            - `return "forward:suc.action";`
            - `return "redirect:suc.action";`

    - 函数名
        >通常写成与 url 同名 `@RequestMapping("login.action")`,
        此函数的函数名就是 `login`

    - 函数的参数
        - 使用 request / response;
            ```java
            @RequestMapping("/success.action")
            public String success(HttpServletRequest req, HttpServletResponse resp) {
                System.out.println("--- --- -Success- --- ---" + req.getParameter("username") + " " + req.getParameter("password"));

                // 请求转发 & 重定向
                // 请求转发时则不会对其解析, 而是请求到转发到目标地址
                return "forward:suc.action";

                // 重定向
                // return "redirect:suc.action";
            }
            ```

        - 直接使用参数名, 但此种方式无法请求转发!
            ```java
            public String success(String username, String password) {
                System.out.println("---- success ---- 用户名: " + username);
                System.out.println("---- success ---- 密码: " + password);

                return "success";
            }
            ```

        - 使用模型类(实体类), 不可请求转发!
            ```java
            public String success(Person p) {
                System.out.println("---- success ---- 用户名: " + p.getUsername());
                System.out.println("---- success ---- 密码: " + p.getPassword());

                return "success";
            }
            ```

        - 模型类与参数混搭, 不可请求转发!
            ```java
            public String success(Person p, String username) {
                System.out.println("---- success ---- 用户名: " + p.getUsername());
                System.out.println("---- success ---- 密码: " + username);

                return "success";
            }
            ```

        - 参数名和前端参数不匹配时可使用注解自动匹配`@RequestParam("")`
            ```
            public String success(@RequestParam("username") String userrr, String password) {
                System.out.println("---- success ---- 用户名: " + userrr);
                System.out.println("---- success ---- 密码: " + password);

                return "success";
            }
            ```

        - 加入 Model / Map / ModelMap
        >若返回值是 String, 容器则会自动将 String 封装成 ModelAndView, 此时的 viewName 是 String 的值. 若想传递 Model, 可以使用参数 Model/Map/ModelMap

        ```
        // Map
        @RequestMapping("/success.action")
        public String success(Person person, Map map) {
            map.put("person", person);
            return "success";
        }
        ```

        ```
        // Model
        @RequestMapping("/success.action")
        public String success(Person person, Model model) {
            model.addAttribute("person", person);
            return "success";
        }
        ```

        ```
        // ModelMap
        @RequestMapping("/success.action")
        public String success(Person person, ModelMap modelMap) {
            modelMap.addAttribute("person", person);
            return "success";
        }
        ```

        ```
        // @ModelAttribute 注解, 等同于 model.addAttribute("person", person);
        @RequestMapping("/success.action")
        public String success(@ModelAttribute("person") Person person) {
            return "success";
        }
        ```

    - @RequestMapping 相关参数
    `@RequestMapping(value = "/success.action", method = RequestMethod.GET)`

    - ModelAndView 对象
        - ModelAndView 是 Model 和 View 的封装

        ```java
        @RequestMapping("/login.action")
        public ModelAndView login() {
            ModelAndView mav = new ModelAndView();
            mav.setViewName("login");
            return mav;
        }
        ```

        ```html
        <%-- 需要自定义 isELIgnored = "false" --%>
        <%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
        <html>
        <head>
            <title>Success</title>
        </head>
        <body>
        Success!
        ${person.username}
        </body>
        </html>
        ```

        ```java
        @RequestMapping("/success.action")
        public ModelAndView success(Person person) {
            System.out.println("--- success --- " + person);

            // 将实体类中的参数通过集合 Map 传递
            Map map = new HashMap();
            map.put("person", person);

            ModelAndView mav = new ModelAndView("success", map);
            return mav;
        }
        ```
