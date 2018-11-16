# Spring Day 1

#### Spring
- spring是一个高度灵活的轻量级框架，其目的是降低企业级应用开发的复杂度

![image](https://ws2.sinaimg.cn/large/0069RVTdly1fv7102se03j30cj0890u4.jpg)



#### spring功能介绍
- Spring 容器提供了IOC机制，可以创建对象以及管理对象之间的调用关系，避免了硬编码造成的程序耦合。
- 提供了AOP（面向切面编程方式）功能，可以实现很多特定功能。
- 声明式事务控制处理。
- 对jdbc进行了轻量级的封装，可以更加灵活的去操作数据库。
- 提供了MVC设计模式的解决方案
- 提供了文件上传、定时器等常用工具类。
- 对于其他优秀框架的支持（如：Struts、Hibernate、MyBatis、Quartz等）


#### 框架:

- SSH(Spring, Structs, Hibernate)
- SSM(Spring, SpringMVC, Mybatis)

#### Spring

- junit 测试单元
- Spring 处理 bean, IOC
- AOP, 面向切面编程
- 数据持久层, 使用 Mybatis
- Spring MVC (Web)

#### 搭建框架
- 创建项目 maven quickstart
- 参数:`archetypeCatalog`:`internal`
- Spring-framework-4.1.0.RELEASE及依赖包
- 更改配置文件 pom.xml, 添加

	```xml
	<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.0.8.RELEASE</version>
    </dependency>
	```

#### 容器
 - 创建 resources
	`src/main/resources/META-INF/applicationContext.xml`
	`resources` → `Mark Directory as Resources Root`

#### 配置

标签 bean 表示实例化对象, class 是应用的类, id 是对象名
`Dog dog = new Dog();`

原理: 反射, 根据传入的字符串查找类, 获得构造方法, 实例化对象
只需在配置文件里配置 bean 标签, 容器则会自动生成相应的对象 dog

IoC: 以前实例化对象都是使用 java 代码, 现在 Spring 框架把生成对象的权利交给配置文件,
通过在配置文件中的配置, 容器协助生成对象, 这种控制权反转的过程叫做 控制反转 (IoC)
Inversion of Control

配置方法:
方式1 -- (无参)构造方法实例化 (给予 id 和 class)

```xml
<bean id="dog" class="com.ric.spring.jav.test1.Dog"></bean>
```

方式2 -- (无参)普通工厂 (给予 factory-bean 和 factory-method)

```xml
<bean id="factory" class="com.ric.spring.jav.test1.DogFactory"></bean>
<bean id="dog2" factory-bean="factory" factory-method="getDog"></bean>
```

方式3 -- (无参)静态工厂 (给予 factory-method)

```xml
<bean id="dog3" class="com.ric.spring.jav.test1.DogFactory" factory-method="getStaticDog"></bean>
```

方式4 -- 有参构造方法

```xml
<bean id="dog" class="com.ric.spring.jav.test2.Dog">

<!--通常使用这种写法, <constructor-arg> 必须和构造方法参数一一呼应-->
<constructor-arg name="name" value="Monkey"></constructor-arg>
<constructor-arg name="age" value="2"></constructor-arg>

<!-- <constructor-arg> 可以使用 index (表示第几个参数) -->
<constructor-arg index="0" value="Monkey"></constructor-arg>
<constructor-arg index="1" value="2"></constructor-arg>

<!-- <constructor-arg> 可以使用 type 来表示数据类型, 以匹配构造方法的参数 -->
<constructor-arg type="java.lang.String" value="Monkey"></constructor-arg>
<constructor-arg type="java.lang.Integer" value="2"></constructor-arg>

</bean>
```

方式5 -- 先调用无参构造方法, 再调用 setter 方法

```xml
<!-- property 标签 -->
<bean id="dog" class="com.ric.spring.jav.test2.Dog">
	<constructor-arg name="name" value="Monkey"></constructor-arg>
	<property name="age" value="2"></property>
</bean>
```

#### 打开容器

- BeanFactory: 早起的 javabean 工厂类实现
- ApplicationContext: 对 BeanFactory 进行扩展. 提供更多功能.

```Java
ApplicationContext context = new ClassPathXmlApplicationContext("META-INF/applicationContext.xml");
Dog dog = (Dog) context.getBean("dog");
dog.bark();
```


在 Java 中, 创建对象的方式:
① 构造器

	Dog dog = new Dog();


② 普通工厂

	public Dog getDog() {
        return new Dog();
    }

③ 静态工厂

	private static Dog dog;
	public static Dog getStaticDog() {
        if (dog == null) {
            dog = new Dog();
        }
        return dog;
    }



- 创建 Bean 的方式
	- 使用构造器(构造方法)

			// getBean(), 参数是在配置文件里设置的 id (String 类型).
			// 返回值是 Object 类型, 想要获得任意类型需要强转.
			Dog dog = (Dog) context.getBean("dog");
			dog.bark();



#### bean 管理
- 基本属性值注入
	- constructor-arg, 构造方法
	- property, setter 方法

- 生命周期
	在配置文件里配置的 bean, 若不加以设定. 则不论是否调用, 都会加载.

```xml
	<!--
	标签 bean 的属性
	生命周期: create、init、service、destroy
	init-method 表示该类的 init 初始化方法,
	destroy-method 表示该类的 destroy 销毁方法.
	-->
	<bean id="dog" class="com.ric.spring.jav.test3.Dog" init-method="initDog" destroy-method="destroyDog"></bean>
```

- 加载方式

	- 若 scope 是 单例模式 singleton 时, 使用属性 lazy-init = "true" 时,
	  配置文件里的 bean 如果不使用就不会被加载 (默认为 false, 即使用与否都全部加载)

```
<bean id="dog" class="com.ric.spring.jav.test3.Dog" scope="singleton" lazy-init="true"></bean>
```

#### 总结

- 使用参数 init-method 和 destroy-method, 来配置类的初始化和销毁时的方法;
- 使用 scope 来配置是否使用单例模式, 默认是 使用单例. (prototype 非单例)
- 使用 lazy-init 来配置加载的时机, 默认是加载配置文件的时候加载 bean. (懒汉模式↔饿汉模式)
