## mybatis-config.xml 配置映射
- 扫描包, 会加载该包下的所有类(或接口)所对应的 mapper.xml 映射文件

```xml
<mappers>
    <!-- 扫描包, 会加载该包下的所有类(或接口)所对应的 mapper.xml 映射文件 -->
    <!-- ※ 若此包下的映射文件没有对应的接口, 则不会被加载 -->
    <package name="com.ric.demo.mapper"/>
</mappers>
```

## GoodsCategory 类

- 创建接口, 方法

- category

```java
public interface CategoryMapper {

    /**
     * 只查询 tb_goods_category 单表
     * @return
     */
    List<GoodsCategory> findAllSimple();


    /**
     * 会把某个类别所对应的商品也查询出来
     * @return
     */
    List<GoodsCategory> findAll();

}
```


- CategoryMapper
    - 集合配置方法

```xml
<!-- 处理映射关系 -->
<!-- 为了减少重复的代码, 可以使用 resultMap 继承其他的 resultMap -->
<resultMap id="categoryMap" type="com.ric.demo.entity.GoodsCategory"  extends="categorySimpleMap">
    <!--<id column="category_id" property="categoryId"/>-->
    <!--<result column="category_name" property="categoryName"/>-->
    <!-- 集合属性 -->
    <!-- ofType: 决定此属性的数据类型 -->
    <collection property="goodsList" ofType="com.ric.demo.entity.Goods">
        <!-- 这里面的 id result 代表如何映射 Goods -->
        <id column="goods_id" property="goodsId"/>
        <result column="category_id" property="categoryId"/>
        <result column="goods_name" property="goodsName"/>
        <result column="goods_price" property="goodsPrice"/>
    </collection>
</resultMap>

<select id="findAll" resultMap="categoryMap">
    SELECT category.category_id, category.category_name, goods.goods_id, goods.goods_name, goods.goods_price
    FROM tb_goods_category category
    LEFT JOIN tb_goods goods
    ON category.category_id = goods.category_id;
</select>
```



- 创建测试类

```java
public class CategoryMapperTest {

    private SqlSessionFactory sessionFactory;
    private CategoryMapper categoryMapper;

    @Before
    public void setUp() throws Exception {
        InputStream in = Resources.getResourceAsStream("mybatis-config.xml");
        sessionFactory = new SqlSessionFactoryBuilder().build(in);
        SqlSession session = sessionFactory.openSession();
        categoryMapper = session.getMapper(CategoryMapper.class);

    }

    @Test
    public void findAllSimple() {
        System.out.println("----- 简单查询所有类别 -----");
        List<GoodsCategory> categoryList = categoryMapper.findAllSimple();
        categoryList.forEach(System.out::println);
    }

    @Test
    public void findAll() {
        System.out.println("----- 简单查询所有类别携带商品信息 -----");
        List<GoodsCategory> categoryList = categoryMapper.findAll();
        categoryList.forEach(System.out::println);
    }
}
```

#### 优化
- 因每次配置都有重复代码, 因此可以优化
    - mybatis-config.xml 设置别名

- 为实体类配置别名, 在之后的 mapper 配置文件中, 所有用到全限定名的位置皆可使用别名来代替

```xml
<typeAliases>
    <typeAlias type="com.ric.demo.entity.GoodsCategory" alias="Category"/>
    <typeAlias type="com.ric.demo.entity.Goods" alias="Goods"/>
    <typeAlias type="com.ric.demo.entity.User" alias="User"/>
    <typeAlias type="com.ric.demo.entity.Order" alias="Order"/>
</typeAliases>
```

- 为了减少重复的代码, 可以使用 resultMap 继承其他的 resultMap
-
```xml
<resultMap id="categoryMap" type="com.ric.demo.entity.GoodsCategory"  extends="categorySimpleMap">
```

## Goods 类

- Mapper 配置

