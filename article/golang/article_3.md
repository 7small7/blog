本篇文章为大家分享在Golang中，如何实现对slice和map两种数据类型进行并发写入。对于入门Golang的开发者来说，可能无法意识到这个问题，这里也会做一个问题演示。
## 切片类型

### 同步写入

在下面的代码中，我们使用`for循环`同步模式对一个切片进行追加操作。通过结果可以得出，是预期的效果。
```go
func main() {
	var slice []string

	for i := 0; i < 9999; i++ {
		slice = append(slice, "demo")
	}

	fmt.Println("slice len", len(slice))
}
// output
slice len 9999
```

### 多协程写入

在很多时候，我们为了提高并发能力，会开启多协程模式对切片写入，如下代码：
```go
func main() {
	var slice []string

	for i := 0; i < 9999; i++ {
		go func() {
			slice = append(slice, "demo")
		}()
	}

	fmt.Println("slice len", len(slice))
}
// output
╰─ go run demo1.go
slice len 7680 // 第一次结果
slice len 8168 // 第二次结果
slice len 7913 // 第三次结果
```
> 通过上图可以看出，实际追加的数据不是我们预期的结果。

### 原理分析

1. 在同步模式下，是一个阻塞式写入过程。每循环一次，往切片中追加一个元素，追完完毕之后在进行下一次循环。因此，不会出现追加的元素不正确情况。如下图：

![](http://qiniucloud.qqdeveloper.com/202205242200383.png)

1. 多协程写入下，是一个并发式写入过程。我们无法保证每一次的写都是有序的，存在第一个协程向某个`索引位`写入数据之后，后执行的协程同样的往这个索引位写入数据，就导致前面的协程写入数据被后面的协程给覆盖掉。如下图：

![](http://qiniucloud.qqdeveloper.com/202205242204096.png)
> 协程20得到的索引位和协程5得到锁因为是同一个，则协程20将协程5写入的数据变成了20。协程100与协程6也是同样原理。因此上述代码和预期结果是有偏差的。

### 解决方案

通过上述的原理分析，知道了多协程写入存在的问题。该如何解决呢？其实我们可以采用上述的同步模式进行写，保证每一个协程的写入是有序的就可以了。要解决该问题，我们可以使用锁。
![](http://qiniucloud.qqdeveloper.com/202205242210250.png)
1. 每次进行循环时，开启一把锁。对切片进行写入数据。
2. 对切片写入之后，释放锁。进行下次循环。

示例代码如下：
```go
func main() {
	var slice []string
	mutex := sync.RWMutex{}

	for i := 0; i < 9999; i++ {
		mutex.Lock()
		go func() {
			slice = append(slice, "demo")
			mutex.Unlock()
		}()
	}

	fmt.Println("slice len", len(slice))
}
// output
slice len 9998
```
> 这种方案，其实也不难看出存在问题。1是每次循环都开启一把锁，循环完释放锁，这样性能低。2是最终的结果是少一个写入操作。如果对应解决方案的可以留言提供解决方案。

## map类型

map并发式写入数据，同样会出现问题。但不会像切片那种直接被覆盖，而是直接会抛出异常。
```go
func main() {
	mapInfo := make(map[int]string)

	for i := 0; i < 10; i++ {
		go func(index int) {
			mapInfo[index] = "demo"
		}(i)
	}

	fmt.Println(len(mapInfo))
}
```
抛出如下异常：
```go
fatal error: concurrent map writes
fatal error: concurrent map writes

goroutine 6 [running]:
runtime.throw(0x10ca607, 0x15)
	/usr/local/go/src/runtime/panic.go:1117 +0x72 fp=0xc000034f60 sp=0xc000034f30 pc=0x10327d2
runtime.mapassign_fast64(0x10b1ee0, 0xc000054030, 0x0, 0x0)
	/usr/local/go/src/runtime/map_fast64.go:176 +0x325 fp=0xc000034fa0 sp=0xc000034f60 pc=0x1010c25
main.main.func1(0xc000054030, 0x0)
```
> Golang默认map是不支持并发写入操作。

### 解决方案

要对map做并发写入，则需要使用`互斥锁`来实现，实现`并发读、同步写`。在使用官方的`sync`包，有两种方案，第一种是`sync.RWMutex`，第二种是`sync.map`。

### sync.RWMutex包实现

```go
func main() {
	mapInfo := make(map[int]string)
	mutex := sync.RWMutex{}

	// 使用for循环模拟多个请求对map进行写操作。
	for i := 0; i < 10000; i++ {
		mutex.Lock()
		go func(index int, mapInfo map[int]string) {
			mapInfo[index] = "demo"
			mutex.Unlock()
		}(i, mapInfo)
	}

	fmt.Println(len(mapInfo))

	// 正常写法
	mapInfo := make(map[int]string)
	mutex := sync.RWMutex{}
	mutex.Lock()
	mapInfo[0] = "demo"
	mutex.Unlock()
}
```
上述代码，也可以使用`匿名结构体`的方式进行编写。
```go
func main() {
	var counter = struct {
		sync.RWMutex
		mapInfo map[int]string
	}{mapInfo: make(map[int]string)}
	
	for i := 0; i < 10000; i++ {
		counter.Lock()
		go func(index int, mapInfo map[int]string) {
			mapInfo[index] = "demo"
			counter.Unlock()
		}(i, counter.mapInfo)
	
	}
	fmt.Println(len(counter.mapInfo))
}
```
> 使用sync.RWMutex包实现，能解决并发写入map问题。当写数据很多时，开启一把锁会导致其他的协程处于阻塞等待过程中，会导致整体的并发能力降低。

### sync.map包实现

官方在新版本中推荐使用`sync.Map`来实现并发写入操作。sync.Map核心思想是`减少锁，使用空间换取时间`。该包实现如下几个优化点：
1. 空间换时间。 通过冗余的两个数据结构(read、dirty)，实现加锁对性能的影响。
2. 使用只读数据(read)，避免读写冲突。
3. 动态调整，miss次数多了之后，将dirty数据提升为read。
4. double-checking。
5. 延迟删除。 删除一个键值只是打标记，只有在提升dirty的时候才清理删除的数据。
6. 优先从read读取、更新、删除，因为对read的读取不需要锁。

![](http://qiniucloud.qqdeveloper.com/202205242241943.png)
```go
var sy sync.Map

func main() {
	sy.Store("name", "tom")

	sy.Range(func(key, value interface{}) bool {
		fmt.Println(key, value)
		return false
	})
}
// outpt
name tom
```
更多关于sync.Map，可以[参考该文章](https://colobu.com/2017/07/11/dive-into-sync-Map/)