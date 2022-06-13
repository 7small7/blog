## map底层数据结构是怎么样的

## map是如何扩容的

## 切片扩容的规则是什么

1. 如果申请扩容的容量(cap)大于旧的容量的二倍，则直接扩容cap为最新的容量(newcap)
2. 如果所需容量不是旧的容量的二倍：
3. 如果旧的silice的长度(len)小于1024，则申请容量为旧的容量的二倍。
4. 如果旧的slice长度(len)大于1024，newcap则每次以1/4旧的cap累加，直到newcap大于所需容量。最后再判断newcap是否溢出，如果溢出，则newcap等于所需容量。
如果容量申请过大，实际数据大小 < 新容量，则容易出现内存浪费。

## 切片和map是否为线程安全

1. 切片和map不是线程安全。

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

// output
fatal error: concurrent map writes
fatal error: concurrent map writes

goroutine 6 [running]:
runtime.throw(0x10ca607, 0x15)
 /usr/local/go/src/runtime/panic.go:1117 +0x72 fp=0xc000034f60 sp=0xc000034f30 pc=0x10327d2
runtime.mapassign_fast64(0x10b1ee0, 0xc000054030, 0x0, 0x0)
 /usr/local/go/src/runtime/map_fast64.go:176 +0x325 fp=0xc000034fa0 sp=0xc000034f60 pc=0x1010c25
main.main.func1(0xc000054030, 0x0)
```
2. 要对map做并发写入，则需要使用互斥锁来实现，实现并发读、同步写。在使用官方的sync包，有两种方案，第一种是sync.RWMutex，第二种是sync.map。

## 数组与切片有什么区别

1. 数组固定长度。数组长度是数组类型的一部分，所以[3]int和[4]int是两种不同的数组类型数组需要指定大小，不指定也会根据初始化，自动推算出大小，大小不可改变。数组是通过值传递的。
2. 切片可以改变长度。切片是轻量级的数据结构，三个属性，指针，长度，容量不需要指定大小切片是地址传递（引用传递）可以通过数组来初始化，也可以通过内置函数make()来初始化，初始化的时候len=cap，然后进行扩容。
3. 切片是数组的内存地址引用。切片中的地址指向数组中的地址。

## make和new的区别

1. 看起来二者没有什么区别，都在堆上分配内存，但是它们的行为不同，适用于不同的类型。
2. new (T) 为每个新的类型 T 分配一片内存，初始化为 0 并且返回类型为 * T 的内存地址：这种方法 返回一个指向类型为 T，值为 0 的地址的指针，它适用于值类型如数组和结构体；它相当于 &T{}。
2. make(T) 返回一个类型为 T 的初始值，它只适用于 3 种内建的引用类型：切片、map 和 channel。
4. 换言之，new 函数分配内存，make 函数初始化；

## 空结构体有什么作用

空结构体的作用主要是节省内存，因为空结构体不占用内存。主要用于实现方法的接收者、实现集合类型和实现空通道。
```go
type User struct {
}
func main() {
    // 空结构体的作用
    nullUser := User{}
    
    set := make(map[string]struct{})
    for _, value := range []string{"apple", "orange", "apple"} {
        set[value] = struct{}{}
    }
    fmt.Println(set)
    fmt.Println("内存大小", unsafe.Sizeof(nullUser), unsafe.Sizeof(set["apple"]))
}
// output
map[apple:{} orange:{}]
内存大小 0 0
```
### 数据类型对比
空结构体对于其他数据类型来说，默认的初始值为0个字节。其他的数据类型默认会分配内存大小。
```go
func main() {
 var a int
 var b string
 var c bool
 var d [3]int32
 var e []string
 var f map[string]bool

 fmt.Println(
  unsafe.Sizeof(a),
  unsafe.Sizeof(b),
  unsafe.Sizeof(c),
  unsafe.Sizeof(d),
  unsafe.Sizeof(e),
  unsafe.Sizeof(f),
 )
}
// output
8 16 1 12 24 8
```