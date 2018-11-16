<font size=4>

## 拦截器 Interceptor

>SpringMVC中所有请求都走 DispatcherServlet.
使用拦截器 interceptor 进行拦截.



application-context.xml 配置
```xml
<!-- interceptor 拦截器配置 -->
    <mvc:interceptors>
        <mvc:interceptor>
            <!-- 拦截/books/add -->
            <mvc:mapping path="/books/add"/>
            <!-- 对 /books/add 和 /books/update 进行拦截 -->
            <mvc:mapping path="/books/*"/>
            <!-- 对此路径下的所有子路径进行拦截 -->
            <mvc:mapping path="/books/**"/>

            <!-- /books/add 拦截 -->
            <!-- /books/update 拦截 -->
            <!-- /books/delete 拦截 -->
            <!-- /books/findAll 不拦截 -->
            <mvc:exclude-mapping path="/books/findAll"/>

            <!-- 给拦截器 @Component注解以生成对象 -->
            <ref bean="loginInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
```


- 创建拦截器, 实现接口 HandlerInterceptor, 重写3个方法
```java
@Component
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("LoginInterceptor.preHandle");

        // 简单校验
        HttpSession session = request.getSession();
        Object user = session.getAttribute("user");
        if (user == null) {
            response.sendRedirect("/WEB-INF/login.jsp");
            return false;
        }

        // 返回值为 false 时则拦截, 不会继续运行接下来的代码
        return true;
    }


    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        // Controller 方法执行完毕后 此方法执行
        System.out.println("LoginInterceptor.postHandle");


    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // 视图解析器渲染完成后返回至前端之后 此方法执行
        System.out.println("LoginInterceptor.afterCompletion");
    }
}
```
