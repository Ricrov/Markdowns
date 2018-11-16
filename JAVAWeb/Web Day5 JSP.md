## JSP java server page java服务页面
### 为什么说JSP是一个Servlet
1. 在网页中输入jsp文件时, 系统会自动生成一个该文件对应的java类
2. 在jsp中写的代码会被写到生成的java类中
3. 系统会执行该类的service方法
4. 该类继承自HttpServlet, 所以一个jsp就是一个Servlet
### 获取jsp生成的类的名字和路径
```js
"获取类的名字";
out.println(this.getClass().getName());
"获取类的路径";
String path = this.getClass().getResource("").getPath();
    out.println(path);
```
### jsp中的注释
-  该注释是给html的
-  Jsp编译成Java文件后, 也会把这部分的内容写入到输出流中
-  该注释中的内容可以在java文件中看到
-  也可以在页面中通过检查看到
```
<!--JSP Java Server Page: -->
```
- 该注释是专门给Jsp使用的
- 编译成java文件后, 这个注释不会在java文件中显示
```
<%----%>
```
- 该注释就是java注释, 会编译到java文件中, 但不会在页面中显示
```
// 或 /* */
```
### JSP常用配置
1. 设置字符集和使用的语言
```js
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
```
2. 用来导入java中的包
```js
<%@ page import="java.util.*" %>
```
3. 用来让生产的类继承自定义的Servlet类
```js
<%@ page extends="com.lanou3g.demo" %>
```
4. 当页面出现一个没有被捕获的异常时, 会跳转到定义的页面
```js
<%@ page errorPage="error.jsp" %>
```
5. 指定当前页面是否是用来处理异常的, 如果为true, 那么可以使用exception内置对象
```js
<%@ page isErrorPage="true" %>
```
6. 用来表示是否忽略EL表达式, 常用
```js
<%@ page isELIgnored="true" %>
```
7. 该配置用来导入一些自定义标签, 不常用
```js
<%@ taglib prefix=""%>
```
8. 用来导入其他文件的, 不常用
```js
<%@include file="login.jsp" %>
```

### JSP标签
- `<%@ %>`: 该标签写的内容是当前Jsp页面的配置
- `<%! %>`: 该标签中可以写java代码, 该代码会生成到类中, 一般用来创建成员变量和方法的
- `<%  %>`: 该标签中的java代码会生成在jsp生成的类中的service方法中
- `<%= %>`: 该标签中填的是表达式,也就是有结果的代码, 不能写逻辑代码
### J==SP常用动作标签==
#### `<jsp:forward page="login.jsp" />`
- 用来进行请求转发的


## JSP 与 Servlet 的相关知识
### ==jsp文件放到WEB-INF中如何访问==
- ==需要通过请求转发来访问==
```
req.getRequestDispatcher("/WEB-INF/Login.jsp").forward(req,resp)
```
### jsp和servlet的作用
- jsp一般用来显示页面的, 不进行逻辑处理
- servlet一般用来处理逻辑的
### 设置编码必须在获取流之前
### CombopooledDataSource必须写成单例的
- 因为每次发送请求的时候都会创建一个对象
- 如果这样的话那么会有很多连接对象, 很消耗资源
### ==请求转发都可以转发哪些数据==
1. 它会把请求中的所有数据都转发过去
2. 响应头可以转发, 响应体不能转发
#### ==在组合使用servelt和jsp时, 一般会使用request对象传递数据==
### jsp 9大内置对象
1. request: 常用
2. response
3. application(对应Servlet中的ServletContext对象)
4. session: 常用
5. out
6. page
7. config(对应Servlet中的ServletConfig对象)
8. pageContext(保存当前页面的一些信息)
9. exception
### ==jsp中request请求对象中的常用获取路径的方法==
1. request.getContextPath()
```
"获取项目的路径";
```
2. request.getServletPath()
```
"获取servlet的相对路径";
```
### ==getParameter(String name)可以获得的属性==
1. 只能获取Context-Type的值为: application/x-www-form-urlencoded的请求参数
2. URL后面拼接的数据: 数据类型为: name=xxx&password=xxx&age=xxx
