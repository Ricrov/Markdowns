

## 统一异常处理

> 统一异常处理:

> 在 Spring 项目执行过程中, 抛出了某个异常而这个异常没有被 try...catch 时, 那么最终这个异常会传递到该方法中. 对异常做某个判断后, 可以返回特定的 ModelAndView, 方法参数中的 Exception 对象就是抛出的异常对象.

#### 全局异常处理器
- 若异常没有处理, 最后都会走到 HandlerExceptionResolver 里
- 实现接口 HandlerExceptionResolver
- 给予 @Component 注解

```java
@Component
public class GlobalExceptionResolver implements HandlerExceptionResolver {

    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {

        ModelAndView mv = new ModelAndView();
        mv.setViewName("exception");
        String message = ex.getMessage();

        // 可根据异常进行不同的处理
        // 若异常是 JSP 类型的异常
        if (ex instanceof JspException) {
            // 可以获取到异常的消息
            message = ex.getMessage();
            mv.addObject("error", message);
        }
        // 若是 json 数据的异常
        else if (ex instanceof JsonException) {
            // 返回 JSON 格式的错误信息
            // 1. 强制类型转换
            JsonException je = (JsonException) ex;
            // 2. 获取 code
            int code = je.getCode();
            // 3. 设置 ModelAndView 为 json 格式的
            mv.setView(new MappingJackson2JsonView());
            mv.addObject("code", code);
            mv.addObject("status", false);
            mv.addObject("message", message);
        } else {
            mv.setViewName("exception");
            mv.addObject("error", "服务器出错了");
        }

        return mv;
    }
}
```

- 可以返回特定页面
- 可以将异常信息返回至页面

> 项目部署之后, 一定要避免将异常信息返回至页面上

- 创建一个基础异常, 继承 RuntimeException
- 重新书写空的构造方法和 message 的构造方法
```java
public class BaseException extends RuntimeException {
    public BaseException() {
        super();
    }
    public BaseException(String message) {
        super(message);
    }
}
```
- 分支1: 普通异常 (JspException)
    - 继承上文 BaseException
```java
public class JspException extends BaseException {
    public JspException() {
        super();
    }
    public JspException(String message) {
        super(message);
    }
}
```
- 分支2: json数据异常 (JsonException)
    - 继承上文 BaseException
```java
public class JsonException extends BaseException {
    // json 异常一般会给一个 code 属性
    private int code;
    // 给予构造方法
    public JsonException(int code) {
        this.code = code;
    }
    public JsonException(String message, int code) {
        super(message);
        this.code = code;
    }
    public int getCode() {
        return code;
    }
    public void setCode(int code) {
        this.code = code;
    }
}
```

- Controller 层
    - 创建一个不处理的异常, 则会进入全局异常处理器
```java
@RequestMapping("/test")
public String test(int id) {
    // 创建一个不处理的异常, 则会进入全局异常处理器
    if (id > 5) {
        throw new IllegalArgumentException("id > 5 出错");
    }
    return "success";
}
```
