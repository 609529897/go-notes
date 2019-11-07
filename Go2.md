# Go 第二部分

## 接口（interface）

**基本使用**

接口是一种类型，是一种特殊的类型，它规定的变量有那些方法，不约束属性

在编程中会遇到以下场景：

- 不关心一个变量的类型，只关心它能调用什么方法

```go
func main() {
	var c1 cat
	var d1 dog
    // 把可以传入接口的变量保存到接口类型的实例
    // 接口保存的值分为两个部分: 1.值的类型、2.值本身
    // 这样就实现了接口可以存储不同类型的值
    var s speaker
    s = c1
    s.speak()
    
	da(c1)
	da(d1)
}

type cat struct{}

type dog struct{}

type speaker interface { // 接口、只要实现 speak 方法的变量都是 speaker 类型、方法签名
    speak() int          // speak 方法和 int 类型的返回值
}

func (c cat) speak() int {
	fmt.Println("喵喵喵")
	return 0
}

func (d dog) speak() int {
	fmt.Println("汪汪汪")
	return 0
}

func da(x speaker) {
	x.speak()
}
```

**接口定义和使用**

```go
// 定义
type interfaceName interface{
    funcName1(arg1, arg2...)(returnType1, returnType2...)
    funcName2(arg1, arg2...)(returnType1, returnType2...)
    ...
}
```

用来给变量\参数\返回值\等设置类型

**接口的实现**

一个变量如果实现了接口中规定的所有的方法，那么这个变量就实现了这个接口，可以称为这个接口类型的变量。 

---

**值接收者和指针接收者实现接口的区别**

- 使用值接收者实现接口，结构体类型和结构体指针类型的变量都能存
- 使用指针接收者实现接口只能存结构体指针类型的变量

---

**多个接口和接口嵌套**

多个类型可以实现一个接口

一个类型可以实现多个接口

接口也可以嵌套嵌套

> 接口名字一般是方法名后面加 er

---

**空接口**

- 任何类型都变量都可以保存到空接口中
- 没有必要取名字，通常定义成下面的格式

```go
interface{}
```

```go
// 应用实例1、表示 map 的值可以是任何类型
func main() {
	m1 := make(map[string]interface{}, 16)
	m1["name"] = "tom"
	m1["age"] = 19
	m1["home"] = "内蒙古"
    m1["hobby"] = [...]string{"唱","跳","rap"}
	fmt.Println(m1["age"])
}
```

```go
// 应用实例2、表示可以传入任何类型的参数
func fn(x interface{}) {
	fmt.Println(x)
}
```

**类型断言**

- 想知道空接口传来值的具体类型

```go
func main() {
	fn(5)
}

func fn(x interface{}) {
    // 类型断言、x 必须是接口
	i, ok := x.(int)
	if !ok {
		fmt.Println("猜错了")
    } else {
        fmt.Println(i)
    }
}
```

```go
// 可以使用 switch 指定类型、做指定操作
func fn(x interface{}) {
	switch t := x.(type) {
	case string:
		fmt.Println("是一个字符串", t)
	case int:
		fmt.Println("是一个数字类型", t)
	}
}
```

> 两个或以上的结构体遵守一个规范时使用接口

---

## package 相关

**包介绍**

`包(package)`：是多个 Go 源码的集合，是一种高级的代码复用方案，Go 语言为我们提供了很多内置包，如 `fmt`，`os`，`io` 等。

**定义包**

我们可以根据需求自定义包，一个包可以简单的理解为存放多个 `.go` 文件的文件夹。该文件夹的所有 go 文件第一行都要放添加如下代码，声明该文件归属的包。 

```go
package 包名
```

**注意事项：**

- 一个文件夹下面只能有一个包，同样一个包的文件不能在多个文件夹下
- 包名可以不和文件夹名字一样，但一般是同名，包名不能包含 - 符号
- 包名 main 的包为应用程序的入口包，编译时不包含 main 包的源代码不会得到可执行文件。

**导入包**

- 包中的标识符小写的表示私有（只能在当前的包中使用），大写的表示可以导出，在别的包里导入
- 默认文件夹名就是包名，go 语言禁止循环导入包
- 包名的导入，一般是从 GOPATH/src 文件夹开始找起、最后到包名就可以。路径使用 `/` 分隔符
- 文件夹只能声明一个包，同包下的函数方法的不用 `import` 就可以用（不知道需不需要首字符大写）

```go
// add 是别名
import add "code.ninggele.com/studygo/calc"

// 匿名导入
import _ "包的路径"

// 直接使用方法、不推荐使用
import(
    . "fmt"
)

Println(666) // 忽略包名、直接使用方法
```

自定义导入导出：

1. 要导出的变量名必须首字符大写
2. `import`，导入从 `src` 文件夹到包的路径
3. 使用包里面的函数等

---

## 管理外部包

**使用 `Github` 上的外部包**

1. 直接从 github clone 下载源文件，放到 GOPATH/src 目录下 github.com
2. 使用 go get 下载文件到 src 目录下

```shell
$ go get gitbub.com/go-sql-driver/mysql
```

```shell
$ go install l_package/pk2
```

**go build 和 go install**

- go build：用于测试编译包，在项目目录下生成可执行文件（有main包）。

- go install：主要用来生成库和工具。一是编译包文件（无main包），将编译后的包文件放到 pkg 目录下（`$GOPATH/pkg`）。二是编译生成可执行文件（有main包），将可执行文件放到 bin 目录（`$GOPATH/bin`）。

- go build 不能生成包文件, go install 可以生成包文件
- go build 生成可执行文件在当前目录下， go install 生成可执行文件在bin目录下（`$GOPATH/bin`）

---

**init 函数**

没有参数，没有返回值。在导入包的时候自动触发的函数，可以有多个

```go
func init() {
    fmt.Println("一旦导入此包时执行此函数")
}
```

执行时机

- 全局声明
- `init()`
- `main()`
- main => A包 => B包 => C白，执行顺序：C包的 `init` 函数 => B包 => A包

---

## 文件操作

**读文件**

使用 `os` ，`os.Open` 打开文件，返回一个 `*File` 和 `err`。对得到的实例使用 `close()` 方法能关闭文件。

> 只是单纯的读的操作

```go
func main() {
	fileObj, err := os.Open("../txt/1.txt")
	if err != nil {
		fmt.Println("读取失败...")
		return
	}
	defer fileObj.Close() // 记得关闭文件
	// 读文件
	var tmp [128]byte // 指定读的长度
	for {
		n, err := fileObj.Read(tmp[:]) // n : 返回读的值个数
		if err != nil {
			fmt.Printf("read from file failed, err:%v", err)
			return
		}
		fmt.Printf("读了%d个字节\n", n)
		fmt.Println(string(tmp[:n]))
		if n < 128 {
			return
		}
	}
}
```

