# Go

> GO 只有 25 个关键字

**应用实例**

+ 云计算语言：
  + docker
  + Kubernetes

+ 区块链语言
  + ethereum
  + HYPERLEDGER

**设置环境变量**

+ path
+ GOPATH
+ GPROOT
``````go
//第一个程序
package main

import (
    "fmt"
    "os"
)

func main()  {
    fmt.Println(os.Args)                   //os.Args返回命令行参数
	fmt.Println("hello!")
    os.Exit(0)                             //os.Exit返回状态
}
//go run 文件名
//go build 文件名
``````

+ 应用程序入口：
  1. 必须是 main 包 ：package main
  2. 必须是 main 方法：func main()
  3. 文件名不一定是 main.go

+ 退出返回值：
   - 与其他主要编程语言的差异
     + Go 中 main 函数不支持任何返回值
     + 通过 os.Exit 来返回状态 

``````go
package main

import (
	"fmt"
	"os"
)

func main()  {
	if len(os.Args)>1 {
		fmt.Println("hello!",os.Args [ 1 ] )      //获得命令行参数
	}
}
``````

**编写测试程序**

1. 源码文件以  _test  结尾：xxx_test.go
2. 测试方法名以  Test  开头：func TestXXX(t *testing.T) {...}

``````go
package test_test

import "testing"

func TestFirst(t *testing.T){
	t.Log("My First try!")
}
``````

## 变量赋值

+ 与其他主要编程语言的差异
  + 赋值可以进行自动类型推断
  + 在一个赋值语句中可以对多个变量进行同时赋值

+ 实现Fibonacci数列  1,2,3,5,8,13,....

`````go
//变量推断
package Fibonacci

import (
	"testing"
)
// var a int
func TestFibList(t *testing.T)  {
	// var a int = 1
	// var b int = 1
	// var (
	// 	a int =1
	// 	b int =1
	// )
    // a = 1
	// :=是类型推断
	a := 1
	b := 1
	t.Log(a)          //单元测试里可以用t.Log输出值
	for i:=0;i<5;i++{
		t.Log(b)
		tmp:=a
		a=b
		b=tmp+a
	}
	t.Log()
}
`````

+ 实现变量值交换

`````go
//在一个赋值语句中可以对多个变量进行同时赋值
func TestExchange(t *testing.T){
	a:=1
	b:=2
	// tmp:=a
	// a = b
	// b = tmp
	a,b = b,a
 	t.Log(a)
}
`````

**常量定义**

+ 与其他主要编程语言的差异

**快速设置连续值**

``````go
//递增加1
const(
Monday = iota + 1        
Tuesday
Wednesday
Thursday
Friday
Saturday
Sunday
)

//比特位常量连续赋值
const(
Open = 1<<iota          //第一位为1其他位为0
Close                   //第一位为2其他位为0
Pending                 //第一位为3其他位为0
)
``````

+ 代码示例

```````go
package constant_test

import "testing"

const(
	Monday = iota + 1
	Tuesday
	Wednesday
)
const(
	Readable = 1<<iota
	Writable
	Executable
)

func TestConstantTry(t *testing.T)  {
	t.Log(Monday,Tuesday)              //1 2
}

func TestConstantTry1(t *testing.T)  {
		 a:=7     //0111
		 t.Log(a&Readable==Readable,a&Writable==Writable,a&Executable==Executable)                //true true true
}
```````

## 数据类型

| 基本数据类型                                               |
| ---------------------------------------------------------- |
| bool                                                       |
| string                                                     |
| int int8 int16 int32 int64                                 |
| uint uint8 uint16 uint32 uint64 uintptr                    |
| byte   //alias for uint8                                   |
| rune   //alias for int32 , represents a Unicode code point |
| float32 float64                                            |
| complex64 complex128                                       |

+ 类型转化
  + 与其他主要编程语言的差异
    1. Go语言不允许隐式类型转换
    2. 别名和原有类型也不能进行隐式类型转换

```````go
package type_test

import "testing"

type MyInt int64                //type设置类型别名

func TestImplicit(t *testing.T){
	var a int32 = 1
	var b int64
	b=int64(a)                  //int64()显式类型转换
	var c MyInt                 //别名也不可以隐式类型转换
	c = MyInt(b)
	t.Log(a,b,c)
}
```````

+ 类型的预定义值
  1. math.MaxInt64
  2. math.MaxFloat64
  3. math.MaxUint32
