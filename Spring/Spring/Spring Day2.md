# Spring Day 2

#### 依赖注入
- 依赖注入的定义

```xml
<bean id="person" class="com.ric.spring.jav.test1.Person"/>

    <bean id="dog1" class="com.ric.spring.jav.test1.Dog">
        <property name="name" value="Monkey"/>
        <property name="age" value="2"/>

        <!-- <依赖注入> -->
        <!-- 注入属性值时, 基本引用类型使用 value 注入 -->
        <!-- 注入自定义类型的属性值需要依赖其他的 bean 进行注入处理: ref -->
        <property name="host" ref="person"/>

    </bean>
```

- 基本引用类型注入 List

	```xml
	<property name="list">
		<list>
			<value>Jack</value>
			<value>Rose</value>
		</list>
	</property>
	```

- 自定义类型注入 List

 	```xml
	<property name="personList">
		<list>
			<ref bean="person"/>
		</list>
	</property>
	```

- 基本引用类型注入 Set

	```xml
	<property name="set">
		<set>
			<value>张三</value>
			<value>李四</value>
		</set>
	</property>
	```

- 自定义类型注入 Set

	```xml
	<property name="personSet">
		<set>
			<ref bean="person"/>
		</set>
	</property>
	```

- 基本引用类型注入 Map

	```
	<property name="map">
		<map>
			<entry key="first" value="One"/>
			<entry key="second" value="Two"/>
		</map>
	</property>
	```

- 自定义类型注入 Map

	```
	<property name="personMap">
		<map>
			<entry key="Person" value-ref="person"/>
		</map>
	</property>
	```

- 注入属性 Properties 类

	```
	<property name="properties">
			<props>
				<prop key="size">57M</prop>
				<prop key="date">2018/9/12</prop>
			</props>
	</property>
	```

#### 面向接口编程
案例:
创建 Person 类, 属性 name 和 Animal, 方法 playWithAnimal
创建接口 Animal, 有方法 bark
创建类 Dog 和 Cat, 遵从接口 Animal

```xml
<bean id="dog" class="com.ric.inter.jav.Dog"></bean>
<bean id="cat" class="com.ric.inter.jav.Cat"></bean>

<bean id="person" class="com.ric.inter.jav.Person">
	<property name="name" value="Ric"></property>
	<property name="animal" ref="cat"></property>
</bean>
```

```
<bean id="dao" class="com.ric.bookstore.model.dao.BookDaoImpl"/>
<bean id="service" class="com.ric.bookstore.model.service.BookService">
	<property name="dao" ref="dao"/>
</bean>
<bean id="bookAction" class="com.ric.bookstore.controller.BookAction">
	<property name="service" ref="service"/>
</bean>
```


#### 三层架构

- Action -- Service -- Dao
- 案例: 查询数据库 bookstore 里面的所有图书

#### 注解

- 创建bean

- pom.xml 配置

	```xml
	<!-- 对于该包下实行自动扫描, 支持注解 -->
	<context:component-scan base-package="com.ric.spring.jav.test2"/>
	```

- @Component: 当所属的 bean 不在以下三层, 或不明确的时候使用


	```Java
	// 使用该类实例化, 即 Person person = new Person();
	// @Component("id值"), 若省略(), 默认值就是该类首字母小写
	@Component("person")
	public class Person {

	}
	```

- @Service: Service 层

	```Java
	// 相当于 BookService bookService = new BookServiceImpl();
	@Service
	public class BookService {

	}
	```

- @Controller: Controller 层

	```Java
	// 相当于 BookAction bookAction = new BookAction();
	// <bean id="bookAction" class=".../BookAction"/>
	@Controller
	public class BookAction {

	}
	```

- @Repository: Dao 层

	```Java
	@Repository
	public class BookDaoImpl implements BookDao {

	}
	```

- 属性注入
	- @Value(value=""): 基本引用类型
	- @Resource(name=""): 自定义引用类型 (相当于 ref)
	- @Autowired: 按类型进行拼配, 使用 Autowired 注解时, 需要注意一个项目里不可以有名字相同的类, 即使是不同的包中
