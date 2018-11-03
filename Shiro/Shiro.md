## Shiro

配置文件
```ini
[users]
# 格式: 用户名 = 密码, 角色1, 角色2, ... 角色n
admin = admin, admin
superAdmin = admin123, admin
moneyManager = money123, money

[roles]
# admin 角色拥有财务的所有权限和管理员的所有权限
admin = money:*, admin:*
# money 角色拥有查询和更新财务权限
money = money:update, money:query
```

### Realm

- 创建 TestRealm 实现 Realm (Shiro 包)
  - 注: Realm 接口是顶层接口, 方法有限. 接下来会实现其子类接口.
```java
public class TestRealm implements Realm {}
```

- 重写 `getName()` 方法
  - `getName()` : 给此自定义 Realm 起名以供内部使用
  ```java
  @Override
  public String getName() {
      return "TestRealm";
  }
  ```
- 重写 `support()` 方法
  - `supports(AuthenticationToken token)` : 判断自定义的 Realm 是否支持某个 Token
    > 前提: 在登录时, 需要调用 login() 方法, 并传递一个 Token 对象. <br>
    > 如果把该 Realm 设置给 SecurityManager, 那么在使用 Shiro 登录时,
    > 登录的逻辑就在该 Realm 里. <br>
    > 故需判断该 Realm 是否可以处理 login() 时传递进来的 Token. <br>

    ```java
    @Override
    public boolean supports(AuthenticationToken token) {
        return token instanceof UsernamePasswordToken;
    }
    ```
- 重写 `getAuthenticationInfo()` 方法
  - `getAuthenticationInfo(AuthenticationToken token)` : 查找数据库并进行判断
  > 若上方 supports() 符合逻辑, 此方法才会被调用, 查询数据库. <br>
  > token 和 supports() 方法中的 Token 是同一对象 <br>

    ```java
    @Override
    public AuthenticationInfo getAuthenticationInfo(AuthenticationToken token)
    throws AuthenticationException {
        // 获取用户名和密码 (注意密码需要先转为 char[])
        String username = (String) token.getPrincipal();
        char[] tempPassword = (char[]) token.getCredentials();
        String password = new String(tempPassword);
        // 查询数据库
        if ("admin".equals(username) && "admin".equals(password)) {
            // 登录成功
            AuthenticationInfo info =
                    new SimpleAuthenticationInfo(username, password, getName());
            return info;
        } else {
            // 登录失败
            throw new AuthenticationException("用户名或密码不正确!");
        }
    }
    ```

- 配置文件中配置此 Realm
  - Shiro.ini
```ini
[main]
# 定义 TestRealm
TestRealm = com.ric.demo.shiro.TestRealm
securityManager.realm = $TestRealm
```

### AuthorizingRealm
##### 配置多个 Realm
```ini
[main]
# 定义多个 Realm
TestRealm = com.ric.demo.shiro.TestRealm
RoleRealm = com.ric.demo.shiro.RoleRealm
securityManager.realms = $TestRealm, $RoleRealm
```

##### 创建新的 Realm 继承 AuthorizingRealm
```java
public class RoleRealm extends AuthorizingRealm {}
```
##### 创建自定义的 SimpleToken 实现 AuthenticationToken 接口
```java
public class SimpleToken implements AuthenticationToken {}
```
- 接下来在登录时就可使用自定义的 Token