+ 指针类型
  + 与其他主要编程语言的差异
    1. 不支持指针运算
    2. string 是值类型，其默认的初始化值为空字符串，而不是 null

``````go
package type_test

import "testing"

func TestPoint(t *testing.T){
	a:=1
	aPtr := &a                    //取指针，指针指向的好像是底层内存地址
	t.Log(a,aPtr)
	t.Logf("%T %T",a,aPtr)
}
``````

``````go
package type_test

import "testing"

func TestString(t *testing.T){
   var s string
   t.Log("*" + s + "*")    //输出**因为string是空字符串
   t.Log(len(s))           //0
}
``````

## 运算符

+ 算术运算符

| 运算符 | 描述 | 实例               |
| ------ | ---- | ------------------ |
| +      | 相加 | A + B 输出结果 30  |
| -      | 相减 | A - B 输出结果 -10 |
| *      | 相乘 | A * B 输出结果 200 |
| /      | 相除 | B / A 输出结果 2   |
| %      | 求余 | B % A 输出结果 0   |
| ++     | 自增 | A++ 输出结果 11    |
| --     | 自减 | A-- 输出结果 9     |

> Go语言没有前置的 ++ , -- , ~~(++a~~)

+ 比较运算符

| 运算符 | 描述                                                    | 实例              |
| ------ | ------------------------------------------------------- | ----------------- |
| ==     | 检查两个值是否相等，相等返回 True ,否则返回 False       | (A == B) 为 False |
| !=     | 检查两个值是否不相等，不相等返回 True ,否则返回 False   | (A != B) 为 True  |
| >      | 检查左边值是否大于右边值，是返回True,否则返回 False     | (A >B) 为 False   |
| <      | 检查左边值是否小于右边值，是返回True,否则返回 False     | (A < B) 为 True   |
| >=     | 检查左边值是否大于等于右边值，是返回True,否则返回 False | (A >= B) 为 False |
| <=     | 检查左边值是否小于右边值，是返回True,否则返回 False     | (A <= B) 为 True  |

+ 用 == 比较数组
  + 相同维数且含有相同个数元素的数组才可以比较
  + 每个元素都相同的才相等

``````go
//示例代码
package operator_test

import "testing"

func TestCompareArray(t *testing.T)  {
	a:=[...]int{1,2,3,4}   
	b:=[...]int{1,2,3,5}
    c:=[...]int{1,2,4,3} 
	// c:=[...]int{1,2,3,4,5}
	d:=[...]int{1,2,3,4}
	t.Log(a == b) //false
	t.Log(a == c) //false
	t.Log(a == d) //true
}
``````

+ 逻辑运算符

| 运算符 | 描述                                                         | 实例              |
| ------ | ------------------------------------------------------------ | ----------------- |
| &&     | 逻辑 AND 运算符。如果两边的操作数都是 True，则条件 True，否则为 False | (A && B)为 False  |
| \|\|   | 逻辑 OR 运算符。如果两边的操作数有一个 True，则条件 True，否则为 False | (A \|\| B)为 True |
| !      | 逻辑 NO T 运算符，如果条件为 Tru，则逻辑 NOT 条件 False，否则为 True | !(A && B)为 True  |

+ 位运算符：不写了太多了😀 根本上差不多，不一样的部分看下
+ 与其他主要编程语言的差异
  + &^ 按位置零
    + 1 &^ 0 -- 1
    + 1 &^ 1 -- 0
    + 0 &^ 1 -- 0
    + 0 &^ 0 -- 0

> 就是后面的是 1 的话输出0，0 的话就是按原样输出（0 就 0，1 就 1 ）

````go
//示例代码
package operator_test

import "testing"

const(
	Readable = 1<<iota
	Writable
	Executable
)

func TestBitClear(t *testing.T)  {
		 a:=7     //0111
		 a = a &^ Readable    //清Readable
		 a = a &^ Executable  //清Executable
		 t.Log(a&Readable==Readable,a&Writable==Writable,a&Executable==Executable)    //true true true
}
````

## 条件和循环

+ 循环

  + 与其他主要编程语言的差异
    1. Go 语言仅支持循环关键字  for 

  `````go
  //while 条件循环
  //while (n < 5)
  n := 0
  for n < 5{
      n++
      fmt.Println(n)
  }
  
  //无限循环
  //while(true)
  n := 0
  for{
  ...
  }
  `````

  `````go
  //示例代码
  package loop_test
  
  import "testing"
  
  func TestWhileLoop(t *testing.T)  {
  	n := 0
  	for n < 5 {
  	 t.Log(n)
  	 n++
  	}
  }
  `````