使用 `bufio` 包读文件

> bufio 缓存？

```go
func readFromFileByBufio() {
	fileObj, err := os.Open("../txt/1.txt")
	if err != nil {
		fmt.Println("读取失败...")
		return
	}
	defer fileObj.Close()
	// 创建一个用来从文件中读内容的对象
	reader := bufio.NewReader(fileObj)
	for {
		line, err := reader.ReadString('\n')
		if err == io.EOF {
			fmt.Println("文件读完了")
			return
		}
		if err != nil {
			fmt.Println("read file failed, err:", err)
			return
		}
		fmt.Print(line)
	}
}
```

`ioutil` 包读写文件更方便

```go
func readFromFileByIoutil() {
	ret, err := ioutil.ReadFile("../txt/1.txt")
	if err != nil {
		fmt.Printf("read file failed, err: %v\n", err)
		return
	}
	fmt.Println(string(ret))
}
```

**写文件**

`os.OpenFile()` 函数能够已指定模式打开文件，从而实现文件写入相关功能

```go
// 函数签名
func OpenFile(name string, flag int, perm FileMode)(*File, error) {
    ...
}
```

其中：

`name` ：要打开的文件名 `flag` ：打开文件的模式。模式有以下几种：

|     模式      |   含义   |
| :-----------: | :------: |
| `os.O_WRONLY` |   只写   |
| `os.O_CREATE` | 创建文件 |
| `os.O_RDONLY` |   只读   |
|  `os.O_RDWR`  |   读写   |
| `os.O_TRUNC`  |   清空   |
| `os.O_APPEND` |   追加   |

 `perm`：文件权限，一个八进制数。r（读）04，w（写）02，x（执行）01。 

**Write和WriteString**

```go
func main() {
	file, err := os.OpenFile("xx.txt", os.O_CREATE|os.O_TRUNC|os.O_WRONLY, 0666)
	if err != nil {
		fmt.Println("open file failed, err:", err)
		return
	}
	defer file.Close()
	str := "hello 沙河"
	file.Write([]byte(str))         //写入字节切片数据
	file.WriteString("hello 小王子") //直接写入字符串数据
}
```

> os.ModePerm go 语言提供的常数：0777

**bufio.NewWriter**

```go
func main() {
	file, err := os.OpenFile("xx.txt", os.O_CREATE|os.O_TRUNC|os.O_WRONLY, 0666)
	if err != nil {
		fmt.Println("open file failed, err:", err)
		return
	}
	defer file.Close()
	writer := bufio.NewWriter(file)
	for i := 0; i < 10; i++ {
		writer.WriteString("hello沙河\n") //将数据先写入缓存
	}
	writer.Flush() //将缓存中的内容写入文件
}
```

**ioutil.WriteFile**

```go
func main() {
	str := "hello 沙河"
	err := ioutil.WriteFile("./xx.txt", []byte(str), 0666)
	if err != nil {
		fmt.Println("write file failed, err:", err)
		return
	}
}
```

使用 bufio 获取用户输入

```go
func useBufio() {
    var s string
    reader := bufio.NewReader(os.Stdin)
    s, _ = reader.ReadString('\n')
    fmt.Println(s)
}
```

文件中间插入内容

```go
func fn() {
    file, err := os.OpenFile("../txt/1.txt", os.O_RDWR, 0644)
        if err != nil {
        fmt.Printf("open from file failed, err:%v", err)
            return
    }
    defer file.Close()
    file.Seek(3, 0)         // 光标移动
    var s []byte
    s = []byte{'c'}
    file.Write(s)
}
```

...

> os.Rename("旧路经", "新路径")：重命名

---

**文件操作**

```go
fileInfo, err := os.Stat("../txt/1.txt")
if err != nil {
    fmt.Println("error")
}
fileInfo.name        // 文件名字
fileInfo.Size()      // 文件大小
fileInfo.IsDir       // 是否是目录
fileInfo.ModTime()   // 修改时间
fileInfo.Mode()      // 权限、符号表示和八进制表示
```

**文件路径**

- 相对路径：相对于当前工程的路径
- 绝对路径：从根目录开始的路径
- `.` 当前目录，`..` 上层目录  

```go
filepath.Abs(filepath1) // 获取绝对路径
path.Join(file, "..")   // 获取父目录
```

**创建目录**

```go
err := os.Mkdir("目录的路径和目录", os.ModePerm/*权限*/)
err := os.MkdirAll("目录的路径和目录", os.ModePerm)      // 创建多层文件夹
```

**创建文件**

```go
file, err := os.Create("路径和文件")
```

**删文件和目录**

慎用，删错了就完了

得把文件内的内容和目录内的文件清空才能删除

```go
err := os.Remove("路径")
err := os.Remove("路径") // 删除嵌套的多个目录
```

**遍历文件夹**

```go
func listFile(dirname string, level int) {
	// level: 记录当前文件的层次、让后可以生成带有层次感的符号
	s := "|--"
	for i := 0; i < level; i++ {
		s = "|  " + s
	}
	fileInfo, err := ioutil.ReadDir(dirname)
	if err != nil {
		log.Fatal(err)
	}
	for _, fi := range fileInfo {
		filename := dirname + "/" + fi.Name()
		fmt.Printf("%s%s\n", s, filename)
		if fi.IsDir() {
			listFile(filename, level+1)
		}
	}
}
```

---

## time 包

 time包提供了时间的显示和测量用的函数。日历的计算采用的是公历。

**时间类型**

`time.Time`类型表示时间。我们可以通过`time.Now()`函数获取当前的时间对象，然后获取时间对象的年月日时分秒等信息。示例代码如下：

```go
func timeDemo() {
	now := time.Now() //获取当前时间
	fmt.Printf("current time:%v\n", now)

	year := now.Year()     //年
	month := now.Month()   //月
	day := now.Day()       //日
	hour := now.Hour()     //小时
	minute := now.Minute() //分钟
	second := now.Second() //秒
	fmt.Printf("%d-%02d-%02d %02d:%02d:%02d\n", year, month, day, hour, minute, second)
}
```

```go
time.Date(2008, 7, 15, 16, 30, 28, 0, time.Local) // 获得指定时间
```

**时间戳**

时间戳是自1970年1月1日（08:00:00GMT）至当前时间的总毫秒数。它也被称为Unix时间戳（UnixTimestamp）。

