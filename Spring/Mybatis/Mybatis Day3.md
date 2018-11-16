## 动态 SQL

#### SQL 片段
Mybatis 可以定义 SQL 片段.
片段定义的越细节化, 复用次数就越多.

- 带有别名的 SQL 片段, 在不同的 SQL 语句中可能会是别的别名, 因此可用变量代替. ${a}, ${b}, ...
- 而后使用 include>property 的形式为其传值

```xml
<!-- 定义带参数的 SQL 片段 -->
<sql id="sql-join-order-item">
    ${a}.order_id, ${a}.order_code, ${b}.order_item_id, ${b}.goods_qty
</sql>

<!-- 定义 SQL 片段 -->
<sql id="sql-all-order">
    SELECT
    <include refid="sql-join-order-item">
        <!-- 为带参数的 SQL 片段传值 -->
        <property name="a" value="oder"/>
        <property name="b" value="item"/>
    </include>,
    goods.goods_id, goods.goods_name, goods.goods_price
    FROM tb_order oder
    LEFT JOIN tb_order_item item ON oder.order_id = item.order_id
    LEFT JOIN tb_goods goods ON item.goods_id = goods.goods_id
</sql>
```

- 引入某个 SQL 片段

```xml
<select id="findAllWithItem" resultMap="allOrderWithItemMap">
    # 引入某个 SQL 片段
    <include refid="sql-all-order"/>
</select>

<select id="findOneWithItem" resultMap="allOrderWithItemMap">
    # 引入某个 SQL 片段
    <include refid="sql-all-order"/>
    WHERE oder.order_id = #{orderId}
</select>
```

#### 动态查询

- User 实体类

```java
private Integer userId;
private String userName;
private String gender;
private Float height;
private Float weight;
private Date birthday;

getter & setter 方法
toString 方法
```



- 创建 UserMapper 接口

```java
多条件查询方法
    /**
     * 进行多条件查询
     * SELECT * FROM tb_user
     * Map 中有很多个 key
     * 实现传哪个 key 就拼接什么 SQL 语句
     *     userName:   " AND user_name LIKE ? "
     *     gender      " AND gender = ?
     *     minHeight   " AND height >= ?
     *     maxHeight   " AND height <= ?
     *     minWeight   " AND weight >= ?
     *     maxWeight   " AND weight <= ?
     *     birthday    " AND birthday = ?
     * @param condition
     * @return
     */
    List<User> findAllByCondition(Map<String, Object> condition);
```

- 配置 UserMapper.xml
    - 元素标签 if 即相当于 java 中的 if, test 属性相当于条件
    - 可以直接拿到 Map 中的 key 所对应的 value

```xml
<mapper namespace="com.lanou3g.demo.mapper.UserMapper">
    <select id="findAllByCondition" resultMap="commons.userMap"> ※此处 commons 是结果集映射配置文件
        SELECT * FROM tb_user WHERE delete_flag = 0

        <if test="userName != null">
            AND user_name LIKE #{userName}
        </if>
        <if test="gender != null">
            AND gender = #{gender}
        </if>
        <if test="minHeight != null">
            AND height >= #{minHeight}
        </if>
        <if test="maxHeight != null">
            # 小于号需要转义
            AND height &lt;= #{maxHeight}
        </if>
        <if test="minWeight != null">
            AND weight >= #{minWeight}
        </if>
        <if test="maxWeight != null">
            # 小于号需要转义
            AND weight &lt;= #{maxWeight}
        </if>
        <if test="birthday != null">
            AND birthday = #{birthday}
        </if>
    </select>
</mapper>
```


- UserMapper 测试类

```java
private UserMapper userMapper;

@Before
public void setUp() throws Exception {
    InputStream in = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(in);
    SqlSession session = sessionFactory.openSession();
    userMapper = session.getMapper(UserMapper.class);
}


@Test
public void findAllByCondition() {
    Map<String, Object> condition = new HashMap<>();
    condition.put("userName", "小%");
    condition.put("gender", "女");
    condition.put("minHeight", 1.80);

    // Date 的写法
    // Date birthday = Date.valueOf("1992-08-23");
    // condition.put("birthday", birthday);
    condition.put("birthday", "1992-08-23");

    List<User> userList = userMapper.findAllByCondition(condition);
    userList.forEach(System.out::println);
}
```