+ if 条件

  + 与其他主要编程语言的差异
    1. condition 表达式结果必须为布尔值
    2. 支持变量赋值

  ```go
  if var declaration;   condition{
      //code to be executed if condition is true
  }
  ```

  ```go
  package condition_test
  
  import "testing"
  
  func TestIfMultiSec(t *testing.T)  {
      if v,err:=someFun(); err == nil {                 
  		t.Log("1 == 1")
      }else{}
  }
  ```

+ switch 条件

  + 与其他主要编程语言的差异
    1. 条件表达式不限制为常量或者整数;
    2. 单个 `case` 中，可以出现多个结果选项，使用逗号隔开;
    3. 与 C 语言等规则相反，Go 语言不需要用 `break` 来明确退出一个 `case` ;
    4. 可以不设定 `switch` 之后的条件表达式，在此情况下，整个 `switch` 结构与多个 `if...else...` 的逻辑作用等同
  
  ````go
  switch os := runtime.GOOS; os{
      case "darwin""
        fmt.Println("OS X.")
    default:
      //freebsd,openbsd
      //plan9,windows
        fmt.Print("%s." )
}
  ````
  
  ````go
  switch{
      case:0 <= Num && Num <= 3:
          fmt.Println("0-3")
      case:4 <= Num && Num <= 6:
          fmt.Println("4-6")    
      case:7 <= Num && Num <= 9:   
          fmt.Println("7-9") 
  }
  ````
  
  ``````go
  //示例代码
  package condition_test
  
  import "testing"
  
  func TestSwitchMultiCase(t *testing.T){
     for i:=0; i < 5; i++ {
  	   switch i {
  	   case 0,2:
  		   t.Log("Even")
  	   case 1,3:
  		   t.Log("odd")
  	   default:
  		   t.Log("it is not 0-3")
  	   }
     }
  }
  
  func TestSwithCaseCondition(t *testing.T){
  	for i:=0; i < 5; i++ {
  		switch {
  		case i%2==0:
             //break,每个case后面自动添加
  		   t.Log("Even")
  		case i%2==1:
  			t.Log("odd")
  		default:
  			t.Log("unknoww")
  		}
  	}   
  }
  ``````
  

---

## Map 与工厂模式

- Map 的 value 可以是一个方法
- 与 Go  的 Dock Type 接口方式，可以方便的实现单一方法对象的工厂模式

```go
func TestMapWithFunValue(t *testing.T) {
	m := map[int]func(op int) int{}
	m[1] = func(op int) int { return op }
	m[2] = func(op int) int { return op }
	m[3] = func(op int) int { return op }
	t.Log(m[1](2), m[2](2), m[3](2))
}
```

**实现 Set**

```go
func TestMapForSet(t *testing.T) {
	mapSet := map[int]bool{}
	mapSet[1] = true
	n := 1
	if mapSet[n] {
		t.Logf("%d is existing", n)
	} else {
		t.Logf("%d is not existing", n)
	}
	t.Log(len(mapSet))
	delete(mapSet, 1)
}
```

---

## 字符串

- 只读的 byte slice
- unicode 编码和 utf-8
- byte/rune

**字符串 package**

- strings
- strconv

---

## func

- 函数是一等公民
- 所以参数都是值传递：slice，map，channel 会有传引用的错觉
- 可以有多个返回值，可以做变量值
- 可以当参数和返回值

```go
// 可变参数
func fn(args ...int) {
    //do something...
}
```

---

## Go 相关接口

**类似继承**

不支持重载

```go
type Pet struct{}

func (p *Pet) Speak() {
	fmt.Print("...")
}

func (p *Pet) SpeakTo(host string) {
	p.Speak()
	fmt.Println(" ", host)
}

type Dog struct {
	Pet
}
```

**空接口**

1. 空接口可以表示任何类型
2. 通过断言来将空接口转换成指定类型

```go
v, ok := p.(int) // ok==true 时表示转换成功
```

```go
func DoSomething(p interface{}) {
	if i, ok := p.(int); ok {
		fmt.Println("integer", i)
	} 
    if i, ok := p.(string); ok {
		fmt.Println("string", i)
	} 
}
```

**Go 接口最佳实践**

