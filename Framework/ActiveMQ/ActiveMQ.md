# ActiveMQ 消息队列
[ActiveMQ 的使用场景](https://www.cnblogs.com/HigginCui/p/6478613.html)

##### 下载
`http://mirrors.hust.edu.cn/apache/activemq/5.15.7/apache-activemq-5.15.7-bin.zip`

##### Docker
- `docker pull webcenter/activemq`
- `docker run -it --name activemq -p 8161:8161 -p 61616:61616 webcenter/activemq`

## 消息队列

#### 管理页面 `localhost:8161/admin/`
- JAVA项目需配置 61616 端口来发送消息队列
- 队列类型 Queues | Topics

##### Queues: 发送一条消息, 只能有一个客户端去'消费'这个消息
##### Topics: 发送一条消息, 可由多个客户端同时取出


### Send 测试用
- Destination: "com.ric.SHORT_MESSAGE"
- Queue or Topic: 选择一个类型
- Message body: 消息体, JSON 数据构造
```JSON
{"phone":"13633344444", "message":"赶紧还钱"}
```
- 此时 Queues 即会有消息进入
![FpSFje.png](https://s1.ax1x.com/2018/11/19/FpSFje.png)

## Queue
#### 配置

- application.properties
```properties
spring.activemq.broker-url=tcp://localhost:61616
```

- service
```java
@Service
public class ShortMessageService {

    // Resource 根据变量名装配, 因此变量名需要为固定的写法.
    @Resource
    private JmsMessagingTemplate jmsMessagingTemplate;

    @Resource
    private Queue shortMessageQueue;

    public void sendVerifyCode() {
        // 参数1: 给予 Queue 或 Topic (创建类)
        // 参数2: 给予消息字符串 (也可以把一个对象转换为 json 数据传递出去)
        jmsMessagingTemplate.convertAndSend(shortMessageQueue, "Hello ActiveMQ!" + Math.random());

    }
}
```

- config.ActiveMQConfig
```java
@Configuration
public class ActiveMQConfig {

    public static final String QUEUE_SHORT_MESSAGE = "com.ric.demo.SHORT_MESSAGE_QUEUE";

    // 创建一个目的地的 Bean
    @Bean
    public Queue shortMessageQueue() {
        return new ActiveMQQueue(QUEUE_SHORT_MESSAGE);
    }
}
```
#### 测试
- controller
```java
@RestController
public class ShortMsgController {

    @Resource
    private ShortMessageService shortMessageService;

    @RequestMapping("/send")
    public String send() {
        shortMessageService.sendVerifyCode();
        return "发送成功";
    }
}
```

- 此时可以在 http://localhost:8161/admin/queues.jsp 中查看到此条消息

#### 使用 Java 获取消息队列中的消息

- service <br>
`@JmsListener(destination = "")`
```java
// 同项目中获取消息队列的消息
@JmsListener(destination = ActiveMQConfig.QUEUE_SHORT_MESSAGE)
public void receiveMessage(String message) {
    System.out.println("接收到消息: " + message);
}
```

## Topic
#### 发送与接收
- ActiveMQConfig <br>
  - 创建 topic 目的地
  - 创建工厂指定 PubSubDomain 为 true

  ```java
  public static final String TOPIC_ORDER_LIST = "com.ric.demo.ORDER_LIST_TOPIC";
  // TOPIC
  @Bean
  public Topic orderTopic() {
      return new ActiveMQTopic(TOPIC_ORDER_LIST);
  }

  @Bean
  public JmsListenerContainerFactory<?> topicListenerContainer(ConnectionFactory activeMQConnectionFactory) {
      DefaultJmsListenerContainerFactory topicListenerContainer = new DefaultJmsListenerContainerFactory();
      topicListenerContainer.setPubSubDomain(true);
      topicListenerContainer.setConnectionFactory(activeMQConnectionFactory);
      return topicListenerContainer;
  }
  ```

- service <br>
```java
@Service
public class OrderService {

    @Resource
    private JmsMessagingTemplate jmsMessagingTemplate;

    @Resource
    private Topic orderTopic;

    public String disposeOrder() {
        jmsMessagingTemplate.convertAndSend(orderTopic, "处理订单" + Math.random());
        return "订单处理完成";
    }
}
```

- controller <br>
```java
@RestController
@RequestMapping("/order")
public class OrderListController {

    @Resource
    private OrderService orderService;

    @GetMapping("/dispose")
    private String dispose() {
        return orderService.disposeOrder();
    }
}
```

#### 接收

- service 层可以单独创建类 OrderHandler 并给予组件注解 @Component
  - 需指定 containerFactory 为 config 中的工厂方法名
```java
@Component
public class OrderHandler {

    @JmsListener(destination = ActiveMQConfig.TOPIC_ORDER_LIST, containerFactory = "topicListenerContainer")
    public void orderReceive(String topic) {
        System.out.println("处理订单消息: " + topic);
    }
}
```

#### 传递实体类 JSON 数据
> 序列化: 把一个对象转换为能记录下来的格式

- entity

  ```java
  @Data
  @NoArgsConstructor
  @AllArgsConstructor
  public class MsgEntity implements Serializable {

      private Long id;
      private String mobile;
      private String message;

  }
  ```

- shortMessageService
```java
public void sendVerifyCode() {
        // 参数1: 给予 Queue 或 Topic (创建类)
        // 参数2: 给予消息字符串 (也可以把一个对象转换为 json 数据传递出去)
        // 但需要一定的操作: 配置消息转换器
        MsgEntity msg = new MsgEntity(10L, "13746351591", "欠债还钱");
        jmsMessagingTemplate.convertAndSend(shortMessageQueue, msg);
    }
```

- 此时发现无法解析出 JSON 数据. 因此需配置一个消息转换器
  - ActiveMQConfig
  ```java
  // 配置消息转换器
      @Bean
      public MessageConverter messageConverter() {
          MappingJackson2MessageConverter converter = new MappingJackson2MessageConverter();
          // 设置文本消息类型
          converter.setTargetType(MessageType.TEXT);
          // 设置 typeId属性名: 随意书写 --> 把原始数据类型转换为 json 数据
          converter.setTypeIdPropertyName("type");
          // 将其返回
          return converter;
      }
  ```
- 消息转换器设置的 "type" <br>
![FpSicD.png](https://s1.ax1x.com/2018/11/19/FpSicD.png)
- 数据结果 <br>
![FpSP1O.png](https://s1.ax1x.com/2018/11/19/FpSP1O.png)

- 当然, 接取数据的时候需要用实体类去接

  ```java
  @JmsListener(destination = ActiveMQConfig.QUEUE_SHORT_MESSAGE)
  public void receiveMessage(MsgEntity message) {
      // 在这里可以处理此 message, 例如可以在此给用户发送手机短信
      System.out.println("接收到消息: " + message);
  }
  ```
