## Token

#### 依赖 (Gradle)
`implementation('com.auth0:java-jwt:3.4.0')`

#### Token 定义
```
Token: 令牌
JWT: Json Web Token, 是一种标准.
java-jwt 框架, 是 Java 实现了 JWT 标准的一个框架. 其他类似框架有 JJWT 等.

JWT 标准: Token 由三部分组成. 每部分之间使用`.`分隔
    第一部分: 头(Header)
    保存了该 Token 的加密方式 (以何算法加密的)

    第二部分: 负载(Payload)
    保存了用户信息/Token 信息 (id/过期时间等)

    第三部分: 校验信息(Verify Signature)

第一部分和第二部分是 Json 格式的数据, 使用 Base64 编码后的信息.
注意: 该部分信息是明文, 不能存储敏感数据.
```

#### 测试
- 创建一个密钥

```java
public static final String SECRET_KEY = "harajkq15a";
```
- 创建 Token 方法

```java
public void createToken() {
    String token = JWT.create()
            // 在负载(Payload)中添加自定义数据
            .withClaim("userId", 10001)
            .withClaim("username", "hello")
            // 设置该 token 的发布时间 (可以记录上次登录的时间)
            .withIssuedAt(new Date())
            // 设置该 Token 的过期时间
            // 10秒钟后, 该 Token 过期
            .withExpiresAt(new Date(System.currentTimeMillis() + 10000))
            // 设置算法, 放入密钥 (sign() 最后调用)
            .sign(Algorithm.HMAC256(SECRET_KEY));
/*
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1NDEwNDEzMzUsInV
zZXJJZCI6MTAwMDEsImlhdCI6MTU0MTA0MTMyNSwidXNlcm5hbWUiOiJoZWxsbyJ9.D
GYwqT1RpgtybSargqsqW1IVEKy4WaknHiwJp60RFt4
*/
```

- 校验此密钥

```java
@Test
public void required() {
    // 验证器
    JWTVerifier verifier = JWT.require(Algorithm.HMAC256(SECRET_KEY))
            .build();

        // 使用校验校验前端传递过来的 Token 是否合法
        // 若合法, 则返回解码后的 JWT 对象
        // 若不合法, 会抛出各种异常
    try {
        DecodedJWT jwt = verifier.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
        .eyJleHAiOjE1NDEwNTI4OTgsInVzZXJJZCI6MTAwMDEsImlhdCI6MTU0MTA0Mjg5OCwid
        XNlcm5hbWUiOiJoZWxsbyJ9.0bcySWwfGLWvHMfyCf-qo0nGE-M4LAUCdxcpD-Cukac");
        System.out.println("Header = " + jwt.getHeader());
        System.out.println("Payload = " + jwt.getPayload());
    } catch (TokenExpiredException e) {
        System.out.println("Token 已过期");
    } catch (SignatureVerificationException e) {
        System.out.println("Token 不合法");
    } catch (Exception e) {
        System.out.println("认证失败");
    }

}

@Test
public void json() throws InterruptedException {
    String token = JwtUtils.newToken(1001L, 10);
    System.out.println("原 token = " + token);
    Thread.sleep(7000);
    String newToken = JwtUtils.autoRequire(token);
    System.out.println("新 token = " + newToken);
}
```

#### 将此 Token 校验工具封装成工具类

- 创建 utils 常量类 Constants 以收纳密钥和过期时间

```java
public class Constants {
    public static final String SECRET_KEY = "fganjirakl1t9fasf";
    public static final long DEFAULT_EXPIRED_SECONDS = 30 * 60;
}
```

- 创建 utils 工具类 JwtUtils

```java
// JWT 工具类
public class JwtUtils {}
```

- 单参(id)构造方法, 返回一个设置好的新 Token

```java
    /**
     * 默认过期时间: 30分钟
     * @param userId
     * @return token
     */
    public static String newToken(Long userId) {
        // 创建默认的 Token, 时效 30 分钟.
        return newToken(userId, Constants.DEFAULT_EXPIRED_SECONDS);
    }
```

- 创建一个 Token
  - JWT.create(): 开始创建`token` <br>
  - withClaim(): 在负载中添加自定义的数据, 保存用户的信息. 保存几条信息就调用几次该方法. <br>
  - withIssuedAt(): 设置`token`的发布时间, 可以用来`稍微`记录上次的登录时间. (在不刷新token的情况下) <br>
  - withExpiresAt(): 设置该`token`的过期时间, 单位是毫秒. 测试用的设置的为 10 秒. <br>
  - sign(): 签名, 设置加密的方法. 调用Algorithm的静态方法, 方法参数为最先定义好的服务器密钥. <br>

```java
    /**
     * 创建 token 方法
     * 一般情况下会传入用户的 id 和过期的时间
     * @param userId 用户 id
     * @param expiredSeconds 过期时长
     * @return token
     */
    public static String newToken(Long userId, long expiredSeconds) {
        return JWT.create()
                .withClaim("userId", userId)
                .withExpiresAt(new Date(System.currentTimeMillis() + expiredSeconds * 1000))
                .sign(Algorithm.HMAC256(Constants.SECRET_KEY));
    }
```