- 倾向于使用小的接口定义，很多接口只包含一个方法
- 较大的接口定义，可以由多个小接口定义组合而成
- 只依赖于必要的最小接口

---

##  异常机制

1. 没有异常机制
2. `error` 类型实现了 `error` 接口

```go
type error interface {
    Error() string
}
```

3. 可以通过 `errors.New` 来快速创建错误实例

```go
errors.New("n must be in the range[0,10]")
```

---

创建错误返回错误，接收错误

```go

func main() {
	n, err := GetFibonace(10)
    // 接收返回值
	if err != nil {
		// do something...
	}
    fmt.Println(n)
}

func GetFibonace(n int) ([]int, error) {
	if n < 0 || n > 100 {
        // errors.New() 新建错误
		return nil, errors.New("n should be in [2, 100]")
	}
	fibList := []int{1, 2}
	for i := 2; i < n; i++ {
		fibList = append(fibList, fibList[i-2]+fibList[i-1])
	}
	return fibList, nil
}
```

返回准确的错误信息，并做相应处理

```go
// 先定义错误
var (
	error1 = errors.New("error1")
	error2 = errors.New("error2")
)

func main() {
	n, err := GetFibonace(10)
    // 按照返回的错误做相应的处理
	if err == error1 {
		// do something...
	} else if err == error2 {
		// do something...
	}
	fmt.Println(n)
}

func GetFibonace(n int) ([]int, error) {
    // 按照不同的错误，返回不同的错误
	if n < 0 {
		return nil, error1
	}
	if n > 100 {
		return nil, error2
	}
	fibList := []int{1, 2}
	for i := 2; i < n; i++ {
		fibList = append(fibList, fibList[i-2]+fibList[i-1])
	}
	return fibList, nil
}
```

> 最佳实践是反方向的：err != nil

## Panic 和 Recover

- `panic` 是不可恢复的错误
- `panic` 退出前会执行 defer 指定的内容

- 可以使用 `os.Exit(-1)` 直接退出程序

```go
panic(errors.New("error"))
```

模拟 try...catch

```go
defer func() {
    // recover 返回的是 panic() 里输入进去的错误
    if err := recover(); err != nil {
        // 恢复错误
    }
}()
```

```go
func main() {
	defer func() {
		if err := recover(); err != nil {
			fmt.Println("recovered from", err)
		}
	}()
	panic(errors.New("Something wrong!"))
}
```

> 慎用 recover，干脆重启程序

---

## Package

1. 基本复用模块单元

   首字母大写表面导出

2. 代码的 package 可以和所在的目录不一致

3. 同一目录里的 Go 代码的 package 要保持一致

**`init()` 函数**

执行时机

1. 全局声明
2. init 函数
3. main 函数

每个包可以有多个，不同包的 init 函数按照导入的依赖关系决定执行顺序

包的每个源文件也可以有多个 init 函数，这点比较特殊

**使用远程的 package**

> 内置的 map 不是线程安全的，sync.Map 比较适合读大于写的情况

```shell
# 应该是自动添加到指定的地址
$ go get -u github.com
```

````go
// 应该是放到 github.com 这个文件夹中
import cm "github.com/..."
````

**vandor**

添加完 vander 路径后的查找依赖包路经的解决方案：

1. 当前包下的 vander 目录
2. 向上级目录查找，直到找到 src 下的 vendor 目录
3. 在 GOPATH 下面查找依赖包
4. 在 GOROOT 目录下查找

现在好像有官方的 go mod 处理

---

## 协程机制

- Goroutine 的 Stack 初始化大小为 2K
- Goroutine 和 KSE(内核系统线程) 的关系为 M:N
- MPG 协程机制

```go
func  main() {
    for i := 0; i < 10; i++ {
        go func(i int) {
            fmt.Println(i)
        }(i)
    }
}
```

**锁**

- Mutex：读和写差不多的情况下使用
- RMLock：读远远大于写的时候使用
- WaitGroup

```go
var mut sync.Mutex
counter := 0
for i := 0; i < 5000; i++ {
    go func() {
        defer func() {
            mut.Unlock()
        }()
        mut.Lock()
        counter++
    }()
}
```

---

## CSP 并发机制

### channel

```go
func main() {
    retCh := AsyncServise()
    fmt.Println(<- retCh) 
    time.Sleep(1*time.Second)
}

// 如果没有接收程序将一直阻塞在这里
func AsyncServise() chan string {
	retCh := make(chan string)
	go func() {
		ret := service()
		fmt.Println("returned result.")
		retCh <- ret
		fmt.Println("service exited.")
	}()
	return retCh
}
```

