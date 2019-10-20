# Go

**什么是 Go 语言**

- Google 开源
- 编译型语言
- 21 世纪 C 语言

**Go 语言的特定**

- 语法简洁
- 天生支持并发
- 开发效率高
- 执行性能好

**应用**

- 滴滴
- 字节跳动
- 蚂蚁金服
- Docker
- K8S

**开发环境设置**

- `GOPATH`
- `GOROOT`
- `go env` 查看 go 的相关环境设置
- `go doc` 查看文档

---

**内置函数**

- append
- copy
- delete
- len
- cap
- new
- make
- close
- complex
- real
- imag
- panic
- recover

---

## 项目目录结构

**个人开发者**

- bin：存放编译后的二进制文件
- pkg：存放编译后的库文件
- src：存放源代码
  + `github.com`
    + 自己域名

---

## Hello World!

```go
// main.go 入口
package main

import "fmt"

// 程序入口
func main() {
    fmt.Println("Hello world!")
}
```

- `go build main.go`：编译

- `go build 项目路径`：在执行的文件夹下产出二进制文件

- `go build -o hello.exe`：给输出的二进制文件取指定的名字

- `go run main.go`：执行

- `go install`：编译完后拷贝到 `GOPATH/bin` 

- 交叉编译（比如在 windows 编译可以在 liunx 执行）：

```shell
 SET CGO_ENABLED=0 # 禁用 CGO
 SET GOOS=linux # 目标平台是 linux
 SET GOAECH=amd64 # 目标处理架构是 amd64
 go build
```

**程序入口**

1. 必须是 main 包 ：package main
   - 可编译成可执行的二进制文件
2. 必须是 main 方法：func main() {}
   - 程序入口，没有参数没有返回值
3. 文件名不一定是 main.go
4. 函数外不能放逻辑语句，只能放==标识符（变量\常量\函数\类型）声明==
5. 正常情况下一个包里只能有一个 `main` 入口函数
6. 在一个文件的全部代码写完后留一个空行
7. 要编译的文件需要 `main` 函数，如果只是工具包不需要编译的话就不用 `main` 函数 

---

## 变量/常量

### 变量

**标识符**

- 和 JS 一样的规范
- 关键字
- 保留字

**定义**

- 变量先声明后使用
- 在局部变量声明，必须使用。全局可以不使用，但别这样做
- Go 语言推荐小驼峰命名法
- 同一个作用域，不能重复使用一个变量名

```go
var 变量 变量类型
var num int/bool/string
```

```go
// 批量声明
var (
    name string // ""
    age int     // 0
    salary int  // false
)
```

```go
// 也可以这样定义
a, b := 1, 3 
var a, b int = 1, 6
var a, b = 1, 6
```

```go
// 声明同时赋值
var str string = "Tom"
```

```go
// 类型推导 - 根据值判断变量的类型
var num = 15
```

```go
// 短变量声明，只能函数里用
foo := true
```

```go
// 匿名变量，当一个变量不需要时赋值给 _ ( 下划线 )
// 比如不需要函数的一个返回值
// 不占用命名空间，不会分配内存，不存在重复声明
```

```go
fmt.Print() // 在一行打印
fmt.Printf("name: %s", name) // 格式化，%s 是个占位符。使用 name 这个变量替换 %s
fmt.Println() // 打印完指定的内容后会添加换行符，跟 Print() 对应
```

**注意事项**

- 函数外的每个语句必须以关键字开始（`var`, `const`, `func`）
- `:=` 不能使用在函数外
- `_` 多用于占位，表示忽略值

---

### 常量

- 恒定不变的值
- `const` 关键字定义
- 定义时必须赋值，不能重新赋值

```go
// 声明
const pi float64 = 3.14
```

```go
// 批量声明
const (
	statusSuccess = 200
	statusNotFound = 404
)
```

```go
// 默认下面没赋值的项跟上面一样
const (
    n1 = 100
    n2
    n3
)

// 报错
const (
    n1
    n2 = 100
    n3
)
```

**iota**

> 微量;极少量;希腊字母表的第9个字母

- 常量计数器，只能在常量使用

- 当 `const` 关键字出现时，`iota` 被重置为 0
- 每增加一行 `const` `iota` 就加 1  

```go
const (
	n1 = iota // 0
	n2        // 1
	n3        // 2
)

const (
	n1 = iota + 2 // 2
	n2            // 3
	n3            // 4
)
```

```go
const (
	n1 = iota + 2 // 2
	n2 = iota     // 1
	n3            // 2
)
```

```go
const (
	n1 = iota     // 0
	n2 = iota + 1 // 2
	n3            // 3
)
```

```go
const  (
    n1 = iota // 0
    n2        // 1
    _          
    n3        // 3
)
```

```go
const (
    n1 = iota // 0
    n2 = 100  // 100
    n3        // 100
    n4        // 100
)
```

```go
const (
    n1 = iota // 0
    n2 = 100  // 100, 有 iota = 1 的值但没用
    n3 = iota // 2
    n4        // 3
)
```

```go
// 多常量在一行声明, 每增加一行 const, iota 就加 1  
const (
    n1, n2 = iota + 1, iota + 2 // 1， 
    n3, n4 = iota, iota + 1
)
```

```go
// 定义数量级
// << : 表示二进制层面左移几位
// >> : 跟 >> 相对，右移
const (
	_  = iota
    // 1 << 10: 1 左移 10 位 => 10,000,000,000(2 进制) -> 1024(10 进制)
    KB = 1 << (10 * iota) 
	MB = 1 << (10 * iota)
	GB = 1 << (10 * iota)
	TB = 1 << (10 * iota)
	PB = 1 << (10 * iota)
)
```

```go
// 单行注释
/*
多行
注释
*/
```



---

## 数据类型

**布尔值（bool）**

+ true / false

**数字类型**

+ 整型

| 有符号                    | 无符号 | 特殊整型                                       |
| ------------------------- | ------ | ---------------------------------------------- |
| int8：八个 bit ，以此类推 | uint8  | uint：32 位操作系统就是 uint32，64 就是 uint64 |
| int16                     | uint16 | int：32 位操作系统就是 int32，64 就是 int64    |
| int32                     | uint32 | uintptr：无符号整型，用于存放一个指针          |
| int64                     | uint64 |                                                |

  + 浮点型
    + float32
    + float64

**字符串类型（string）**

**派生类型**

- (a) 指针类型（Pointer）
- (b) 数组类型
- (c) 结构化类型(struct)
- (d) Channel 类型
- (e) 函数类型
- (f) 切片类型
- (g) 接口类型（interface）
- (h) Map 类型

---

**int 和 uint**

- int是带符号的，表示范围是：-2147483648到2147483648，即-2^31到2^31次方。
- uint则是不带符号的，表示范围是：2^32即0到4294967295。
- 计算机存储容量大小以字节数来度量，1024进位制：
  - 1024B=1K(千)B 
  - 1024KB=1M(兆)B 
  - 1024MB=1G(吉)B 
  - 1024GB=1T( 编码太)B 
- go 语言 字符串 是用 UTF-8 编码，UTF-8 一个常用汉字一般占用 3 个字节。
- 不同位之间是不能相互比较的

---

### 基本数据类型

#### 数字类型

**整型**

- go 语言中无法直接定义 2 进制数

- 8 进制数 0 开头
- 16 进制 0x 开头

```go
package main

import "fmt"

func main() {
	a := 10
	fmt.Printf("%d \n", a) // 10   %d 表示十进制
	fmt.Printf("%b \n", a) // 1010 %b 表示二进制数输出

	b := 077
	fmt.Printf("%o \n", b) // 77   %o 表示八进制

	c := 0xff
	fmt.Printf("%x \n", c) // ff   %x 十六进制字母小写输出
	fmt.Printf("%X \n", c) // FF   %X 十六进制字母大写输出
}
```

- 查看变量类型

```go
a := 077
fmt.Printf("%T", a) //int, %T 查看变量类型
```

```go
i := int8(9)        // 需要 int8 类型的数值时，直接用函数转换成 int8 类型
j := int16(9)   
fmt.Printf("%T", i) // int8
fmt.Printf("%T", j) // int16
```

---

**浮点数**

- 默认定义的浮点数是 float64 位的
- 强制定义 32 位数：`f = float32(1.23456)`

- 最大的数：`math.MaxFloat32 / math.MaxFloat64`

