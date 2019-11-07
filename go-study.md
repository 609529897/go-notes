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

**变量赋值**

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

**数据类型**

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

**运算符**

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

**条件和循环**

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
  

**数组与切片**

+ 数组的声明

`````go
var a [3]int    //声明并初始化为默认零值
a[0] = 1
 
b := [3]int{1, 2, 3}             //声明同时初始化
c := [2][2]int{{1, 2}, {3, 4}}   //多维数组初始化
`````

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

 