基于时间对象获取时间戳的示例代码如下：

```go
func timestampDemo() {
	now := time.Now()            //获取当前时间
	timestamp1 := now.Unix()     //时间戳
	timestamp2 := now.UnixNano() //纳秒时间戳
	fmt.Printf("current timestamp1:%v\n", timestamp1)
	fmt.Printf("current timestamp2:%v\n", timestamp2)
}
```

使用`time.Unix()`函数可以将时间戳转为时间格式。

```go
func timestampDemo2(timestamp int64) {
	timeObj := time.Unix(timestamp, 0) //将时间戳转为时间格式
	fmt.Println(timeObj)
	year := timeObj.Year()     //年
	month := timeObj.Month()   //月
	day := timeObj.Day()       //日
	hour := timeObj.Hour()     //小时
	minute := timeObj.Minute() //分钟
	second := timeObj.Second() //秒
	fmt.Printf("%d-%02d-%02d %02d:%02d:%02d\n", year, month, day, hour, minute, second)
}
```

**时间间隔**

`time.Duration`是`time`包定义的一个类型，它代表两个时间点之间经过的时间，以纳秒为单位。`time.Duration`表示一段时间间隔，可表示的最长时间段大约290年。

time包中定义的时间间隔类型的常量如下：

```go
const (
    Nanosecond  Duration = 1
    Microsecond          = 1000 * Nanosecond
    Millisecond          = 1000 * Microsecond
    Second               = 1000 * Millisecond
    Minute               = 60 * Second
    Hour                 = 60 * Minute
)
```

例如：`time.Duration`表示1纳秒，`time.Second`表示1秒。

**时间操作**

- Add

我们在日常的编码过程中可能会遇到要求时间+时间间隔的需求，Go语言的时间对象有提供Add方法如下：

```go
func (t Time) Add(d Duration) Time
```

举个例子，求一个小时之后的时间：

```go
func main() {
	now := time.Now()
	later := now.Add(time.Hour) // 当前时间加1小时后的时间
	fmt.Println(later)
}
```

- Sub

求两个时间之间的差值：

```go
func (t Time) Sub(u Time) Duration
```

返回一个时间段t-u。如果结果超出了Duration可以表示的最大值/最小值，将返回最大值/最小值。要获取时间点t-d（d为Duration），可以使用t.Add(-d)。

- Equal

```go
func (t Time) Equal(u Time) bool
```

判断两个时间是否相同，会考虑时区的影响，因此不同时区标准的时间也可以正确比较。本方法和用t==u不同，这种方法还会比较地点和时区信息

- Before

```go
func (t Time) Before(u Time) bool
```

如果t代表的时间点在u之前，返回真；否则返回假。

- After

```go
func (t Time) After(u Time) bool
```

如果t代表的时间点在u之后，返回真；否则返回假。

**定时器**

使用`time.Tick(时间间隔)`来设置定时器，定时器的本质上是一个通道（channel）。

```go
func tickDemo() {
	ticker := time.Tick(time.Second) //定义一个1秒间隔的定时器
	for i := range ticker {
		fmt.Println(i)//每秒都会执行的任务
	}
}
```

**时间格式化**

时间类型有一个自带的方法`Format`进行格式化，需要注意的是Go语言中格式化时间模板不是常见的`Y-m-d H:M:S`而是使用Go的诞生时间2006年1月2号15点04分（记忆口诀为2006 1 2 3 4）。也许这就是技术人员的浪漫吧。

补充：如果想格式化为12小时方式，需指定`PM`。

```go
func formatDemo() {
	now := time.Now()
	// 格式化的模板为Go的出生时间2006年1月2号15点04分 Mon Jan
	// 24小时制
	fmt.Println(now.Format("2006-01-02 15:04:05.000 Mon Jan"))
	// 12小时制
	fmt.Println(now.Format("2006-01-02 03:04:05.000 PM Mon Jan"))
	fmt.Println(now.Format("2006/01/02 15:04"))
	fmt.Println(now.Format("15:04 2006/01/02"))
	fmt.Println(now.Format("2006/01/02"))
```

**解析字符串格式的时间**

```go
time, err := time.Parse("2006-7-27", s3)
```

```go
now := time.Now()
// 加载时区
loc, err := time.LoadLocation("Asia/Shanghai")
if err != nil {
	fmt.Println(err)
	return
}
// 按照指定时区和指定格式解析字符串时间
timeObj, err := time.ParseInLocation("2006/01/02 15:04:05", "2019/08/04 14:15:20", loc)
if err != nil {
	fmt.Println(err)
	return
}
fmt.Println(timeObj)
fmt.Println(timeObj.Sub(now))
```

````go
y, m, d := t1.Date() // 年月日
h, m, s := t1.Date() // 时分秒
````

> time.Sleep(100 * time.Millisecond)：表示阻塞 100 秒

---

**time 包通道相关操作**

```go
timer := time.NewTimer(3*time.Second) // 定时器、三秒后执行
ch := timer.C                         // 三秒钟后的时间
timer.Stop()                          // 取消计时器 
```

---

## Log 日志库

**需求分析**

1. 支持往不同的地方输出日志
2. 日志分级别
   - debug
   - info
   - warning
   - Error
   - Fatal
3. 日志都要支持开关控制
4. 完整的日志要包含时间，行号，文件名，日志级别，日志信息
5. 日志文件要切割

```go
func main() {
	file, err := os.OpenFile("../txt/1.txt", os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0644)
	if err != nil {
		fmt.Printf("open file failed err:%v\n", err)
		return
	}
    // 把日志输出到文件 1.txt
	log.SetOutput(file)
	for {
		log.Println("666")
		time.Sleep(time.Second * 3)
	}
}
```

...

---

**runtime.Caller**

```go
pc, file, line, ok := runtime.Caller(0)
// pc：  调用函数的相关信息
// file：当前执行的文件的文件路径
// line：得到 runtime.Caller() 所在行数
// ok：  如果取到了值 ok 就是 true
```

````go
func f1() {
    pc, file, line, ok := runtime.Caller(1) // 1 表示它的外面还包着一个函数、并得到外面函数的相关信息、依次类推
}

func main() {
    f1()
}
````

---

## 反射

**反射介绍：**

反射是指程序运行期间对程序本身进行访问和修改的能力。

**reflect 包**

`reflect.TypeOf` 和 `reflect.ValueOf`

```go
func reflectType(x interface{}) {
	v := reflect.TypeOf(x)     // 得到类型
	fmt.Printf("type:%v\n", v)
}

func main() {
	var a float32 = 3.14
	reflectType(a)             // float
	var b int64 = 100
	reflectType(b)             // int64
}
```

