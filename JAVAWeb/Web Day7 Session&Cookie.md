## Session
Session(会话),一个客户端多次产生的请求,是一个会话 (A存的值只能由A取,别人取不到)
- 获取一个Session对象
```java
    HttpSession session = req.getSession(boolean b);
    - 构造方法中可以填写一个boolean类型的参数,表示早获取session对象的时候
    如果没有session对象,是否创建一个.默认值为true.
```
- 获取Session对象的ID
```java
    String id = session.getId();
    - 每个用户的Session对象的Id都是不同的
```
- 设置session在服务器中存活的时长
    + 默认情况下session在服务器的内存中存活30分钟,在用户每次访问服务器的时候,这个时间都会刷新
    + 也可以手动设置session的存活时间,时间单位秒(s),手动设置有两种方法
        - 第一种方式,在java代码中调用setMaxInactiveInterval()方法
         ```java
        session.setMaxInactiveInterval(30);
        ```
        - 第二种方式,在web.xml文件中进行配置
        ![image](https://ws1.sinaimg.cn/large/006tNbRwly1fuqsup3ot4j30il05jab6.jpg)


- 让Session对象失效
```java
    session.invalidate();
```
- 清空session对象
```java
    session.removeAttribute("user");
```

## Cookie
- cookie是浏览器缓存数据的一种技术,cookie是保存在浏览器中的.
- cookie以key-value的形式保留数据
- response(响应)会告知浏览器保存哪些数据,并且在下次请求同一个网站的时候,把这些信息再次发送给服务器,所以说cookie是存在请求中的.

#### cookie相关的操作
- 获取浏览器缓存的所有cookie --> req.getCookies()
```java
    Cookie[] cookies = req.getCookies();
    for(Cookie c : cookies){
        String key = c.getName();   // 获取cookie的key
        String value = c.getValue(); // 获取cookie的value
    }
```
- 通知浏览器缓存某个数据,这样浏览器下次再发送请求,会把这个数据携带上来
```java
    Cookie c1 = new Cookie("key","value");
```
- 手动指定cookie的过期时间,单位秒

设置了cookie的过期时间之后,就算浏览器关闭了,这个cookie也会保存在浏览器中.下次访问网站的时候,也会携带这个cookie.这个超时时间是绝对时间,不会因为再次访问网站而刷新
```java
    c1.setMaxAge(30);
```
- 配置cookie在哪个url生效
```java
    c1.setPath("/login");
```
&emsp;通过设置后,这个cookie只会在访问`/login`地址或者`/login子路径`的时候发送

- 设置cookie在哪个域名下生效
```
    c1.setDomain("localhost")
```
![image](https://ws4.sinaimg.cn/large/006tNbRwly1furo04bl5sj30bb04jt9c.jpg)

- 将cookie添加到响应中
```java
    resp.addCookie(c1);
```

#### 说明:
- cookie最大为4kb.一次请求或响应cookie最大为20个,一个服务端最多要求浏览器缓存20个cookie
- 正常情况下,一个浏览器最多能保存300个cookie.但是基本没有正常的浏览器(随便存)
- 默认情况下在浏览器关闭的时候,会把cookie清除掉.

## 在web.xml文件中设置session和cookie的存活时间
```xml
    <session-config>
    <!-- 单位为分钟  -->
        <session-timeout>30</session-timeout>
        <cookie-config>
        <!-- 单位为秒 -->
            <max-gae>6000</max-age>
        <cookie-config>
    </session-config>
```