```go
// 无缓存的 channel、会阻塞
ch1 := make(chan int)
// 有缓存的 channel、缓存满了才阻塞
ch2 := make(chan int, 10)
```

### select

```go
// 多渠道的选择
// 两个都不阻塞 -> 随机
// 一个阻塞    -> 执行另一个
// 两个都阻塞  -> 执行 default
select {
case ret := <- retCh1:
    t.Logf("result %s", ret)
case ret := <- retCh2:
    t.Logf("result %s", ret)
default:
    t.Error("No one returned")
}
```

```go
// 超时控制
// 当上面的 case 超过下面设定的时间时触发下面的 case
select {
case ret := <- retCh:
    t.Logf("result %s", ret)
case <- time.After(time.Second*1):
    t.Error("time out")
}
```

### channel close

```go
func main() {
	var wg *sync.WaitGroup
	ch := make(chan int)
	wg.Add(2)
	dataProducer(ch, wg)
	dataReceiver(ch, wg)
	wg.Wait()
}

func dataProducer(ch chan int, wg *sync.WaitGroup) {
	go func() {
		for i := 0; i < 10; i++ {
			ch <- i
		}
		close(ch) // 使用 close 关闭、接收方不用知道接收多少数据、直接判断 ok 的值就行
		wg.Done()
	}()
}

func dataReceiver(ch chan int, wg *sync.WaitGroup) {
	go func() {
		for {
			// ok -> false 表示通道关闭
			if data, ok := <-ch; ok {
				fmt.Println(data)
			} else {
				break
			}
		}
		wg.Done()
	}()
}
```

### 任务的取消

```go
func cancel_2(cancelChan chan struct{}) {
	close(cancelChan)
}

func isCancelled(cancelChan chan struct{}) bool {
	select {
	case <-cancelChan:
		return true
	default:
		return false
	}
}

func cancel_1(cancelChan chan struct{}) {
	cancelChan <- struct{}{}
}

func Cannel() {
	cancelChan := make(chan struct{}, 0)
	for i := 0; i < 5; i++ {
		go func(i int, cancelCh chan struct{}) {
			for {
				if isCancelled(cancelCh) {
					break
				}
				time.Sleep(time.Millisecond * 5)
			}
			fmt.Println(i, "Cancelled")
		}(i, cancelChan)
	}
	cancel_2(cancelChan)
	time.Sleep(time.Second * 1)
}
```

```go
// context
func isCancelled(ctx context.Context) bool {
	select {
	case <-ctx.Done():
		return true
	default:
		return false
	}
}

func Cannel() {
	ctx, cancel := context.WithCancel(context.Background())
	for i := 0; i < 5; i++ {
		go func(i int, ctx context.Context) {
			for {
				if isCancelled(ctx) {
					break
				}
				time.Sleep(time.Millisecond * 5)
			}
			fmt.Println(i, "Cancelled")
		}(i, ctx)
	}
	cancel()
	time.Sleep(time.Second * 1)
}
```

---

### 仅执行一次

```go
type Singleton struct{}

var singleInstance *Singleton
var once sync.Once

func GetSingletonObj() *Singleton {
	once.Do(func() {
		fmt.Println("Create Singleton obj.")
		singleInstance = new(Singleton)
	})
	return singleInstance
}

func main() {
    var wg sync.WaitGroup
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func() {
            // 仅创建一次实例
            obj := GetSingletonObj()
            fmt.Printf("%x\n", unsafe.Pointer(obj))
            wg.Done()
        }()
    }
    wg.Wait()
}
```

###  仅需完成任意任务

```go
func main() {
	fmt.Println(FirstResponse())
}

func runTask(id int) string {
	time.Sleep(0 * time.Millisecond)
	return fmt.Sprintf("The result is from %d", id)
}

func FirstResponse() string {
	numOfRunner := 10
	ch := make(chan string, numOfRunner)
	for i := 0; i < numOfRunner; i++ {
		go func(i int) {
			ret := runTask(i)
			ch <- ret
		}(i)
	}
	return <-ch
}
```

### 所有任务完成

