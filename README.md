# a8m/golang-cheat-sheet的中文版本

## [English](https://github.com/a8m/golang-cheat-sheet)

golang-cheat-sheet是目前GitHub上最流行的golang代码速查表。

作者Ariel Mashraki也是Facebook著名ORM框架ent(2019年开源)的作者和首席布道师。

本文是该速查表的中文版本，会根据原版实时更新。

## 版本

* 当前更新版本：2021-04-04 版本地址：[commit#master](https://github.com/a8m/golang-cheat-sheet/commit/0c55f5357b14663682f7dbc5b272d2338b9944a5)
* 如果您发现更新、问题或改进，欢迎随时提issue或PR。
* please feel free to open an issue or PR if you find any updates, issues or improvement.

# 目录

1. [基础语法](#基础语法)
2. [操作符](#操作符)
    * [算术操作符](#算术操作符)
    * [比较操作符](#比较操作符)
    * [逻辑操作符](#逻辑操作符)
    * [其它](#其它)
3. [声明](#声明)
4. [函数](#函数)
    * [函数作为值和闭包](#函数作为值和闭包)
    * [参数可变的函数](#参数可变的函数)
5. [内置类型](#内置类型)
6. [类型转换](#类型转换)
7. [包](#包)
8. [控制结构](#控制结构)
    * [If](#if)
    * [循环](#循环)
    * [Switch](#switch)
9. [数组，切片和range迭代](#数组，切片和range迭代)
    * [数组](#数组)
    * [切片](#切片)
    * [数组和切片上的操作](#数组和切片上的操作)
10. [集合](#集合)
11. [结构体](#结构体)
12. [指针](#指针)
13. [接口](#接口)
14. [接口和结构体嵌套](#接口和结构体嵌套)
15. [错误处理](#错误处理)
16. [并发](#并发)
    * [协程Goroutine](#协程Goroutine)
    * [管道Channel](#管道Channel)
    * [Channel原则](#Channel原则)
17. [打印](#打印)
18. [反射](#反射)
    * [switch类型](#switch类型)
    * [示例](https://github.com/a8m/reflect-examples)
19. [代码片段](#代码片段)
    * [文件嵌入](#文件嵌入)
    * [HTTP服务器](#HTTP服务器)

## 致谢

大多数代码示例来源于[A Tour of Go](https://go.dev/tour/list), 它对Go做了非常棒的介绍.
很认真地说，如果你是Go新手，一定要看完[A Tour of Go](https://go.dev/tour/list)。

## Go特性一览

* 命令式编程语言
* 静态类型
* 语法类似C语言(但是和C语言相比，圆括号更少，没有分号)，结构类似Oberon-2编程语言
* 编译成机器代码(没有JVM)
* 没有类，但是有可以带方法的结构体
* 接口类型Interfaces
* 没有继承，但是有[类型嵌套](https://go.dev/doc/effective_go#embedding)
* 函数是一等公民
* 函数可以返回多个值
* 有闭包
* 有指针，但是没有指针运算
* 内置并发原语：协程goroutine和管道channel

# 基础语法

## Hello World
文件 `hello.go`:
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello Go")
}
```
`$ go run hello.go`

## 操作符
### 算术操作符
|Operator|Description|
|--------|-----------|
|`+`|加|
|`-`|减|
|`*`|乘|
|`/`|除|
|`%`|取余|
|`&`|按位与|
|`\|`|按位或|
|`^`|按位异或，这个也可以当做”按位取反“操作符|
|`&^`|bit clear (and not)操作，x&^y表示把y按位取反，再和x做位与&操作，也即x&(^y)|
|`<<`|按位左移|
|`>>`|按位右移|

### 比较操作符
|Operator|Description|
|--------|-----------|
|`==`|等于|
|`!=`|不等于|
|`<`|小于|
|`<=`|小于等于|
|`>`|大于|
|`>=`|大于等于|

### 逻辑操作符
|Operator|Description|
|--------|-----------|
|`&&`|逻辑与|
|`\|\|`|逻辑或|
|`!`|逻辑非|

### 其它
|Operator|Description|
|--------|-----------|
|`&`|取变量地址或者创建指针|
|`*`|取指针指向的变量的值|
|`<-`|管道channel的发送和接收操作符|

## 声明
类型在标识符后面
```go
var foo int // 只声明，不做初始化
var foo int = 42 // 声明的同时做初始化
var foo, bar int = 42, 1302 // 一次声明和初始化多个变量
var foo = 42 // 忽略类型，编译器自行推导
foo := 42 // 简写，只能在函数或者方法体内使用，没有var关键字，变量类型也是隐式推导而来
const constant = "This is a constant"

// iota的值从0开始，可以用来can be used for incrementing numbers, starting from 0
const (
    _ = iota
    a
    b
    c = 1 << iota
    d
)
    fmt.Println(a, b) // 1 2 (0 is skipped)
    fmt.Println(c, d) // 8 16 (2^3, 2^4)
```

## 函数
```go
// a simple function
func functionName() {}

// function with parameters (again, types go after identifiers)
func functionName(param1 string, param2 int) {}

// multiple parameters of the same type
func functionName(param1, param2 int) {}

// return type declaration
func functionName() int {
    return 42
}

// Can return multiple values at once
func returnMulti() (int, string) {
    return 42, "foobar"
}
var x, str = returnMulti()

// Return multiple named results simply by return
func returnMulti2() (n int, s string) {
    n = 42
    s = "foobar"
    // n and s will be returned
    return
}
var x, str = returnMulti2()

```

### 函数作为值和闭包
```go
func main() {
    // assign a function to a name
    add := func(a, b int) int {
        return a + b
    }
    // use the name to call the function
    fmt.Println(add(3, 4))
}

// Closures, lexically scoped: Functions can access values that were
// in scope when defining the function
func scope() func() int{
    outer_var := 2
    foo := func() int { return outer_var}
    return foo
}

func another_scope() func() int{
    // won't compile because outer_var and foo not defined in this scope
    outer_var = 444
    return foo
}


// Closures
func outer() (func() int, int) {
    outer_var := 2
    inner := func() int {
        outer_var += 99 // outer_var from outer scope is mutated.
        return outer_var
    }
    inner()
    return inner, outer_var // return inner func and mutated outer_var 101
}
```

### 参数可变的函数
```go
func main() {
	fmt.Println(adder(1, 2, 3)) 	// 6
	fmt.Println(adder(9, 9))	// 18

	nums := []int{10, 20, 30}
	fmt.Println(adder(nums...))	// 60
}

// By using ... before the type name of the last parameter you can indicate that it takes zero or more of those parameters.
// The function is invoked like any other function except we can pass as many arguments as we want.
func adder(args ...int) int {
	total := 0
	for _, v := range args { // Iterates over the arguments whatever the number.
		total += v
	}
	return total
}
```

## 内置类型
```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32 ~= a character (Unicode code point) - very Viking

float32 float64

complex64 complex128
```

All Go's predeclared identifiers are defined in the [builtin](https://golang.org/pkg/builtin/) package.  

## 类型转换
```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)

// alternative syntax
i := 42
f := float64(i)
u := uint(f)
```

## 包
* Package declaration at top of every source file
* Executables are in package `main`
* Convention: package name == last name of import path (import path `math/rand` => package `rand`)
* Upper case identifier: exported (visible from other packages)
* Lower case identifier: private (not visible from other packages)

## 控制结构

### If
```go
func main() {
	// Basic one
	if x > 10 {
		return x
	} else if x == 10 {
		return 10
	} else {
		return -x
	}

	// You can put one statement before the condition
	if a := b + c; a < 42 {
		return a
	} else {
		return a - 42
	}

	// Type assertion inside if
	var val interface{} = "foo"
	if str, ok := val.(string); ok {
		fmt.Println(str)
	}
}
```

### 循环
```go
    // There's only `for`, no `while`, no `until`
    for i := 1; i < 10; i++ {
    }
    for ; i < 10;  { // while - loop
    }
    for i < 10  { // you can omit semicolons if there is only a condition
    }
    for { // you can omit the condition ~ while (true)
    }
    
    // use break/continue on current loop
    // use break/continue with label on outer loop
here:
    for i := 0; i < 2; i++ {
        for j := i + 1; j < 3; j++ {
            if i == 0 {
                continue here
            }
            fmt.Println(j)
            if j == 2 {
                break
            }
        }
    }

there:
    for i := 0; i < 2; i++ {
        for j := i + 1; j < 3; j++ {
            if j == 1 {
                continue
            }
            fmt.Println(j)
            if j == 2 {
                break there
            }
        }
    }
```

### Switch
```go
    // switch statement
    switch operatingSystem {
    case "darwin":
        fmt.Println("Mac OS Hipster")
        // cases break automatically, no fallthrough by default
    case "linux":
        fmt.Println("Linux Geek")
    default:
        // Windows, BSD, ...
        fmt.Println("Other")
    }

    // as with for and if, you can have an assignment statement before the switch value
    switch os := runtime.GOOS; os {
    case "darwin": ...
    }

    // you can also make comparisons in switch cases
    number := 42
    switch {
        case number < 42:
            fmt.Println("Smaller")
        case number == 42:
            fmt.Println("Equal")
        case number > 42:
            fmt.Println("Greater")
    }

    // cases can be presented in comma-separated lists
    var char byte = '?'
    switch char {
        case ' ', '?', '&', '=', '#', '+', '%':
            fmt.Println("Should escape")
    }
```

## 数组，切片和range迭代

### 数组
```go
var a [10]int // declare an int array with length 10. Array length is part of the type!
a[3] = 42     // set elements
i := a[3]     // read elements

// declare and initialize
var a = [2]int{1, 2}
a := [2]int{1, 2} //shorthand
a := [...]int{1, 2} // elipsis -> Compiler figures out array length
```

### 切片
```go
var a []int                              // declare a slice - similar to an array, but length is unspecified
var a = []int {1, 2, 3, 4}               // declare and initialize a slice (backed by the array given implicitly)
a := []int{1, 2, 3, 4}                   // shorthand
chars := []string{0:"a", 2:"c", 1: "b"}  // ["a", "b", "c"]

var b = a[lo:hi]	// creates a slice (view of the array) from index lo to hi-1
var b = a[1:4]		// slice from index 1 to 3
var b = a[:3]		// missing low index implies 0
var b = a[3:]		// missing high index implies len(a)
a =  append(a,17,3)	// append items to slice a
c := append(a,b...)	// concatenate slices a and b

// create a slice with make
a = make([]byte, 5, 5)	// first arg length, second capacity
a = make([]byte, 5)	// capacity is optional

// create a slice from an array
x := [3]string{"Лайка", "Белка", "Стрелка"}
s := x[:] // a slice referencing the storage of x
```

### 数组和切片上的操作
`len(a)` gives you the length of an array/a slice. It's a built-in function, not a attribute/method on the array.

```go
// loop over an array/a slice
for i, e := range a {
    // i is the index, e the element
}

// if you only need e:
for _, e := range a {
    // e is the element
}

// ...and if you only need the index
for i := range a {
}

// In Go pre-1.4, you'll get a compiler error if you're not using i and e.
// Go 1.4 introduced a variable-free form, so that you can do this
for range time.Tick(time.Second) {
    // do it once a sec
}

```

## 集合

```go
m := make(map[string]int)
m["key"] = 42
fmt.Println(m["key"])

delete(m, "key")

elem, ok := m["key"] // test if key "key" is present and retrieve it, if so

// map literal
var m = map[string]Vertex{
    "Bell Labs": {40.68433, -74.39967},
    "Google":    {37.42202, -122.08408},
}

// iterate over map content
for key, value := range m {
}

```

## 结构体

There are no classes, only structs. Structs can have methods.
```go
// A struct is a type. It's also a collection of fields

// Declaration
type Vertex struct {
    X, Y int
}

// Creating
var v = Vertex{1, 2}
var v = Vertex{X: 1, Y: 2} // Creates a struct by defining values with keys
var v = []Vertex{{1,2},{5,2},{5,5}} // Initialize a slice of structs

// Accessing members
v.X = 4

// You can declare methods on structs. The struct you want to declare the
// method on (the receiving type) comes between the the func keyword and
// the method name. The struct is copied on each method call(!)
func (v Vertex) Abs() float64 {
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

// Call method
v.Abs()

// For mutating methods, you need to use a pointer (see below) to the Struct
// as the type. With this, the struct value is not copied for the method call.
func (v *Vertex) add(n float64) {
    v.X += n
    v.Y += n
}

```
**Anonymous structs:**
Cheaper and safer than using `map[string]interface{}`.

```go
point := struct {
	X, Y int
}{1, 2}
```

## 指针
```go
p := Vertex{1, 2}  // p is a Vertex
q := &p            // q is a pointer to a Vertex
r := &Vertex{1, 2} // r is also a pointer to a Vertex

// The type of a pointer to a Vertex is *Vertex

var s *Vertex = new(Vertex) // new creates a pointer to a new struct instance
```

## 接口
```go
// interface declaration
type Awesomizer interface {
    Awesomize() string
}

// types do *not* declare to implement interfaces
type Foo struct {}

// instead, types implicitly satisfy an interface if they implement all required methods
func (foo Foo) Awesomize() string {
    return "Awesome!"
}
```

## 接口和结构体嵌套

There is no subclassing in Go. Instead, there is interface and struct embedding.

```go
// ReadWriter implementations must satisfy both Reader and Writer
type ReadWriter interface {
    Reader
    Writer
}

// Server exposes all the methods that Logger has
type Server struct {
    Host string
    Port int
    *log.Logger
}

// initialize the embedded type the usual way
server := &Server{"localhost", 80, log.New(...)}

// methods implemented on the embedded struct are passed through
server.Log(...) // calls server.Logger.Log(...)

// the field name of the embedded type is its type name (in this case Logger)
var logger *log.Logger = server.Logger
```

## 错误处理

There is no exception handling. Instead, functions that might produce an error just declare an additional return value of type [`error`](https://golang.org/pkg/builtin/#error). This is the `error` interface:

```go
// The error built-in interface type is the conventional interface for representing an error condition,
// with the nil value representing no error.
type error interface {
    Error() string
}
```

Here's an example:
```go
func sqrt(x float64) (float64, error) {
	if x < 0 {
		return 0, errors.New("negative value")
	}
	return math.Sqrt(x), nil
}

func main() {
	val, err := sqrt(-1)
	if err != nil {
		// handle error
		fmt.Println(err) // negative value
		return
	}
	// All is good, use `val`.
	fmt.Println(val)
}
```

# 并发

## 协程Goroutine
Goroutines are lightweight threads (managed by Go, not OS threads). `go f(a, b)` starts a new goroutine which runs `f` (given `f` is a function).

```go
// just a function (which can be later started as a goroutine)
func doStuff(s string) {
}

func main() {
    // using a named function in a goroutine
    go doStuff("foobar")

    // using an anonymous inner function in a goroutine
    go func (x int) {
        // function body goes here
    }(42)
}
```

## 管道Channel
```go
ch := make(chan int) // create a channel of type int
ch <- 42             // Send a value to the channel ch.
v := <-ch            // Receive a value from ch

// Non-buffered channels block. Read blocks when no value is available, write blocks until there is a read.

// Create a buffered channel. Writing to a buffered channels does not block if less than <buffer size> unread values have been written.
ch := make(chan int, 100)

close(ch) // closes the channel (only sender should close)

// read from channel and test if it has been closed
v, ok := <-ch

// if ok is false, channel has been closed

// Read from channel until it is closed
for i := range ch {
    fmt.Println(i)
}

// select blocks on multiple channel operations, if one unblocks, the corresponding case is executed
func doStuff(channelOut, channelIn chan int) {
    select {
    case channelOut <- 42:
        fmt.Println("We could write to channelOut!")
    case x := <- channelIn:
        fmt.Println("We could read from channelIn")
    case <-time.After(time.Second * 1):
        fmt.Println("timeout")
    }
}
```

### Channel原则
- A send to a nil channel blocks forever

  ```go
  var c chan string
  c <- "Hello, World!"
  // fatal error: all goroutines are asleep - deadlock!
  ```
- A receive from a nil channel blocks forever

  ```go
  var c chan string
  fmt.Println(<-c)
  // fatal error: all goroutines are asleep - deadlock!
  ```
- A send to a closed channel panics

  ```go
  var c = make(chan string, 1)
  c <- "Hello, World!"
  close(c)
  c <- "Hello, Panic!"
  // panic: send on closed channel
  ```
- A receive from a closed channel returns the zero value immediately

  ```go
  var c = make(chan int, 2)
  c <- 1
  c <- 2
  close(c)
  for i := 0; i < 3; i++ {
      fmt.Printf("%d ", <-c)
  }
  // 1 2 0
  ```

## 打印

```go
fmt.Println("Hello, 你好, नमस्ते, Привет, ᎣᏏᏲ") // basic print, plus newline
p := struct { X, Y int }{ 17, 2 }
fmt.Println( "My point:", p, "x coord=", p.X ) // print structs, ints, etc
s := fmt.Sprintln( "My point:", p, "x coord=", p.X ) // print to string variable

fmt.Printf("%d hex:%x bin:%b fp:%f sci:%e",17,17,17,17.0,17.0) // c-ish format
s2 := fmt.Sprintf( "%d %f", 17, 17.0 ) // formatted print to string variable

hellomsg := `
 "Hello" in Chinese is 你好 ('Ni Hao')
 "Hello" in Hindi is नमस्ते ('Namaste')
` // multi-line string literal, using back-tick at beginning and end
```

## 反射
### Switch类型
A type switch is like a regular switch statement, but the cases in a type switch specify types (not values), and those values are compared against the type of the value held by the given interface value.
```go
func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}

func main() {
	do(21)
	do("hello")
	do(true)
}
```

# 代码片段

## 文件嵌入

Go programs can embed static files using the `"embed"` package as follows:

```go
package main

import (
	"embed"
	"log"
	"net/http"
)

// content holds the static content (2 files) or the web server.
//go:embed a.txt b.txt
var content embed.FS

func main() {
	http.Handle("/", http.FileServer(http.FS(content)))
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

[Full Playground Example](https://play.golang.org/p/pwWxdrQSrYv)

## HTTP服务器
```go
package main

import (
    "fmt"
    "net/http"
)

// define a type for the response
type Hello struct{}

// let that type implement the ServeHTTP method (defined in interface http.Handler)
func (h Hello) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    fmt.Fprint(w, "Hello!")
}

func main() {
    var h Hello
    http.ListenAndServe("localhost:4000", h)
}

// Here's the method signature of http.ServeHTTP:
// type Handler interface {
//     ServeHTTP(w http.ResponseWriter, r *http.Request)
// }
```

