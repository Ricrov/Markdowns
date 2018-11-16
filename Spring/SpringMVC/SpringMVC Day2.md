#### 小结
- 在用户自定义的 Controller 方法中, 如果想把后台的参数通过 Model 传递到前台, 不能在方法里面自定义 Model (无法传递至前端). 只能使用参数的形式, 或是注解@ModelAttribute
- @ModelAttribute 如果不写 key, 则默认是数据类型名(首字母小写), 例如: (@ModelAttribute User user)
- 在一个工程里, 不能出现两个相同的 url, 例如:@RequestMapping("/success.action") 不可以出现两次,
但@RequestMapping(value = "/success.action", method = RequestMethod.GET)
和@RequestMapping(value = "/success.action", method = RequestMethod.POST)
可以并存.

- @RequestMapping 可以根据参数指定请求方式

    - @RequestMapping(value = "/success.action", method = RequestMethod.GET)
        - value=""
        - method="RequestMethod.GET/POST"
        - params="page=5"


- 指定 GET 请求
```java
    @RequestMapping(value = "/success.action", method = RequestMethod.GET)
    // @ModelAttribute 如果不写 key, 默认是数据类型名(首字母小写)
    public String success(@ModelAttribute User user) {
        // 具体的登录判断
        System.out.println("---------GET---------");
        return "success";
    }
```
- 指定 POST 请求
```java
    @RequestMapping(value = "/success.action", method = RequestMethod.POST)
    // @ModelAttribute 如果不写 key, 默认是数据类型名(首字母小写)
    public String successs(@ModelAttribute User user) {
        // 具体的登录判断
        System.out.println("=========POST=========");
        return "success";
    }
```

- 同一请求, 不同参数 (※参数值不能有空格!)
```java
    @RequestMapping(value = "/page.action", params = "pagenum=1")
    public String page() {
        return "first";
    }
    @RequestMapping(value = "/page.action", params = "pagenum=2")
    public String page2() {
        return "second";
    }
```
- 同理
```java
    @RequestMapping("/page.action")
    public String page3(String pagenum) {
        String str = null;
        if ("1".equals(pagenum)) {
            str = "first";
        } else if ("2".equals(pagenum)) {
            str = "second";
        }
        return str;
    }
```

#### 虚拟路径
- 路径: 相对路径和绝对路径
    - 对于 linux 和 unix 而言, 绝对路径是指从根目录 "/" 开始的路径
    - 相对路径是相对某个路径而言的路径

- 虚拟路径:
    - 在工程文件夹中并不存在, 而是虚拟出来的目录结构, 方便管理

> 项目中 index.jsp 的路径是 /Users/dllo/IdeaProjects/springmvcDemo2/src/main/webapp/index.jsp, 该路径是 index.jsp 对于硬盘而言的真是路径. 项目部署后, 虚拟路径是 ip:8080/index.jsp, 无论是真是路径或是虚拟路径, 在项目中, 使用 "/" 表示项目的根目录, 即相对硬盘而言的真实路径是 /Users/dllo/IdeaProjects/springmvcDemo2/src/main/webapp/, 相对项目而言的虚拟路径是 ip:8080/

> user/login.action, 对于此虚拟路径而言, 因为项目启动时所在的位置就是 "/", 所以写成 user 和 /user 是一样的.

```java
@Controller
// 如果该模块整体放置到虚拟路径 /user/下面, 可以整体配置(可以理解成更改了此模块的根路径为 /user/)
@RequestMapping("/user/")
public class UserAction {
}
```

- ViewResolver

- 拦截器
