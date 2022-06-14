> 专注于PHP、MySQL、Linux和前端开发，感兴趣的感谢点个关注哟！！！文章整理在[GitHub](https://github.com/7small7),[Gitee](https://gitee.com/bruce_qiq)主要包含的技术有PHP、Redis、MySQL、JavaScript、HTML&CSS、Linux、Java、Golang、Linux和工具资源等相关理论知识、面试题和实战内容。

@author:[7small7](https://github.com/7small7)。

@source:[公众号-菜鸟成长学习笔记](/site/)。

@project:[微信小程序 程序员面试题大全](/site/)。

## 聊聊Redis现状

Redis作为一种内存型的非关系型的数据库，不管在互联网大厂，小厂，大项目和小项目中，几乎都会被使用。为什么Redis会受到如此青睐呢？关于这个问题，可能很多的程序员只是看着别人用而用，缺乏对Redis一个全面的了解。

## Redis使用场景

### 缓存
缓存现在几乎是所有中大型网站都在用的必杀技，合理的利用缓存不仅能够提升网站访问速度，还能大大降低数据库的压力。Redis提供了键过期功能，也提供了灵活的键淘汰策略，所以，现在Redis用在缓存的场合非常多。

### 排行榜
很多网站都有排行榜应用的，如京东的月度销量榜单、商品按时间的上新排行榜等。Redis提供的有序集合数据类构能实现各种复杂的排行榜应用。

### 计数器
什么是计数器，如电商网站商品的浏览量、视频网站视频的播放数等。为了保证数据实时效，每次浏览都得给+1，并发量高时如果每次都请求数据库操作无疑是种挑战和压力。Redis提供的incr命令来实现计数器功能，内存操作，性能非常好，非常适用于这些计数场景。

### 分布式会话
集群模式下，在应用不多的情况下一般使用容器自带的session复制功能就能满足，当应用增多相对复杂的系统中，一般都会搭建以Redis等内存数据库为中心的session服务，session不再由容器管理，而是由session服务及内存数据库管理。

### 分布式锁
在很多互联网公司中都使用了分布式技术，分布式技术带来的技术挑战是对同一个资源的并发访问，如全局ID、减库存、秒杀等场景，并发量不大的场景可以使用数据库的悲观锁、乐观锁来实现，但在并发量高的场合中，利用数据库锁来控制资源的并发访问是不太理想的，大大影响了数据库的性能。可以利用Redis的setnx功能来编写分布式的锁，如果设置返回1说明获取锁成功，否则获取锁失败，实际应用中要考虑的细节要更多。

### 社交网络
点赞、踩、关注/被关注、共同好友等是社交网站的基本功能，社交网站的访问量通常来说比较大，而且传统的关系数据库类型不适合存储这种类型的数据，Redis提供的哈希、集合等数据结构能很方便的的实现这些功能。

### 最新列表
Redis列表结构，LPUSH可以在列表头部插入一个内容ID作为关键字，LTRIM可用来限制列表的数量，这样列表永远为N个ID，无需查询最新的列表，直接根据ID去到对应的内容页即可。

### 消息系统
消息队列是大型网站必用中间件，如ActiveMQ、RabbitMQ、Kafka等流行的消息队列中间件，主要用于业务解耦、流量削峰及异步处理实时性低的业务。Redis提供了发布/订阅及阻塞队列功能，能实现一个简单的消息队列系统。另外，这个不能和专业的消息中间件相比。

## 如何使用

上面提到了各种使用场景，在这些场景使用中，无非就是对Redis数据类型的操作。就需要对Redis数据类型有所了解。在Redis中有这些数据类型。
String、Hash、List、Set、Zset、GEO、Stream、HyperLogLog、BitMap。

## 数据使用场景

## String类型

String类型是一种字符串类型，类似一种键值对的形式。

![](http://qiniucloud.qqdeveloper.com/202206150044835.png)

一般我们用String类型用来存储商品数量、用户信息和分布式锁等应用场景。

### 存储商品数量。
```redis
set goods:count:1 1233
set goods:count:2 100
```
### 用户信息。
```redis
set user:1 "{"id":1, "name":"张三", "age": 12}"
set user:2 "{"id":2, "name":"李四", "age": 12}"
```
### 分布式锁。
```redis
# 设置一个不存在的键名为id:1值为10， 过期时间为10秒。
127.0.0.1:6379> set id:1 10 ex 10 nx
OK
127.0.0.1:6379> get id:1
"10"
# 当前的键还未过期，在次设置则不会设置成功。
127.0.0.1:6379> set id:1 10 ex 10 nx
(nil)
# 当10秒之后去获取，当前的键则为空。
127.0.0.1:6379> get id:1
(nil)
```
> 用Redis实现分布式锁的原理，当一个键存在则设置失败。

## hash类型

hash类型是一种类似关系型数据库结构的数据结构。有一个键名，键存的内容是以键值对的形式存在。

![](http://qiniucloud.qqdeveloper.com/202206150045349.png)

利用hash结构，我们可以用来存储用户信息、对象信息等业务场景。

### 存用户信息。
```redis
127.0.0.1:6379> hset user:1 id 1 name zhangsan age 12 sex 1
(integer) 4
127.0.0.1:6379> hset user:2 id 2 name lisi age 14 sex 0
(integer) 4
127.0.0.1:6379> hmget user:1 id name age sex
1) "1"
2) "zhangsan"
3) "12"
4) "1"
```
### 存储对象信息。
```redis
127.0.0.1:6379> hset object:user id public-1 name private-zhangsan
(integer) 2
127.0.0.1:6379> hmget object:uesr id name
1) (nil)
2) (nil)
127.0.0.1:6379> hget object:user id
"public-1"
127.0.0.1:6379>
```
> 这里存储一个user对象，对象里面有两个属性，分别是id和name字，分别存储的是属性的访问权限和默认值拼接。

## list类型

list类型是一个列表类型的数据结构。用一个键，按照顺序排列数据。

![](http://qiniucloud.qqdeveloper.com/202206150045983.png)

list一般用在的场景是队列、栈和秒杀等场景。

### 队列。
```redis
127.0.0.1:6379> lpush list:1 0 1 2 3 4 5 6
(integer) 7
127.0.0.1:6379> rpop list:1 1
1) "0"
127.0.0.1:6379> rpop list:1 1
1) "1"
127.0.0.1:6379> rpop list:1 1
1) "2"
```
> 使用list实现队列，是因为队列遵循先进先出的特点。

### 栈。
```redis
127.0.0.1:6379> lpush list:1 3 4 5 6
(integer) 3
127.0.0.1:6379> lpop list:1
"6"
127.0.0.1:6379> lpop list:1
"5"
127.0.0.1:6379> lpop list:1
"4"
127.0.0.1:6379> lpop list:1
"3"
```
> 使用list实现队列，是因为队列遵循后进先出的特点。

### 秒杀。
```redis
127.0.0.1:6379> lpush order:user 11 12 14 15 16 17
(integer) 6
```
> 在秒杀场景下，我们可以将秒杀成功的用户先写进队列，后续的业务在根据队列中数据进行处理。

## set类型

zet是一种集合类型，并且这种集合内的元素是无需且不会重复的。

![](http://qiniucloud.qqdeveloper.com/202206150043477.png)

set类型一般可以用在用户签到、网站访问统计、用户关注标签、好友推荐、猜奖、随机数生成等业务场景。

### 某日用户签到情况。
```redis
127.0.0.1:6379> sadd sign:2020-01-16 1 2 3 4 5 6 7 8
(integer) 8
127.0.0.1:6379> smembers sign:2020-01-16
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
6) "6"
7) "7"
8) "8"
```
> 键为具体某日，存储的值则是签到用户的id。

### 用户关注标签。
```redis
127.0.0.1:6379> sadd user:1:friend 1 2 3 4 5 6
(integer) 6
127.0.0.1:6379> sadd user:2:friend 11 22 7 4 5 6
(integer) 6
127.0.0.1:6379> sinterstore user:relation user:1:friend user:2:friend
(integer) 3
127.0.0.1:6379> smembers user:relation
1) "4"
2) "5"
3) "6"
```
> 用户1关注了id为1，2，3，4，5，6的栏目。用户2关注了id为11，22，7，4，5，6的栏目。这里取两个用户共同关注的栏目。

### 猜奖。
```redis
127.0.0.1:6379> spop user:2:friend 1
1) "5"
```
> 用set实现猜奖，主要是使用了随机抛出集合类的元素的特点。

## zset类型

zset类型和set类型都是属于集合类型，两者不同点，在设置zset数据时要设置一个分数，这个分数可以用来做数据排序,并且zset类型的数据是有序的，因此zset也被叫做有序集合。

![](http://qiniucloud.qqdeveloper.com/202206150042519.png)

zset除了可以用在set可以用的场景下，更多的是可以用在排序的场景，如排行榜、延迟队列，就像未支付的订单在多少时间内就失效。

### 签到排行榜。
```redis
127.0.0.1:6379> zadd goods:order 1610812987 1
(integer) 1
127.0.0.1:6379> zadd goods:order 1610812980 2
(integer) 1
127.0.0.1:6379> zadd goods:order 1610812950 3
(integer) 1
127.0.0.1:6379> zadd goods:order 1610814950 4
(integer) 1
127.0.0.1:6379> zcard goods:order
(integer) 4
127.0.0.1:6379> zrangebyscore goods:order 1610812950 1610812987
1) "3"
2) "2"
3) "1"
```
> 将用户的签到时间作为排行的分数，最后查询指定范围内签到用户的id。

## Bitmaps类型

Bitmaps底层存储的是一种二进制格式的数据。在一些特定场景下，用该类型能够**极大的减少存储空间**，因为存储的数据只能是0和1。为了便于理解，可以将这种数据格式理解为一个数组的形式存储。

![](http://qiniucloud.qqdeveloper.com/202206150046037.png)

利用该特点，可以将该类型用在一些访问统计、签到统计等场景。

### 某个用户一个月的签到记录。
```redis
127.0.0.1:6379> setbit user:2020-01 0 1
(integer) 0
127.0.0.1:6379> setbit user:2020-01 1 1
(integer) 0
127.0.0.1:6379> setbit user:2020-01 2 1
(integer) 0
127.0.0.1:6379> bitcount user:2020-01 0 4
(integer) 3
```
> 统计出该用户这个月只有4天签到。

### 统计某一天网站的签到数量。
```redis
127.0.0.1:6379> setbit site:2020-01-17 1 1
(integer) 0
127.0.0.1:6379> setbit site:2020-01-17 3 1
(integer) 0
127.0.0.1:6379> setbit site:2020-01-17 4 1
(integer) 0
127.0.0.1:6379> setbit site:2020-01-17 6 1
(integer) 0
127.0.0.1:6379> bitcount site:2020-01-17 0 100
(integer) 4
127.0.0.1:6379> getbit site:2020-01-17 5
(integer) 0
```
> 这里将用户的id作为偏移量，签到就是1。可以统计出具体访问的总数，同时可以根据某个用户的id查询是否在当前签到。如果根据偏移量重复设置一个值，此时不会被重复添加，只是Redis会返回1表示当前已经存在。

### 计算某段时间内，都签到的用户数量。
```redis
127.0.0.1:6379> setbit site:2020-01-18 6 1
(integer) 0
127.0.0.1:6379> setbit site:2020-01-18 4 1
(integer) 0
127.0.0.1:6379> setbit site:2020-01-18 7 1
(integer) 0
127.0.0.1:6379> bitop and continue:site site:2020-01-18 site:2020-01-17
(integer) 1
```
> 使用该场景，是因为该数据类型可以计算出多个key的交集(and)。同时可以取并集(or),或(or),异或(xor)。

## HypefLogLog类型

HypefLogLog类型从使用上来说，有点类似于集合类型。该类型实际是一种字符串类型的数据结构。使用该类型最大的好处就是减少空间、但是也存在一定的误差率。该类型也是不允许同一个key存在重复元素。该类型也支持合并多个key的值。

![](http://qiniucloud.qqdeveloper.com/202206150046832.png)

该数据类型一般用在一些不需要精确计算的统计类场景。

### 用户签到统计。
```redis
127.0.0.1:6379> pfadd 2020:01:sgin  1 2 3 4 5 6 7 8
(integer) 1
# 尝试重复添加
127.0.0.1:6379> pfadd 2020:02:sgin  1 2 3 4 5 6 7 8
(integer) 0
127.0.0.1:6379> pfadd 2020:02:sgin  9
(integer) 1
127.0.0.1:6379> pfadd 2020:02:sgin  10
(integer) 1
127.0.0.1:6379> pfadd 2020:02:sgin  11
(integer) 1
127.0.0.1:6379> pfcount 2020:02:sgin
(integer) 11
```

## GEO类型

GEO类型是一种存储地理信息的数据格式，基于该数据特点。可以用在一些距离计算、附近推荐等业务场景。

### 距离计算
```redis
127.0.0.1:6379> geoadd city:distance nx 121.32 42.36 beijing 121.20 38.56 
shanghai 100.36 38.56 sichuan
(integer) 3
127.0.0.1:6379> geopos city:distance beijing shanghai sichuan
1) 1) "121.32000178098678589"
   2) "42.36000020595371751"
2) 1) "121.19999974966049194"
   2) "38.55999947301710762"
3) 1) "100.3599974513053894"
   2) "38.55999947301710762"
# 计算出北京到上海的距离
127.0.0.1:6379> geodist city:distance beijing shanghai km
"422.7819"
```

## Stream类型

Stream类型是Redis在5.0之后版本新增的一种数据结构。该数据结构主要用户消息队列的场景。Redis 本身是有一个 Redis 发布订阅 (pub/sub) 来实现消息队列的功能，但它有个缺点就是消息无法持久化，如果出现网络断开、Redis 宕机等，消息就会被丢弃。

### 消息队列
```redis
# 添加消息队列
127.0.0.1:6379> xadd message * name zhangsan age 12
"1610873104343-0"
127.0.0.1:6379> xrange message - +
1) 1) "1610873104343-0"
   2) 1) "name"
      2) "zhangsan"
      3) "age"
      4) "12"
# 获取消息队列
127.0.0.1:6379> xrevrange message + - count 1
1) 1) "1610873104343-0"
   2) 1) "name"
      2) "zhangsan"
      3) "age"
      4) "12"
```