```go
func main() {
	fmt.Println(AllResponse())
}

func runTask(id int) string {
	time.Sleep(0 * time.Millisecond)
	return fmt.Sprintf("The result is from %d", id)
}

func AllResponse() string {
	numOfRunner := 10
	ch := make(chan string, numOfRunner)
	for i := 0; i < numOfRunner; i++ {
		go func(i int) {
			ret := runTask(i)
			ch <- ret
		}(i)
	}
	finalRet := ""
	for j := 0; j < numOfRunner; j++ {
		finalRet += <-ch + "\n"
	}
	return finalRet
}
```

---

## 对象池

对象池模式是一种创建型模式，根据需求来预测将要使用的对象，提前创建并保存在内存中。

```go
func main() {
    pool := newObjPool(10)
    for i := 0; i < 11; i++ {
        if v, err := pool.GetObj(time.Second * 1); err != nil {
            t.Error(err)
        } else {
            if err := pool.ReleaseObj(v); err != nil {
                t.Error(err)
            }
        }
    }
    fmt.Println("Done")
}

type ReusableObj struct{}

type ObjPool struct {
	bufChan chan *ReusableObj
}

func newObjPool(numOfObj int) *ObjPool {
	objPool := ObjPool{}
	objPool.bufChan = make(chan *ReusableObj, numOfObj)
	for i := 0; i < numOfObj; i++ {
		objPool.bufChan <- &ReusableObj{}
	}
	return &objPool
}

func (p *ObjPool) GetObj(timeout time.Duration) (*ReusableObj, error) {
	select {
	case ret := <-p.bufChan:
		return ret, nil
	case <-time.After(timeout):
		return nil, errors.New("time out")
	}
}

func (p *ObjPool) ReleaseObj(obj *ReusableObj) error {
	select {
	case p.bufChan <- obj:
		return nil
	default:
		return errors.New("overflow")
	}
}
```

---

## sync.Pool

对象获取

- 尝试从私有对象获取
- 私有对象不存在，尝试从当前 Processor 的共享池获取
- 当前的 Processor 共享池也是空的，那么就尝试去其他 Processor 的共享池获取
- 如果所有子池都是空的，最后就用用户指定的 New 函数产生一个新的对象返回

对象放回

- 如果私有对象不存在则保存为私有对象
- 如果私有对象存在，放入当前 Processor 子池的共享池中

```go
func main() {
	pool := &sync.Pool{
		New: func() interface{} {
			return 0
		},
	}
	v := pool.Get().(int)
	fmt.Println(v)
	pool.Put(3)
    runtime.GC()
	v1, _ := pool.Get().(int)
	fmt.Println(v1)
}
```

声明周期

- GC 会清除 sync.pool 缓存的对象
- 对象的缓存有效期为下一次 GC 之前

总结

- 适用于通过复用，降低复杂对象的创建和 GC 代价
- 协程安全，会有锁的开销
- 生命周期受 GC 影响，不适合于做连接池等，需自己管理生命周期的资源的池化

---

## 单元测试

- 测试文件命名：`要测试的文件名_test.go`

- 和要测试的文件在同一个目录下
- Error：该测试失败，该测试继续，其他测试继续执行
- Fatal：该测试失败，该测试中止，其他测试继续执行

- 代码覆盖率

```shell
$ go test -V -cover
```

- 断言

```shell
$ go get -u https://github.com/stretchr/testify
```

**Benchmark**

查看一段代码的性能

```go
func BenchmarkConcatStringByAdd(b *testing.B) {
	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		//测试代码
	}
	b.StopTimer()
}
```

**BDD**

```shell
$ go got -u github.com/smartystreets/goconvey/convey
```

```go
// 表示直接使用
import . "github.com/smartystreets/goconvey/convey"
```

```shell
# 查看 GOPATH 的路径
$ go env GOPATH
```

---

## 反射编程

在程序运行阶段获得变量的类型和值

- reflect.TypeOf 返回类型（reflect.Type）
- reflect.ValueOf 返回值（reflect.Value）
- 可以从 reflect.Value 获得类型
- 通过 kind 来判断类型

```go
func main() {
	var f int64 = 10
	fmt.Println(reflect.TypeOf(f), reflect.ValueOf(f))
	fmt.Println(reflect.ValueOf(f).Type())
    CheckType(f)
}

func CheckType(v interface{}) {
	t := reflect.TypeOf(v)
	switch t.Kind() {
	case reflect.Float32, reflect.Float64:
		fmt.Println("float")
	case reflect.Int, reflect.Int32, reflect.Int64:
		fmt.Println("Integer")
	default:
		fmt.Println("Unknown", t)
	}
}
```