- 最小的数：`math.SmallestNonzeroFloat32`

- 类型一样，但位数不一样，不可以相互赋值

---

**复数**

- complex
- complex64
- complex128

---

#### 布尔值 bool

- 默认值是 `false`
- go 语言中不允许将整型转换为布尔值
- 布尔值无法参与数值运算，也无法与其他语言进行转换

```go
fmt.Printf("value: %v", i) // %v : 打印变量值
```

---

#### fmt

**`fmt.Printf()`占位符**

`fmt.Printf` 格式化输出

```go
%d ：10 进制输出
%b ：2 进制输出
%o ：8 进制输出
%x ：16 进制小写输出
%X ：16 进制大写输出
%T ：输出变量类型
%v : 输出变量值
%s : 输出字符串
%f : 浮点数
%t : 布尔类型
%c : 可以把其他类型转换成字符
%#v : 输出变量值的准确形式（详细语法表示）
%p : 指针
%p + &值 : 获得二进制的底层数组
```

---

#### 字符串 string

**定义**

```go
// go 语言里字符是用双引号包裹的
// 单引号包裹的是 字符 -> 单独的字母，汉字，符号 => 'u', '啥'，'.'
var s = "tom" 
var s1 = 'u'
```

> 1字 == 2 字节 == 16 bit == 16 位
>
> 一个英文字母（不分大小写）占一个字节的空间，一个中文汉字占两个字节的空间 

- ASCII码：一个英文字母等于一个字节；
- UTF-8编码：一个英文字母等于一个字节；
- Unicode编码：一个英文字母等于两个字节。
- 字节：计算机信息技术用于计量存储容量的一种计量单位，也表示一些计算机编程语言中的数据类型和语言字符。 

**转义符**

| 转义符 | 含义               |
| ------ | ------------------ |
| \r     | 回车符（返回行首） |
| \n     | 换行符             |
| \ t    | 制表符             |
| \ '    | 单引号             |
| \ "    | 双引号             |
| \ \    | 反斜杠             |

**多行字符串**

```go
// 怎么写的怎么输出
s1 := `1
2
3
`
s2 := `D:\Go\bin` // \ 不用转义
```

**字符串操作**

| 方法                                     | 介绍               |
| ---------------------------------------- | ------------------ |
| len(str)                                 | 长度               |
| + 或 fmt.Sprintf                         | 拼接字符串         |
| strings.split                            | 分割               |
| strings.Contains                         | 判断是否包含       |
| strings.HasPrefix，strings.HasSuffix     | 前缀/后缀 判读     |
| strings.Index，strings.LastIndex         | 判断字串出现的位置 |
| strings.Join(a []*string*, sep *string*) | join 操作          |

-   拼接字符串

```go
name1 := "tom"
name2 := "jerry"

name3 := name1 + name2

name4 := fmt.Sprintf("%s%s", name1, name2) // 会返回变量
fmt.Printf("%s%s", name1, name2)           // 会打印到控制台
```

- 分割

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	s1 := `D:\Go\bin`
	s2 := strings.Split(s1, "\\")
	fmt.Println(s2)               //[D: Go bin]
}
```

- 判断是否包含，返回布尔值

```go
s1 := `D:\Go\bin`
fmt.Println(strings.Contains(s1, ":")) // true
```

- 前缀/后缀 判断

```go
s1 := `D:\Go\bin`
s2 := strings.HasPrefix(s1, "D")
s4 := strings.HasSuffix(s1, "bin")
fmt.Printf("%v,%v", s2, s4) // true, true
```

- 判断字串出现的位置

```go
s1 := `D:\Go\bin`
s2 := strings.Index(s1, "D")
s3 := strings.LastIndex(s1, "b")
fmt.Printf("%v, %v", s2, s3)    // 0, 6
```

- join

```go
// strings.Join(a []string, sep string)
// 产生类似这样的结果
D + : + \ + Go + bin
```

---

**字符串修改**

- byte：uint8，1 字节
- rune：int32，3 字节，前面的 8 位表示符号

```go
num := 1
s := fmt.Sprintf("%c", num)
fmt.Printf("%T", s)        // string
```

- 存入的一个英文字节我们叫他 byte，代表了 ASCII 编码的一个字符。 
- 存入的一个别的语言（汉语，日语等）叫做 rune，代表了 UTF-8 字符

---

- ==字符串是无法修改的==，想要修改只能强制转换成 `byte` 或者 `rune` 再修改

```go
func main() {
	s2 := "红萝卜"
	newS2 := []rune(s2)         // 把字符串强制转换成 rune 切片类型
	newS2[0] = '白'             // 字符串每项都是字符，修改项也必须是'字符'
	fmt.Println(string(newS2))  // 白萝卜
}

s := "红"    // 字符串
r := '红'    // 字符、 rune(int32) 类型

s0 := "H"    // 字符串
r0 := 'H'    // 字符、 rune(int32) 类型

newS2 := byte('H') // 强制转换成 byte
```

> byte：字节    rune：符文

---

**类型转换**

- bool 跟谁都不可以类型转换

| 类型A         | 类型B  | 可不可以                   |
| ------------- | ------ | -------------------------- |
| int           | float  | 可以互相转换               |
| int           | string | i 可转换成 s，反过来不可以 |
| string        | float  | 互相不可以转换             |
| 实践出真理... |        |                            |

---

### if 判断，for 循环

**if 判断**

```go
func main() {
	if age := 19; age > 18 {
		fmt.Println(6)
	} else if age > 8 {
		fmt.Println(5)
	} else {
		fmt.Println(4)
	}
}
```

---

**for 循环**

- 基本使用

```go
func main() {
	for i := 0; i < 10; i++ { // 满足中间的条件，一直执行
		fmt.Println(i)
	}
}

-----------------------------------------------------------------------------------------

func main() {
	i := 0
	for ; i < 10; i++ { // 忽略初始值
		fmt.Println(i)
	}
}

-----------------------------------------------------------------------------------------

// 忽略控制变量，无限循环
i := 6
for ; i < 10 ; {
    fmt.Println(666)
}
```

- 无限循环

```go
for {
    fmt.Println(666)
}
```

- 另一种方式

```go
i := 5
for i < 18 {
    fmt.Println(i)
    i++
}
```

- for range（键值循环）
  + 可以用来遍历数组，切片，字符串，map，通道
  + 返回索引和值

```go
func main() {
	s := "hello"
	for i, v := range s {
		fmt.Printf("%d, %c\n", i, v)
	}
}

// 只有一个接受值时，接受的是索引
for i := range s {
    fmt.Printf("%d", i)
}

// 不想接受索引或者值时，使用 _ 代替
func main() {
	s := "hello"
	for _, v := range s {
		fmt.Printf("%c", v)
	}
}

```

> 英文一个字母占 1 个字节，汉字占 4 个字节（其实是 3 个字节，前面的一个字节表示这个一个 rune）

**打印 9*9 乘法表**

```go
package main

import "fmt"

