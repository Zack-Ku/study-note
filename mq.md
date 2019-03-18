# 特性

https://github.com/doocs/advanced-java/blob/master/docs/high-concurrency/why-mq.md

###优点

- 解耦：通过订阅/发布的模型，达到系统间的解耦 

- 异步：A系统给B系统发送消息后，B系统自己消费处理。A无需理会

- 削峰：通过控制mq消费的最大速率，达到削封填谷的效果。



###缺点

- 降低可用性：mq挂了影响系统

- 增加系统复杂度：增加多中间件，提高维护成本

- 一致性问题：A系统发送消息完会就返回成功了，但其他系统可能消费失败

## 各MQ对比

**分布式MQ**：

kafka：大数据领域常用，社区活跃，broker为一个进程、一个topic数据分到不同的partition，落到不同的broker上做分布式。

rocketMQ：阿里开发

**传统MQ**：

RabbitMQ：erlang写的，时效性高、延时低，主从镜像做高可用，不丢数据

activeMQ：比较老旧，有一定几率丢失数据



# 如何避免重复消费

消息重发消费无法避免，需要做的是代码做幂等处理，保证数据不重复。例如db不写多条

# 如何避免数据丢失

rabbitmq：rabbitmq自身开启数据持久化、然后消费者ack机制

# 手写MQ

大概是在synchronize关键词用notify await

# 延时队列

rabbitMQ中，可以设置TTL，然后在一条queue上的消息过期了，就会转到Dead letter exchange中，然后消费对应绑定的queue