利用反射编写灵活的代码

```go
// 按名字访问结构体的成员
reflect.ValueOf(*e).FiledByName("name")

// 按名字访问结构的方法
reflect.ValueOf(e).MethodByName("UpdateAge").Call([]reflect.Value{reflect.ValueOf(1)})
```

```go
func main() {
	e := &Employee{"1", "Mike", 10} // 利用反射获得属性值
	fmt.Println(reflect.ValueOf(*e).FieldByName("Name"))
	if nameFiled, ok := reflect.TypeOf(*e).FieldByName("Name"); !ok {
		errors.New("Failed to get 'Name' field.")
	} else {
        // 利用反射得到 struct Tag 名字
		fmt.Println(nameFiled.Tag.Get("format"))
	}
    // 利用反射调用方法
	reflect.ValueOf(e).MethodByName("UpdateAge").
		Call([]reflect.Value{reflect.ValueOf(1)})
	fmt.Println(e)
}

type Employee struct {
	EmployeeID string
	Name       string `format:"name"`
	Age        int
}

func (e *Employee) UpdateAge(newVal int) {
	e.Age = newVal
}
```

---

## 万能程序

DeepEqual

- 比较切片和 map

```go
func main() {
	a := map[int]string{1: "one", 2: "two"}
	b := map[int]string{1: "one", 2: "two"}
	fmt.Println(reflect.DeepEqual(a, b))   // true
    
    s1 := []int{1, 2, 3}
	s2 := []int{1, 2, 3}
	s3 := []int{2, 3, 5}
	fmt.Println(reflect.DeepEqual(s1, s2)) // true
	fmt.Println(reflect.DeepEqual(s2, s3)) // false
}
```

关于 "反射" 应该了解

- 提高了程序的灵活性
- 降低了程序的可读性
- 降低了程序的性能

> 编译型语言必须告诉程序，你使用的变量是什么类型。可以用类型断言和 reflect 实现。对吗？

---

## 不安全编程

"不安全" 行为的危险性

```go
func main() {
	i := 10
	fmt.Printf("%T\n", i)                   // int
	f := *(*float64)(unsafe.Pointer(&i))
	fmt.Printf("%T", f)                     // float64
}
```

合理的类型转换

> 就是要把转换完的类型，变成自定义类型

```go
type MyInt int

func main() {
	a := []int{1, 2, 3, 4}
	b := *(*[]MyInt)(unsafe.Pointer(&a))
	fmt.Printf("%T\n", b)
}
```

原子类型转换

- atomic

---

## Pipe-Filter 架构

- 非常适合与数据处理及数据分析系统
- Filter 封装数据处理的功能
- 松耦合：Filter 只跟数据（格式）耦合
- Pipe 用于连接 Filter 传递数据或者在异步处理过程中缓冲数据流进程内同步调用时，pipe 演变为数据在方法调用间传递

---

## Micro Kernel 架构

特点

- 易于扩展
- 错误隔离
- 保持架构一致性

要点

- 内核包含公共流程或通用逻辑
- 将可变或可扩展部分规划为扩展点
- 抽象扩展点行为，定义接口
- 利用插件进行扩展

> defer 调用函数

---

## 内置 JSON 解析

json 包

```go
type BasicInfo struct {
    Name string `json: "name"` // 表示 json 输出时使用下面的格式
    Age  int    `json: "age"`
}
```

**EasyJson**

```shell
$ go get -u github.com/mailru/easyjson/...
```

```shell
$ ~/go/bin/easyjson -all struct_def.go
```

---

## HTTP 服务

```go
package main

import (
	"fmt"
	"net/http"
	_ "net/http/pprof"
	"time"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintf(w, "Hello world!")
	})
	http.HandleFunc("/time", func(w http.ResponseWriter, r *http.Request) {
		t := time.Now()
		timeStr := fmt.Sprintf("{\"time\":\"%s\"}", t)
		w.Write([]byte(timeStr))
	})
	http.ListenAndServe(":8082", nil)
}
```

> `fmt.Fprintf(w, "Hello world!")`：按照第一个参数的格式写入第一个参数内

---

## 构建 Restful 服务

https://github.com/julienschmidt/httprouter