func main() {
	for i := 1; i < 10; i++ {
		for j := 1; j <= i; j++ {
			fmt.Printf("%d*%d=%d\t", j, i, j*i)
		}
		fmt.Println()
	}
}
```

---

### switch 和 goto

**switch**

```go
finger := 3
switch finger {
case 1:
    fmt.Println(1)
case 2:
    fmt.Println(2)
case 3:
    fmt.Println(3)
default:
    fmt.Println("其他“)
} 
```

```go
// 一个分支可以有多个值
switch n := 7; n {
case 1, 3, 5, 7:
    fmt.Println("奇数")
case 2, 4, 6:
    fmt.Println("偶数")
default:
    fmt.Println(n)
}
```

```go
// 分支也可以是表达式
// fallthrough 可以执行满足条件的 case, 和下一个 case
n := 5
switch {
case n > 0:
    fmt.Println("比 0 大")
    fallthrough
case n < 0:
    fmt.Println("比 0 小")
default:
    fmt.Println(n)
}
```

---

**goto（跳转到指定标签）**

```go
// 简化跳出操作
func gotoDemo2() {
	for i := 0; i < 10; i++ {
		for j := 0; j < 10; j++ {
			if j == 2 {
				// 设置退出标签
				goto breakTag
			}
			fmt.Printf("%v-%v\n", i, j)
		}
	}
	return
	// 标签
breakTag:
	fmt.Println("结束for循环")
}
```

**break(跳出循环)**

`break`语句可以结束`for`、`switch`和`select`的代码块。

`break`语句还可以在语句后面添加标签，表示退出某个标签对应的代码块，标签要求必须定义在对应的`for`、`switch`和 `select`的代码块上。 举个例子：

```go
func breakDemo1() {
BREAKDEMO1:
	for i := 0; i < 10; i++ {
		for j := 0; j < 10; j++ {
			if j == 2 {
				break BREAKDEMO1
			}
			fmt.Printf("%v-%v\n", i, j)
		}
	}
	fmt.Println("...")
}
```

**continue（继续下次循环）**

`continue`语句可以结束当前循环，开始下一次的循环迭代过程，仅限在`for`循环内使用。

在 `continue`语句后添加标签时，表示开始标签对应的循环。例如：

```go
func continueDemo() {
forloop1:
	for i := 0; i < 5; i++ {
		// forloop2:
		for j := 0; j < 5; j++ {
			if i == 2 && j == 2 { // 跳过此次循环
				continue forloop1
			}
			fmt.Printf("%v-%v\n", i, j)
		}
	}
}
```

---

### 运算符

Go 语言内置的运算符有：

1. 算术运算符
2. 关系运算符
3. 逻辑运算符
4. 位运算符
5. 赋值运算符

#### 算数运算符

| 运算符 | 描述 |
| :----: | :--: |
|   +    | 相加 |
|   -    | 相减 |
|   *    | 相乘 |
|   /    | 相除 |
|   %    | 求余 |

**注意：** `++`（自增）和`--`（自减）在Go语言中是单独的语句，并不是运算符。

> 不能这样用：a = a++，只能 a++ 这样单独用；函数外好像也不可以使用

#### 关系运算符

| 运算符 |                             描述                             |
| :----: | :----------------------------------------------------------: |
|   ==   |    检查两个值是否相等，如果相等返回 True 否则返回 False。    |
|   !=   |  检查两个值是否不相等，如果不相等返回 True 否则返回 False。  |
|   >    |  检查左边值是否大于右边值，如果是返回 True 否则返回 False。  |
|   >=   | 检查左边值是否大于等于右边值，如果是返回 True 否则返回 False。 |
|   <    |  检查左边值是否小于右边值，如果是返回 True 否则返回 False。  |
|   <=   | 检查左边值是否小于等于右边值，如果是返回 True 否则返回 False。 |

#### 逻辑运算符

| 运算符 |                             描述                             |
| :----: | :----------------------------------------------------------: |
|   &&   | 逻辑 AND 运算符。 如果两边的操作数都是 True，则为 True，否则为 False。 |
|  \|\|  | 逻辑 OR 运算符。 如果两边的操作数有一个 True，则为 True，否则为 False。 |
|   !    | 逻辑 NOT 运算符。 如果条件为 True，则为 False，否则为 True。 |

#### 位运算符

位运算符对整数在内存中的二进制位进行操作。

| 运算符 |                             描述                             |
| :----: | :----------------------------------------------------------: |
|   &    |    参与运算的两数各对应的二进位相与。 （两位均为1才为1）     |
|   \|   |  参与运算的两数各对应的二进位相或。 （两位有一个为1就为1）   |
|   ^    | 参与运算的两数各对应的二进位相异或，当两对应的二进位相异时，结果为1。 （两位不一样则为1） |
|   <<   | 左移n位就是乘以2的n次方。 “a<<b”是把a的各二进位全部左移b位，高位丢弃，低位补0。 |
|  ">>"  | 右移n位就是除以2的n次方。 “a>>b”是把a的各二进位全部右移b位。 |

> ">>"：表示二进制层面向后移动多少位，比如 101 >> 1 等于 010 == 10
>
> 注意：位移动的范围不要超过变量值的内存大小
>
> a := int8(1)
>
> a << 10  //报错，超过了一个字节

> 位运算的实际运用：
>
> - 192.168.1.1    IP运算时会用
> - 权限   文件操作
> - 0644

---

#### 赋值运算符

| 运算符 |                      描述                      |
| :----: | :--------------------------------------------: |
|   =    | 简单的赋值运算符，将一个表达式的值赋给一个左值 |
|   +=   |                  相加后再赋值                  |
|   -=   |                  相减后再赋值                  |
|   *=   |                  相乘后再赋值                  |
|   /=   |                  相除后再赋值                  |
|   %=   |                  求余后再赋值                  |
|  <<=   |                   左移后赋值                   |
|  >>=   |                   右移后赋值                   |
|   &=   |                  按位与后赋值                  |
|  \|=   |                  按位或后赋值                  |
|   ^=   |                 按位异或后赋值                 |

---

### 复合数据类型

#### 数组（Array）

- 一个数组里只能放同一种类型的值

**定义数组**

```go
// 容器、存放基本的数据类型。必须指定存放的元素 类型 和 长度 
// arr 数组长度为 3、每一项的类型时 int 类型
var arr [3]int
```

```go
// 长度是类型的一部分、a1 和 a2 不能比较
var a1 [2]int
var a2 [3]int
```

**初始化数组**

- 如果不初始化默认元素都是零值（布尔值：false、整型和浮点型：0、字符串：空字符串（""））

- 方式 1

```go
var a [2]int
a = [2]int{7, 8}

// 类型推导
var a = [2]int{1, 2}

// 短声明
a := [2]int{1, 2}
```

- 方式 2

```go
// 动态设置数组的长度、... 会自动按照设置的数组长度动态的设置数组的长度
// 必须定义且初始化
a := [...]int{5, 8}
```

- 方式 3

```go
// 这样定义后面的三位是默认值
a := [5]int{1, 2}

// 索引 0 是 1、索引 4 是 2
a1 := [5]int{0: 1, 4: 2}
```

**数组遍历**

1. 索引遍历
2. range 遍历

```go
func main() {
	var c = [...]string{"北京", "上海", "深圳"}
	// 方法1：for循环遍历
	for i := 0; i < len(c); i++ {
		fmt.Println(c[i])
	}

	// 方法2：for range遍历
	for index, value := range a {
		fmt.Println(index, value)
	}
}
```

**多维数组**

 **注意：** 多维数组**只有第一层**可以使用 `...` 来让编译器推导数组长度。

- 二维数组的定义初始化

```go
var all [2][2]int

arr := [2][2]int{
    {1, 2}, 
    {2, 5}
}
```

- 二维数组的遍历

```go
package main

import "fmt"

func main() {
	arr := [3][2]int{{1, 2}, {2, 5}}
	for _, v := range arr {
		for _, value := range v {
			fmt.Println(value)
		}
		fmt.Println()
	}
}
```

> go 语言里数组的两个值由**空格**来分开

**数组是值类型**

数组是值类型，赋值和传参会复制整个数组。因此改变副本的值，不会改变本身的值。

```go
a1 := [2]int{10, 2}
a2 := a1
a1[0] = 2
fmt.Println(a1, a2) // [2 2] [10 2]
```

==注意：==

1. 因为是值类型，所以数组支持 “==“、”!=” 操作符，因为内存总是被初始化过的。
2. `[n]*T`表示指针数组，`*[n]T`表示数组指针 。

**数组练习**

1. 求数组中各项的和

```go
func main() {
	a := [...]int{1, 3, 5, 7, 8}
	var sum int
	for _, v := range a {
		sum += v
	}
	fmt.Println(sum)
}
```

2. leetcode 第一题

```go
func twoSum(nums []int, target int) []int {
	h := make(map[int]int)
	for i, value := range nums {
		if wanted, ok := h[value]; ok {
			return []int{wanted, i}
		} else {
			h[target-value] = i
		}
	}
	return nil
}
```

---

#### 切片（slice）

**切片（Slice）**是一个拥有相同类型元素的可变长度的序列。它是基于数组类型做的一层封装。它非常灵活，支持自动扩容。

切片是一个==引用类型==，它的内部结构包含`地址`、`长度`和`容量`。切片一般用于快速地操作一块数据集合。

数组的长度是固定的并且数组长度属于类型的一部分，所以数组有很多的局限性。为了弥补数组的局限性所以产生了切片。

切片：指针，长度，容量

**定义切片** 

```go
var sliceName []T

// 例如、定义一个存放 int 类型值的切片 
var s []int

// 这也是切片?
s := []int{2, 5, 2}
```

```go
func main() {
	// 声明切片类型
	var a []string              //声明一个字符串切片
	var b = []int{}             //声明一个整型切片并初始化
	var c = []bool{false, true} //声明一个布尔切片并初始化
	var d = []bool{false, true} //声明一个布尔切片并初始化
	fmt.Println(a)              //[]
	fmt.Println(b)              //[]
	fmt.Println(c)              //[false true]
	fmt.Println(a == nil)       //true
	fmt.Println(b == nil)       //false
	fmt.Println(c == nil)       //false
	// fmt.Println(c == d)      //切片是引用类型，不支持直接比较，只能和nil比较
}
```

**切片的长度和容量**

- 切片拥有自己的长度和容量，我们可以通过使用内置的==len()==函数求长度，使用内置的==cap()==函数求切片的容量。数组的也可以。

- `nil`：空

- 切皮定义未初始化等于 nil，一旦初始化不等于 nil

- `var v uint8`：这意思是要开辟，初始化才开辟？

**基于数组定义切片**

- ==切片容量==

  + 切片是一种包装的 "数组"

  + 切片支持扩容

  + 从 0 的位置切：从数组切的切片继承数组的容量最大限
  + 从中间位置切（比如[4: 5]）：那么前面的空间就不算到容量里

  + 切片的容量是指底层数组的容量，底层数组从切片的第一个元素到最后的元素数量

- 切皮是引用类型，同一个数组切割出来的切片，都底层都指向那个数组
  + 是指向数组的，自己没有复制

```go
ary := [5]int{2}
sle := ary[0:4]  //索引 0 到 4 切割（左包含右不包含）并返回一个 slice 
```

还支持如下方式：

```go
c := a[1:] // 切割从 1 到结束     [1:len(a)]
d := a[:4] // 从一开始到第 4 个   [0:4]
e := a[:]  // 切割全部、类似于复制 [0:len(a)]
```

**切片再切片**

```go
a := [3]int{2, 5, 2}
s1 := a[0:3]
s2 := s1[0:1]

// 也可以这样、那意思切片只是隐藏数组没有移除?
a := [3]int{2, 5, 2}
s1 := a[0:2]        // [2 5]
s2 := s1[0:3]       // [2 5 2]

// 可以改值
a := [3]int{2, 5, 2}
s1 := a[0:2]
s1[0] = 0

// 一旦改变切割的数组、切片的值也会改变 
a := [3]int{2, 5, 2}
s := a[:3]
a[1] = 1
fmt.Println(a, s) // [2 1 2] [2 1 2]
```

> **注意：** 对切片进行再切片时，索引不能超过原数组的长度，否则会出现索引越界的错误。 

----

**使用 `make()` 函数构造切片**

我们上面都是基于数组来创建的切片，如果需要动态的创建一个切片，我们就需要使用内置的`make()`函数，格式如下：

```bash
make([]T, size, cap)
```

其中：

- T:切片的元素类型
- size:切片中元素的数量
- cap:切片的容量

举个例子：

```go
func main() {
	a := make([]int, 2, 10)
	fmt.Println(a)      //[0 0]
	fmt.Println(len(a)) //2
	fmt.Println(cap(a)) //10
}

// 表示长度和容量都是 5
// 长度应该比容量小或者等于容量
make([]int, 5)
```

上面代码中`a`的内部存储空间已经分配了10个，但实际上只用了2个。 容量并不会影响当前元素的个数，所以`len(a)`返回2，`cap(a)`则返回该切片的容量。

---

**切片的本质**

- 切片就是一个框，框住了一块连续的内存。
- 属于引用类型，真正的数据都是保存在底层数组里。
- 切片不能直接比较，当一个切片的变量为 `nil` 时，它是没有底层数组的
- 一个 `nil` 值的切片长度和容量都为 0 ，当不能说一个长度和容量都是 0 的切片一定是 `nil` 如下：

```go
var s1 []int
s2 := []int{}
s3 := make([]int, 0)
```

-  所以要判断一个切片是否是空的，要是用`len(s) == 0`来判断，不应该使用`s == nil`来判断。 

**切片的赋值拷贝**

- 下面的代码中演示了拷贝前后两个变量共享底层数组，对一个切片的修改会影响另一个切片的内容，这点需要特别注意。
- 切片是不保存值得，值是由底层数组保存。切片指向底层数组

```go
func main() {
	s1 := make([]int, 3) //[0 0 0]
	s2 := s1             //将s1直接赋值给s2，s1和s2共用一个底层数组
	s2[0] = 100
	fmt.Println(s1) //[100 0 0]
	fmt.Println(s2) //[100 0 0]
}
```

**切片遍历**

切片的遍历方式和数组是一致的，支持索引遍历和`for range`遍历。

```go
func main() {
	s := []int{1, 3, 5}

	for i := 0; i < len(s); i++ {
		fmt.Println(i, s[i])
	}

	for index, value := range s {
		fmt.Println(index, value)
	}
}
```

> 学点 go 的感想：JS 皆对象，很多方法是从对象点（.）出来的。go 是没有一起皆对象的概念，很多方法都是内置函数的形式存在的

---

#### append 和 copy 方法

**append 方法为 slice 追加元素**

1. Go 语言的内建函数 `append()` 可以为切片动态添加元素。 每个切片会指向一个底层数组，这个数组能容纳一定数量的元素。当底层数组不能容纳新增的元素时，切片就会自动按照一定的策略进行“扩容”，此时该切片指向的底层数组就会更换。“扩容”操作往往发生在 `append()` 函数调用时。 
2. `append()`函数，只能给切片追加元素，将元素追加到切片的最后并返回该切片。
3. 每次扩容后都是扩容前的2倍。

```go
s1 := []string{"上海", "北京"}
s1[3] = "广州"
fmt.Println(s1) // errer、索引越界: index out of range
```

append 追加元素

```go
s1 := []string{"上海", "北京"}
s1 = append(s1, "广州") // 应该使用原变量接受 append 的返回值
```

```go
// 超过容量、长度也没问题。可以一次性追加多个元素
// append 追加元素、原来的底层数组放不下的时候、go 底层就把数组换一个
s1 := make([]int, 2, 10)
s1 = append(s1, 5, 6, 8, 9, 10, 1, 5, 8, 9, 10, 1, 5, 8, 9, 10, 1, 5)
```

**切片的扩容策略**

 可以通过查看`$GOROOT/src/runtime/slice.go`源码，其中扩容相关代码如下： 

```go
newcap := old.cap
doublecap := newcap + newcap
if cap > doublecap {
	newcap = cap
} else {
	if old.len < 1024 {
		newcap = doublecap
	} else {
		// Check 0 < newcap to detect overflow
		// and prevent an infinite loop.
		for 0 < newcap && newcap < cap {
			newcap += newcap / 4
		}
		// Set newcap to the requested cap when
		// the newcap calculation overflowed.
		if newcap <= 0 {
			newcap = cap
		}
	}
}
```

- 首先判断，如果新申请容量（cap）大于2倍的旧容量（old.cap），最终容量（newcap）就是新申请的容量（cap）。
- 否则判断，如果旧切片的长度小于1024，则最终容量(newcap)就是旧容量(old.cap)的两倍，即（newcap=doublecap）
- 否则判断，如果旧切片长度大于等于1024，则最终容量（newcap）从旧容量（old.cap）开始循环增加原来的1/4，即（newcap=old.cap,for {newcap += newcap/4}）直到最终容量（newcap）大于等于新申请的容量(cap)，即（newcap >= cap）
- 如果最终容量（cap）计算值溢出，则最终容量（cap）就是新申请容量（cap）。

需要注意的是，切片扩容还会根据切片中元素的类型不同而做不同的处理，比如`int`和`string`类型的处理方式就不一样。

**切片追加切片**

```go
a := []int{1, 2}
b := []int{3, 4}
a = append(a, b...)
```

> 三点（...）：展开运算符，跟别的语言的一样。但跟 JS 不同，go 是放后面的

---

**使用 copy() 函数赋值切片**

首先我们来看一个问题：

```go
func main() {
	a := []int{1, 2, 3, 4, 5}
	b := a
	fmt.Println(a) //[1 2 3 4 5]
	fmt.Println(b) //[1 2 3 4 5]
	b[0] = 1000
	fmt.Println(a) //[1000 2 3 4 5]
	fmt.Println(b) //[1000 2 3 4 5]
}
```

由于切片是引用类型，所以a和b其实都指向了同一块内存地址。修改b的同时a的值也会发生变化。

go语言内建的`copy()`函数可以迅速地将一个切片的数据复制到另外一个切片空间中，`copy()`函数的使用格式如下：

```go
// t: 赋值后存放的切片
// s: 要复制的切片
copy(t, s []T)
```

```go
func main() {
	// copy()复制切片
	a := []int{1, 2, 3, 4, 5}
	c := make([]int, 5, 5)
	copy(c, a)     //使用copy()函数将切片a中的元素复制到切片c
	fmt.Println(a) //[1 2 3 4 5]
	fmt.Println(c) //[1 2 3 4 5]
	c[0] = 1000
	fmt.Println(a) //[1 2 3 4 5]
	fmt.Println(c) //[1000 2 3 4 5]
}
```

```go
func main() {
	a := []int{1, 6}
	b := make([]int, 6, 6)
	c := copy(b, a)
	fmt.Println(c)  // 返回 2、copy 的返回值好像是 - 要复制赋值切片的长度
}
```

**切片删除元素**

Go语言中并没有删除切片元素的专用方法，我们可以使用切片本身的特性来删除元素。 代码如下：

```go
func main() {
	// 从切片中删除元素
	a := []int{30, 31, 32, 33, 34, 35, 36, 37}
	// 要删除索引为2的元素
	a = append(a[:2], a[3:]...)
	fmt.Println(a) //[30 31 33 34 35 36 37]
}
```

- 总结一下就是：要从切片a中删除索引为`index`的元素，操作方法是`a = append(a[:index], a[index+1:]...)`
- 这个也会影响底层的数组

---

**练习题**

```go
// 输出啥
func main() {
	var a = make([]string, 5, 10)
	for i := 0; i < 10; i++ {
		a = append(a, fmt.Sprintf("%v", i))
	}
	fmt.Println(a) //追加、[0 0 0 0 0 0 1 2 3 4 5 6 7 8 9]
}
```

```go
// 请使用内置的sort包对数组var a = [...]int{3, 7, 8, 9, 1}进行排序。
// sort 包内置了很多内置的排序方法、但最好自己会实现、应该看看源码
func main() {
	a := []int{5, 1, 3}
	sort.Ints(a)
	fmt.Println(a)
}
```

---

#### 指针，make 及 new

**指针**

go 语言的指针是只读的。

区别于C/C++中的指针，Go语言中的指针不能进行偏移和运算，是安全指针。

要搞明白Go语言中的指针需要先知道3个概念：指针地址、指针类型和指针取值。

**go 语言的指针**

==指针==就是内存地址，可以直接访问内存。

Go语言中的函数传参都是值拷贝，当我们想要修改某个变量的时候，我们可以创建一个指向该变量地址的指针变量。传递数据使用指针，而无须拷贝数据。类型指针不能进行偏移和运算。Go语言中的指针操作非常简单，只需要记住两个符号：`&`（取地址）和`*`（根据地址取值）。 

- `&`：得到变量的地址
- `*`：得到变量的值

```go
func main() {
	a := 1
	b := &a           // b 也会指针
	c := *b
	fmt.Println(b, c) // 1 的地址、值
}
```

```go
func main() {
	a := 1
	b := &a
	c := *b
	fmt.Println(c)
	foo(b)
}

func foo(arg *int) *int {
	return arg
}
```

**==指针地址==和==指针类型==**

每个变量在运行时都拥有一个地址，这个地址代表变量在内存中的位置。Go语言中使用`&`字符放在变量前面对变量进行“取地址”操作。 Go语言中的值类型（int、float、bool、string、array、struct）都有对应的指针类型，如：`*int`、`*int64`、`*string`等。

取变量指针的语法如下：

```go
ptr := &v    // v的类型为T
v := *ptr    // 得到地址内存的值
```

其中：

1. v:代表被取地址的变量，类型为`T`

2. ptr:用于接收地址的变量，ptr的类型就为`*T`，称做T的指针类型。*代表指针。

**总结：** 取地址操作符`&`和取值操作符`*`是一对互补操作符，`&`取出地址，`*`根据地址取出地址指向的值。

变量、指针地址、指针变量、取地址、取值的相互关系和特性如下：

- 对变量进行取地址（&）操作，可以获得这个变量的指针变量。
- 指针变量的值是指针地址。
- 对指针变量进行取值（*）操作，可以获得指针变量指向的原变量的值。

**指针传值示例：**

```go
func modify1(x int) {
	x = 100
}

func modify2(x *int) {
	*x = 100
}

func main() {
	a := 10
	modify1(a)
	fmt.Println(a) // 10
	modify2(&a)
	fmt.Println(a) // 100 ？
}
```

---

**new**

- 跟值类型分配内存，返回的是指针

new是一个内置的函数，它的函数签名如下：

```go
func new(Type) *Type
```

其中，

- Type表示类型，new函数只接受一个参数，这个参数是一个类型
- *Type表示类型指针，new函数返回一个指向该类型内存地址的指针。

new函数不太常用，使用new函数得到的是一个类型的指针，并且该指针对应的值为该类型的零值。举个例子：

```go
func main() {
	a := new(int)
	b := new(bool)
	fmt.Printf("%T\n", a) // *int
	fmt.Printf("%T\n", b) // *bool
	fmt.Println(*a)       // 0
	fmt.Println(*b)       // false
}	
```

本节开始的示例代码中`var a *int`只是声明了一个指针变量a但是没有初始化，指针作为引用类型需要初始化后才会拥有内存空间，才可以给它赋值。应该按照如下方式使用内置的new函数对a进行初始化之后就可以正常对其赋值了：

```go
func main() {
	var a *int
	a = new(int)
	*a = 10
	fmt.Println(*a)
}
```

可以这样理解（吧）：

- 想要访问指针（内存地址），需要使用 `*`

- 怎样才访问：
  1. 自己 `new()` 一个指针
  2. 用 `&` 取得指针的地址，用 `*` 才能操作这个指针

---

**make**

- make 也是分配内存的
- 区别于 new ，make 只作用于 slice，map，channel

- 而且它返回的类型就是这三个类型本身，而不是他们的指针类型，因为这三种类型就是引用类型，所以就没有必要返回他们的指针了。make函数的函数签名如下：

```go
func make(t Type, size ...IntegerType) Type
```

make函数是无可替代的，我们在使用slice、map以及channel的时候，都需要使用make进行初始化，然后才可以对它们进行操作。 

本节开始的示例中`var b map[string]int`只是声明变量b是一个map类型的变量，需要像下面的示例代码一样使用make函数进行初始化操作之后，才能对其进行键值对赋值：

```go
func main() {
	var b map[string]int
	b = make(map[string]int, 10)
	b["沙河娜扎"] = 100
	fmt.Println(b)
}
```

**new与make的区别**

1. 二者都是用来做内存分配的。
2. new 很少用，一般用来给基本数据类型申请内存，string\int...
3. make只用于slice、map以及channel的初始化，返回的还是这三个引用类型本身；
4. 而new用于类型的内存分配，并且内存对应的值为类型零值，返回的是指向类型的指针(*string \ *int)。

---

#### map

Go语言中提供的映射关系容器为`map`，其内部使用`散列表（hash）`实现。

map是一种无序的基于`key-value`的数据结构，Go语言中的map是引用类型，必须初始化才能使用。 

**map 定义**

```go
map[KeyType]ValueType

// 实例、引用必须初始化（分配内存）后才能使用
var m map[string]int
m = make(map[string]int, 10)
m["老五"] = 5

// 取值
fmt.Println(m["老五"])
```

**使用 make 定义**

 map类型的变量默认初始值为nil，需要使用make()函数来分配内存。语法为： 

```go
// 其中cap表示map的容量，该参数虽然不是必须的，但是我们应该在初始化map的时候就为其指定一个合适的容量。
make(map[KeyType]ValueType, [cap])

// 估量好该 map 的容量、避免在程序运行期间再动态扩容
m := make(map[string]int, 10)
```

**map 的基本使用**

- 判断一个值是否存在

```go
// 当对一个未知的 map 时、取值
// 返回的第一个值是个布尔值、表示是否有这个变量
v, ok := m[5]
if !ok {
    fmt.Println("没有这个键对应的值")
} eles {
    fmt.Println(v)
}

// 如果不存在这个key、会返回对应类型的零值
```

> 约定成俗用 ok 接受返回的布尔值

map也支持在声明的时候填充元素，例如：

```go
// 这样也不用 make 来分配内存了
func main() {
	userInfo := map[string]string{
		"username": "沙河小王子",
		"password": "123456",
	}
	fmt.Println(userInfo)
}
```

- map 的遍历

Go语言中使用`for range`遍历map。

```go
func main() {
	m := map[string]int{
		"id":  1,
		"uid": 10,
	}
	for _, v := range m {
		fmt.Println(v)
	}
}
```

但我们只想遍历key的时候，可以按下面的写法：

```go
func main() {
	scoreMap := make(map[string]int)
	scoreMap["张三"] = 90
	scoreMap["小明"] = 100
	scoreMap["娜扎"] = 60
	for k := range scoreMap {
		fmt.Println(k)
	}
}
```

**注意：** 遍历map时的元素顺序与添加键值对的顺序无关。

---

- `delete()` 删除键值对

使用`delete()`内建函数从map中删除一组键值对，`delete()`函数的格式如下：

```go
delete(map, key)
```

其中，

- map:表示要删除键值对的map
- key:表示要删除的键值对的键

```go
func main() {
	userInfo := map[string]string{
		"username": "沙河小王子",
		"password": "123456",
	}
	delete(userInfo, "username")
	fmt.Println(userInfo)
}
```

> go doc：看文档，delete 内置函数好像只可以删除 map 的项

---

**按照指定顺序遍历map**

```go
func main() {
	rand.Seed(time.Now().UnixNano()) //初始化随机数种子

	var scoreMap = make(map[string]int, 200)

	for i := 0; i < 100; i++ {
		key := fmt.Sprintf("stu%02d", i) //生成stu开头的字符串
		value := rand.Intn(100)          //生成0~99的随机整数
		scoreMap[key] = value
	}
	//取出map中的所有key存入切片keys
	var keys = make([]string, 0, 200)
	for key := range scoreMap {
		keys = append(keys, key)
	}
	//对切片进行排序
	sort.Strings(keys)
	//按照排序后的key遍历map
	for _, key := range keys {
		fmt.Println(key, scoreMap[key])
	}
}
```

**元素为map类型的切片**

下面的代码演示了切片中的元素为map类型时的操作：

```go
func main() {
	var mapSlice = make([]map[string]string, 3)
	for index, value := range mapSlice {
		fmt.Printf("index:%d value:%v\n", index, value)
	}
	fmt.Println("after init")
	// 对切片中的map元素进行初始化
	mapSlice[0] = make(map[string]string, 10)
	mapSlice[0]["name"] = "小王子"
	mapSlice[0]["password"] = "123456"
	mapSlice[0]["address"] = "沙河"
	for index, value := range mapSlice {
		fmt.Printf("index:%d value:%v\n", index, value)
	}
}
```

> 切片的扩容操作会把切片直接迁移到别的位置？好像很耗性能！
>
> 初始化：make，new，赋值

**值为切片类型的map**

下面的代码演示了map中值为切片类型的操作：

```go
func main() {
	var sliceMap = make(map[string][]string, 3)
	fmt.Println(sliceMap)
	fmt.Println("after init")
	key := "中国"
	value, ok := sliceMap[key]
	if !ok {
		value = make([]string, 0, 2)
	}
	value = append(value, "北京", "上海")
	sliceMap[key] = value
	fmt.Println(sliceMap)
}
```

- HashMap

```go
package main

import (
	"fmt"
)

type HashMap struct {
	key      string
	value    string
	hashCode int
	next     *HashMap
}

var table [16](*HashMap)

func initTable() {
	for i := range table {
		table[i] = &HashMap{"", "", i, nil}
	}
}

func getInstance() [16](*HashMap) {
	if table[0] == nil {
		initTable()
	}
	return table
}

func genHashCode(k string) int {
	if len(k) == 0 {
		return 0
	}
	var hashCode int = 0
	var lastIndex int = len(k) - 1
	for i := range k {
		if i == lastIndex {
			hashCode += int(k[i])
			break
		}
		hashCode += (hashCode + int(k[i])) * 31
	}
	return hashCode
}

func indexTable(hashCode int) int {
	return hashCode % 16
}

func indexNode(hashCode int) int {
	return hashCode >> 4
}

func put(k string, v string) string {
	var hashCode = genHashCode(k)
	var thisNode = HashMap{k, v, hashCode, nil}

	var tableIndex = indexTable(hashCode)
	var nodeIndex = indexNode(hashCode)

	var headPtr [16](*HashMap) = getInstance()
	var headNode = headPtr[tableIndex]

	if (*headNode).key == "" {
		*headNode = thisNode
		return ""
	}

	var lastNode *HashMap = headNode
	var nextNode *HashMap = (*headNode).next

	for nextNode != nil && (indexNode((*nextNode).hashCode) < nodeIndex) {
		lastNode = nextNode
		nextNode = (*nextNode).next
	}
	if (*lastNode).hashCode == thisNode.hashCode {
		var oldValue string = lastNode.value
		lastNode.value = thisNode.value
		return oldValue
	}
	if lastNode.hashCode < thisNode.hashCode {
		lastNode.next = &thisNode
	}
	if nextNode != nil {
		thisNode.next = nextNode
	}
	return ""
}

func get(k string) string {
	var hashCode = genHashCode(k)
	var tableIndex = indexTable(hashCode)

	var headPtr [16](*HashMap) = getInstance()
	var node *HashMap = headPtr[tableIndex]

	if (*node).key == k {
		return (*node).value
	}

	for (*node).next != nil {
		if k == (*node).key {
			return (*node).value
		}
		node = (*node).next
	}
	return ""
}

//examples
func main() {
	getInstance()
	put("a", "a_put")
	put("b", "b_put")
	fmt.Println(get("a"))
	fmt.Println(get("b"))
	put("p", "p_put")
	fmt.Println(get("p"))
}
```

```go
// 找出一个字符串里的每个单词出现的次数
func main() {
	var liZi = make([]string, 0, 4)
	liZi = append(liZi, "how", "do", "you", "do")
	fmt.Println(liZi)
	var tongJi = make(map[string]int)
	for _, v := range liZi {
		tongJi[v] = tongJi[v] + 1
	}
	fmt.Println(tongJi)
}
```

---

#### 函数

**函数存在的意义**

- 函数是一段代码的封装
- 把一段逻辑抽象封装到一个函数中，给它起一个名字，方便多次使用
- 使用函数可以让代码结构更佳清晰

**定义**

其中：

- 函数名：由字母、数字、下划线组成。但函数名的第一个字母不能是数字。在同一个包内，函数名也称不能重名（包的概念详见后文）。
- 参数：参数由参数变量和参数变量的类型组成，多个参数之间使用`,`分隔。
- 返回值：返回值由返回值变量和其变量类型组成，也可以只写返回值的类型，多个返回值必须用`()`包裹，并用`,`分隔。
- 函数体：实现指定功能的代码块。

我们先来定义一个求两个数之和的函数：

```go
func intSum(x int, y int) (ret int) { 
	return x + y
}

func sum(s string, x, y int) int { // 一个返回值、相邻的参数类型一样的话可以只写一个
	return x + y
}

func intSum(x int, y int) (ret int) { 
    // 给返回值命名、相当于直接命名了一个变量、可以直接在函数内使用
    // 可以不写 return 后面的、因为函数已经知道了要返回这个值
	ret = x + y
    return
}

func intSum(x, y int)(int, int) { // 可以返回多个值
    // do something
}

func sum(x int, y ...int) int {
    fmt.Println(y[0]) // 2
	return x
}
// 表示 x: 1
// 切片 y: [2 3]; 除了第一个参数别的参数可以不传
ret := sum(1, 2, 3)
```

函数的参数和返回值都是可选的，例如我们可以实现一个既不需要参数也没有返回值的函数：

```go
func sayHello() {
	fmt.Println("Hello 沙河")
}
```

> 注意，调用有返回值的函数时，可以不接收其返回值。
>
> Go 语言没有默认参数这个概念。参数要么传，要么没有。

**函数传参也是值类型传参**

````go
func main() {
	a := 6
	f1(a)
	fmt.Println(a) // 6
}

func f1(arg int) {
	// var arg int 类似于有这么一个操作
	arg = 5
	fmt.Println(arg) // 5
}
````

**嵌套函数**

```go
// 在命名函数里命名函数、不支持
func f1() {
    //...
    func f2() {
        
    }
}

// 嵌套函数的话只能使用匿名函数
```



---

#### defer

- 讲跟随 defer 关键字后面的语句进行延迟处理
- defer 后面好像必须是函数调用
- defer 语句把后面的语句延迟到函数即将返回时执行

- 先执行正常的语句，后逆序执行 defer 后面的语句
- defer 多用于函数结束之前释放内存（文件句柄，数据库连接，socket连接）
- 虽然会延迟执行，但是它后面的变量（等）会运行。准确的说应该叫延迟释放。

```go
func main() {
    defer fmt.Println(666)
    defer fmt.Println(66)
    fmt.Println(555)
}
// 555
// 66
// 666
```

**函数 return 分为三步**

1. 给返回值赋值
2. 有 defer 的话按规则执行 defer 后面的语句
3. 返回

![defer执行时机](https://www.liwenzhou.com/images/Go/func/defer.png)

```go
func f1() int {
	x := 5
	defer func() {
		x++
	}()
	return x
}

func f2() (x int) {
	defer func() {
		x++
	}()
	return 5
}

func f3() (y int) {
	x := 5
	defer func() {
		x++
	}()
	return x
}
func f4() (x int) {
	defer func(x int) {
		x++
	}(x)
	return 5
}
func main() {
	fmt.Println(f1()) // 5
	fmt.Println(f2()) // 6
	fmt.Println(f3()) // 5
	fmt.Println(f4()) // 5
}
```

---

==**作用域**==

- 全局作用域： 全局变量是定义在函数外部的变量，它在程序整个运行周期内都有效。 在函数中可以访问到全局变量。 
- 函数作用域： 函数内定义的变量无法在该函数外使用，如果局部变量和全局变量重名，优先访问局部变量。

- 语句块：if条件判断、for循环、switch语句也有作用域。
- 同级也不可以直接访问变量

```go
var fu = func() {
	a := 5
}

func main() {
	fmt.Println(a) // undefined a
}
```

---

==**函数类型和变量**==

**高阶函数**

函数可以作为类型存在，函数也可以作为参数的类型

```go
func f1(f func() int) {
	fmt.Println(f())
}

func main() {
	f1(func() int {
		return 5
	})
}
```

函数还可以作为返回值

```go
func f1(f func() int) func(x int) int {
	f()
	return func(x int) int {
		return 666
	}
}
func main() {
	f1(func() int {
		return 5
	})
}
```

---

==**匿名函数**==

- 可当参数传入，可当返回值返回
- 可赋值给变量
- 一般用在函数内部定义函数
-  匿名函数多用于实现回调函数和闭包。 

```go
func main() {
	foo := func() {
		fmt.Println(666)
	}
	foo()
}

// 如果只是调用一次的函数、可以简写成立即执行函数
func(x int) (ret int) {
    ret = x + 1
    return
}(5)
```

> 函数传参数时不用指定类型的

---

==**闭包**==

- 闭包是函数，除了包含自己作用域的变量，还保含外部作用域的变量

- 闭包指的是一个函数和与其相关的引用环境组合而成的实体。简单来说，`闭包=函数+外部变量的引用`。 

- 原理：
  1. 函数可以作为返回值
  2. 函数内部查找变量的顺序，从里到外

```go
// 把 f2 送到 f1 执行
package main

import "fmt"

func main() {
	fn := f3(f2, 1, 3)
	f1(fn)
}

func f1(fn func()) {
	fmt.Println("this is f1")
	fn()
}

func f2(x, y int) {
	fmt.Println("this is f2")
	fmt.Println(x + y)
}

func f3(fn func(int, int), m, n int) func() {
	temp := func() {
		fn(m, n)
	}
	return temp
}
```

```go
// 返回值是 6
func f1() (x int) {
	defer func(x *int) {
		(*x)++
	}(&x)
	return 5
}
```

---

#### 内置函数

|     内置函数     |                             介绍                             |
| :--------------: | :----------------------------------------------------------: |
|      close       |                     主要用来关闭channel                      |
|       len        |      用来求长度，比如string、array、slice、map、channel      |
|       new        | 用来分配内存，主要用来分配值类型，比如int、struct。返回的是指针 |
|       make       |   用来分配内存，主要用来分配引用类型，比如chan、map、slice   |
|      append      |                 用来追加元素到数组、slice中                  |
| panic 和 recover |                        用来做错误处理                        |

==panic 和 recover==

当你的程序出现崩溃级别的 panic 时，recover 这种错误

Go 语言中目前（Go1.12）是没有异常机制，但是使用`panic/recover`模式来处理错误。 `panic`可以在任何地方引发，但`recover`只有在`defer`调用的函数中有效。 首先来看一个例子： 

```go
func funcA() {
	fmt.Println("func A")
}

func funcB() {
	panic("panic in B")
}

func funcC() {
	fmt.Println("func C")
}
func main() {
	funcA()
	funcB()
	funcC()
}
```

输出：

```bash
func A
panic: panic in B

goroutine 1 [running]:
main.funcB(...)
        .../code/func/main.go:12
main.main()
        .../code/func/main.go:20 +0x98
```

程序运行期间`funcB`中引发了`panic`导致程序崩溃，异常退出了。这个时候我们就可以通过`recover`将程序恢复回来，继续往后执行。

```go
func funcA() {
	fmt.Println("func A")
}

func funcB() {
	defer func() {
		err := recover()
		//如果程序出出现了panic错误,可以通过recover恢复过来
		if err != nil {
			fmt.Println("recover in B")
		}
	}()
	panic("panic in B")
}

func funcC() {
	fmt.Println("func C")
}
func main() {
	funcA()
	funcB()
	funcC()
}
```

**注意：**

1. `recover()`必须搭配`defer`使用。
2. `defer`一定要在可能引发`panic`的语句之前定义

---

#### fmt 标准库

`fmt.Print()`：输入啥输出啥

`fmt.Printf()`：格式化输出

`fmt.Println()`：换行输出

`Fprint`： `Fprint`系列函数会将内容输出到一个`io.Writer`接口类型的变量`w`中，我们通常用这个函数往文件中写入内容。 

`Sprint`： `Sprint`系列函数会把传入的数据生成并返回一个字符串。 

`Errorf`： `Errorf`函数根据format参数生成格式化字符串并返回一个包含该字符串的错误。 

**占位符**

**布尔型**

| 占位符 |    说明     |
| :----: | :---------: |
|   %t   | true或false |

**整型**

| 占位符 |                             说明                             |
| :----: | :----------------------------------------------------------: |
|   %b   |                         表示为二进制                         |
|   %c   |                    该值对应的unicode码值                     |
|   %d   |                         表示为十进制                         |
|   %o   |                         表示为八进制                         |
|   %x   |                   表示为十六进制，使用a-f                    |
|   %X   |                   表示为十六进制，使用A-F                    |
|   %U   |          表示为Unicode格式：U+1234，等价于”U+%04X”           |
|   %q   | 该值对应的单引号括起来的go语法字符字面值，必要时会采用安全的转义表示 |

**浮点数与复数**

| 占位符 |                          说明                          |
| :----: | :----------------------------------------------------: |
|   %b   |   无小数部分、二进制指数的科学计数法，如-123456p-78    |
|   %e   |              科学计数法，如-1234.456e+78               |
|   %E   |              科学计数法，如-1234.456E+78               |
|   %f   |           有小数部分但无指数部分，如123.456            |
|   %F   |                        等价于%f                        |
|   %g   | 根据实际情况采用%e或%f格式（以获得更简洁、准确的输出） |
|   %G   | 根据实际情况采用%E或%F格式（以获得更简洁、准确的输出） |

**字符串和[]byte**

| 占位符 |                             说明                             |
| :----: | :----------------------------------------------------------: |
|   %s   |                   直接输出字符串或者[]byte                   |
|   %q   | 该值对应的双引号括起来的go语法字符串字面值，必要时会采用安全的转义表示 |
|   %x   |           每个字节用两字符十六进制数表示（使用a-f            |
|   %X   |          每个字节用两字符十六进制数表示（使用A-F）           |

**指针**

| 占位符 |              说明              |
| :----: | :----------------------------: |
|   %p   | 表示为十六进制，并加上前导的0x |

**宽度标识符**

宽度通过一个紧跟在百分号后面的十进制数指定，如果未指定宽度，则表示值时除必需之外不作填充。精度通过（可选的）宽度后跟点号后跟的十进制数指定。如果未指定精度，会使用默认精度；如果点号后没有跟数字，表示精度为0。举例如下：

| 占位符 |        说明        |
| :----: | :----------------: |
|   %f   | 默认宽度，默认精度 |
|  %9f   |  宽度9，默认精度   |
|  %.2f  |  默认宽度，精度2   |
| %9.2f  |    宽度9，精度2    |
|  %9.f  |    宽度9，精度0    |

**其他falg**

| 占位符 |                             说明                             |
| :----: | :----------------------------------------------------------: |
|  ’+’   | 总是输出数值的正负号；对%q（%+q）会生成全部是ASCII字符的输出（通过转义）； |
|  ’ ‘   | 对数值，正数前加空格而负数前加负号；对字符串采用%x或%X时（% x或% X）会给各打印的字节之间加空格 |
|  ’-’   | 在输出右边填充空白而不是默认的左边（即从默认的右对齐切换为左对齐）； |
|  ’#’   | 八进制数前加0（%#o），十六进制数前加0x（%#x）或0X（%#X），指针去掉前面的0x（%#p）对%q（%#q），对%U（%#U）会输出空格和单引号括起来的go字面值； |
|  ‘0’   | 使用0而不是空格填充，对于数值类型会把填充的0放在正负号后面； |

**获取输入**

Go语言`fmt`包下有`fmt.Scan`、`fmt.Scanf`、`fmt.Scanln`三个函数，可以在程序运行过程中从标准输入获取用户的输入。 

==fmt.Scan==

- Scan从标准输入扫描文本，读取由空白符分隔的值保存到传递给本函数的参数中，换行符视为空白符。
- 本函数返回成功扫描的数据个数和遇到的任何错误。如果读取的数据个数比提供的参数少，会返回一个错误报告原因。

```go
func main() {
	var a string
	fmt.Scan(&a)
	fmt.Println(a) // 输出从终端输入的值
}
```

==fmt.Scanln==

- Scan 的换行

==fmt.Scanf==

- 格式化呗

- Scanf从标准输入扫描文本，根据format参数指定的格式去读取由空白符分隔的值保存到传递给本函数的参数中。
- 本函数返回成功扫描的数据个数和遇到的任何错误。

```go
func main() {
	var (
		name    string
		age     int
		married bool
	)
	fmt.Scanf("1:%s 2:%d 3:%t", &name, &age, &married)
	fmt.Printf("扫描结果 name:%s age:%d married:%t \n", name, age, married)
}
```

**bufio.NewReader**

有时候我们想完整获取输入的内容，而输入的内容可能包含空格，这种情况下可以使用`bufio`包来实现。示例代码如下：

```go
func bufioDemo() {
	reader := bufio.NewReader(os.Stdin) // 从标准输入生成读对象
	fmt.Print("请输入内容：")
	text, _ := reader.ReadString('\n') // 读到换行
	text = strings.TrimSpace(text)
	fmt.Printf("%#v\n", text)
}
```

**Fscan系列**

这几个函数功能分别类似于`fmt.Scan`、`fmt.Scanf`、`fmt.Scanln`三个函数，只不过它们不是从标准输入中读取数据而是从`io.Reader`中读取数据。

```go
func Fscan(r io.Reader, a ...interface{}) (n int, err error)
func Fscanln(r io.Reader, a ...interface{}) (n int, err error)
func Fscanf(r io.Reader, format string, a ...interface{}) (n int, err error)
```

**Sscan系列**

这几个函数功能分别类似于`fmt.Scan`、`fmt.Scanf`、`fmt.Scanln`三个函数，只不过它们不是从标准输入中读取数据而是从指定字符串中读取数据。

```go
func Sscan(str string, a ...interface{}) (n int, err error)
func Sscanln(str string, a ...interface{}) (n int, err error)
func Sscanf(str string, format string, a ...interface{}) (n int, err error)
```

---

#### 递归函数

- 自己调用自己
- 应用场景：适合问题相同，规模越来越小的问题
- 递归一定要有一个明确的退出条件

```go
func foo() {
    foo()
}
```

```go
// 计算 n 的阶乘
func f(n uint64) uint64 {
	if n <= 1 {
		return 1
	}
	return n * f(n-1)
}
```

```go
// 上台阶
// n 个台阶、一次走 1 部、也可以一次走 2 步、有多少种走法
func ztj(n uint64) uint64 {
	if n == 1 {
		return 1
	}
	if n == 2 {
		return 2
	}
	return ztj(n-1) + ztj(n-2)
}
```

#### 类型别名和自定义类型

```go
// 自定义类型
type myInt int

func main() {
	var i myInt
    fmt.Printf("%T", i) // main.myInt 
}
```

```go
// 类型别名
// 只在代码编写阶段有效
type yourInt = int

func main() {
	var i yourInt
	fmt.Printf("%T", i) // int
}

// rune 和 byte 底层都是类型别名
// rune: int32、两者可以相比较等
// byte: uint8、两者可以相比较等
```

---

#### 结构体

**简单定义使用**

一个多维度的结构体，类似面向对象语言的类

一个包里类型名不能重复，一个结构体里字段名不能重复

结构体是值类型

```go
// 声明
type person struct {
	name, gender string // 类型一样的话可以简写
	age          int
	hobby        []string
}

func main() {
    // 定义使用、p1 的类型是 main.person
	var p1 person
	p1.name = "Tom"
	p1.age = 5
	p1.gender = "man"
	p1.hobby = []string{"ball"}
	fmt.Println(p1.name) // "Tom"
}
```

**匿名结构体**

多用于临时场景

```go
// 类型是 struct 类型
var person = struct {
    name string
    age  int
}
```

---

**结构体指针和结构体初始化**

Go 语言中函数参数永远是拷贝

结构体是值类型

想要修改内存中的变量就需要指针来获取改变

指针就是内存地址

如果初始化对应字段的值，那么就会是类型的零值

```go
func main() {
	var p1 person
	p1.name = "Tom"
	p1.gender = "男"
	f(&p1)
	fmt.Println(p1.gender) // "女"
}

type person struct {
	name, gender string
}

func f(x *person) {
	(*x).gender = "女"
    // 语法糖、自动根据指针找对应的变量: x.gender = "女"
	fmt.Println(x.gender)  // "女"
}
```

使用 new 关键字实例化结构体

```go
type person struct {
    name, sex string
}

// 结构体指针1
var p1 = new(person)
p1.name = "tom"
p1.sex = "man"
fmt.Prinf("%T", p1) // *main.person
```

 ```go
// 实例化并初始化
var p2 = person{
    name: "jerry",
    sex:  "man",
}

var p2 = person{
    "jerry",
    "man",
}

// 求结构体的指针
var p2 = &person{
    name: "jerry",
    sex:  "man",
}
 ```

```go
// 使用值列表的形式初始化、值得顺序和结构体定义时的顺序一样
p3 := person {
    "小王子",
    "男",
}

// 求结构体的指针
p3 := &person {
    "小王子",
    "男",
}
```

**结构体内存布局**

结构体的字段在内存地址是连在一起的

有内存对齐的

**结构体和面向对象**

struct 可以模拟面向对象编程

```go
type person struct {
    name string
    age  int
}

// 构造函数、在 Go 语言约定构造函数名用 new 开头
// 这里返回的是结构体还是结构体指针需要按情况而定
// 当结构体较大时尽量使用结构体指针、减少程序的内存才能运行
func newPerson(name string, age int) *person {
    return &person{
        name: mane,
        age:  age,
    }
}

func main() {
    // 实例化
    p1 := newPerson("tom", 18)
    p2 := newPerson("jerry", 20)
}
```

---

#### 方法和接收者

**方法 Method**：一种作用于特定类型变量的函数

```go
func (接收者变量 接收者类型) 方法名 (参数列表) (返回参数) {
    函数体
}
```

```go
type dog struct {
	name string
}

func newDog(name string) dog {
	return dog{
		name: name,
	}
}

// 方法 method
// (d dog): 接受者表示的是调用方法的具体类型变量、约定成俗接受者的名称使用类型的首字符
func (d dog) wang() {
	fmt.Println("汪汪汪", d.name)
}

func main() {
    d1 := newDog("tom")
    d1.wang()
}
```

> Go 中标识符首字母大写表示对外部包可见的（暴漏的，共有的），还有 Go 语言会要求对这种标识符都加上注释

**值接收者和指针接收者**

