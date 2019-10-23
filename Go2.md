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