`Type（类型）` 和 `Kind（种类）` 

kind 是底层的类型

```go
// Kind
type Kind uint
const {
    Invalid Kind = iota
    Bool
    Int
    Int8
    ...
    Array
    Chan
    Func
    Interface
    Map
    Ptr
    Slice
    String
    Struct
    UnsafePointer
}
```

**通过反射设置变量的值**

```go
func reflectSetVal(x interface{}) {
    v := reflect.ValueOf(x)
    // if v.Kind() == reflect.Int64 {
    //   v.SetInt(200)
    // }
    // 反射得到得值、使用 Elem() 方法进行值得修改
    if v.Elem().Kind() == reflect.Int64 {
        v.Elem().SetInt(200)
    }
}

func main() {
    var a int64 = 8
    reflectSetVal(&a) // 必须传指针
}
```

```go
func main() {
    var num float64 = 1.23
    // "接口类型变量" -> "反射类型对象"
    value := reflect.ValueOf(num)
    
    // "反射类型对象" -> "接口类型变量"
    converValue := value.Interface().(float64)
    fmt.Println(converValue)        //1.23
}
```



`isNil()` 常用于判断指针是否为空，`isValid()` 常用于判断返回值是否有效

---

**结构体反射**

```go
package main

import (
	"fmt"
	"reflect"
)

type student struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	stu1 := student{
		Name: "Tom",
		Age:  8,
	}
	t := reflect.TypeOf(stu1)
	fmt.Println(t.Name(), t.Kind())
	for i := 0; i < t.NumField(); i++ {
		field := t.Field(i)
		fmt.Println("name:%s index:%d type:%v json tag:%v\n", field.Name, field.Index, field.Type, field.Tag.Get("json"))
	}

	if scoreFiled, ok := t.FieldByName("Score"); ok {
		fmt.Println("name:%s index:%d type:%v json tag:%v\n", scoreFiled.Name, scoreFiled.Index, scoreFiled.Type, scoreFiled.Tag.Get("json"))
	}
}
```

---

## strconv 标准库

`int` 类型转换成 `string` 类型得方法

```go
ret := fmt.Sprintf("%d", i) // 这个可以
fmt.Printf("%#v", ret)
```

从字串中解析出 `int` 类型

```go
func main() {
	s := "10000"
    // 转换成 10 进制、返回 int64 位的值
	ret, err := strconv.ParseInt(s, 10, 0)
	if err != nil {
		fmt.Println("parseInt failed, err:", err)
	}
	fmt.Printf("%T", ret) // int64
}
```

```go
// 字符串返回整型
strconv.Atoi(s string) (i int) {}

// 把数字转换成字符串
strconv.Itoa(i int) (s string) {}
```

```go
// 从字符串解析出布尔值
boolV, err := strconv.ParseBool("true")

// 从字符串解析浮点数
floatV, err := strconv.Float("3.14")
```

---

## 并发编程

**并发和并行**

并发（Concurrency）：同一时间段内执行多个任务。

- 跑步和系鞋带

并行（Parallelism）：同一时刻执行多个任务。

- 跑步和听音乐

> 串行：一个执行完了执行另一个
>
> 并行：同意时刻执行多个任务
>
> 并发：一个任务还没执行完毕前执行另一个任务，这样切换执行

**进程（Process）、线程（Thread）、协程（Coroutine，也叫轻量级线程）**

- 进程：正在执行的程序
- 线程：轻量级进程
- 协程：用户态的轻量级线程，也叫做微线程

Go 语言的并发通过 `goroutine` 实现。`goroutine` 类似于线程，属于用户态的线程，我们可以根据需要创建成千上万个 

`goroutine` 并发工作。`goroutine` 是由 Go 语言的运行时（runtime）调度完成，而线程是由操作系统调度完成。

Go 语言还提供 `channel` 在多个 `goroutine` 间进行通信。`goroutine` 和 `channel` 是 Go 语言秉承的 

`CSP`（Communicating Sequential Process）并发模式的重要实现基础。

---

## Goroutine

把任务包装成函数并在函数前添加 `go` 关键字就可以为函数添加一个 `goroutine`

```go
// 程序启动后会创建一个主 goroutine 去执行
func main() {
    // go：单独开启一个 goroutine 执行该函数
	go hello() // goroutine 后面的函数是没有返回值的、有也会被舍弃
	fmt.Println("main")
}

func hello() {
	fmt.Println("hello")
}
// 因为 main 函数执行结束了所有 main 函数里创建的所有 goroutine 也结束了
// 所有输出 hello
```

```go
func main() {
	go hello()
	fmt.Println("main")
	time.Sleep(time.Second)
}

func hello() {
	fmt.Println("hello")
}
// main
// hello
```

**goroutine 什么时候结束？**

当 `goroutine` 对应的函数结束时 `goroutine` 结束

`main` 函数执行完时 `main` 函数创建的 `goroutine` 也都就结束

**协调多个 goroutine**

得到随机数

```go
func fn() {
    // 记得加随机数的种子
	rand.Seed(time.Now().UnixNano())
	for i := 0; i < 5; i++ {
		r1 := rand.Int()
		r2 := rand.Intn(10) // 0 <= x < 10
		fmt.Println(r1, r2)
	}
}

```

---

## sync.WaitGroup

实现所有的 `goroutine` 结束后主程序才结束

```go
var wg sync.WaitGroup

func main() {
	for i := 0; i < 10; i++ {
		wg.Add(1)
		go fn(i)
	}
	wg.Wait() // 等待 wg 的计数器减为 0
}

func fn(i int) {
	defer wg.Done()
	time.Sleep(time.Millisecond * time.Duration(rand.Intn(300)))
	fmt.Println(i)
}
```

```go
// wg.Add(-1) == wg.Done
// 所以 wg.Add(-2) 表示从计数器里减 2
```

虽然多个 `goroutine` 的执行顺序是不一样的，但是可以使用 `time.Sleep(time.Scound)` 等方法调整它们的顺序。

---

## Goroutine 什么时候结束？

通常我们在 main 中需要等待所有的 goroutine 都执行完毕时再退出
 一般有两种方法
 方法一大家都能想到用 channel 进行同步

```go
func main() {
    ch := make(chan int, 2)

    go func() {
        for i := 0; i < 10; i++ {
            time.Sleep(1 * time.Second)
            fmt.Println("go routine1", i)
        }
        ch <- 1
    }()
    go func() {
        for i := 0; i < 10; i++ {
            time.Sleep(1 * time.Second)
            fmt.Println("go routine2", i)
        }
        ch <- 2
    }()

    // 等待
    for i := 0; i < 2; i++ {
        <-ch
    }

    fmt.Println("main exist")
}
```

