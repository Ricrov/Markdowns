### web.xml冷门配置
1. load-on-startup标签
```
"该标签用来配置servlet随着tomcat的启动而加载";
"该标签的值是一个大于等于0的整数, 表示servlet加载的优先级";
"值越小, 优先级越高";
<servlet>
    <servlet-name>HomeServlet</servlet-name>
       <servlet-class>com.lanou3g.demo.HomeServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
    </servlet>
```
2. 使用通配符来配置servlet对应的url
```
"以下为常用通配符使用方式";
"在一个servlet-mapping映射中,可以配置多个url-parrtern, 就是多个url对应一个servlet";
<servlet-mapping>
    <servlet-name>HomeServlet</servlet-name>
    <url-pattern>/home</url-pattern>
    <url-pattern>*.hello</url-pattern>
    <url-pattern>/hello/*</url-pattern>
    "该条最常用";
    <url-pattern>/*</url-pattern>
    </servlet-mapping>
```
3. 自定义错误页面风格
```
"使用error-page标签自定义页面风格";
<error-page>
    "erroer-code表示错误码";
    <error-code>404</error-code>
    "location表示自定义的错误页面,  地址前必须加杠(/)";
    <location>/html404.html</location>
    </error-page>
```
### 常用请求头
1. User-Agent
```
"该请求头用来保存本次请求所使用的浏览器的信息";
"也就是本次请求时通过什么浏览器发送过来的";
"一般用来统计数据的";
```
2. ==Referer==
```
"标识当前的Servlet是从哪个地址发送过来的";
"它有防盗链的功能";
```
### 获取请求头的方式
1. request.getHeader(String name): 通过请求头名字获取到value
2. request.getHeaderNames(): 获取请求头的所有key, 通过key获取到对应的value
```
Enumeration<String> headerNames = req.getHeaderNames();
    while (headerNames.hasMoreElements()) {
    System.out.println(headerNames.nextElement());
        }
```
3. request.getHeaders(String name): 通过key获取到value的集合, 因为一个key可以对应多个value
4. req.getDateHeader(String name): 通过key后去Date头信息, 返回long型数值
### 请求常用方法
1. request.getMethod: 获取请求方法
2. req.getQueryString(): 获取查询参数, 就是url中?号后面的字符串
3. req.getRemoteAddr(): 获取用户的ip地址
### ==Servlet中三大域对象==
- 域对象的作用: 能存值, 能取值
1. ServletContext
```
"它的作用域是全局的, 所有Servlet中数据是共享的";
ServletContext ct = req.getServletContext()
ct.getAttribute("ct-count")
ct.setAttribute("ct-count", attribute)
```
2. Session 会话
```
"一个客户端多次产生的请求, 是一个会话";
"会话是独立的, 不同用户之间不能共享数据";
HttpSession session = req.getSession()
session.getAttribute("session-count")
session.setAttribute("session-count", attr)
```
3. Request 请求
```
它的值只在请求中生效
req.setAttribute()
req.getAttribute()
```
### 请求转发和请求重定向
#### 请求转发
```
"参数可以填任意参数, 表示要转发的地址";
RequestDispatcher dispatcher = req.getRequestDispatcher("home");
dispatcher.forward(req,resp);
```
#### 请求重定向
1. 重定向方法1
```
response.setStatus(302);
response.setHeader("Location", "重定向的地址");
```
2. 重定向的方法2
```
response.sendRedirect("重定向的地址");
```
#### ==请求转发和请求重定向的区别==
1. 请求转发url地址不变, 可以隐藏真正处理这个请求的Servlet
2. 请求转发客户端url不变, 但重定向变
3. 请求转发只能转发到自己的资源上, 重定向可以定向到任意资源
4. 转发浏览器只发送一次请求, 定向浏览器发送两次请求
5. 请求转发是服务器行为, 重定向是客户端行为
6. 请求转发: 在转发到的Servlet中仍然可以获取到请求中的所有数据(请求头和请求体都能获取到), 但重定向做不到
### 包装类和基本数据类型的转换
1. 包装类转换成基本数据类型, 叫拆箱
```
Integer i = null;
int a = i;
"调用的方法是:"
i.intvalue(): 所以说可能会发生空指针异常
```
2. 基本数据类型转成包装类, 叫装箱
```
int a = 10;
Integer i = a;
"调用的方法是:"
"Integer.valueOf(a)";不会发生空指针异常
```
