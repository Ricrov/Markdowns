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

#### REST 依赖包
`implementation('org.springframework.boot:spring-boot-starter-data-rest')`

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
