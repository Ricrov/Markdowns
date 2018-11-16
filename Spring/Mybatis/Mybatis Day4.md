[myBatis学习网站](http://www.mybatis.org/mybatis-3/zh/dynamic-sql.html)

## mybatis-config.xml配置文件
```xml
        <typeAliases>
        <package name="com.mrd.demo.entity"/>
       </typeAliases>
```
==以上的代码会自动扫描到指定的包下,自动为包下的实体类的全限定名创建别名, 默认的别名格式为类名的小驼峰==

## mybatis定义sql片段
> sql语句可以是完整的,也可以是个单独的片段
### 1.定义一个不带参数的sql片段

==1.使用`sql`标签定义sql片段,需要添加一个`id`属性==
```xml
<sql id="mysql">
    select * from tb_user
</sql>
```
==2.在statement中使用`include`标签引用自己定义的sql片段==
```xml
<select id="" resultMap="">
    <include refid="mysql" />
</select>
```

### 2.定义一个带参数的sql片段
==1.使用`${}`定义参数==
```xml
    <sql id="sql-join-order-item">
        ${a}.*,
        ${a}.order_item_id,
        ${b}.goods_id,
        ${b}.goods_qty
    </sql>
```
==2.使用`property`标签为带参数的sql传值==
```xml
     select
             <include refid="sql-join-order-item">
                 <property name="a" value="o" />
                 <property name="b" value="oi" />
             </include>,
               g.goods_name,
               g.goods_price,
               g.category_id
        from tb_order o
                 left join tb_order_item oi on o.order_id = oi.order_id
                 left join tb_goods g on g.goods_id = oi.goods_id
```
==3.注意事项==
> 根据拼接片段后的sql,如果缺少标点符号,注意在合适的位置添加一个标点符号<br>
> 比如上述的代码在</include>标签后面加一个`,`(逗号)

## 动态sql语句
>MyBatis 的强大特性之一便是它的动态 SQL。你能根据不同条件拼接 SQL语句。有时拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL 这一特性可以很好完成拼接。
### 1.if标签
==`if`标签里面有一个必须填写的`test`属性,`test`属性填的值就相当于java中if语句()里面的条件语句==

==下面xml文档的意思为: 以==`select * from tb_user where delete_flag = 0`==为基准,<br>如果下面的if标签的`test`属性填写的条件为true的话,就在之前的sql语句后面继续拼接if里面的条件==
```xml
    --模糊查询符合条件的记录
    <select id="findAllByCondition" resultMap="commons.userMap">
        select * from tb_user where delete_flag = 0
      <!-- test相当于java语句if中的条件-->
        <if test="userName != null">
            and user_name like #{userName}
        </if>
        <if test="gender != null">
            and gender = #{gender}
        </if>
        <if test="minHeight != null">
            and height >= #{minHeight}
        </if>
        <if test="maxHeight != null">
            # 小于号需要转义
            and height &lt;= #{maxHeight}
        </if>
        <if test="minWeight != null">
            and weight >= #{minWeight}
        </if>
        <if test="maxWeight != null">
            and weight &lt;= #{maxWeight}
        </if>
        <if test="birthday != null">
            and birthday = #{birthday}
        </if>
    </select>
```
#### 注意事项:
1.小于号在配置文件中需要转义,所以用`&lt;`来代替 <br>
2.如果查询的表没有delete_flag,我们可以使用任意一个为true的语句来代替,为的是让在if里面的语句拼接后呈现一个完美的sql语句, 如果不自拟一个where条件,那么if里面的条件直接拼接后直接从 and 开始(即 ==select * from tb_user and ...==),就会报异常 <br>3.
上面的基准sql语句也可以写成:     ==select * from tb_user where 1 = 1==

### 2.where标签
> 在上面举的例子中,如果后面没有where语句为基准,而是以==select * from tb_user==而且if标签中的内容都是and开头的,直接拼接sql语句格式就不正确了,怎样解决这样的问题?

> mybatis提供给我们一个 `where` 标签 <br>
`where` 元素会智能把第一个子条件的前缀改写成 `where`,就算第一个子元素之前有`and` 或者 `or` ,`where`元素也会将它移除掉,直接在前面改写成`where`后面的正常都是`and`。
```xml

    -- 模糊查询符合条件的记录
     <select id="findAll" resultMap="commons.userMap">
        select * from tb_user
        <where>
        <if test="userName != null">
            and user_name like #{userName}
        </if>
        <if test="gender != null">
            and gender = #{gender}
        </if>
        <if test="minHeight != null">
            and height >= #{minHeight}
        </if>
        <if test="maxHeight != null">
            and height &lt;= #{maxHeight}
        </if>
        <if test="minWeight != null">
            and weight >= #{minWeight}
        </if>
        <if test="maxWeight != null">
            and weight &lt;= #{maxWeight}
        </if>
        <if test="birthday != null">
            and birthday = #{birthday}
        </if>
        </where>
    </select>
```
#### 注意点:
==`where`标签的使用方式就是在需要进行拼接的子条件元素的最外层添加一对`where`标签== <br>
==`where`标签大多数用在查询语句中,即`statement`为`<select></select>`==
### 3.set标签
> `set`标签和`select`的使用方式差不多,只不过`select`是将第一个子条件的==前缀==改写成`where`,<br>
而`set`除了在第一个子元素的前面加上==set==,而且还会把最后一个子条件的==后缀==的逗号去掉,最后别忘了在`set`标签的外层添加条件语句
```xml

    --根据user_id更新某个用户的信息
     <update id="updateOne">
        update tb_user
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
        where user_id = #{userId}
    </update>
```
#### 注意点:
====`set`标签大多数用在更新语句中,即`statement`为`<update></update>`==== <br>
==按照逻辑来说,在书写顺序上的最后一个子条件的后缀的逗号可以不写,写了也没关系. <br> 包括在select语句中的在书写顺序排在第一个的子条件的语句中前缀可以不书写`and`==

### 4.foreach标签
> 通常是在构建 ==IN 条件语句==的时候使用`foreach`标签<br>
> `foreach`标签就是相当于java中的循环,`foreach`标签里有以下几个属性需要注意一下
- open: 表示真正的遍历之前拼接的内容
- separator: 表示每次遍历拼接的时候,中间的分割内容(比如: , : () 之类的)
- item: 遍历的时候取出的数据的名字(这个名字任意起,注意下面的#{}里面填写的内容与该名要保持一致)
- close: 整个集合遍历结束后拼接的内容
- collection: collection属性需要填写的值是固定的,有下面三种填写的方式
    + (1).如果方法的参数传递的是一个集合(List),那么collection的属性值就填 `list`
    + (2).如果方法的参数传递的是一个数组(Array),那么collection的属性值就填 'array'
    + (3).如果方法的参数传递的是一个Map,那么collection的属性就填`map` (index表示key,item表示value)
```xml

    --根据user_id删除某些用户
   <delete id="deleteByUserIds">
        delete from tb_user where user_id in
        <if test="list != null and list.size > 0">
        <foreach collection="list" open="(" item="id" separator="," close=")">
            #{id}
        </foreach>
        </if>
    </delete>

```
上面的例子在`foreach`标签的外面嵌套了一对`if`标签,主要功能就是对传进来的集合进行合法性的校验,即判断传进来的集合是否为空集合. <br>
其中用到了==list.size==, 表示传递进来的集合中的元素的数量<br>
如果是数组,可以使用==array.length来获取数组的长度==

练习: 使用foreach 批量插入数据
```xml
    <insert id="addUsers">
        insert into tb_user(user_name, gender, height, weight, birthday, delete_flag) values
        <foreach collection="list"  item="user" separator="," >
            (#{user.userName},#{user.gender},#{user.height},#{user.weight},
              #{user.birthday},0)
        </foreach>
    </insert>
```

### 5.trim标签
> trim标签主要用于实现`where` 和 `set` 标签的功能,因为这两个标签都是基于`trim`标签写的,使用`trim`标签需要注意以下的问题

#### 5-1 使用`trim`标签实现`where`标签的功能
> prefix属性是指明实现WHERE的功能 <br>
> prefixOverrides属性表示 是覆盖一个前缀 and 或者 or
```xml

    <trim prefix="WHERE" prefixOverrides="AND |OR ">
    ...
    ...
    </trim>

```
#### 5-2 使用`trim`标签实现`set` 标签的功能
> prefix属性是指明实现SET的功能 <br>
> suffixOverrides属性同时也会删掉无关的逗号，因为用了条件语句之后很可能就会在生成的 SQL 语句的后面留下这些逗号。
```xml
    <trim prefix="SET" suffixOverrides=",">
    ...
    ...
    </trim>

```

### 6. choose, when, otherwise
> ==有时我们不想应用到所有的条件语句，而只想从中择其一项。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句==。

> 举个例子，如果提供了“title”就按“title”查找，提供了“author”就按“author”查找的情形，若两者都没有提供，就返回所有符合条件的 BLOG（实际情况可能是由管理员按一定策略选出 BLOG 列表，而不是返回大量无意义的随机结果）。

```xml
    <select id="findActiveBlogLike"
     resultType="Blog">
     SELECT * FROM BLOG WHERE state = ‘ACTIVE’
    <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
     </choose>
    </select>
```
