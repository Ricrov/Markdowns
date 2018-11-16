## 代理模式
> 代理提供了对目标对象额外的访问方式,即通过代理对象来访问目标对象.

>  这样可以在不修改原目标对象的前提下,提供额外的功能操作,扩展目标对象的功能.

> 简言之,代理模式就是设置了一个中间代理来控制访问原目标对象,以达到增强原对象的功能和简化访问方式

### I.静态代理:
> 这种代理方式需要代理对象和目标对象实现一样的接口,

>把java源文件编译成class文件过程中改变执行结果(扩展功能)

- 优点:可以在不修改目标对象的前提下扩展目标对象的功能
- 缺点:
    + 冗余.由于代理对象要实现与目标对象一致的接口,会产生过多的代理类
    + 不易维护.一旦接口修改方法,代理对象和目标对象都得进行修改
- 静态代理实例: 装饰者模式
- 静态代理框架: AspectJ

### II.动态代理:
> 动态代理利用了JDK API,动态的在内存中构建代理对象,从而实现对目标对象的代理功能.动态代理又被称为JDK代理或接口代理。

>把字节码运行到JVM虚拟机的过程中改变执行结果
- 特点: 动态代理对象不需要实现接口,但要求目标对象必须实现接口,否则不能用动态代理
- 动态代理实例
    + 使用的JDK反射框架下的代理类,只能针对有接口的类进行扩展功能
    ![image](https://ws4.sinaimg.cn/large/006tNbRwly1fvdnmlvwhqj30sl0dlada.jpg)
    调用LogProxy类的静态方法newProxyInstance(),完成代理.
    ```java
        public class UserServiceProxyTest {

        private UserService userService;

        @Before
        public void setUp() throws Exception {
        userService = new UserServiceImpl();
        userService = LogProxy.newProxyInstance(userService);
        }
         @Test
          public void LoginTest(){
          userService.login();
      }
    }
    ```
    + 使用第三方的CGLib框架,只能针对没有有接口但是可继承的类进行扩展功能
    ![image](https://ws1.sinaimg.cn/large/006tNbRwly1fvdnt3ojzvj30zt09vjtj.jpg)![image](https://ws3.sinaimg.cn/large/006tNbRwly1fvdnu0x3f0j30lq0cu76h.jpg)

### III.静态代理与动态代理的区别
1. 静态代理是在编译时就已经实现,编译完成后代理类是一个实际的class文件
2. 动态代理是在运行时动态生成的,即编译完成后没有实际的class文件,而是在运行时动态生成类字节码,并加载到JVM中.


## AOP(面向切面编程)
- AOP概述: 实际上在规定在代码的某个位置(何处)什么时候(何时)做什么事情(什么事情).

例如: 在登录的方法(何处)执行之前(何时),打印日志(什么事情)

### 一. 五大概念

#### I.    接入点(连接点) JoinPoint
> 接入点: 一般就是方法.可以为其添加增强功能的"位置",在spring中JoinPoint是一个类,代表被增强了的方法
#### II.   切点 Pointcut
> 切点: 切点实际上是一个表达式,表示哪些方法需要增强(何处)
#### III.  通知 advice
> 通知的作用: 对切点所描述的方法进行增强,在何时怎么增强.

- 前置通知: 方法执行之前
- 后置通知: 方法执行之后(不管抛没抛出异常)
- 返回通知: 方法执行完毕(若抛异常则此方法不执行)
- 异常通知: 方法执行抛出异常
- 环绕通知: 前后置都有

#### IV.   切面 aspect
> 切面: 切点和通知组成切面
#### V.    织入 weave
> 织入: 表示的是把切面与目标整合起来的过程

### 二. 在xml配置文件中配置AOP

- 将创建出来的bean设置为切面类(告诉spring这个对象是个切面)
```xml
    <bean id="loggerAspect" class="com.mrd.demo.aop.LoggerAspect"></bean>
    <aop:config>
    <aop:aspect id="logger" ref="loggerAspect"></aop>
    </aop:config>
```
- 配置切点表达式
```xml
    <aop:pointcut id="allMethod" expression="execution(* com.mrd.demo.service.UserService.*(..))"/>
```

- 配置通知

```xml
    <aop:config>
          <!-- 给UserService的所有方法设置成切点表达式   配置切点表达式 -->
        <aop:pointcut id="allMethod" expression="execution(* com.mrd.demo.service.UserService.*(..))"/>

        <!--   将上面创建出来的bean配置为切面类(告诉spring这个对象是个切面)-->
        <aop:aspect id="logger" ref="loggerAspect">
            <!-- 切点以可以设置在切面里面,配置在切面里面就不能达到共享,所以一般都设置在外面的最上面,为的是共享-->
            <aop:before method="LogBefore" pointcut-ref="allMethod" />
            <aop:after method="LogAfter" pointcut-ref="allMethod" />
            <aop:after-returning method="LoginAfterReturn" pointcut-ref="allMethod" />
            <aop:after-throwing method="LogAfterThrow" pointcut-ref="allMethod" />
            <aop:around method="around" pointcut-ref="allMethod" />
        </aop:aspect>
    </aop:config>

```
### 三. 使用注解配置AOP
> 使用注解前, 配置文件中配置

```xml
<aop:aspectj-autoproxy/>
```

- @Aspect
> 用来标记当前的类是一个切面
- @Order(0)
> 用来控制多个切面类的织入顺序,数值越小,优先级越高.

![image](https://ws2.sinaimg.cn/large/006tNbRwly1fvdtc61vjbj30dk03bmxv.jpg)

- @Pointcut("execution(* com.mrd.demo.service.*.*(..))")
> 配置切点表达式,多与一个空的方法配合使用

![image](https://ws4.sinaimg.cn/large/006tNbRwly1fvdtfzmdyfj30hd02m0sz.jpg)

- @Before()
> 前置通知,括号中可以直接填切点表达式,也可以填被@Pointcut注解的空方法名,以下的通知同理

```java
@Before("execution(* com.mrd.demo.service.*.*(..))")
public void LogBefore(JoinPoint jp) {
    // 接入点对象JoinPoint中保存了被增强的方法和类的信息

    // 获取到被增强的原始对象
    Object target = jp.getTarget();

    // 获取被增强的方法的名字
    String name = jp.getSignature().getName();

    System.out.println(jp.toLongString() + "方法执行之前");

    // jp.toLongSstring 返回的是该方法调用的切点表达式
}

@Before("allServiceMethod()")
```

- @After  前置通知
- @AfterThrowing  异常通知
- @AfterReturning 返回通知
- @Around 环绕通知(功能最强)

```java
    @Around("allServiceMethod()")
    public Object around(ProceedingJoinPoint jp) {

        try {
            // 此处 前置通知

            // 在进行方法的实际调用
            Object result = jp.proceed();
            //此处 返回通知
            return result;
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            // 此处 异常通知
        }
        // 后置通知
        return null;
    }
}
```
