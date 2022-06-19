> 专注于PHP、MySQL、Linux和前端开发，感兴趣的感谢点个关注哟！！！文章整理在[GitHub](https://github.com/7small7),[Gitee](https://gitee.com/bruce_qiq)主要包含的技术有PHP、Redis、MySQL、JavaScript、HTML&CSS、Linux、Java、Golang、Linux和工具资源等相关理论知识、面试题和实战内容。

@author:[7small7](https://github.com/7small7)。

@source:[公众号-菜鸟成长学习笔记](/site/)。

@project:[微信小程序 程序员面试题大全](/site/)。

[B站视频链接地址一](https://www.bilibili.com/video/BV1iT411G71S?share_source=copy_web)

[B站视频链接地址二](https://www.bilibili.com/video/BV1Jf4y1f7oJ?share_source=copy_web)

## Redis实现队列功能

在日常开发中，很多时候我们可能会使用队列实现异步任务的分发。例如用户下单的积分成长值增加、消息发送等等常见。这种场景可以使用Redis中的`list`数据类型来实现队列功能。但存在不足的几点：

1. 程序异常如何实现队列消息回滚?

2. 消息消费中，进程异常如何保证消息不丢失?

3. 多消费者该如何处理?

## Redis Stream是什么?

1. Redis Stream 是 Redis 5.0 版本新增加的数据结构。

2. Redis Stream 主要用于消息队列（MQ，Message Queue），Redis 本身是有一个 Redis 发布订阅 (pub/sub) 来实现消息队列的功能，但它有个缺点就是消息无法持久化，如果出现网络断开、Redis 宕机等，消息就会被丢弃。简单来说发布订阅 (pub/sub) 可以分发消息，但无法记录历史消息。而 Redis Stream 提供了消息的持久化和主备复制功能，可以让任何客户端访问任何时刻的数据，并且能记住每一个客户端的访问位置，还能保证消息不丢失。

3. Redis Stream 的结构如下所示，它有一个消息链表，将所有加入的消息都串起来，每个消息都有一个唯一的 ID 和对应的内容：
![](http://qiniucloud.qqdeveloper.com/202206051520866.png)

## 组成部分

1. Redis Stream由消息ID、消费者群组和消费者三大部分组成。每一次添加消息到Streams中，消息ID会向后增加。消息ID可以手动指定也可以有Redis内部自动生成。`消息ID只能是整数`，采用Redis自动生成时，组成的部分是`当前时间毫秒时间戳-当前毫秒数生成的序号`。每一个消费者ID对应一个消费者群组，每一个消费者群组下面又存在多个消费者。

2. 一个消息队列，可以存在多个消费者群组。每个消费者群组之间消息消息是隔离的。

3. 每个消费者群组消费一次消息之后，`last_delivered_id`会自动往后移动。当该群组下的消费者消费消息之后，其他的消费者就不能接着消费该消息。每一个消费者消费之后，严格需要ack进行确认，该消息才会被标识为真正消费。否则Pending_ids[]将记录未进行ack的消息。

## 基础操作

### 队列操作

1. 添加队列，`xadd 队列名称 队列id key1 value1 key2 value2 ....`。

```shell
127.0.0.1:6379> xadd stream1 * id 1 name lisi age 12
"1654413784100-0"
```
> 队列ID的组成部分由当前的时间戳-序号生成组成，并且都只能是整数。如果当前同一个时间戳内生成多个ID，则序号自增的。这种方式也可以解决时间回拨问题。

1. 队列长度

```shell
127.0.0.1:6379> xlen stream1
(integer) 1
```
3. 队列元素，`xrange 队列名称 队列起始位置 队列结束位置 [count 读取个数]`。

```shell
127.0.0.1:6379> xrange stream1 - +
1) 1) "1654413784100-0"
   2) 1) "id"
      2) "1"
      3) "name"
      4) "lisi"
      5) "age"
      6) "12"
```
> 起始位置实则会消息的ID，使用-、+作为起始位置表示从队列的开始位置和结束位置开始读取。

## 消费组操作

### 插入队列数据
首先我们创建一个队列，并向其中插入消息。
```shell
127.0.0.1:6379> xadd stream1 * id 1 name zhangsan age 12
"1654335810726-0"
127.0.0.1:6379> xadd stream1 * id 2 name lisi age 12
"1654335821262-0"
127.0.0.1:6379> xadd stream1 * id 3 name wangwu age 12
"1654335829951-0"
127.0.0.1:6379> xadd stream1 * id 4 name tony age 12
"1654335839247-0"
127.0.0.1:6379> xadd stream1 * id 5 name tom age 12
"1654335850424-0"
```

### 创建消费者群组
向队列创建消费群组。`xgroup create 队列名称 消费者组 消息ID开始位置-消息ID结束位置`。
```shell
127.0.0.1:6379> xgroup create stream1 cg1 0-0
OK
127.0.0.1:6379> xgroup create stream1 cg2 0-0
OK
```

### 查看消费者群组
查看消费者群组。`xinfo groups 队列名称`。
```
127.0.0.1:6379> xinfo groups stream1
1)  1) "name"// 群组名称
    2) "cg1"
    3) "consumers"// 群组下消费者数量
    4) (integer) 0
    5) "pending"// 待确认的ack消息数
    6) (integer) 0
    7) "last-delivered-id"// 消费群组最后一次消费的消息ID
    8) "0-0"
    9) "entries-read"
   10) (nil)
   11) "lag"
   12) (integer) 5
2)  1) "name"
    2) "cg2"
    3) "consumers"
    4) (integer) 0
    5) "pending"
    6) (integer) 0
    7) "last-delivered-id"
    8) "0-0"
    9) "entries-read"
   10) (nil)
   11) "lag"
   12) (integer) 5
```

### 读取消费组信息

消费队列消息。`xreadgroup group 消费者群组名 消费者名称 count 消费个数 streams 队列名称 消息消费ID`。
```
127.0.0.1:6379> xreadgroup group cg1 c1 count 1 streams stream1 >
1) 1) "stream1"
   1) 1) 1) "1654335810726-0"
         1) 1) "id"
            1) "1"
            2) "name"
            3) "zhangsan"
            4) "age"
            5) "12"
127.0.0.1:6379> xreadgroup group cg1 c2 count 1 streams stream1 >
1) 1) "stream1"
   2) 1) 1) "1654335821262-0"
         2) 1) "id"
            2) "2"
            3) "name"
            4) "lisi"
            5) "age"
            6) "12"
```

![](https://qiniucloud.qqdeveloper.com/public_image.png)