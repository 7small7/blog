本文将分享在Golang中如何操作Redis。文章中演示的组件库为[go-redis](https://redis.uptrace.dev/guide/go-redis-vs-redigo.html)，本文会对该组件进行详细的演示。

## go-redis

go-redis是一个基于Golang语言的Redis客户端组件。其功能也非常的强大与完善。支持如下功能。
1. ✅ Redis通用命令支持、各大数据类型支持。
2. ✅ Redis Cluster支持。
3. ✅ Redis Replication支持。
4. ✅ Redis Sentinel支持。
5. ✅ 支持管道、事务、发布/订阅、Luau脚本、模拟和分布式锁等。

对应使用Golang操作Redis，另外还有一个组件，该组件相对go-redis有一些区别，2个项目之间的主要区别在于go-redis为每个Redis命令提供了类型安全的API。大致区别如下图：
![](http://qiniucloud.qqdeveloper.com/202205241506624.png)

## 使用演示

首先在本地编译安装Redis服务，这里可以根据自己的方式来进行安装，只要能保证Redis可使用就行。
```shell
// 解压文件
tar -zxvf redis-5.3.7.tgz

// 编译并安装
cd redis-5.3.7 && make && make install

// 配置Redis
需要将redis.conf中的daemon 设置为yes 开启守护进程模式运行。

// 启用Redis服务
redis-server ./redis.conf
```

接下来就可以正常操作Redis服务。

### 配置Redis连接信息

```go
package config

import (
	"time"

	"github.com/go-redis/redis/v8"
)

func GetConnect() *redis.Client {
	return redis.NewClient(&redis.Options{
		Addr:        "localhost:6379", // 连接地址
		Password:    "",               // 密码
		DB:          0,                // 数据库编号
		DialTimeout: 1 * time.Second,  // 链接超时
	})
}
```

### 操作字符串

```go
result, err := config.GetConnect().Set(context.Background(), "name", time.Second, time.Second*10).Result()
if err != nil {
  fmt.Println(err)
  return
}
fmt.Println(result)
// 运行脚本
╰─ go run main.go
redis服务连接成功
OK
```

### 操作List

```go
func list() {
	// push数据
	result, err := config.GetConnect().LPush(context.Background(), "list", 1).Result()
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(result)
	// pop数据
	str, err := config.GetConnect().LPop(context.Background(), "list").Result()
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(str)
}
// 运行脚本
╰─ go run main.go
redis服务连接成功
1
1
```

### 操作hash

```go
func hash() {
	// 单个添加
	//result, err := config.GetConnect().HSet(context.Background(), "hash", "key1", "value1").Result()
	// 批量添加
	//result, err := config.GetConnect().HSet(context.Background(), "hash", "key1", "value1", "key2", "value2").Result()
	// 使用切片
	//result, err := config.GetConnect().HSet(context.Background(), "hash", []string{"key3", "value3", "key4", "value4"}).Result()
	// 使用map
	hasMap := make(map[string]string)
	hasMap["key5"] = "value5"
	hasMap["key6"] = "value6"
	result, err := config.GetConnect().HSet(context.Background(), "hash", hasMap).Result()
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(result)
}
```

### 限流

```shell
go get github.com/go-redis/redis_rate/v9
```

```go
rdb := redis.NewClient(&redis.Options{
    Addr: "localhost:6379",
})


limiter := redis_rate.NewLimiter(rdb)
res, err := limiter.Allow(ctx, "project:123", redis_rate.PerSecond(10))
if err != nil {
    panic(err)
}

fmt.Println("allowed", res.Allowed, "remaining", res.Remaining)
```

### 布隆过滤器

```go
func bloomFilter(ctx context.Context, rdb *redis.Client) {
	inserted, err := rdb.Do(ctx, "BF.ADD", "bf_key", "item0").Bool()
	if err != nil {
		panic(err)
	}
	if inserted {
		fmt.Println("item0 was inserted")
	}

	for _, item := range []string{"item0", "item1"} {
		exists, err := rdb.Do(ctx, "BF.EXISTS", "bf_key", item).Bool()
		if err != nil {
			panic(err)
		}
		if exists {
			fmt.Printf("%s does exist\n", item)
		} else {
			fmt.Printf("%s does not exist\n", item)
		}
	}
}
```

