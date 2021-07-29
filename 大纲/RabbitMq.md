queue可以有很多人都来监听。

订单服务启动多个，同一个消息，只能有一个客户端收到

只有一个消息完全处理完，方法运行结束，就可以接收到下一个消息





### 使用

1. 添加@EnableRabbitMq注解

2. 在配置文件中配置RabbitMq相关信息

   ```properties
   spring.rabbitmq.host=192.168.90.10
   spring.rabbitmq.port=5672
   spring.rabbitmq.virtual-host=/
   ```

3. 配置RabbitMqConfig

   ```java
   @Configuration
   public class RabbitConfig {
   
       @Bean
       public MessageConverter messageConverter() {
           return  new Jackson2JsonMessageConverter();
       }
   }
   ```

   

4. 发送消息使用spring封装好的RabbitTemplate，通过convertAndSend方法，其中可以发送Object类型数据，但是需要Object类型数据实现序列化接口

5. 接受消息使用@RabbitListener（可以标注在类和方法上），可以在方法的参数列表中添加上消息的Bean类型，自动转换为所需类型。

6. @RabbitHandler（只能标在方法上）： 可以和@RabbitListener联合使用，将@RabbitListener标在类上，然后将@RabbitHandler标在方法上，方法的参数类型不同，就可以接受不同类型的参数，让不同的方法处理不同的参数类型。

### RabbitMq可靠投递

#### 抵达服务器确认

1. 配置spring.rabbitmq.publisher-confirm-type = correlated

2. 在RabbitMqConfig中设置抵达服务器的回调函数：

   ```java
   @PostConstruct // 在MyRabbitConfig对象创建完成以后，执行这个方法
   public void initRabbitTemplate() {
       rabbitTemplate.setConfirmCallback((correlationData, b, s) -> {
           System.out.println("confirm >>> correlationData["+ correlationData + "]");
           System.out.println("ack >>> ack["+ b + "]");
           System.out.println("ack >>> cause["+ s + "]");
       });
   }
   ```

#### 投递队列成功确认

1. 配置投递确认

   ```
   spring.rabbitmq.publisher-returns=true
   ## 只要抵达队列，就以异步发送优先回调return confirm
   spring.rabbitmq.template.mandatory=true
   ```

2. 设置投递给队列成功的回调函数

```java
// 只有在投递给队列失败的时候，才会打印消息
rabbitTemplate.setReturnsCallback(returnedMessage -> System.out.println("failed Message:"+returnedMessage));
```
3. 当发现有投递失败的消息时，可以将消息保存到数据库中，定时扫描数据库，然后重新投递

#### 消费端确认 -- ACK消息确认机制

保证每个消息被正确消费，此时才可以在broker删除消息

默认自动确认，只要消息接收到，客户端就自动确认，服务端就会移除消息。   

**问题：** 收到很多消息，自动恢复给服务器ACK ， 只有一个消息处理成功，宕机了。发生消息丢失。

**解决办法 ：**手动确认。只要没有明确告诉MQ ，没有ack，消息就一直是unacked，即使consumer宕机，消息不会丢失，会重新变为ready状态。下次有新的consumer连接就发给它。



```java
channel.basicAck(deliveryTag, false); // 签收
channel.basicNack(deliveryTag, false, false); // 拒签
```