> 这种方法需要知道子 goroutine 的个数

方法二用 sync.WaitGroup，这也是官方推荐方法，不需要知道 gotoutine 的个数

```go
package main

import "fmt"
import "time"
import "sync"

func main() {
    var wg sync.WaitGroup

    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func(i int) {
            defer wg.Done()
            time.Sleep(1 * time.Second)
            fmt.Println(i)
        }(i)
    }

    wg.Wait() // 等待

    fmt.Println("main exist")
}
```

---

## Goroutin 调度模型 `GMP`

**`goroutine` 和 线程的关系：**

 `OS`线程（操作系统线程）一般都有固定的栈内存（通常为`2MB`）,一个`goroutine`的栈在其生命周期开始时只有很小的栈（典型情况下`2KB`），`goroutine`的栈不是固定的，他可以按需增大和缩小，`goroutine`的栈大小限制可以达到`1GB`，虽然极少会用到这个大。所以在Go语言中一次创建十万左右的`goroutine`也是可以的。 

**`goroutine` 的调度**

`GPM`是Go语言运行时（runtime）层面的实现，是go语言自己实现的一套调度系统。区别于操作系统调度OS线程。

- `G`很好理解，就是个goroutine的，里面除了存放本goroutine信息外 还有与所在P的绑定等信息。
- `P`管理着一组goroutine队列，P里面会存储当前goroutine运行的上下文环境（函数指针，堆栈地址及地址边界），P会对自己管理的goroutine队列做一些调度（比如把占用CPU时间较长的goroutine暂停、运行后续的goroutine等等）当自己的队列消费完了就去全局队列里取，如果全局队列里也消费完了会去其他P的队列里抢任务。
- `M（machine）`是Go运行时（runtime）对操作系统内核线程的虚拟， M与内核线程一般是一一映射的关系， 一个groutine最终是要放到M上执行的

P与M一般也是一一对应的。他们关系是： P管理着一组G挂载在M上运行。当一个G长久阻塞在一个M上时，runtime会新建一个M，阻塞G所在的P会把其他的G 挂载在新建的M上。当旧的G阻塞完成或者认为其已经死掉时 回收旧的M。

P的个数是通过`runtime.GOMAXPROCS`设定（最大256），Go1.5版本之后默认为物理线程数。 在并发量大的时候会增加一些P和M，但不会太多，切换太频繁的话得不偿失。

单从线程调度讲，Go语言相比起其他语言的优势在于OS线程是由OS内核来调度的，`goroutine`则是由Go运行时（runtime）自己的调度器调度的，这个调度器使用一个称为m:n调度的技术（复用/调度m个goroutine到n个OS线程）。 其一大特点是goroutine的调度是在用户态下完成的， 不涉及内核态与用户态之间的频繁切换，包括内存的分配与释放，都是在用户态维护着一块大的内存池， 不直接调用系统的malloc函数（除非内存池需要改变），成本比调度OS线程低很多。 另一方面充分利用了多核的硬件资源，近似的把若干goroutine均分在物理线程上， 再加上本身goroutine的超轻量，以上种种保证了go调度方面的性能。

**`GOMAXPROCS`**

Go运行时的调度器使用`GOMAXPROCS`参数来确定需要使用多少个OS线程来同时执行Go代码。默认值是机器上的CPU核心数。例如在一个8核心的机器上，调度器会把Go代码同时调度到8个OS线程上（GOMAXPROCS是m:n调度中的n）。

Go语言中可以通过`runtime.GOMAXPROCS()`函数设置当前程序并发时占用的CPU逻辑核心数。

Go1.5版本之前，默认使用的是单核心执行。Go1.5版本之后，默认使用全部的CPU逻辑核心数。

我们可以通过将任务分配到不同的CPU逻辑核心上实现并行的效果，这里举个例子： 

```go
func a() {
	for i := 1; i < 10; i++ {
		fmt.Println("A:", i)
	}
}

func b() {
	for i := 1; i < 10; i++ {
		fmt.Println("B:", i)
	}
}

func main() {
	runtime.GOMAXPROCS(1)
	go a()
	go b()
	time.Sleep(time.Second)
}
```

两个任务只有一个逻辑核心，此时是做完一个任务再做另一个任务。 将逻辑核心数设为2，此时两个任务并行执行，代码如下。

```go
func a() {
	for i := 1; i < 10; i++ {
		fmt.Println("A:", i)
	}
}

func b() {
	for i := 1; i < 10; i++ {
		fmt.Println("B:", i)
	}
}

func main() {
	runtime.GOMAXPROCS(2)
	go a()
	go b()
	time.Sleep(time.Second)
}
```

Go语言中的操作系统线程和goroutine的关系：

1. 一个操作系统线程对应用户态多个goroutine。
2. go程序可以同时使用多个操作系统线程。
3. goroutine和OS线程是多对多的关系，即m:n。

> m:n：把 m 个 `goroutine` 分配给 n 个操作系统线程
>
> `goroutine` 的初始大小是 2k

**线程模型**

- 内核级线程模型
  + 一对一关系、一个用户级线程映射到一个内核线程
- 用户级线程模型 
  + 多对一、多个用户级线程映射到一个内核线程
- 两级线程模型
  + 多对多、多个用户级线程映射到多个内核线程

---

## Channel

多个 `goroutine` 之间使用 `channel` 进行通信。

Go 语言中的通道（channel）是一种特殊的类型。通道像一个传送带或者队列，总是遵循先入先出（FIFO）的规则，保证收发数据的顺序。每个通道都是一个具体类型的导管，也就是声明 channel 的时候需要为其指定元素类型。

**声明通道类型**

```go
var 变量 chan 元素类型
var c chan int // 指定通道中元素的类型
```

channel 是引用类型，需要使用 make 函数初始化才能使用

```go
var c chan int
// 通道的初始化、无缓冲区的通道（进一个取一个）
c = make(chan int)

// 通道的初始化、带缓冲区的通道（可以先放 10 个进去、让后再取值）
c = make(chan int, 10)
```

**channel 操作**

==发送==

```go
// 发送 10 到通道
c <- 10
```

==接受==

```go
// 从通道接受一个值
// ok 为 true 表示从通道中获取的了值、为 false 表示通道已关闭从通道中获取到了类型的零值
v,ok := <-c

// 丢弃值、不接受值
<-c
```

==关闭==

```go
// 例：range 获取通道内的值时会产生死锁（获取没有对应的输入）这时需使用 close() 来关闭通道
close(c)
```

