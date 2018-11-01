## Gradle

- 相关配置

> gradle模板与maven模板有一样的作用gradle中的`build.gradle`与maven中的`pom.xml`作用一致用于注入依赖.

> gradle下载的依赖包都保存在`.gradle`文件夹中

> 使用Jpa之前,需要在`application.properties`中配置数据源

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/db_netctoss?serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=root
```
_**配置文件的数据库连接后面必须加上时区配置**_

## REST
> 利用rest包加JPA可以直接使用Restful风格的url代替JPA简单SQL操作(不需要写Controller)

#### 相关配置
- application.properties
  `spring.data.rest.base-path=` 在主页后拼接等号后的内容, 以便分别哪些是 Restful 风格, 哪些是普通的 JPA 操作.

#### Repository 层自定义查询方法

- Repository 接口可以自定义请求地址
  > 注解 `@RepositoryRestResource(path = "自定义")`

- 方法名可以自定义请求地址
  > 注解 `@RestResource(path = "自定义")`

- 地址栏发送查询请求时, 拼接地址需要加 /search
  > 例: `localhost:8080/roleList/search/findByName?roleName=名字`

```java
@RepositoryRestResource(path = "roleList")
public interface RoleInfoRepository extends JpaRepository<RoleInfo, Long> {

    // http://localhost:8080/roleList/search/findByName?roleName='名字'
    @RestResource(path = "findByName")
    List<RoleInfo> findByRoleNameContains(@Param("roleName") String roleName);

    // http://localhost:8080/api/roleList/search/findByIdBetween?start=100&end=300
    @RestResource(path = "findByIdBetween")
    List<RoleInfo> findByRoleIdBetween(@Param("start") Long start, @Param("end") Long end);

}
```

## JPA

### 基本查询
#### 1-1 预先生成方法
> `spring data jpa` 默认预先生成了一些基本的 CURD 的方法, 例如: 增、删、改等等
#### 1-2 自定义简单查询
>自定义的简单查询就是根据方法的名来自动生成SQL,主要的语法是: `findXXBy`, `readXXBy`, `queryXXBy`, `countXXBy`, `getXXBy`后面跟属性的名称

- 例: 根据`userName` 查找到某个 `User` 对象
```java
    User findByUserName(String userName)
```
- 也可以使用一些关键字 `And`(和)、`Or`(或)、`firstn`(前n条数据)
```java
    根据用户的名字或者邮箱查询到某个用户
    User findByUserNameOrEmail(String username, String email);
```
- 修改、删除、统计也是类似语法
 ```java
    Long deleteById(Long id);

    Long countByUserName(String userName)
 ```
- 基本上SQL体系中的关键词都可以使用，例如：LIKE、 IgnoreCase、 OrderBy。
```java
    List<User> findByEmailLike(String email);

    User findByUserNameIgnoreCase(String userName);

    List<User> findByUserNameOrderByEmailDesc(String email);
```
![image](https://s1.ax1x.com/2018/10/31/iRXtpt.png)
![image](https://s1.ax1x.com/2018/10/31/iRXaX8.png)


#### 配置文件 application.properties

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/db_jpa_demo?serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=root

可以指定某 sql 文件为创建表的 sql 文件
spring.datasource.schema=classpath:schema.sql

可以指定某 sql 文件为操作数据的 sql 文件
spring.datasource.data=classpath:data.sql

开发过程中, 表的结构一般不可以被改变
连接数据库的账号不一定都有操作表的权限
指定可以更改表结构的账号
spring.datasource.schema-username=root
spring.datasource.schema-password=root

指定可操作除更改以外权限的账号
spring.datasource.data-username=test
spring.datasource.data-password=test

每次启动项目时都会对数据库进行初始化
spring.datasource.initialization-mode=always
```

#### 自动生成表 & 实现多对多查询
- RoleEntity

```java
@Entity
// 因自动创建表, 故需指定表名
@Table(name = "role_info")
public class RoleEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long roleId;

    @Column(name = "role_name", unique = true, nullable = false, length = 50)
    private String roleName;

    private Integer deleteFlag;

    // 配置多对多关系需初始化对象集合
    @Getter
    @Setter
    // 多对多关系注解
    @ManyToMany
    // 表示和哪个表关联
    @JoinTable(
            name = "role_module", // name: 中间表表名, 若没有则自动创建
            joinColumns = @JoinColumn(name = "temp_role_id"), // 中间表的列名, 与当前表的主键相关联
            inverseJoinColumns = @JoinColumn(name = "temp_module_id") // 中间表的另一个列名, 与另一张表的主键相关联
    )
    private List<ModuleEntity> moduleList = new ArrayList<>();
}
```

- ModuleEntity

```java
@Entity
@Setter // Lambok 自动生成 Setter
@Getter // Lambok 自动生成 Getter
@NoArgsConstructor // Lambok 自动生成无参构造方法
@AllArgsConstructor // Lambok 自动生成全参构造方法
@Table(name = "module_info")
public class ModuleEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long moduleId;

    @Column(name = "module_name", unique = true, nullable = false, length = 50)
    private String moduleName;

}

```

- RoleInfoRepository
> 书写 native SQL 语句: nativeQuery

```java
public interface RoleInfoRepository extends JpaRepository<RoleEntity, Long> {
    // nativeQuery = true 时, 可以书写原生 SQL 语句
    @Query(value = "SELECT * FROM role_info", nativeQuery = true)
    List<RoleEntity> roles();
}
```

- Controller

```java
@RestController
public class RoleInfoController {

    @Resource
    private RoleInfoRepository roleInfoRepository;

    @RequestMapping("/list")
    public List<RoleEntity> list() {
        return roleInfoRepository.findAll();
    }

}
```
