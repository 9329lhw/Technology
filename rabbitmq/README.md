## rabbimq
### 基
    ACK（confirm机制） 
    如何保证消息百分百投递成功 
        方案一:消息信息落库,对消息状态进行打标(常见方案)
        方案二:消息的延迟投递，做二次确认，回调检查（不常用，大厂在用的高并发方案）
    幂等性 
        用户对于同一操作发起的一次请求或者多次请求的结果是一致的
        主流实现方案:唯一ID+指纹码
    return机制 
        用于处理一些不可路由的消息。也是生产段添加的一个监听。
    限流
        假设我们有这样的场景 Rabbitmq服务器有上万条未处理的消息,我们随便打开一个Con - Client,
        会造成:巨量的消息瞬间全部推送过来,然而我们单个客户端无法同时处理这么多数据!此时很有可
        能导致服务器崩溃，严重的可能导致线上的故障。 
        还有一些其他的场景，比如说单个Pro一分钟产生了几百条数据,但是单个Con一分钟可能只能处理
        60条,这个时候Pro-Con肯定是不平衡的。通常Pro是没办法做限制的。所以Con肯定需要做一些限流
        措施，否则如果 超出最大负载，可能导致Con性能下降，服务器卡顿甚至崩溃等一系列严重后果
    重回队列 
        重回队列是为了对没有处理成功的消息,将消息重新投递给Broker 
        重回队列,会把消费失败的消息重新添加到队列的尾端,供Con继续消费 
        一般在实际应用中,都会关闭重回队列,即设置为false
    TTL 
        TTL(Time To Live),即生存时间 
        RabbitMQ支持消息的过期时间，在消息发送时可以进行指定 
        RabbitMQ支持为每个队列设置消息的超时时间，从消息入队列开始计算，只要超过了队列的超时时间
        配置，那么消息会被自动清除
    死信队列
        DLX - 死信队列(dead-letter-exchange) 利用DLX,当消息在一个队列中变成死信 (dead message) 之后,
        它能被重新publish到另一个Exchange中,这个Exchange就是DLX
        使用场景：消息被拒绝(basic.reject / basic.nack),并且requeue = false 消息因TTL过期 
        队列达到最大长度
### 常見問題
     如何保证RabbitMQ不被重复消费？    
     
     PHP实战RabbitMQ之延时队列篇
     https://segmentfault.com/a/1190000022774099
     
     RabbitMQ和Kafka:如何处理消息丢失问题
     https://segmentfault.com/a/1190000040394882
     RabbitMQ和Kafka:如何保证消息的顺序性
     https://segmentfault.com/a/1190000040394974
     七种模式介绍与应用场景
     https://segmentfault.com/a/1190000040126023
     
     消息中间件MQ与RabbitMQ面试题（2021最新版）
     https://segmentfault.com/a/1190000039973497
