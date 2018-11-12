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

## 更多
#### 自定义查询语句返回自定义数据
> 注: 必须给予别名以和实体类相对应

```java
public interface RoleInfoRepository extends JpaRepository<RoleEntity, Long> {
    // 注意: 筛出的每一列都需要给予别名, 否则不能被识别
    @Query (value = "SELECT role.role_id AS roleId, role.role_name AS roleName, COUNT(rm.temp_module_id) AS moduleCount " +
            "FROM role_info role LEFT JOIN role_module rm ON role.role_id = rm.temp_role_id " +
            "GROUP BY role.role_id WHERE role.role_id = :参数", nativeQuery = true)
    List<RoleEx> roleGroup();

    // 自定义查询方式2, 不给予 @Query 注解. 通过创建类的方式.
    List<?> queryTest();

}
```

#### 自定义查询的另一种方式: Repository 中创建类, 类名在接口的基础上 + Impl
> 给予 @Repository 注解后系统会自动把此类当做接口的实现类

```java
@Repository
public class RoleInfoRepositoryImpl {

    // 系统会注入 EntityManager 对象, 通过此对象执行 SQL 语句
    @Resource
    private EntityManager entityManager;

    // 手动实现 RoleInfoRepository 的方法
    public List<?> queryTest() {

        // 手动查询所有
        String sql = "SELECT * FROM role_info";
        Query query = entityManager.createNativeQuery(sql, RoleEntity.class);
        List list = query.getResultList();

        return list;
    }

}
```

#### 针对自定义查询创建的接口
```java
public interface RoleEx {
    // 因返回结果有 3 列, 故书写 3 个抽象方法
    Long getRoleId();

    String getRoleName();

    Integer getModuleCount();
}
```

#### Controller 中补全方法
```java
@RestController
public class RoleInfoController {
    @Resource
    private RoleInfoRepository roleInfoRepository;

    @RequestMapping("/listGroup")
    public List<RoleEx> listGroup() {
        return this.roleInfoRepository.roleGroup();
    }

    @RequestMapping("/autoList")
    public List<?> autoList() {
        return this.roleInfoRepository.queryTest();
    }
}
```
