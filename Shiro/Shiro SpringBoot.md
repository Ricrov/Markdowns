## Shiro & SpringBoot 整合

#### 依赖
```java
implementation('org.apache.shiro:shiro-spring:1.3.2')
```

#### 在 SpringBoot 中配置 Shiro
> 在 SpringBoot 中配置 Shiro 可使用 config 类完全代替 shiro.ini

- 创建包 `config` -> 创建类 `ShiroConfig`
- 给予 @Configuration 注解
- 获取到创建的 Realm (@Resource)
- 创建方法 `securityManager()` 并给予 @Bean 注解以供容器调用
- 此方法的主要功能是创建一个 SecurityManager 并将自定义的(成员变量) Realm 放入其中
- 然后将此 SecurityManager 设置到 SecurityUtils 中以供调用
- 注意: 需要配置 SessionManager, 可配置为默认的 DefaultSessionManager
- **特别注意: SecurityManager 的包为 `import org.apache.shiro.mgt.SecurityManager;`**
```java
@Configuration
public class ShiroConfig {

    @Resource
    private SimpleRealm simpleRealm;

    // 让容器调用此方法
    @Bean
    public SecurityManager securityManager() {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(simpleRealm);
        securityManager.setSessionManager(new DefaultSessionManager());
        SecurityUtils.setSecurityManager(securityManager);
        return securityManager;
    }
}
```

#### Repository 层
- 创建示例接口 UserInfoRepository 实现 JpaRepository 接口并给予 @Component 注解
```java
public interface UserInfoRepository extends JpaRepository<UserInfo, Integer> {}
```
- 示例方法: 根据用户名查出实体类数据
```java
UserInfo findByUsername(String username);
```

#### Service 层
- 创建实现类 `UserInfoServiceImpl`
- 获取 `Repository` 层对象
- 重写 `login()` 方法
- 进行简单登录判断, 成功则返回 `UserInfo` 对象, 否则返回 `null`
```java
@Service
public class UserInfoServiceImpl implements UserInfoService {
    @Resource
    private UserInfoRepository userInfoRepository;
    @Override
    public UserInfo login(String username, String password) {
        UserInfo userInfo = this.userInfoRepository.findByUsername(username);
        if (userInfo != null && userInfo.getPassword().equals(password)) {
            // 查询到了用户信息
            return userInfo;
        }
        return null;
    }
}
```

#### 创建自定义 Token
- 供 Realm 使用, 实现接口 `AuthenticationToken`
- 声明成员变量用户名和密码, 给予全参构造方法
```java
public class SimpleUsernameToken implements AuthenticationToken {
    private String username;
    private String password;

    public SimpleUsernameToken(String username, String password) {
        this.username = username;
        this.password = password;
    }

    @Override
    public Object getPrincipal() { return this.username; }

    @Override
    public Object getCredentials() { return this.password; }
}
```

#### 创建自定义 Realm
- 创建 `SimpleRealm` 类继承 `AuthorizingRealm` 并给予 @Component 注解
```java
@Component
public class SimpleRealm extends AuthorizingRealm {}
```
- 注入 `Service` 层对象 `UserInfoService` 以便调用其登录方法进行认证
```java
@Resource
private UserInfoService userInfoService;
```
- 重写 `getName()` 方法, 设置 Realm 名为 `"simpleRealm"`
```java
@Override
public String getName() { return "simpleRealm"; }
```
- 重写 `supports()` 方法, 设定使用创建的自定义 Token: `SimpleUsernameToken`
```java
@Override
public boolean supports(AuthenticationToken token) {
    return token instanceof SimpleUsernameToken;
}
```
- 认证
```java
@Override
protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token)
      throws AuthenticationException {}
```
  - 从 `Token` 中获取用户名和密码
  ```java
  String username = (String) token.getPrincipal();
  String password = (String) token.getCredentials();
  ```
  - 调用 `UserInfoService` 的 `login()` 方法
  ```java
  UserInfo user = this.userInfoService.login(username, password);
  ```
  - 若 UserInfo 对象不为空, 则说明已从数据库中查询到数据.
  - 为了方便之后的调用, 将此对象存入 Principal 中.
  - 将密码存入 Credentials, 将 Realm 名存入 realmName 中.
  - 将 SimpleAuthenticationInfo 对象返回.
  ```java
  if (user != null) {
      // 说明从数据库中能查到用户信息
      // 为了方便之后的调用, 主要信息存入该用户的详情(UserInfo 对象)
      SimpleAuthenticationInfo info = new SimpleAuthenticationInfo(user, password, getName());
      return info;
  }
  ```
  - 若认证失败, 返回异常
  ```java
  throw new AuthenticationException("登录失败!");
  ```

- 授权
```java
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {}
```
  - 获取 `subject` 对象
  ```java
  Subject subject = SecurityUtils.getSubject();
  ```
  - 由于上面认证时 Principal 存储的是 UserInfo 对象, 因此取出的 Principal 就是此对象
  ```java
  UserInfo user = (UserInfo) subject.getPrincipal();
  ```
  - `UserInfo` 对象中拥有着角色和权限的 List, 遍历将角色和权限取出
  - 声明 `SimpleAuthorizationInfo` 对象, 将此用户的角色和权限放入 `UserInfo` 中
  ```java
  SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
  /* 遍历 UserInfo 中的每一个角色和权限 role 对象 */
  for (SysRole role : user.getRoleList()) {
      // role.getRole() 方法获得每一个角色, 添加到 SimpleAuthorizationInfo 中
      info.addRole(role.getRole());
      // role.getPermissions() 获得权限集合, 遍历
      // 将每一个权限添加到 SimpleAuthorizationInfo 中
      for (SysPermission permission : role.getPermissions()) {
          info.addStringPermission(permission.getPermission());
      }
  }
  ```
  - 将 `SimpleAuthorizationInfo` 对象返回
  ```java
  return info;
  ```
