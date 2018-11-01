## Spring Boot & Jedis 整合

#### 依赖
```XML
<dependency>
  <groupId>redis.clients</groupId>
  <artifactId>jedis</artifactId>
  <version>2.9.0</version>
</dependency>
```

##### application.yml
- 等同于 application.properties (书写格式不同)

```yml
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db_netctoss
    username: root
    password: root

  jpa:
    # 显示 SQL 语句
    show-sql: true
    hibernate:
      # 不自动改变 DDL 结构
      ddl-auto: none

  # 序列化输出 Json 数据: 美观
  jackson:
    serialization:
      indent_output: true
```


##### 获得 Jedis 对象
```java
Jedis jedis = new Jedis("127.0.0.1", 6379);
```

##### 使用连接池获得 Jedis 对象

```java
JedisPool pool = new JedisPool();
Jedis jedis = pool.getResource();
```

##### 批量设置值

```java
String status = jedis.mset("a", "1", "b", "2", "c", "3");
System.out.println(status); // 'OK'
System.out.println(jedis.get("a")); // 1
```

#### SpringBoot

- 实体类 Entity

```java
  @Entity // 标明此类为实体类
  @Table(name = "role_info") // 数据库表名
  public class RoleInfo {
      @Id // 表示此属性对应表中主键 id
      @GeneratedValue // 表示此属性自增长
      private Long roleId;
      private String roleName;
  }
```

- Controller

```java
  // {} 表示浏览器发送请求时此位置的值会被作为 roleId 传入
  @RequestMapping("/findOne/{roleId}")
  // 与之呼应, 在方法形参中需要用 @PathVariable 来接取
  public RoleInfo findOne(@PathVariable Long roleId) {
        return this.roleService.findOne(roleId);
    }
```

- Repository <br>
继承 JpaRepository, 给予泛型 <实体类, Id 数据类型>

```java
public interface RoleRepository extends JpaRepository<RoleInfo, Long> {}
```

## 缓存
#### 启用缓存
- 主方法添加注解 @EnableCaching
```java
@SpringBootApplication
@EnableCaching // 此注解开启缓存
public class SpringRedisApplication {}
```

- 实体类需实现序列化接口
```java
public class RoleInfo implements Serializable {}
```

- ServiceImpl 添加注解 @Cacheable
> 此注解并不是针对 redis 的, 全局通用, 归属于 spring boot <br>
> 当添加其他缓存框架 (如 redis) 时, 会优先缓存至框架中

```java
@Cacheable(value = "roleService")
@Override
public List<RoleInfo> findAll() {
  System.out.println("RoleServiceImpl.findAll");
  return this.roleRepository.findAll();
}

@Cacheable(value = "roleService")
@Override
public RoleInfo findOne(Long roleId) {
  System.out.println("RoleServiceImpl.findOne");
  return this.roleRepository.findById(roleId).get();
}
```

数据有更新时, 需要清空某些缓存, 使用注解 `@cacheEvict` <br>
表示当调用此方法时系统会寻找名字为 value 的缓存, 将其清空 <br>
若想删除此 key 相关的所有信息, 则需要添加 `allEntries = true` <br>

```java
@CacheEvict(value = "roleService", allEntries = true)
@Override
public boolean deleteOne(Long roleId) {
  System.out.println("RoleServiceImpl.deleteOne");
  this.roleRepository.deleteById(roleId);
  return true;
}
```

> 此时, 缓存将存储于 Redis.



#### Key 的规则
根据方法参数的值拼接到 Cacheable 的 value 之后 <br>
亦可自定义 key 的规则
`@Cacheable(value = "roleService", key = "#roleId")`

当传入的参数为实体类时 (比如新增) <br>
新增的实体类数据不但存入了数据库, 也同时原封不动的存入 Redis <br>
※ 实际上是从方法的返回值获取到对象的 <br>
注解 @CachePut, key 为 #形参对象.id <br>
```java
@CachePut(value = "roleService", key = "#role.roleId")
@Override
public boolean addOne(RoleInfo role) {
  System.out.println("RoleServiceImpl.addOne");
  RoleInfo info = this.roleRepository.save(role);
  return true;
}
```

## Redis
Spring Boot 中需创建 config (相当于 Spring 中的 xml 配置)
- 创建包 config
- 创建类 RedisConfig
- 加入注解 @Configuration

#### 创建 bean

```java
@Configuration
public class RedisConfig {

    // 泛型: 前面是 key, 后面是 value
    // 根据方法名获取对象
    @Bean
    // 从容器中拿出 redis 连接工厂
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory factory) {
        // 创建 RedisTemplate 对象
        RedisTemplate<Object, Object> template = new RedisTemplate<>();

        // 创建连接工厂
        template.setConnectionFactory(factory);

        // 对 key 进行序列化. 因 Redis 中 key 是 String 类型. 故需序列化 Object 后才能存入 Redis.
        // setKeySerializer(), 告知 Redis key 是如何序列化的.
        template.setKeySerializer(new StringRedisSerializer());
        // setValueSerializer() 值需要序列化成 json 格式.
        // 也可对 jackson 进行自定义序列化后再对 Redis 的 value 进行序列化.
        Jackson2JsonRedisSerializer<Object> valueSerializer = new Jackson2JsonRedisSerializer<>(Object.class);
        // 对 mapper 配置后可自定义 json 的格式.
        ObjectMapper mapper = new ObjectMapper();
        mapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd hh:mm:ss"));
        // 调研:
        // 对象的 null 属性是否需要序列化
        // 是否需要转成更美观的 Json 格式
        valueSerializer.setObjectMapper(mapper);
        template.setValueSerializer(valueSerializer);
        // 在属性配置之后也加载其他配置, 即在 Application.yml 中的配置也会加载.
        template.afterPropertiesSet();
        return template;
    }

}
```

#### 实例:点赞功能
- serviceImpl

```java
// 拿到配置好的 Redis 模板
@Resource
private RedisTemplate<Object, Object> redisTemplate;

// 点赞方法 -- 调用方法即对此 id 点赞
public boolean thumb(Long roleId) {
  // 获取 zset 操作
  ZSetOperations<Object, Object> opsZSet = this.redisTemplate.opsForZSet();
  // 根据 id 拿出对象
  RoleInfo roleInfo = findOne(roleId);
  // 调用自增方法
  // 设置 key, value, score
  opsZSet.incrementScore("thumb:roleId", roleInfo, 1);
  return true;
}

// 查询 1-3 名返回集合
public Set<?> thumbList() {
  ZSetOperations<Object, Object> opsZSet = this.redisTemplate.opsForZSet();
  // Set<Object> set = opsZSet.reverseRange("thumb:roleId", 0, 2);
  Set<ZSetOperations.TypedTuple<Object>> rangeWithScores = opsZSet.reverseRangeWithScores("thumb:roleId", 0, 2);
  return rangeWithScores;
}
```

- Controller

```java
@RequestMapping("/like/{roleId}")
public boolean like(@PathVariable Long roleId) {
  return this.roleService.thumb(roleId);
}

@RequestMapping("/thumbList")
public Set<?> thumbList() {
  return this.roleService.thumbList();
}
```


#### 其他

```java
public class SpringRedisApplicationTests {

    // 拿到配置的 bean 对象
    @Resource(name = "redisTemplate")
    private ValueOperations<Object, Object> valOps;

    @Test
    public void contextLoads() {
        RoleInfo roleInfo = new RoleInfo();
        roleInfo.setRoleId(1000L);
        roleInfo.setRoleName("孙悟空");
        valOps.set(roleInfo, "男");
        System.out.println(valOps.get(roleInfo));
    }
}
```
