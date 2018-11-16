## 配置相关

- property 今后使用 spring 集成的功能
- mapper 今后使用 spring 创建配置成 bean

### mybatis-config setting 配置

==cacheEnable 二级缓存开关==
>    - *全局地开启或关闭配置文件中的所有映射器已经配置的任何缓存*



#### 一级缓存

> mybatis 自带了一级缓存, 一级缓存的作用范围范围是同一个 SqlSession,<br>
> 即在同一个 SqlSession 下进行的多次同样的查询时会有缓存

2. flushCache<br>
在 statement 标签中, 有标签属性 flushCache, 表示是否清空缓存区. <br>
select 的默认值是 false, insert/update/delete 的默认值是 true.

> 假设查了'张三', 若更新了'李四', '张三'的缓存也会被清除

`<select id="findAllByCondition" resultMap="commons.userMap" flushCache="true">` 默认值即为 true

#### 二级缓存

> 同一个 Mapper 下的同一个查询会有缓存, 使用频度最多

- ++二级缓存表示, 同一个 Mapper 中的同一个查询是有缓存的, 与是否是同一个 SqlSession 无关++<br>

==注意: <br>==
==1. SqlSession 关闭的时候才会向二级缓存中缓存数据 session.close()<br>==
==2. 实体类需要序列化<br>==
==3. 在 SELECT 中可以使用 userCache 属性单独控制该 SELECT 是否使用缓存 ( flushCache="true") 默认为 true<br>==
==4. 可以自定义(更专业的)二级缓存的框架 ehcache oscache==

- 打开二级缓存

```xml
<settings>
    此开关表示是否开启二级缓存, 默认值为 true
    打开二级缓存 并在 查询语句的 mapper.xml 下加入 <cache/> 标签
    <setting name="cacheEnable" value="true"/>
</settings>
```
- 若想缓存二级数据, 查询的实体类和其相关的实体类必须实现序列化接口
`public class User implements Serializable {}`

#### 解决 n + 1 的问题

GoodsMapper 接口
```java
public interface GoodsMapper {
    /**
     * 查询所有商品, 并携带出商品的种类
     * @return
     */
    List<Goods> findAll();
}
```

GoodsCategoryMapper 接口
```java
public interface GoodsCategoryMapper {
    GoodsCategory findById(Integer categoryId);
}
```

GoodsMapper.xml
```xml
<resultMap id="goodsMap" type="goods" extends="commons.goodsMap">
    <association property="category" javaType="goodsCategory"
                 select="com.lanou3g.demo.mapper.GoodsCategoryMapper.findById"
                 column="category_id"/>
</resultMap>

<select id="findAll" resultMap="goodsMap">
    SELECT * FROM tb_goods
</select>
```

GoodsCategoryMapper.xml
```xml
<select id="findById" resultMap="commons.categoryMap">
    SELECT * FROM tb_goods_category WHERE category_id = #{categoryId}
</select>
```

GoodsMapperTest 测试类
```java
@Test
public void findAll() {
    List<Goods> goodsList = goodsMapper.findAll();
    goodsList.forEach(System.out::println);
}
```

#### 懒加载
**lazyLoadingEnabled**

```xml
<settings>
    <!-- 懒加载开关, 默认为 false. 若设置为 true, 关联的 SQL 会根据需要的时候才会进行查询 -->
    <!-- false 时, 在查询时, 会把所有关联的 SQL 都会进行查询 -->
    <setting name="lazyLoadingEnabled" value="true"/>
</settings>
```


#### 积极懒加载
**aggressiveLazyLoading**

> 生效前提: 懒加载开关需为 true

```xml
<settings>
    <!--
    积极懒加载开关, 默认为 false. 若设置为 true, 则调用某个对象的任意方法时都会把它关联的其他 SQL 进行调用
    例如, 获取 goods 的名字属性时, 也会查询该商品的类别等信息
    -->
    <setting name="aggressiveLazyLoading" value="true"/>
</settings>
```

#### 自动映射
mapUnderscoreToCamelCase & autoMappingBehavior <br>
自动映射适应类型: 单表查询, 一对一的多表查询, 多对一 <br>
自动映射不适应类型: 一对多的多表查询

```xml
<settings>
<!-- 下划线形式的列名与小驼峰形式的属性名自动映射, 默认为 false -->
    <setting name="mapUnderscoreToCamelCase" value="true"/>
<!-- NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。 FULL 会自动映射任意复杂的结果集（无论是否嵌套）。 -->
    <setting name="autoMappingBehavior" value="FULL"/>
</settings>
```

只需要指定结果集类型即可
```xml
<select id="findById" resultType="goodsCategory">
    SELECT * FROM tb_goods_category WHERE category_id = #{categoryId}
</select>
```

#### logImpl
指定 MyBatis 所用日志的具体实现，未指定时将自动查找。
开发时可能会引入多个日志框架. 因此需要指定使用哪个框架.

```xml
<settings>
    <!-- 指定 MyBatis 所用日志的具体实现，未指定时将自动查找 -->
    <setting name="logImpl" value="LOG4J"/>
</settings>
```