**无缓冲区的 channel**

> 阻塞：读和写是阻塞的，你不写就一直等着你写，你写完后才输出

```go
var wg sync.WaitGroup

func main() {
	c := make(chan int)
	wg.Add(1)
	go func() {
		defer wg.Done()
		v := <-c
		fmt.Println(v)
		close(c)
	}()
	c <- 10
	wg.Wait()
}
```

**有缓冲区的 channel**

```go
// 不会阻塞、队列（先进先出）
func main() {
    c := make(chan int, 10) // len:0、cap:10
	c <- 10
}
```

```go
// 确保某个操作只执行一次
var once sync.Once
once.Do(func() { close(c) })
```

---

## 单向通道

某个通道只发送或者只接收，多用于限制函数的参数

确保我暴漏出去的通道只做一个操作

```go
// 只能往里面发送值的通道
func fn(c chan<- int)

// 只能取值的通道
func fn(c <-chan int)
```

## 通道的异常情况总结

| channel | nil   | 非空                         | 空的               | 满了                         | 没满                         |
| ------- | ----- | ---------------------------- | ------------------ | ---------------------------- | ---------------------------- |
| 接收    | 阻塞  | 接收值                       | 阻塞               | 接收值                       | 接收值                       |
| 发送    | 阻塞  | 发送值                       | 发送值             | 阻塞                         | 发送值                       |
| 关闭    | panic | 关闭成功，读完数据后返回零值 | 关闭成功，返回零值 | 关闭成功，读完数据后返回零值 | 关闭成功，读完数据后返回零值 |

---

## worker pool（goroutine 池）

```go
package main

import (
	"fmt"
	"math/rand"
	"sync"
	"time"
)

var wg sync.WaitGroup

func main() {
	wg.Add(1)
	go f1(jobChan)
	wg.Add(24)
	for i := 0; i < 24; i++ {
		go f2(jobChan, resultChan)
	}
	for result := range resultChan {
		fmt.Printf("value:%d sum:%d\n", result.job.v, result.sum)
	}
	wg.Wait()
}

type job struct {
	v int64
}

type result struct {
	job *job
	sum int64
}

var jobChan = make(chan *job, 100)
var resultChan = make(chan *result, 100)

func f1(zl chan<- *job) {
	defer wg.Done()
	for {
		x := rand.Int63()
		newJob := &job{
			v: x,
		}
		zl <- newJob
		time.Sleep(time.Millisecond * 500)
	}
}

func f2(zl <-chan *job, resultChan chan<- *result) {
	defer wg.Done()
	for {
		job := <-zl
		sum := int64(0)
		n := job.v
		for n > 0 {
			sum += n % 10
			n = n / 10
		}
		newResult := &result{
			job: job,
			sum: sum,
		}
		resultChan <- newResult
	}
}
```

---

## select

随机的执行下面的某个（只执行某一个）通道操作，当 case 都阻塞时执行 default 

```go
select {
    case <-c1:
    ...
    case data := <-c2:
    ...
    case c3 <- data:
    ...
    default:
    默认操作
}
```

> type []int != type []interface{}
>
> 因为它们的内存模型
>
> Pointer 指针

> int 的内存模型：{1, 2, 3}
>
> interface 的内存模型：{{T, Pointer}, {T, Pointer}}

---

## 并发安全和锁

比如当两个 `goroutine` 操作一个全局变量时会产生问题，而这时我们可以给他一个锁，当一个 `goroutine` 操作完成后释放，另一个 `goroutine` 才启动。

**互斥锁**

可以保证同一时刻只有一个 `goroutine` 可以操作全局变量，多个 `goroutine` 等待一个锁时，唤醒的策略是随机的。

```go
func main() {
	wg.Add(2)
	go addition()
	go addition()
	wg.Wait()
	fmt.Println(x)
}

var (
	x int64
	wg 	sync.WaitGroup
	lock sync.Mutex    // 互斥锁、只有一个 goroutine 可以获取这个锁
)

func addition() {
	for i := 0; i < 5000; i++ {
		lock.Lock()    // 加锁
		x = x + 1
		lock.Unlock()  // 释放锁
	}
	wg.Done()
}
```

```go
var (
	ticket = 10
	wg     sync.WaitGroup
	mutex  sync.Mutex
)

func main() {
	wg.Add(4)
	go saleTickets("a")
	go saleTickets("b")
	go saleTickets("c")
	go saleTickets("d")
	wg.Wait()
}

func saleTickets(name string) {
	defer wg.Done()
	for {
        // 互斥锁
		mutex.Lock()
		if ticket > 0 {
			fmt.Println(name, "售出", ticket)
			ticket--
		} else {
			mutex.Unlock()
			fmt.Println("无票可买")
			break
		}
        // 解锁
		mutex.Unlock()
	}
}
```

**读写锁**

当读多写少时使用，读完再写。

基本遵循两大原则：

1. 可以随便读。多个 goroutine 同时读。
2. 写的时候，啥都不能干。不能读，也不能写。

```go
func main()  {
	start := time.Now()
	for i := 0; i < 1000; i++ {
		wg.Add(1)
		go read()
	}
	for i := 0; i < 10; i++ {
		wg.Add(1)
		go write()
	}
	wg.Wait()
	end := time.Now()
	fmt.Println(end.Sub(start))
}

var (
	x      int64
	wg     sync.WaitGroup
	lock   sync.Mutex
	rwlock sync.RWMutex
)

func read() {
	//lock.Lock()
	rwlock.RLock()
	time.Sleep(time.Millisecond)
	wg.Done()
	//lock.Unlock()
	rwlock.RUnlock()
}

func write()  {
	//lock.Lock()
	rwlock.Lock()
	x = x + 1
	time.Sleep(time.Millisecond * 10)
	//lock.Unlock()
	rwlock.Unlock()
	wg.Done()
}
```

---

**sync.WaitGroup**

```go
var wg sync.WaitGroup
wg.Add()   // 计数器 + delta
wg.Done()  // 计数器 - 1
wg.Wait()  // 阻塞到计数器变为 0
```

---

**sync.Once**

我们要确保在某些操作在高并发的场景下只执行一次，例如只加载一次配置文件，只关闭一次通道等。这时可以使用这个方法了。

`sync.Once` 只有一个 `Do` 方法，函数签名如下：

```go
func (o *Once) Do(f func()) {}
```

---

**sync.Map**

因为 Go 语言中的 map 不是并发安全的，所以有些场景下可以使用 `sync.Map`

可以开箱即用，不用 make 分配内存。并且读取等方法跟内置的 map 不一样。

