---
id: 95a4a7bf-c871-4fd1-8bf7-f64afb11fcfa
title: 一起学Go（一）
created_time: 2024-07-26T13:09:00.000Z
last_edited_time: 2024-07-26T13:10:00.000Z
cover_image: ./met_william_morris_1875_PlGBmmX2.jpg
icon_emoji: 👾
date: 2024-07-26T00:00:00.000Z
status: Published
layout: post
tag: learn
name: 一起学Go（一）
_thumbnail: ./met_william_morris_1875_PlGBmmX2.jpg

---

# 开始前准备一下

## 安装

### 下载

[下载连接](https://golang.google.cn/dl/)

windows 版本安装到一个比较好找的目录,比如 F:/Go

### 配置Go PATH

添加环境变量GOPATH，写入一个路径，这里是你写代码的地方，用户变量下也有GOPATH【有默认值】，需要更新一下

新建 bin, pkg, src.

bin路径加入PATH

添加GOROOT 并设置为GO安装的目录（这里是自己出了问题才加上的）

> 使用 go env 检查环境配置是否正确

## 目录结构

*   bin 存放编译后的二进制文件

*   pkg存放编译后的库文件

*   src是放源代码的地方

再src下以网站的形式构建文件夹： 比如创建 jelech.top 名字的文件夹。 这里实际上是写的仓库连接名，所以可以写入github的连接地址

在jelech.top下创建作者名，比如jelech

在作者名下创建项目名

## IDE，用VSCODE，不多说了

*   安装中文插件

*   安装GO插件

    *   在写go时候，vscode会提示安装各种插件。而由于墙的原因会下载失败。需手动下载安装

    *   [从release下载src包](https://github.com/golang/tools/releases)

    *   再额外从lint直接拉取 `git clone https://github.com/golang/lint.git`

    *   到src的根目录，也就是GOPATH上运行 `go install golang.org/x/lint`

    *   重新运行的时候安装就能成功。还是不行就手动复制到cmd中

## 编译

`go build` 编译到当前目录 -o 重新定义名字，默认名为项目名【文件夹】

`go run path` 可以直接编译完成后直接运行，但没有保存

`go install` 编译完成后放到GOPATH/bin路径下

### 交叉编译

交叉编译代表在一个平台编译其他平台的代码，需要设置一些东西：

```plain text
SET CGO_ENABLED=0   # 禁用CGO
SET GOOS=linux      # 目标平台
SET GOARCH=amd64    # 目标处理架构为amd64
```

## GO辅助命令

`go doc builtin.delete` 查看内置的go文档

# GO基础

`package main` 当前包名

`import "name"` 导入其他包

只可以可设置全局变量、函数。和python**不一样**, 他是和c++一样，main函数等。

## 变量与常量

> 标识符和其他语言一样

### 关键字

大部分和其他语言差不多，有一些特殊的，比如`select, defer, func, chan...`

### 变量声明再使用

go语言中变量声明后**必须使用**

`var name string` 声明一个保存字符串类型的变量，名字为name

```go
// 批量声明
var (
    name string
    age int
    man bool
)
// 声明自动赋值【这里等号前的类型可以省略】
var s1 = "hi"
var i1 = 12

// **函数内**使用简短变量声明
s3 := "hahah"
i2 := 15
s1, s2 := 1, 2
```

### 匿名变量

一个下划线, 他不会占用空间，不会分配内存，所以不存在重复声明

### 常量 const

就是普通的常量，和c++类似性质 如果某一行没有写值，则表示和上面是一样的。这个只有在const的批量声明时可用

### iota常量计数器

也就是可用代表为从0开始的enum, 一般使用在const批量声明中，每新增的一行时候，iota会自动+1 来用列子检测学习一下：

```go
const (
    b1 = iota //0
    b2 = iota //1
    _
    b3        // 3
    b4 = 100  // 100
    b5        // 100  记住和上面一样
    b6 = iota // 6    记住iota是const每新增一行就+1
)
```

## 数据类型

### 整型

*   int8

*   int16

*   int32

*   int64

*   以上对应无符号uint\*\*

*   uintptr 存放指针

### 进制

无法直接定义二进制，八进制前为0，16进制前为0x

### 浮点

32长度和64长度，默认为float64。不能直接赋值，需要强转函数

```go
f1 := 123.123

```

### 复数

complex64 / complex128

### 布尔值

Go中不能将整形转为布尔类型，不能参与数值运算

### 字符串

UTF-8编码，天然自动支持中文，**只能用双引号**包括，单引号包括是字符

> 注意名字是strings，有个s

*   `\` 为转义字符

*   `"`"\` esc下面的字符用于表示多行字符串和整个转义, 注意这个前缀会加入

*   `+` 进行字符串拼接

*   `strings.Split(string, str)` 按照str进行分割

*   `strings.Contains(string, str)` 包含

*   `strings.HasPrefix`

*   `strings.HasSuffix`

*   `strings.Index(str, c)` 某个字符的位置，从前往后第一个

*   `strings.LastIndex(str, c)` 从后往前第一个

*   `strings.Join(ret[], c)` 字符串数组用c进行拼接起来

*   string 不可修改，必须强转为 `[]byte 或 []rune` 类型后再做修改

*   单引号存的是int32的整数（中文字符）

    ```go
    fmt.Printf("%T %T\n", 'c', '测') // int32 int32
    ```

### 打印

打印的时候对应 %b:2 %d:10 %o:8 %x:16 %T:类型 %v:打印值(任何类型)

```go
import "fmt"
fmt.Printf("name:%s", name) //可以格式化
println("name:", name) // 只能序列打印
s1 := fmt.Sprintf() // 格式化后返回为字符串
```

## 判断

语句是python和c++的结合体，没有小括号，但有大括号

```go
if 1 > 0 {
    println("Hello! Let's Go0!")
} else if 2 > 1 {
    println("Hello! Let's Go1!")
} else {
    println("Hello! Let's Go2!")
}

if age := 19; age > 18 {
    // 临时的age进行判断， 作用域只在if中
}
```

## 循环

循环语句只有for语句，但是有很多的变种。break, continue正常使用

```go
for i := 0; i < 10; i++ { // go中没有++i
}

for i < 10 {
}

for { // 无限循环
}

s := "123hihi"
for k, v := range s { // 遍历数组
}
```

## switch语句

```go
switch variable:=3;variable { // 赋值特性和上面的if所说的类似
    case 1:
        println("1")
    case 2:
        println("2")
    default:
        println("default")
}

switch {
    case age < 25:
        println(25)
    case age > 60:
        println(60)
}

fallthrough // 兼容c语言中的case穿透，意思是执行多个case
```

### goto

> 不鼓励使用

设置一个标志位进行跳转

## 运算符

`+ - * / %` 基本运算符

`++, --` 是语句，不能放到=右侧

`== != > >= < <=` 返回布尔值

`&& || !` 基本逻辑运算

`& | ^ << >>` 位运算符

`= += -= *= /= %= <<= >>= &= |= ^=` 基本赋值运算符

## 复合数据类型

### 数组

数组是值类型，赋值时会赋值一个新的过去

`len()` 求长度

```go
var a [3]int // 存放大小为10，类型为int的数组

// 初始化
a = [3]int{1, 2, 3} //末尾补0
b := [...]int{1, 2, 3, 4} // 根据初始值自动推断
c := [5]int {0:1, 4:2} // 1,0,0,0,2 用【位置：值】赋值
x := [...]int{0: 1, 5: 2} // 中间自动添0

// 多维数组
d := [1][2]int{
    [2]int{1, 2}
} // 3*2 的数组， 3行2列
```

### 切片

> 数组上做的一层封装，是一个可变长度的序列

*   切片本质就是框住了一块**连续**的内存，所以在对切片的任何修改时，会影响到整个内存映射的数组上

*   切片不能直接比较

*   声明没初始化才是nil，len()和cap()为0时候并不一定是nil

*   **注意** 切片是一个**引用**类型，都指向了底层的一个数组

```go
// 基本和数组一样，但是中括号中没有任何东西
var s []int // s默认值为nil

//基本初始化和数组一样， 可以由数组来初始化
s1 := [...]int{1,2,3,4}
//不可超过原来的大小，类似python，可省略
s2 := s1[1:3] //左闭右开
```

切片长度用len()， 可扩充容量为cap()

```plain text
fmt.Println(len(s2)) // 2 len为长度 取当前切片的长度
fmt.Println(cap(s2)) // 3 cap为容量 取当前切片的第一个到切片源最后
```

切片再切片

```go
s3 := s2[2:] // 一个元素，3
```

**make函数构造切片**

```go
s1 := make([]int, 5, 10) // int类型，长度5，容量5的切片
```

**切片的append() 与 copy()**

`s = append(s, 1)` 用于切片元素的添加，必须有接收变量

`s = append(s, s1...)` 将s1切片拆开添加到s中

切片的赋值`=`是引用赋值, 而copy是深拷贝 `copy(des, src)`

**没有删除函数, 需要自定义**

`a1 = append(a1[:1], a1[2:]...)` 删除第一个

**sort函数对切片进行排序**

**cap如果不够了会自动增倍.策略为:**

*   切片item不同类型的处理方式不一样

*   新申请容量大于原来 2\*old.cap, 最终容量就是新申请的

*   如果旧的小于1024则新的翻倍

*   如大于1024,则一直增加旧的1/4,直到大于需要的

*   如果最终容量计算溢出,则最终容量就是新申请的

### 指针

`&` 取地址

*   根据地址取值

### new, make区别

*   两者都是申请内存的

*   new很少用,用于对基本数据`int, string`声明

*   make只用于slice, map, channel的初始化

### map

```go
map[keyType]valueType
```

map需要初始化后才能使用 `mapname = make(map[keyType]valueType, len)` 。尽量再开始就估算好容量，避免在之后进行动态的扩容

返回值是value值与ok是否能查到值 `v, ok := mapNmae[key]`， 取不到会返回0值

`for k,v := range mapName` 遍历key值与value值

删除不存在的key值时会直接跳过

## 函数

```go
func funName(参数)(返回值) {
}
```

如果返回值有值，则一定要写return，返回值代表在函数中提前声明了一个遍历，如果是命名了的返回值，则return后可以不写。

参数简写：如果多个参数的类型一致，可以把前面的进行省略

```go
func f(x,y,z int, a,b string) {
}
```

可变长参数：`func f(a int, b ...string)` 可以传多个同类型的。**b的类型是切片**。必须放在最后

函数不可重载，意思是必须名字不一样

函数中不嫩声明函数

### defer语句

把后面的跟着的函数延迟到函数即将返回的时候再执行

比如说可以先用defer关闭一个文件、关闭数据库等操作。他会自动再推出函数的时候自动运行

执行的顺序是逆序的，也就是说多个defer之间，先声明的defer会后执行

### 机制：

return语句不是原子操作，他会先返回值赋值，再RET指令，**而defer语句就发生在这里两个操作之间**。

**这里需要提醒一点**，在返回值是在函数名后声明的，那么返回值是这个变量，而如果在defer语句对这个变量操作，会影响到返回值！而如果没有声明名字，是匿名的返回值，或者是在函数内声明的变量进行返回，这样返回值是赋值在其他地方的，defer并不会影响到返回值。

```go
func f1() (x int) {
    defer func(x int) {x++} // 会修改
    return 1
}

func f2() int {
    x := 1
    defer func(x int) {x++} // 不会修改
    return x
}
```

### 函数类型与变量

函数作为参数传入,也可以作为返回值：

```go
func f(ft func()int) {
}
func f(x func()int) (func(int, int) int) {
    return x
}
```

**有匿名函数**，用法是直接省略函数名的声明，这样可以在函数内部声明一个函数。如果是直调用一次的函数，还可以简写成立即执行函数

### 闭包

一个函数包含了他外部作用域的其他变量的引用

```go
func adder(x int) func(int)int{
    return func(y int) int {
        x += y
        return x
    }
}
ret := adder(100)
ret2 := ret(200) // 300
ret3 := ret(3) // 303
// 注意这里x是封装到了函数内部，这个函数就能带有一定的记忆性了
```
