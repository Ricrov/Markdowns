## AOP实现原理

#### 静态代理
- 实现接口
    - 针对这样的类进行扩展功能, 可以使用装饰者模式.


- 无实现接口
    - 可以直接创建子类进行扩展, 也可以使用装饰者模式.

- 使用 AspectJ 框架 (需使用 AspectJ 自己的编译器)

- 弊端
> 使用继承父类和实现接口的方法有很大的弊端,
  如果一个类或接口中的方法过多, 则必须要把所有的方法扩展.
  可以使用动态代理的方式解决此问题.


##### 动态代理
- 使用 JDK 中反射包下的 Proxy
    - 前提: 目标类必须有可实现的接口
        1. 获取类加载器
            `ClassLoader classLoader = userService.getClass().getClassLoader();`
        2. 获取类所实现的所有接口
        3. 创建 InvocationHandler 实现类
        4. 调用 Proxy.newProxyInstance 方法, 填入上面的参数, 创建代理对象

- 使用第三方的 CGLib 框架
    - 前提: 目标类不能是 final 的
        1. 创建 Enhancer 对象
            `Enhancer enhancer = new Enhancer();`
        2. 设置类加载器
        3. 设置父类(实际上就是目标类本身)
            `enhancer.setSuperclass(clazz);`
        4. 设置 Callback 回调, 方法中填入 MethodInterceptor 实现类
        5. 调用 Enhancer 的 create 方法, 创建代理对象

- 前提
    - 如果一个类即是 final 修饰, 又没有接口, 那么就无法对其进行扩展.
      不论是使用静态或是动态代理亦或是使用 Spring 的切面, 都需遵循此原则.


## AOP

#### 基本概念
> 面向切面编程 (AOP) 实际上就是在规定:
> 在何处何时执行什么代码 (where when what)


- 接入点 JoinPoint 可增强的位置
    - 在 Spring 中, 若切面配置完毕, 接入点就是被增强的方法

- 切点 PointCut 表达式
    - 例: service 层的所有方法 `excution(* com.ric.demo.*.service.*.*(..))`
    - 表达式描述的是对哪些接入点进行增强

- 通知 advice
    - **前置通知 @Before**
        - 在接入点方法执行之前被调用的方法

    - **后置通知 @After**
        - 在接入点方法执行之后被调用的方法
        - 不论接入点方法是否抛出异常都会执行 (可理解为写在 finally 中)

    - **返回通知 @AfterReturning**
        - 在接入点方法正常执行之后被调用的方法
        - 也就是接入点方法没有抛出异常

    - **异常通知 @AfterThrowing**
        - 在接入点方法抛出异常时被调用的方法
        - 写在 catch 中

    - **环绕通知 @Around**
        - 最强大的通知, 可以在接入点方法的周围自定义任意功能
        - 集成了上面的4种通知

    > 在 Spring 中, 前置、后置、返回、异常通知的方法参数中都可以添加一个 JoinPoint 类型的参数, 用来获取本次通知执行时的接入点(方法)信息.

    > 环绕通知的方法和其他通知有显著的不同, 该方法必须有 Object 类型的返回值. 用来返回接入点方法的执行结果. 该方法的参数是 ProceedingJoinPoint 类型.

    > 返回通知方法的参数中可以额外增加一个 Object 类型的参数, 这个参数保存着接入点方法的返回值.

    > 异常通知的参数中可以额外增加一个 Exception 类型的参数, 用来获取本次接入点方法执行过程中抛出的异常.

- 切面
    - 在 Spring 中是一个类, 类中保存着切点表达式和通知

- 织入
    - 将切面类与目标类进行整合的过程


## AOP 配置

#### xml 方式配置
- <aop:config> 导入 aop 的命名空间
- 在 config 里声明切点表达式
    `<aop:point-cut>`
- 在 config 里声明切面 `<aop:aspect>`, 在 aspect 的属性中指定切面的 bean
- 在 aspect 里声明通知 `<aop:before>`, 在 before 的属性中指定切点表达式和切面 bean 中的方法

#### 注解方式配置
- `<aop:aspectj-autoproxy />` 开启注解方式的 aop
- 新建切面类, 添加注解 @Component, @Aspect 注解
- 创建切点表达式方法, 为方法添加 @Pointcut 注解, 并在注解中填写切点表达式
- 创建通知方法, 为通知方法添加某个通知注解: @Before / @After / @AfterReturning / @AfterThrowing / @Around, 并且在通知注解中指定某个切点表达式方法.
- `<mvc:default-servlet-handler />` 使用默认的 Servlet 去访问静态资源