```xml
<mapper namespace="com.ric.demo.mapper.GoodsMapper">
    <resultMap id="goodsMap" type="Goods">
        <id column="goods_id" property="goodsId"/>
        <result column="category_id" property="categoryId"/>
        <result column="goods_name" property="goodsName"/>
        <result column="goods_price" property="goodsPrice"/>
        <!-- 处理一对一关系: 关联对象 property="对象名" javaType="对象类型(别名)" -->
        <association property="category" javaType="Category">
            <id column="category_id" property="categoryId"/>
            <result column="category_name" property="categoryName"/>
        </association>
    </resultMap>

    <select id="findAllWithCategory" resultMap="goodsMap">
        SELECT goods.goods_id, goods.category_id, goods.goods_name, goods.goods_price, category.category_name
        FROM tb_goods goods LEFT JOIN tb_goods_category category on goods.category_id = category.category_id
    </select>
</mapper>
```

-------

### useGeneratedKeys, keyColumn, keyProperty属性

#### 1. useGeneratedKeys属性
- useGeneratedKeys属性只能用在insert和update的statement中
- 当它的值为true时表示使用生成的主键key

```xml
<insert id="addOne" useGeneratedKeys="true" keyColumn="person_id, gender" keyProperty="id, gender" >
    insert into tb_person (person_name, age)
    values (#{name}, #{age})
</insert>
```

#### 2. keyColumn属性
- 它指的是数据库中的列名
- 它常和keyProperty一起使用

#### keyProperty属性
- 它指的是实体类中的属性名
- 它常和keyColumn一起使用

#### 3. ==它们三个一起使用时的作用==

- ==就可以在插入和更新数据的时候, 把所有信息都赋值给实体类, 就可以获取非SQL语句中的数据了==
- ==比如说在更新用户信息的时候, 只插入了名字和年龄, 那么如果设置了上面的三条属性, 那么该用户的所有信息都可以获取到==

```java
SqlSession sqlSession = sessionFactory.openSession();
PersonMapper mapper = sqlSession.getMapper(PersonMapper.class);
Person person = new Person();
person.setName("犬夜叉");
person.setAge(500);
int i = mapper.addOne(person);
"在插入数据的时候, 只提供了姓名和年龄";
"那么通过设置上面的三条属性就可以获取该用户的所有信息了";
System.out.println(person);
sqlSession.commit();
sqlSession.close();
```
### Mybatis配置文件中的一些其他配置

#### 1. ==给类或接口的全限定名起别名==

```xml
"如果给类或接口的全限定名起了别名了";
"那么在以后的mapper的配置文件中";
"凡是用到了配置类或接口的全限定名的位置, 都可以用别名来代替";
<typeAliases>
        <typeAlias type="com.lanou3g.demo.entity.GoodsCategory" alias="Category"/>
    </typeAliases>
```

#### 2. mappers中的配置
- ==package标签的作用==

