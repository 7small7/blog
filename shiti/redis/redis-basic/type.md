## Redis使用场景

1.数据缓存(用户信息、商品数量、文章阅读数量)

2.消息推送(站点的订阅)

3.队列(削峰、解耦、异步)

4.排行榜(积分排行)

5.社交网络(共同好友、互踩、下拉刷新)

6.计数器(商品库存，站点在线人数、文章阅读、点赞)

7.基数计算

8.GEO计算

## Redis功能特点

1. 持久化
2. 丰富的数据类型(string、list、hash、set、zset、发布订阅等)
3. 高可用方案(哨兵、集群、主从)
4. 事务
5. 丰富的客户端
6. 提供事务
7. 消息发布订阅
8. Geo
9. HyperLogLog
10. 事务

## 你是怎么用Redis做异步队列的？

1. 一般使用list结构作为队列，rpush生产消息，lpop消费消息。当lpop没有消息的时候，要适当sleep一会再重试。

2. 如果对方追问可不可以不用sleep呢？list还有个指令叫blpop，在没有消息的时候，它会阻塞住直到消息到来。

3. 如果对方追问能不能生产一次消费多次呢？使用pub/sub主题订阅者模式，可以实现1:N的消息队列。

4. 如果对方追问pub/sub有什么缺点？在消费者下线的情况下，**生产的消息会丢失**，可以使用Redis6增加的**stream数据类型**，也可以使用专业的**消息队列如rabbitmq等**。

5. 如果对方追问redis如何实现延时队列？**使用sortedset，拿时间戳作为score**，消息内容作为key调用zadd来生产消息，消费者用zrangebyscore指令获取N秒之前的数据轮询进行处理。