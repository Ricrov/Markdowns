#### http://localhost:8080/ 代表的是工程目录下的web文件夹(实际上对应的是out目录下的文件)
### ==text/plain: 以纯文本形式显示==
#### ==如果在响应头中如果需要显示中文, 需要把中文进行RUL编码, 编码方式==
```
"使用RULEncoder.encode()方法";
String s = URLEncoder.encode("http协议", "utf-8");
```
### MIME
- image/png: 以图片格式显示
- text/html: 以html文本格式显示
- text/plain: 以纯文本形式显示
### 一个完整的web访问流程
- 客户端发送一个请求
- 根据域名的ip地址找到服务器电脑
- 根据域名的端口号找到服务器应用程序
- 在应用程序的web.xml文件中, 根据请求内容中的端口号后面的内容找到对应的servlet
- 根据servlet找到对应的类
- 根据不同的HTTP请求调用类中的不同方法
### Servlet类注意事项
- 每个Servlet类的对象都只有一个
- Servlet对象的方法可以执行在不同的线程中
- 在Servlet类中最好不要声明成员变量
- Servlet的成员变量只能赋值一次
- 成员变量中最好不要声明引用数据类型
- 如果要声明引用数据类型, 那么这个类中最好不要有属性
- 如果非要有属性, 那么属性最好是final的
- 最好用ThreadLoacl队成员变量进行处理, 用来保证每个线程都是同一个对象
### 如何创建一个Servlet类
- ==当servlet对象被创建的时候, 会自动执行service方法==
- ==Servletconfig: 可以获取Servlet的配置信息==
- 编写一个类实现Servlet接口

```java
"当servlet对象被创建的时候, 会自动执行service方法";
public class HelloServlet implements Servlet {
    private ServletConfig servletConfig;
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
    "init方法为初始化方法, 该方法只会执行一次";
    "在该servlet对象创建的时候会执行一次";
    "一般执行一些初始化的工作"
   "把系统传进来的ServletConfig赋值给成员变量";
    this.servletConfig = servletConfig;
    System.out.println("HelloServlet.init");

    "像这种的, 某些方法不是由开发者自己调用的"
    "并且规定了某些对象的出生到死亡的过程"
    "这样的方法叫做: 生命周期方法";
    }

    @Override
    public ServletConfig getServletConfig() {
    "获取ServletConfig";
        return this.servletConfig;
    }

    @Override
    public void service(ServletRequest req, ServletResponse resp) throws ServletException, IOException {
        System.out.println("HelloServlet.service");
        PrintWriter out = resp.getWriter();
        out.println("Hello Servlet!");
        out.close();
    }

    @Override
    public String getServletInfo() {
    "获取servlet信息"
    "不常用, 相当于给servlet做简介";
        return "";
    }

    @Override
    public void destroy() {
       "在该servlet对象销毁的时候, 会执行该方法";
       "当tomcat退出的时候, servlet对象就销毁了";
        "可以做一些资源释放的操作";
        System.out.println("HelloServlet.destroy");
    }
}
```

2. 编写一个类, 继承GenericServlet

```java
public class HomeServlet extends GenericServlet {
    @Override
    public void service(ServletRequest req, ServletResponse resp) throws ServletException, IOException {
        PrintWriter writer = resp.getWriter();
        writer.println("HomeServlet!");
        writer.close();
    }
}
```

3. ==编写一个类继承HttpServlet 最常用==
- ==如果是get请求, 就重写get方法==
- ==如果是post请求, 就重写post方法==
- ==HttpServlet封装了Http协议==
- ==当重写doGet或doPost方法时: super.doGet(req, resp)必须被删除, 否则系统不认为重写了doGet方法==
- 为了让浏览器可以显示中文, 需要在浏览器的响应头中设置编码格式

```java
public class LoginServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // super.doGet(req, resp);
    "专门处理前端发送过来的get请求";
    "为了让前端浏览器可以正常显示中文";
    "需要告知浏览器汉字的编码格式";
    "此信息需要保存在响应头中";
    "注意: 必须在获取输出流之前设置响应头";
    "设置响应头信息的两个方法";
    "1. resp.addHeader();: 一个key可以对应多个value";
    "2. resp.setHeader();: 一个key只能对应一个value";
    "设置响应头的key 有的响应头是固定的, 有的不是固定的";
    "如果设置编码格式 响应头的key是固定的 为Content-Type";
    "告知浏览器响应数据为:文本类型, html格式, 编码为utf-8";
    resp.setHeader("Content-Type","text/html;charset=utf-8");
    PrintWriter writer = resp.getWriter();
        writer.println("<h1>你好, Servlet</h1>");
        writer.close();
    }
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        "专门处理前端发送过来的post请求";
    }
}
```

### 如何注册Servlet
- 编写完一个Servlet, 必须在web.xml中注册, 否则无法使用
- servlet标签用来声明一个servle,并为其起名并绑定某个servlet类
- servlet-mapping用来声明一个映射, 规定访问某个url时, 执行哪个servlet
- url路径必须加 / (杠)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>com.lanou3g.demo.HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

### ServletConfig对象
- 它已经在GenericServlet类中初始化了
- 可以直接在其他Servlet类中, 通过getServletConfig方法获取该对象
- 通过该对象可以获取到一些Servlet配置信息
- 可以获取到Servlet名字
- 可以获取到ServletContext对象
- 可以获取到Servlet的一些初始化参数
### ServletContext对象
#### 获取该对象的方法
- getServletContext()
- getServletConfig().getServletContext()
- request.getServletContext(): 通过请求对象, 获取ServletContext()对象
#### 该对象的作用
- tomcat为每个web项目都创建了一个ServletContext对象
- tomcat服务器在启动时创建, 服务器销毁时关闭
- 在一个web项目中共享数据, 管理web项目资源
- 通过它的setAttribute()方法和getAttribute()方法实现了数据的共享, 就是说每个Servlet都可以获得该数据
- 通过getInitParameter() 获取全局配置信息
- 也可以获取到web项目的资源

```java
"1. 获取web项目下指定资源的路径：getServletContext().getRealPath("/WEB-INF/web.xml
"getRealPath()方法可以动态获取到文件的真实路径, 无论该文件在哪个环境下";
"2. InputStream getResourceAsStream(java.lang.String path)";
"3.getResourcePaths(java.lang.String path)  指定路径下的所有内容。";
"4. RequestDispatcher  getRequestDispatcher(String path) ;//参数表示要跳转到哪去";
```