```
"该属性会加载配置的包里面的所有类或接口所对应的mapper.xml映射文件";
"也就是会加载该包下和类或接口同名的mapper.xml映射文件";
```
- ==如果要加载和类名或接口名不同的映射文件, 那么需要使用resource属性来引用==
```
<mappers>
        <mapper resource="com/lanou3g/demo/mapper/CommonsMapper.xml"/>
        <package name="com.lanou3g.demo.mapper"/>
    </mappers>
```
### ==Mybatis高级映射==
#### 1. ==当某个实体类中有集合类型的属性时==
```
<resultMap id="categoryMap" type="Category" extends="Commons.categoryMap">
        "使用id标签和result标签配置类中的非集合类型映射关系";
        "比如基本数据类型, 单个的引用数据类型";
       <id column="category_id" property="categoryId"/>
        <result column="category_name" property="categoryName"/>
        "使用collection标签来映射类中的集合属性";
        "使用ofType来配置集合中的数据类型";
        <collection property="goodsList" ofType="Goods" resultMap="Commons.goodsMap">
            <id column="goods_id" property="goodsId"/>
            <result column="goods_name" property="goodsName"/>
            <result column="goods_price" property="goodsPrice"/>
            <result column="category_id" property="categoryId"/>
        </collection>
    </resultMap>
```
#### 2. ==当某个类中有其他类作为成员变量时==
```
<resultMap id="GoodsMap" type="Goods" extends="Commons.goodsMap">
        "在resultMap中使用association标签进行关联";
        "使用javaType属性指定关联的类的数据类型";
        "在非resultMap标签中, 如果有resultMap属性";
        "也可以使用该属性来引用其他的映射关系";
        <association property="category" javaType="Category" resultMap="Commons.categoryMap">
            <id column="category_id" property="categoryId"/>
            <result column="category_name" property="categoryName"/>
        </association>
    </resultMap>
```
#### 3. ==高级映射的一些优化==
- ==当多个文件中都引用了同一种映射关系时==
- 可以把相同的映射关系提取出来, 然后在resultMap中通过命名空间.(点)resultMap的id的方式引用映射关系即可
```
"<1 = "映射关系的基类">
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="Commons">
    <resultMap id="categoryMap" type="Category" >
        <id column="category_id" property="categoryId"/>
        <result column="category_name" property="categoryName"/>
    </resultMap>
</mapper>
```
```
1. "如果在rusultMap标签中引用父类的映射关系时
使用extends属性来引用";
<resultMap id="GoodsMap" type="Goods" extends="Commons.goodsMap"></resultMap>
2. "可以在标签中的resultMap属性引用映射关系";
<association property="category" javaType="Category" resultMap="Commons.categoryMap"/>
```
- ==如果分类中指定了映射的类型(tpye属性的话), 那么引用它的子类会继承该属性, 就不需要显示的声明出来了==
- ==当需要引用父类的映射关系的时候, 需要在mybtis的配置文件中注册该配置文件, 否则无法使用==
```
<mappers>
    <mapper resource="com/lanou3g/demo/mapper/CommonsMapper.xml"/>
</mappers>
```
### ==ResultMap元素==
- ==ResultMap元素是mybatis中最重要的元素之一==
#### ==使用resultMap处理复杂的结果映射, 嵌套==
- 比如说: 查询用户, 用户里面有订单的集合, 每一个订单对应不同的商品, 每一个商品对应不同的分类
- 这样的话就可以在ResultMap中使用嵌套来完成该要求
- ==注意事项==
```
"使用嵌套的时候, 最多只能嵌套一层";
"如果存在多层嵌套关系的话, 需要把其他的分离出来";
"然后通过resultMap属性来引用即可";
"具体使用方法如下";
```
```
<mapper namespace="com.lanou3g.demo.mapper.UserMapper">
    <resultMap id="userMap" type="User" extends="Commons.UserMap" autoMapping="false">
        <collection property="orderList" ofType="Order" resultMap="OrderMap"/>
    </resultMap>
    <resultMap id="OrderMap" type="Order" extends="Commons.OrderMap">
        <association property="goods" javaType="Goods" resultMap="goodsMap"/>
    </resultMap>
    <resultMap id="goodsMap" type="Goods" extends="Commons.goodsMap">
        <association property="category" javaType="Category" resultMap="Commons.categoryMap"/>
    </resultMap>
    <select id="findAllStrong" resultMap="userMap">
        select user.*,
               orders.order_id,
               goods.goods_id,
               goods.goods_name,
               goods.goods_price,
               category.category_id,
               category.category_name,
               orders.goods_qty
        from tb_user user
                 left join tb_order orders on user.user_id = orders.user_id
                 left join tb_goods goods on orders.goods_id = goods.goods_id
                 left join tb_goods_category category on category.category_id = goods.category_id;
    </select>
</mapper>
```

#### typeAliases 配置别名的方法
- mybatis-config.xml 配置

```xml
<typeAliases>
    <!-- 扫描 entity 包, 自动为实体类创建别名, 默认的别名格式为类名小驼峰 -->
    <package name="com.ric.demo.entity"/>
</typeAliases>
```

```xml
<!-- 因自动扫描, 故 type 要改为小驼峰实体类名 -->
```