- 验证 Token 方法 verifyToken()
  > 功能: 创建验证器.[方法 JWT.require(算法(密钥)).build()] <br>
  > 使用验证器对获取到的用户传递的 Token 数据进行验证. 若验证失败则自动抛出异常. <br>
  > 故进行 try...catch, 验证通过返回 true; 若异常返回 false.

```java
    /**
     * 校验 Token 是否合法
     * @param token 需校验的 Token
     * @return 是否合法
     */
    public static boolean verifyToken(String token) {
        JWTVerifier verifier = JWT.require(Algorithm.HMAC256(Constants.SECRET_KEY))
                .build();
        try {
            verifier.verify(token);
            return true;
        } catch (JWTVerificationException e) {
            e.printStackTrace();
            return false;
        }
    }
```

- 刷新 Token 方法 refreshToken()
  > 功能: 获取用户传递的 Token 数据, 并以此获取出用户ID, 生成新的 Token 返回.

```java
    /**
     * 刷新 Token
     * @param token 原 Token
     * @return 新 Token
     */
    public static String refreshToken(String token) {
        // 解析出 header 和 payload
        DecodedJWT jwt = JWT.decode(token);
        // 获取到负载中的 userId
        Long userId = jwt.getClaim("userId").asLong();
        // 针对此用户创建新的 token
        return newToken(userId);
    }
```

- 将验证和刷新方法整合实现自动刷新功能
  - 进行验证

  > 验证结果为 true: <br>
  > 获取 Token 创建时间`"iat"`; 获取当前时间(秒); 获取时效时间`"exp"`; <br>
  > 若已经过的时间(即 current - start)的值超过了设定的过期总时间(即 end - start)的50%, <br>
  > 则返回刷新的 Token, 否则返回原来的 Token.

  > 验证结果为 false: <br>
  > 即说明验证未通过, 则抛出异常(附带自定义异常信息)以便在全局异常中处理.

```java
    public static String autoRequire(String token) {
        boolean check = verifyToken(token);
        if (check) {
            // 计算时间是否超过 50% (秒单位)
            Long start = JWT.decode(token).getClaim("iat").asLong();
            long current = System.currentTimeMillis() / 1000;
            Long end = JWT.decode(token).getClaim("exp").asLong();

            if ((current - start) * 1.0 / (end - start) > 0.5) {
                return refreshToken(token);
            } else {
                return token;
            }

        } else {
            throw new JWTVerificationException("token 不合法");
        }
    }
```

- 拦截器配置类 InterceptorConfiguration
  - 给与注解 @Configuration 或 @SpringBootConfiguration
  - 获取具体的拦截器类对象 loginInterceptor 并给与 @Resource 注解
  - 重写方法 addInterceptors()
  - 定制拦截对象

```java
@SpringBootConfiguration
public class InterceptorConfiguration implements WebMvcConfigurer {

    @Resource
    private LoginInterceptor loginInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // addPathPatterns 即 需要拦截的地址. 可以使用通配符.
        registry.addInterceptor(loginInterceptor).addPathPatterns("/index");
        // 可设置排除地址.
        registry.addInterceptor(loginInterceptor).excludePathPatterns("/login");
    }
}
```

- 创建拦截器 LoginInterceptor
  - 加入 `@Component` 注解
  - 获取通过请求头传递过来的 Token 进行验证
  - 若想在 response 中返回 Json 数据, 需设置 `ContentType` 为 `"application/json;charset=utf-8"`
  - 验证通过则根据时间返回新的 Token 或返回原 Token
  - 验证失败则拦截, 可通过全局异常处理的 MV 返回给前端

```java
@Component
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request,
    HttpServletResponse response, Object handler) throws Exception {
        response.setContentType("application/json;charset=utf-8");
        String token = request.getHeader("Authentication");

        // 若未登录则抛出异常
        if (token == null || token.trim().equals("")) {
            throw new NullPointerException("请登录");
        }
        String newToken = JwtUtils.autoRequire(token);
        response.addHeader("NewToken", newToken);
        return true;

    }
}
```

- 全局异常处理
  - 创建全局异常处理类 GlobalExceptionResolver
  - 实现 HandlerExceptionResolver 接口
  - 给予 @Component 注解
  - 重写 resolveException() 方法
  - 声明 ModelAndView
  - 将其视图设置为 Json 格式
  - 自定义异常数据

```java
@Component
public class GlobalExceptionResolver implements HandlerExceptionResolver {

    @Override
    public ModelAndView resolveException(HttpServletRequest request,
    HttpServletResponse response, Object handler, Exception ex) {

        // 声明 ModelAndView
        ModelAndView mv;

        if (ex instanceof JWTVerificationException || ex instanceof NullPointerException) {
            mv = new ModelAndView();
            // 设置视图为 json 格式
            mv.setView(new MappingJackson2JsonView());
            mv.addObject("status", false);
            mv.addObject("code", 250);
            mv.addObject("message", ex.getMessage());
            return mv;
        }
        return null;
    }
}
```

- Controller & Service 略 -> 补全 ResultWrapper 以统一传递数据格式.
