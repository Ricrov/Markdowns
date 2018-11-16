## 过滤器Filter
![image](https://ws1.sinaimg.cn/large/006tNbRwly1fury0hzimaj30p30bs764.jpg)

### 创建一个Fifter,需要实现Filter接口(导入javax.sevlet包下的Filter),需要重写三个方法
```java
public class TestFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // 生命周期方法
        // 当这个过滤器对象创建的时候执行,一个过滤器只会创建一个对象,也就是这个方法只会执行一次

        System.out.println("TestFilter.init");  // 快捷键 soutm
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
                        //  请求对象                        响应对象                                链

        System.out.println("在放行之前执行");
        // 放行:  直接把请求和响应放行
        filterChain.doFilter(servletRequest,servletResponse);
        System.out.println("在放形之后执行");
    }

    @Override
    public void destroy() {
        // 生命周期方法
        // 当这个过滤器对象销毁的时候会执行该方法
        System.out.println("TestFilter.destroy");

    }
}
```

### 在web.xml文件中对过滤器进行注册
```xml
    <filter>
        <description>这个过滤器的描述标签,该描述标签必须写在第一行</description>
        <filter-name>filter的名字</filter-name>
        <filter-class>filter所对应的类</filter-class>
        <init-param>
            <param-name>charset</param-name>
            <param-value>utf-8<param-value>
        </init-param>
    </filter>

    <filter-mapping>
        <filter-name>filter的名字字</filter-name>
        <!-- 确定过滤哪些url, /* 代表全部 -->
        <url-pattern>/*</url-pattern>
        <!-- 默认请求转发的不过滤,设置完本标签后同样过滤 -->
        <dispatcher>FORWARD</dispatcher>
        <!--  过滤跳转错误的页面 -->
        <dispatcher>ERROR</dispatcher>
    </filter-mapping>
```
### 创建一个过滤器的基类
- 为了方便使用,自己创建一个过滤器的基类.
- 方便之处:
    + init和destory两个方法可以根据需要重写
    + doFilter的参数让它支持Http协议
```java
    public class TestFilter implements Filter {   // Fifter 导javax.servlet的包
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // 生命周期方法
        // 当这个过滤器对象创建的时候执行,一个过滤器只会创建一个对象,也就是这个方法只会执行一次

        System.out.println("TestFilter.init");  // 快捷键 soutm
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
                        //  请求对象                        响应对象                                链

        System.out.println("在放行之前执行");
        // 放行:  直接把请求和响应放行
        filterChain.doFilter(servletRequest,servletResponse);
        System.out.println("在放形之后执行");
    }

    @Override
    public void destroy() {
        // 生命周期方法
        // 当这个过滤器对象销毁的时候会执行该方法
        System.out.println("TestFilter.destroy");

    }
}
```

### 通过写注释的方式注册过滤器
![image](https://ws3.sinaimg.cn/large/006tNbRwly1furyx9uhs7j30kp019mxf.jpg)

## 扩展resp的功能
![image](https://ws4.sinaimg.cn/large/006tNbRwly1furz2qns79j30q30e6tbf.jpg)

```java
@WebFilter(filterName = "JsonCacheFilter",urlPatterns = "*.json")
    public class JsonCacheFilter extends HttpFilter {

    private Map<String,String> cache = new HashMap<>();

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // 转为线程安全的Map
       cache = Collections.synchronizedMap(cache);
    }

    @Override
    public void doFilter(HttpServletRequest req, HttpServletResponse resp, FilterChain chain) throws IOException, ServletException {
        /** 功能:
         *      查询数据的时候,只有第一次请求会去数据库中做查询,
         *      以后如果想要请求同样的数据,从缓存中取.
         *
         *      根据请求的地址和请求的参数就可以区分不同的数据,
         *      所以我们创建一个map,用来缓存数据. key为网址和请求参数,value为对应的json数据
          */

        resp.setContentType("application/json;charset=utf-8");

        /** 问题:
         *     1. 如何生成这个key
         *       key的规则: 请求方法:url?请求参数
         */
        String method = req.getMethod();

        if ("get".equalsIgnoreCase(method)){
            // 如果是GET请求才过滤
            String key = parseRequestTokey(req);
            String json = cache.get(key);

            if (json == null){
                // 代表这个地址没有缓存

                // 扩展响应的功能
                HttpCacheResponse response = new HttpCacheResponse(resp);
                // 把增强功能的response对象放行
                chain.doFilter(req,response);
                // 2. 如何从响应中拿到数据,拿到数据之后存到cache中
                String jsonData = response.getResponseBody();

                cache.put(key,jsonData);

                //把数据返回给前端: 把数据写到原始的输出流中
                resp.getWriter().println(jsonData);
            } else{
                 // 拿到了缓存
                resp.getWriter().println(json);
            }
            return;
        } else {
            chain.doFilter(req,resp);
        }
    }

    private String parseRequestTokey(HttpServletRequest req){
        // 拼接网址
        String method = req.getMethod();
        String requestURI = req.getRequestURI();
        Map<String, String[]> parameterMap = req.getParameterMap();

        StringBuilder sb = new StringBuilder();
        sb.append(method).append(":")
                .append(requestURI)
                .append("?");

        for (Map.Entry<String, String[]> entry : parameterMap.entrySet()) {
            String key = entry.getKey();
            String[] values = entry.getValue();

            for (String value : values) {
                sb.append(key).append("=").append(value).append("&");
            }
        }
        // 删除最后一个 & 符号
        sb.deleteCharAt(sb.length() - 1);

        return sb.toString();
    }
}
```
## urlPatterns匹配规则
- /* 匹配所有网址
- /abc/def/* 匹配/abc/def/ 后面任意的网址, 例如: /abc/def/dfdfdfd/fdfd
- *.jsp 匹配所有以.jsp结尾的网址, 例如: dsds.jsp、fdf/gh/aa.jsp
- 要么就写一个具体的网址,这样情况下只匹配这一个网址

## web.xml文档中设置欢迎页
欢迎页:既tomcat服务器启动后访问的首页,默认是index.jsp
```xml
    <welcom-file-list>
        <welcom-file>设置的访问页</welcom-list>
    </welcom-file-list>
```