<hr>

#### 多条件查询
- 当条件语句都是动态语句时, 由于每个条件所在的位置是未知的, 因此无法得知 WHERE 所在的位置<br>
  解决方法: ==将所有的查询条件放入 <where> 标签==, 此时 mybatis 会自动判断插入 WHERE 和 AND

```xml
<select id="findAll" resultMap="commons.userMap">
    SELECT * FROM tb_user
    # 将所有 if 放入 where 标签
    <where>
        <if test="userName != null">
            AND user_name LIKE #{userName}
        </if>
        <if test="gender != null">
            AND gender = #{gender}
        </if>
        <if test="minHeight != null">
            AND height >= #{minHeight}
        </if>
        <if test="maxHeight != null">
            # 小于号需要转义
            AND height &lt;= #{maxHeight}
        </if>
        <if test="minWeight != null">
            AND weight >= #{minWeight}
        </if>
        <if test="maxWeight != null">
            # 小于号需要转义
            AND weight &lt;= #{maxWeight}
        </if>
        <if test="birthday != null">
            AND birthday = #{birthday}
        </if>
    </where>
</select>
```

- 根据 userId 更新一条数据
    - 接口方法

```java
default int updateUsers(List<User> users) {
    int sum = 0;
    for (User user : users) {
        int i = updateOne(user);
        sum += i;
    }
    return sum;
}
```

```xml
<update id="updateOne">
    UPDATE tb_user
    <set>
        <if test="userName != null">
            user_name = #{userName},
        </if>
        <if test="gender != null">
            gender = #{gender},
        </if>
        <if test="height != null">
            height = #{height},
        </if>
        <if test="weight != null">
            weight = #{weight},
        </if>
        <if test="birthday != null">
            birthday = #{birthday},
        </if>
    </set>
    WHERE user_id = #{userId}
</update>
```
- 测试类

```
@Test
public void updateOne() {

    User user = new User();
    user.setUserId(6);
    user.setHeight(1.90F);
    user.setWeight(78.00F);
    user.setUserName("必胜客");

    int count = userMapper.updateOne(user);

    System.out.println(count);

}
```

#### 多条数据删除

- collection 方法的参数如果传递的是集合, 则填 list
    - 如果是数组, 则填 array
    - 如果是 Map, 就填 map, index 表示 key, item 表示 value
- open 表示真正的遍历之前拼接的内容
- separator 表示每次遍历拼接时, 中间的分隔内容(符)
- item 表示遍历的时候取出的数据
- close 表示遍历结束时拼接的内容

- list.size 可以获取传递过来的集合中元素的数量
- array.length 可以获取数组中元素的数量


```xml
<delete id="deleteByUserIds">
    DELETE FROM tb_user WHERE

    <if test="list != null and list.size > 0">
        <foreach collection="list" open="user_id IN (" item="id" separator="," close=")">
            #{id}
        </foreach>
    </if>
    <if test="list == null or list.size == 0">
        1 = 2
    </if>
</delete>
```

- 测试

```java
@Test
public void deleteByIds() {
    List<Integer> ids = new ArrayList<>();
    // 需要数据传递至 dao 层之前进行合法性校验 (非空判断等)
    ids.add(1);
    ids.add(2);
    ids.add(6);
    int count = userMapper.deleteByUserIds(ids);
    System.out.println(count);
}
```

#### 插入多条数据

```xml
<insert id="addUsers">
    INSERT INTO tb_user (user_name, gender, height, weight, birthday, delete_flag) VALUES
    <foreach collection="list" open="(" item="user" separator="),(" close=")">
        #{user.userName}, #{user.gender}, #{user.height}, #{user.weight}, #{user.birthday}, 0
    </foreach>
</insert>
```

```java
@Test
public void addUsers() {

    List<User> list = new ArrayList<>();
    for (int i = 1; i < 8; i++) {
        User user = new User();
        user.setUserName("武" + i);
        user.setGender("女");
        user.setWeight(100.0F);
        user.setHeight(1.0F);
        user.setBirthday(Date.valueOf("1999-09-09"));
        list.add(user);
    }

    int count = userMapper.addUsers(list);
    session.commit();
    session.close();
    System.out.println(count);

}
```