-  `Store` ：存储键值对

-  `load` ：根据 key 取值

-  `loadOrStore`：先获取，没有的话保存值
-  `Delete`：删除
-  `Range`：遍历

```go
var SyncMap = sync.Map{}
Sync.Store(i, v)
v, _ := SyncMap.Load(i)
```

---

**原子操作**

atomic 包，只针对基本的数据类型，并发安全的

---

## Set

```go
package mapset

type MapSet map[interface{}]struct{}

// 数据结构经常会遇到的方法：增删查
// 集合特有的方法：交井差

func NewMapSet(elements ...interface{}) *MapSet {
	set := make(MapSet)
	for _, e := range elements {
		set.Add(e)
	}
	return &set
}

// add
func (set *MapSet) Add(e interface{}) bool {
	if _, found := (*set)[e]; found {
		return false
	}
	(*set)[e] = struct{}{}
	return true
}

// remove
func (set *MapSet) Remove(e interface{}) {
	delete(*set, e)
}

// contains
func (set *MapSet) Contains(e interface{}) bool {
	_, found := (*set)[e]
	return found
}

// 查看当前集合的长度
func (set *MapSet) Cardinatity() int {
	return len(*set)
}

// 交集
func (set *MapSet) Intersect(other *MapSet) *MapSet {
	newSet := NewMapSet()
	if set.Cardinatity() < other.Cardinatity() {
		for _, e := range *set {
			if _, found := (*other)[e]; found {
				newSet.Add(e)
			}
		}
	} else {
		for _, e := range *other {
			if _, found := (*set)[e]; found {
				newSet.Add(e)
			}
		}
	}
	return set
}

// 并集
func (set *MapSet) Union(other *MapSet) *MapSet {
	unionSet := new(MapSet)
	for _, e := range *set {
		unionSet.Add(e)
	}
	for _, e := range *other {
		unionSet.Add(e)
	}
	return unionSet
}

// 差集
func (set *MapSet) Difference(other *MapSet) *MapSet {
	// set - other
	differenceSet := NewMapSet()
	for _, e := range *set {
		if _, found := (*other)[e]; !found {
			differenceSet.Add(e)
		}
	}
	return differenceSet
}

```

>  Github 上有个现成的包 golang-set（主要代码逻辑位置：threadunsafe.go）

---

## socket

**互联网协议**

OSI 七层模型

- 物理层
- 数据链路层
- 网络层
- 传输层
- 会话层
- 表示层
- 应用层

**socket 编程**

TCP/IP 协议

TCP 服务端程序的处理流程：

1. 监听端口
2. 接收客户端请求建立链接
3. 创建 `goroutine` 处理链接

### TCP 通信

**server**

```go
func main() {
	// 1.开启服务
	listen, err := net.Listen("tcp", "127.0.0.1:20000")
	if err != nil {
		fmt.Printf("listen failed,err:%v\n", err)
		return
	}
	for {
		// 2.等待客户端来连接
		conn, err := listen.Accept()
		if err != nil {
			fmt.Printf("accept failed, err:%v\n", err)
			continue
		}
		// 3.启动单独的 goroutine 处理连接
		go process(conn)
	}
}

func process(conn net.Conn)  {
	// 关闭连接
	defer conn.Close()
	// 针对当前的连接作数据的发送和接受操作
	for {
		reader := bufio.NewReader(conn)
		var buf [128]byte
		n, err := reader.Read(buf[:])
		if err != nil {
			fmt.Printf("read from failed, err:%v\n", err)
			break
		}
		resv := string(buf[:n])
		fmt.Printf("接受到的数据:%v\n", resv)
		conn.Write([]byte("ok")) // 把收到的数据返回给客户端
	}
}
```

**client**

```go
func main() {
	// 1.与服务端建立连接
	conn, err := net.Dial("tcp", "127.0.0.1:20000")
	if err != nil {
		fmt.Printf("dial failed, err:%v\n", err)
		return
	}
	// 2.利用该连接进行数据的发送和接收
	input := bufio.NewReader(os.Stdin)
	for {
		s, _ := input.ReadString('\n')
		s = strings.TrimSpace(s)
		if strings.ToUpper(s) == "Q" {
			return
		}
		// 给服务端发消息
		_, err := conn.Write([]byte(s))
		if err != nil {
			fmt.Printf("send failed, err:%v\n", err)
			return
		}
		// 从服务端接收回复的消息
		var buf [1024]byte
		n, err := conn.Read(buf[:])
		if err != nil {
			fmt.Printf("read failed,err:%v\n", err)
			return
		}
		fmt.Println("收到服务端回复:", string(buf[:n]))
	}
}
```

> client：客户，Dial：拨号

**黏包示例**

为啥会出现黏包？不知道传输的数据边界（数据到那个字节结束）

解决方法：

比如可以给传输的数据前 4 节填写包的大小，这样接收方可以先读前 4 节，明白了数据占用了几个字节。让后动态的读它的全部字节。这样就知道了这个消息数据的边界。

---

### UDP 通信

与 TCP 协议的区别：

- 不是基于连接的协议，所以比 TCP 协议更快。用于直播，网络会议等
- 缺点：不可靠，不安全

> 网络通信都是使用 byte 进行通信

**server**

```go
func main() {
	listen, err := net.ListenUDP("udp", &net.UDPAddr{
		IP:   net.IPv4(127, 0, 0, 1),
		Port: 30000,
	})
	if err != nil {
		fmt.Printf("listen failed:%v\n", err)
	}
	defer listen.Close()
	for {
		var buf [1024]byte
		n, adder, err := listen.ReadFromUDP(buf[:])
		if err != nil {
			fmt.Printf("read from failed, err:%v\n", err)
			return
		}
		fmt.Println("接收到的数据:", string(buf[:n]))
		_, err = listen.WriteToUDP(buf[:n], adder)
		if err != nil {
			fmt.Printf("WriteTo from failed, err:%v\n", err)
		}
	} 
}
```

**client**

```go
func main() {
	c, err := net.DialUDP("udp", nil, &net.UDPAddr{
		IP:   net.IPv4(127, 0, 0, 1),
		Port: 30000,
	})
	if err != nil {
		fmt.Printf("dial from failed, err:%v\n", err)
		return
	}
	defer c.Close()
	// 发送数据
	input := bufio.NewReader(os.Stdin)
	for {
		s, _ := input.ReadString('\n')
		_, err = c.Write([]byte(s))
		if err != nil {
			fmt.Printf("send to server failed, err:%v\n", err)
			return
		}
		// 接收数据
		var buf [1024]byte
		n, addr, err := c.ReadFromUDP(buf[:])
		if err != nil {
			fmt.Printf("recv from udp failed, err:%v\n", err)
			return
		}
		fmt.Printf("from:%v data:%v", addr, string(buf[:n]))
	}
}
```

