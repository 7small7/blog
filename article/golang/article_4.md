> 专注于PHP、MySQL、Linux和前端开发，感兴趣的感谢点个关注哟！！！文章整理在[GitHub](https://github.com/7small7),[Gitee](https://gitee.com/bruce_qiq)主要包含的技术有PHP、Redis、MySQL、JavaScript、HTML&CSS、Linux、Java、Golang、Linux和工具资源等相关理论知识、面试题和实战内容。本文转自[segmentfault](https://segmentfault.com/a/1190000039843497) 如若涉及侵权，请联系平台进行删除。

@author:[7small7](https://github.com/7small7)。

@source:[公众号-菜鸟成长学习笔记](/site/)。

@project:[微信小程序 程序员面试题大全](/site/)。

参考文章1:[计算机中的栈与堆区别](article/computer/article_2.md)

## 什么是内存逃逸

1. 在程序中，每个函数块都会有自己的内存区域用来存自己的局部变量（内存占用少）、返回地址、返回值之类的数据，这一块内存区域有特定的结构和寻址方式，寻址起来十分迅速，开销很少。这一块内存地址称为栈。
1. 栈是线程级别的，大小在创建的时候已经确定，当变量太大的时候，会"逃逸"到堆上，这种现象称为内存逃逸。
1. 简单来说，局部变量通过堆分配和回收，就叫内存逃逸。

## 内存逃逸的危害

1. 堆是一块没有特定结构，也没有固定大小的内存区域，可以根据需要进行调整。
1. 全局变量，内存占用较大的局部变量，函数调用结束后不能立刻回收的局部变量都会存在堆里面。
1. 变量在堆上的分配和回收都比在栈上开销大的多。
1. 对于 go 这种带 GC 的语言来说，会增加 gc 压力，同时也容易造成内存碎片。
> 总结：因为栈中的对象会随着函数的的介绍而自动被销毁，减轻运行时的内存分配和垃圾回收负担。

## 如何分析内存逃逸

在使用`go build`命令时添加编译参数`-gcflags=-m`。完整命令`go build --gcflags=-m ./demo1.go`，运行结果中带有下面的`heap`表示发生了内存逃逸情况。
```go
╰─ go build --gcflags=-m ./demo1.go
# command-line-arguments
./demo1.go:13:6: can inline main
./demo1.go:18:2: moved to heap: y(提示发生了内存逃逸情况)
```

## 内存逃逸发生的场景

内存逃逸总结下来，会发生在如下几个场景中。

1. 向 channel 发送指针数据。因为在编译时，不知道channel中的数据会被哪个 goroutine 接收，因此编译器没法知道变量什么时候才会被释放，因此只能放入堆中。

```go
package main
func main() {
    ch := make(chan int, 1)
    x := 5
    ch <- x  // x不发生逃逸，因为只是复制的值
    ch1 := make(chan *int, 1)
    y := 5
    py := &y
    // y逃逸，因为y地址传入了chan中，编译时无法确定什么时候会被接收，
    //所以也无法在函数返回后回收y
    ch1 <- py  
}
```

1. 局部变量在函数调用结束后还被其他地方使用，比如函数返回局部变量指针或闭包中引用包外的值。因为变量的生命周期可能会超过函数周期，因此只能放入堆中。

```go
package main

func Foo () func (){
    // x发生逃逸，因为在Foo调用完成后，被闭包函数用到，
    //还不能回收，只能放到堆上存放
    x := 5            
    return func () {
        x += 1
    }
}
func main() {
    inner := Foo()
    inner()
}
```

3. 在 slice 或 map 中存储指针。比如 []*string，其后面的数组可能是在栈上分配的，但其引用的值还是在堆上。

```go
package main
func main() {
    var x int
    x = 10
    var ls []*int
    // x发生逃逸，ls存储的是指针，
    //所以ls底层的数组虽然在栈存储，但x本身却是逃逸到堆上
    ls = append(ls, &x)
}
```

4. 切片扩容后长度太大，导致栈空间不足，逃逸到堆上。

```go
package main

func main() {
    s := make([]int, 10000, 10000)
    for index, _ := range s {
        s[index] = index
    }
}
```

5. 在 interface 类型上调用方法。 在 interface 类型上调用方法时会把interface变量使用堆分配， 因为方法的真正实现只能在运行时知道。

```go
package main
type foo interface {
    fooFunc()
}
type foo1 struct{}
func (f1 foo1) fooFunc() {}
func main() {
    var f foo
    f = foo1{}
    // 调用方法时，f发生逃逸，因为方法是动态分配的
    f.fooFunc()   
}
```

## 如何避免内存逃逸

1. 对于小型的数据，使用传值而不是传指针，避免内存逃逸。
1. 避免使用长度不固定的slice切片，在编译期无法确定切片长度，只能将切片使用堆分配。
1. interface调用方法会发生内存逃逸，在热点代码片段，谨慎使用。

避免内存逃逸需要遵循如下两个原则：

1. 指向栈对象上的指针不能被存储到堆中。
1. 指向栈对象上的指针不能超过该栈对象的声明周期。