```go
package main

import (
	"fmt"
	"log"
	"net/http"

	"github.com/julienschmidt/httprouter"
)

func Hello(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
	fmt.Fprintf(w, "hello, %s!\n", ps.ByName("name"))
}

func Index(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {
	fmt.Fprint(w, "welcome!\n")
}

func main() {
	router := httprouter.New()
	router.GET("/", Index)
	router.GET("/hello/:name", Hello)
	log.Fatal(http.ListenAndServe(":8082", router))
}
```

> 书：面向资源的架构（Resource Oriented Architecture）
>
> Restful：平静的

---

## 性能分析问题

准备工作

- 安装 graphviz

  ```shell
  $ brew install graphviz
  ```

- 将 $GOPATH/bin 加入 $PATH

  + MacOS：在 .bash_profile 中修改路径

- 安装 go-torch

  ```shell
  $ go get github.com/uber/go-torch
  ```

  + 下载并复制 flamegraph.pl 至 $GOPATH/bin 路径下
  + 将 $GOPATH/bin 加入 $PATH

---

通过文件方式输出 Profile

- 灵活性高，适用于特定代码段的分析
- 通过手动调用 runtime/pprof 的 API
- API 相关文档 https://studygolang.com/static/pkgdoc/pkg/runtime pprog.htm

- ```shell
  $ go tool pprof [binary] [binary.prof]
  ```

通过 HTTP 方式输出 Profile

- 简单，适合于持续性运行的应用
- 在应用程序中导入 import _ "net/http/pprof"，并启动 http server 即可

```http
http:// <host> : <port> /debug/pprof/
```

```shell
$ go tool pprof _http://<host> : <port> /debug/pprof/profile?seconds=10（默认值为30秒）
```

```shell
$ go-torch -seconds 10 http:// <host> : <port> /debug/pprof/profile
```

---

## 性能调优示例

常见分析指标

- Wall Time
- CPU time
- Block Time
- Memory allocation
- GC times/time spent

```go
func createRequest() string {
	payload := make([]int, 100, 100)
	for i := 0; i < 100; i++ {
		payload[i] = i
	}
	req := Request{"demo_transaction", payload}
	v, err := json.Marshal(&req)
	if err != nil {
		panic(err)
	}
	return string(v)
}

func processRequest(reqs []string) []string {
	reps := []string{}
	for _, req := range reqs {
		reqObj := &Request{}
		json.Unmarshal([]byte(req), reqObj)
		res := ""
		for _, e := range reqObj.PayLoad {
			res += strconv.Itoa(e) + ","
		}
		repObj := &Response{reqObj.TransactionID, ret}
		repJson, err := json.Marshal(&repObj)
		if err != nil {
			panic(err)
		}
		reps = append(reps, string(repJson))
	}
	return reps
}
```

---

## 别让性能被"锁"住

- 减少锁的影响范围
- 减少发生锁冲突的概率
  + sync.Map
  + ConcurrentMap
- 避免锁的使用

sync.Map

- 适合读多写少，且 key 相对稳定的环境
- 采用了空间换时间的方案，并且采用指针的方式间接实现值得映射，所有存储空间会较 built-in map 大

Concurrent Map

- 适用于读写都很频繁得情况

---

GC 友好的代码

避免内存分配和复制

- 复杂对象尽量传递引用
  + 数组的传递
  + 结构体的传递
- 初始化至合适的大小
  + 自动扩容是有代价的
- 复用内存

go tool trace

普通程序输出 trace 信息

```go
package main

import (
    "os"
    "runtime/trace"
)

func main() {
    f, err := os.Create("trace.out")
    if err != nil {
        panic(err)
    }
    defer f.Close()
    
    err = trace.Start(f)
    if err != nil {
        panic(err)
    }
    defer trace.Stop()
}
```

```shell
$ go test -bench=BenchmarkPassingArrayWithRef -trace=trace
```

---

## 高效字符串连接

```go
var builder Strings.Builder
builder.WriteString(strconv.Itoa("HELLO"))
```

---

## 面向错误的设计

隔离

- 微内核
- 微服务

冗余

限流

---

## 面向恢复的设计

健康检查

- 注意僵尸进程
  + 池化资源耗尽
  + 死锁

**Let it Crash!**

```go
defer func() {
    if err := recover(); err != nil {
        log.Error("recovered panic", err)
    }
}()
```

构建可恢复的系统

- 拒绝单体系统
- 面向错误和恢复的设计
  + 在依赖服务不可用时，可以继续存活
  + 快速启动
  + 无状态

---

## Chaos Engineering 混沌工程

```
"If something hurts, do it more often!"
```

```
service_decorators
```