---

## 单元测试很重要

```shell
$ go test
```

使用 `go test` 命令测试文件，约定在包目录中以 `_test.go` 结尾的文件都是 `go test` 的一部分，不会被

`go build` 编译到最终的可执行文件中。

在测试文件 `*_test.go` 中有三种类型的函数：

1. 单元测试函数
2. 基准测试函数
3. 示例函数 

|   类型   |         格式          |              作用              |
| :------: | :-------------------: | :----------------------------: |
| 测试函数 |   函数名前缀为Test    | 测试程序的一些逻辑行为是否正确 |
| 基准函数 | 函数名前缀为Benchmark |         测试函数的性能         |
| 示例函数 |  函数名前缀为Example  |       为文档提供示例文档       |

`go test`命令会遍历所有的`*_test.go`文件中符合上述命名规则的函数，然后生成一个临时的main包用于调用相应的测试函数，然后构建并运行、报告测试结果，最后清理测试中生成的临时文件。

**测试函数**

```go
func TestName(t *testeing.T){
    // do something
}
```

其中参数`t`用于报告测试失败和附加的日志信息。 `testing.T`的拥有的方法如下：

```go
func (c *T) Error(args ...interface{})
func (c *T) Errorf(format string, args ...interface{})
func (c *T) Fail()
func (c *T) FailNow()
func (c *T) Failed() bool
func (c *T) Fatal(args ...interface{})
func (c *T) Fatalf(format string, args ...interface{})
func (c *T) Log(args ...interface{})
func (c *T) Logf(format string, args ...interface{})
func (c *T) Name() string
func (t *T) Parallel()
func (t *T) Run(name string, f func(t *T)) bool
func (c *T) Skip(args ...interface{})
func (c *T) SkipNow()
func (c *T) Skipf(format string, args ...interface{})
func (c *T) Skipped() bool
```

---

**示例**

```go
func TestSplit(t *testing.T) {
	got := Split("tom", "o")
	want := []string{"t", "m"}
	if !reflect.DeepEqual(got, want) {
		t.Errorf("want:%v got:%v", want, got)
	}
}
```

**测试组**

我们现在还想要测试一下`split`函数对中文字符串的支持，这个时候我们可以再编写一个`TestChineseSplit`测试函数，但是我们也可以使用如下更友好的一种方式来添加更多的测试用例。

```go
func TestSplit(t *testing.T) {
   // 定义一个测试用例类型
	type test struct {
		input string
		sep   string
		want  []string
	}
	// 定义一个存储测试用例的切片
	tests := []test{
		{input: "a:b:c", sep: ":", want: []string{"a", "b", "c"}},
		{input: "a:b:c", sep: ",", want: []string{"a:b:c"}},
		{input: "abcd", sep: "bc", want: []string{"a", "d"}},
		{input: "沙河有沙又有河", sep: "沙", want: []string{"河有", "又有河"}},
	}
	// 遍历切片，逐一执行测试用例
	for _, tc := range tests {
		got := Split(tc.input, tc.sep)
		if !reflect.DeepEqual(got, tc.want) {
			t.Errorf("excepted:%v, got:%v", tc.want, got)
		}
	}
}
```

> t.Run 
>
> go test -v 可以看详细的测试结果
>
> go test -run=chinese 指定某个测试再跑一下
>
> go test -cover 测试覆盖率， 测试覆盖率是你的代码被测试套件覆盖的百分比。通常我们使用的都是语句的覆盖率，也就是在测试中至少被运行一次的代码占总代码的比例。 
>
> go test -cover -coverprofile=c.out：在当前的文件夹下生成一个 c.out 文件，更好的可视化测试覆盖率
>
> go tool cover -html=c.out：把 c.out 文件按照 html 的格式打开

---

**性能基准测试**

调用你的函数 100k 次 1000k 看看性能

```go
func BenchmarkSplit(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Split("tom", "o")
    } 
}
```

> go test -bench=Split 运行性能基准测试
>
> 输出值：干活的操作系统的进程数，执行的次数，每次操作花费的时间
>
> go test -bench=Split -benchmem 查看内存相关的数据

```go
// 改良后的代码
func Split(s, sep string) (ret []string) {
    // 只在代码开头位置申请一次内存
	ret = make([]string, 0, strings.Count(s, sep)+1)
	idx := strings.Index(s, sep)
	for idx > -1 {
		ret = append(ret, s[:idx])
		s = s[idx+len(sep):]
		idx = strings.Index(s, sep)
	}
	ret = append(ret, s)
	return
}
```

---

## runtime

> 类似于 Java 语言中的虚拟机

负责内存分配，垃圾回收，栈的回收，Goroutine，channel，切片，反射等

```go
func main() {
	fmt.Println(runtime.GOROOT())        // go 的安装目录
	fmt.Println(runtime.GOOS)            // 现在的操作系统平台
    fmt.Println(runtime.NumCPU)          // 获取逻辑 CPU 数量
    runtime.GOMAXPROCS(runtime.NumCPU()) // 设置逻辑 CPU 的数量、返回值是上一次设置的数量
}

// 最好是程序运行前设置逻辑 CPU 的数量、1.8 版本后不需要这样设置了
func init() {
    runtime.GOMAXPROCS(runtime.NumCPU())
}
```

```go
// 当一个 Goroutime 堵塞时、go 会把在跟它一个线程上的 Goroutine 移植到另一个线程上
func main() {
    go func() {
        for i := 0; i < 5; i++ {
            fmt.Println("goroutine")
        }
    }
    for i := 0; i < 4; i++ {
        // 让出时间片让别的 goroutine 执行
        runtime.Gosched()
        fmt.Println("main")
    }
}
```

```go
func main() {
    go func() {
        fmt.Println("goroutine start")
        fn()
        fmt.Println("goroutine end") // 不会打印、已经 runtime.Goexit()
    }()
    time.Sleep(time.Second*3)
}
func fn() {
    defer fmt.Println("defer")
    runtime.Goexit()           // 终止当前的 goroutine、 defer 还是会执行
    fmt.Println("fn function")
}
```

---

## 临界资源安全问题

**临界资源**：多个 goroutine 可以访问的资源

当多个 goroutine 共享同一个内存会出问题，使用锁来解决这个问题

> 不要以共享内存的方式去通信，而要以通信的方式共享内存