##### RoleRealm
- 重写 getName() 和 support() 以及其他两个必须重写方法
  - `getName()` -> 设定自定义 Realm 名
    ```java
    @Override
    public String getName() {
        return "RoleRealm";
    }
    ```
  - `supports()` -> 判定是否是自定义 Token: 如果使用 SimpleToken 来登录, 则由该 Realm 处理
    ```java
    @Override
    public boolean supports(AuthenticationToken token) {
        return token instanceof SimpleToken;
    }
    ```

  - 认证方法 `doGetAuthenticationInfo()` -> 此方法可根据传递的 `Token` 获取用户名密码并验证
    - 当有方法调用 `SecurityUtil.getSubject().login(token)` 方法时需要传输一个 `Token` 对象.
    - 本方法也会随着 `.login()` 方法的执行而执行并获取到此 `Token` 对象.
    - 故可对此 `Token` 进行操作取出账号密码, 与数据库比对后校验.
    - 若校验通过, 可创建一个 `SimpleAuthenticationInfo` 对象并将 `principal`、`Credentials`、和 `Realm` 名传入并返回.
    - 此处的 `principal` 可以是任何类型. 可以只放入用户名, 亦可放入一个用户实体类对象. 后者的好处就是可以在任何地方取出并使用.
    - 若校验不通过, 可返回一个自定义异常并传输自定义异常数据.
    - 将此对象返回后, 调用 `.login()` 方法的方法即可接收到用户数据或异常信息.
    - (p.s:另外通过下方的授权用方法可将角色权限一并传出)
    ```java
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token)
    throws AuthenticationException {
        // 获取用户名和密码
        String username = (String) token.getPrincipal();
        String password = (String) token.getCredentials();
        // 查询数据库 -> 此处使用测试数据
        if ("role".equals(username) && "123".equals(password)) {
            // 登录成功
            AuthenticationInfo info =
                    new SimpleAuthenticationInfo(username, password, getName());
            return info;
        } else {
            // 登录失败
            throw new AuthenticationException("用户名或密码不正确!");
        }
    }
    ```

  - 授权方法 `doGetAuthorizationInfo()` -> 此方法可根据传递来的用户信息查询数据库并注入角色权限
    - 当有方法调用 `SecurityUtils.getSubject().isPermitted(String str)` 方法时需要传输一个权限字符串(组).
    - 本方法会随着 `.hasRole()` 以及 `.isPermitted()` 方法的执行而执行.
    - 注意: 目前得知若想获得用户信息最好是使用工具类 `SecurityUtils.getSubject()` 获得 `Subject` 对象之后, 使用 subject.getPrincipal() 来获取用户信息. 根据上方认证方法传入的数据类型进行强转.
    - 用获取到的用户信息从数据库查询出此用户拥有的角色(roleList)和权限(permissionList)
    - 创建 SimpleAuthorizationInfo 对象, 利用 `addRole()` 方法和 `addStringPermission()`方法放入角色权限信息.
    - 将此对象返回. 此时外部调用 `.hasRole()` 或 `.isPermitted()` 方法即可取出角色或权限.
    ```java
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        // 从数据库查询出当先登录用户的所有角色和权限

        // 1-1. 获取到登录用户
        Subject subject = SecurityUtils.getSubject();
        // 1-2. 获取到用户名
        String username = (String) subject.getPrincipal();
        System.out.println("用方法获取到的用户名: " + username);
        System.out.println("用参数获取到的用户名: " + principals);
        // 1-3. 根据用户名找到所有权限(查询数据库)
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        // 2. 为当前用户添加角色和权限(仮)
        info.addRole("admin");
        info.addRole("money");
        info.addStringPermission("admin:insert");
        info.addStringPermission("admin:query");
        info.addStringPermission("money:delete");
        info.addStringPermission("money:update");

        // 3. 返回此 info 对象
        return info;
    }
    ```


##### SimpleToken
- 此自定义 `Token` 实现 `AuthenticationToken` 接口
> AuthenticationToken 用于收集用户提交的身份（如用户名）及凭据（如密码） <br>
> 此接口拥有两个扩展接口 `HostAuthenticationToken` `RememberMeAuthenticationToken` <br>
> 前者提供了`boolean isRememberMe()` "记住我"的功能 <br>
> 后者提供了`String getHost()` 获取用户"主机"的功能 <br>

- 若登录时使用自定义的 `SimpleToken` 对象, 则通过构造方法将用户名密码传递至成员变量
- 这样在 Realm 的认证方法中即可通过参数拿到此对象并获得相应信息
  ```java
  @Data
  @NoArgsConstructor
  @AllArgsConstructor
  public class SimpleToken implements AuthenticationToken {

      private String username;
      private String password;

      @Override
      public Object getPrincipal() {
          return this.username;
      }

      @Override
      public Object getCredentials() {
          return this.password;
      }
  }
  ```

### 测试

- 创建工厂, 加载配置文件
```java
Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");
```

- 使用工厂对象, 创建 SecurityManager
```java
SecurityManager securityManager = factory.getInstance();
```

- 把 securityManager 设置给工具类
```java
SecurityUtils.setSecurityManager(securityManager);
```

- 获取当前的用户
  - 若当前没有人登录过, 那么该方法获取的是一个临时用户 <br>
  - 如果在登录之后调用该方法, 则获取的是当前登录的用户 <br>
```java
Subject currentUser = SecurityUtils.getSubject();
```

- 判断当前用户是否已经认证
  - 登录方法若没有抛出异常说明登录成功
  - 判断角色和权限 `currentUser.hasRole();`
  - 断言 `Assert`, 若正确则通过, 若错误则抛出异常, 多用于测试
```java
if (!currentUser.isAuthenticated()) {
    // 6. 登录
    UsernamePasswordToken token = new UsernamePasswordToken("moneyManager", "money123");

    try {
        // 登录方法若没有抛出异常说明登录成功
        currentUser.login(token);
        System.out.println("登录成功");

        // 7. 判断角色和权限
        System.out.println(currentUser.hasRole("admin")); // true
        System.out.println(currentUser.hasRole("money")); // false
        // 断言
        Assert.assertTrue(currentUser.isPermitted("admin:query"));
        Assert.assertTrue(currentUser.isPermitted("admin:insert"));
        Assert.assertTrue(currentUser.isPermitted("money:query"));
        Assert.assertTrue(currentUser.isPermitted("money:insert"));

    } catch (IncorrectCredentialsException e) {
        System.out.println("密码错误");
    } catch (UnknownAccountException e) {
        System.out.println("用户名错误");
    }
}
```
