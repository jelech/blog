---
id: f71dd3bb-aae2-4226-92d0-18df3944cc9a
title: GO高性能编程
created_time: 2024-07-26T13:14:00.000Z
last_edited_time: 2024-07-26T13:15:00.000Z
cover_image: ./high-performance-go_gp7DhJER.jpg
icon_emoji: 📖
date: 2024-07-26T00:00:00.000Z
status: Published
layout: post
tag: learn
name: GO高性能编程
_thumbnail: ./high-performance-go_gp7DhJER.jpg

---

[书籍源地址](https://github.com/geektutu/high-performance-go)

# Benchmark

## 编写

Go中testing的另一种测试方式，可以测试函数的运行时间和内存占用.

```go
func BenchmarkABC(b *testing.B) {
	for i := 0; i < b.N; i++{
		b.StopTimer()
		b.StartTimer()
		ABC(a,b,c)	
	}
}
```

b.N是benchmark多次调用的N上限变化逻辑大概是`1, 2, 3, 5, 10, 20, 30, 50, 100`。

b.StopTimer()和b.StartTimer()可以让计时器暂时停止，用处在于某些值的初始化是需要时间的。

## 命令行调用

`go test <module name>/<package name>` 用来运行某个 package 内的所有测试用例。

*   运行当前 package 内的用例：`go test example` 或 `go test .`

*   运行子 package 内的用例： `go test example/<package name>` 或 `go test ./<package name>`

*   如果想递归测试当前目录下的所有的 package：`go test ./...` 或 `go test example/...`

*   `go test` 命令默认不运行 benchmark 用例的，如果我们想运行 benchmark 用例，则需要加上 `-bench` 参数。`bench` 参数支持传入一个正则表达式，匹配到的用例才会得到执行。

*   使用 `-benchtime` 和 `-count` 两个参数提升准确度，`-benchtime=5s` 指定测试时间。

*   `-benchmem` 参数可以度量内存分配的次数，显示在allocs/op字段

# 自带数据结构优化

## 字符串拼接性能

先下结论：`preByteConcat` > `strings.Builder` > `bytes.Buffer` >> `fmt.Sprintf` > `+`

*   其中preByteConcat的数据需要提前预分配空间

*   大部分时候strings.Builder是比较优良的选择，否则string转byte也会需要花时间

*   `strings.Buidler.Grow(len)` 可以对空间进行预分配

性能差距原因

其中`strings.Builder`和`+` 差距明显的原因是底层的数据分配方式不一样，字符串在 Go 语言中是不可变类型，占用内存大小是固定的，当使用 + 拼接 2 个字符串时，生成一个新的字符串，那么就需要开辟一段新的空间

## slice的性能及陷阱

结论：slice是底层数组的部分切片，而每次切片的复制是引用关系，外部的引用会导致底层更大的数组无法被释放，进而导致内存无法释放

## for与range性能比较

结论： range返回的item是个拷贝版本，如果list中的item是个很大的item，那么在拷贝时会额外花费更多的时间(当然可以用指针，这样就解决了这里的问题)。因此日常使用的时候要么尽量使用指针，要么尽量使用fori进行遍历

## reflect反射

结论：尽量避免使用，反射`赋值` 性能很低。FieldByName方法也很慢，尽量避免使用

## 空结构节省内存

结论：在channel的传输里，map的另一项定义里（比如用map做set）可以使用`struct{}` 该字段的长度为0

## 字段对齐以节省内存

结论：让字段定义以字节能对齐的方式进行排列，可以减少内存和cpu查询所花费的时间，也能一减少部分的内存占用。当 `struct{}` 作为其他 struct 最后一个字段时，需要填充额外的内存保证安全。

# 并发编程

## 读写锁与互斥锁的效率

每次读写操作在1微秒的情况下：

*   读多写少(读占 90%)

    读写比为 9:1 时，读写锁的性能约为互斥锁的 8 倍

*   读少写多(读占 10%)

    读写比为 1:9 时，读写锁性能相当

*   读写一致(各占 50%)

    读写比为 5:5 时，读写锁的性能约为互斥锁的 2 倍

## 协程超时退出

*   尽量使用非阻塞 I/O（非阻塞 I/O 常用来实现高性能的网络库），阻塞 I/O 很可能导致 goroutine 在某个调用一直等待，而无法正确结束。

*   业务逻辑总是考虑退出机制，避免死循环。

*   任务分段执行，超时后即时退出，避免 goroutine 无用的执行过多，浪费资源。

## sync.Once提升性能

`sync.Once` 是 Go 标准库提供的使函数只执行一次的实现，常应用于单例模式，例如初始化配置、保持数据库连接等。

## sync.Cond来通知多个等待

一句话总结：`sync.Cond` 条件变量用来协调想要访问共享资源的那些 goroutine，当共享资源的状态发生变化的时候，它可以用来通知被互斥锁阻塞的 goroutine。
