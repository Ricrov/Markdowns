### 响应头
#### 如何设置响应头
1. response.setHead(key, value): 一个key只能对应一个value
2. response.addHead(key, value): 一个key可以对应多个value
#### 响应头常用属性
1. Content-Type: 用来设置以什么形式把数据显示给浏览器
```
text/html: 以文本形式显示html;
text/plain: 以纯文本形式显示;
image/jpg: 以图片形式显示;
audio/mp3: 以音乐格式显示
```
2. Content-Disposition: 用来指定该文件为下载类型的
```
attachment: 表示该文件为下载类型;
inline: 表示该文件显示在浏览器中
```
3. Location 发送重定向, 有两种方式
    - 它相当于发送了两次请求
    1. 通过设置状态码和响应头
    ```
    "302状态码表示请求重定向";
    resp.setStatus(302);
    resp.setHeader("Location", "http://www.baidu.com");
    ```
    2. 通过sendRedirect()方法设置
    ```
    resp.sendRedirect("http://www.baidu.com");
    ```
4. refresh响应头
    - 它的作用是: 浏览器隔多少秒之后跳转到其他页面
    ```
    "表示浏览器隔5秒中之后跳到百度页面";
    resp.setHeader("refresh","5;url=http://www.baidu.com");
    ```
### 在响应头中显示中文的方式
- 需要进行URL编码
```
"如果在设置响应头方法中需要使用中文";
"需要使用URLEncode.encode()方法进行转码";
String s = URLEncoder.encode("http协议", "utf-8");
resp.setHeader("Content-Disposition","attachment;filename=" + s + ".png");
```
### 转码相关问题
#### Get请求发送数据设置编码格式
1. Get请求发送的数据默认会以iso-8859-1编码格式传给后台
2. 如果要以utf-8形式显示: 有两种方法
    1. 可以先把iso-8859-1格式的数据转换成字节数组, 然后把该字节数组, 以utf-8的形式转码

    ```java
    byte[] bytes = account.getBytes("iso-8859-1");
     account = new String(bytes,"utf-8");
    ```

    2. 在tomcat的server.xml文件中进行配置
    ```xml
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               URIEncoding="utf-8"/>
    ```
#### Post请求发送数据设置编码格式
- 给request对象通过setCharacterEncoding()方法设置编码格式
- 该方式只对post生效
```
req.setCharacterEncoding("utf-8");
```
### ServletContext域对象
#### 获取到它的三种方式
1. 通过servlet.getServletContext()方法获取
2. 通过servlet.getServletConfig().getServletContext()方法获取
3. 通过request.getServletContext()方法获取
#### 它的一个方法 getRealPath()
- 该方法可以获取文件的真实路径
##### ==该方法的优点:==
- ==该方法可以动态获取到文件的真实路径, 无论该文件在哪个环境下==
