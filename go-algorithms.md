# Go 算法数据结构

## 数据结构算法的介绍

- 数据结构是一门研究算法的学科，有了编程语言也就有了数据结构，学好数据结构可以编写出更加漂亮，更加有效率的代码
- 程序 = 数据结构 + 算法

## 二分查找

```go
package main

import "fmt"

func main() {
	fmt.Println(BSearch([]int{1, 3, 5, 7}, 7))
}

func BSearch(slice []int, target int) int {
	start := 0
	end := len(slice) - 1
	for start <= end {
		guess := start + (end-start)/2
		if slice[guess] == target {
			return guess
		} else if slice[guess] < target {
			start = guess + 1
		} else if slice[guess] > target{
			end = guess - 1
		}
	}
	return -143
}
```

---

## 稀疏数组 Sparse Array

当一个数组中大部分元素为 0，或者为同一个值得数组时，可以使用稀疏数组来保存该数组

稀疏数组得处理方法是：

1. 记录数组一共1有几行几列，有多少个不同得值
2. 把具有不同值得元素得行列及值记录在一个小规模得数组中，从而缩小程序的规模

**代码实现**

把原始数组转换成稀疏数组

```go
func main() {
	var chessMap [11][11]int
	chessMap[1][2] = 1 // 黑子
	chessMap[2][3] = 2 // 蓝子

	for _, v1 := range chessMap {
		for _, v2 := range v1 {
			fmt.Printf("%d\t", v2)
		}
		fmt.Println()
	}
	// 转换成稀疏数组
	var sparseArr []ValNode

	// 标准的稀疏数组应该还有一个记录元素的二维数组的规模(行和列))和默认值
	valNode := ValNode{
		row: 11,
		col: 11,
		val: 0,
	}
	sparseArr = append(sparseArr, valNode)
	for i, v1 := range chessMap {
		for j, v2 := range v1 {
			if v2 != 0 {
				valNode := ValNode{
					row: i,
					col: j,
					val: v2,
				}
				sparseArr = append(sparseArr, valNode)
			}
		}
	}
	for i, valNode := range sparseArr {
		fmt.Printf("%d: %d %d %d\n", i, valNode.row, valNode.col, valNode.val)
	}
	// 0: 11 11 0
	// 1: 1 2 1
	// 2: 2 3 2
    
    // 将稀疏数组存盘
}

type ValNode struct {
	row int
	col int
	val interface{}
}
```

把转换的稀疏数组放到一个文件，想要恢复道原始数组按照文件的内容直接转换

```go
// 恢复原始数组
// 1. 打开文件所在的路径(例：d:/chessmap.data) => 恢复原始数组
// 2. 使用稀疏数组恢复  
var chessMap2 [11][11]int
for i, valNode := range sparseArr {
    if i != 0 {
        v := valNode.val.(int)
        chessMap2[valNode.row][valNode.col] = v
    }
}
```

---

## 队列 Queue

- 有序列表，可以用数组和链表实现

- 先进先出

### 数组模拟队列

实现思路

- 非环形，用完就完
- 需要 maxSize 最大容量

- 需要 front（随着输出改变） 和 rear（随着输入改变） 记录队列前后端的下标

> front：头针（不含值），rear：尾针（含值）

代码实现

```go
func main() {
	q := &Queue{
		maxSize: 4,
		front:   -1,
		rear:    -1,
	}
	if err := q.addQueue(5); err != nil {
		fmt.Println("error failed from", err)
	}
	q.showQueue()
}

// 使用结构体管理队列
type Queue struct {
	maxSize int
	array   [4]int // 数组 => 模拟队列
	front   int    // 指向队列首
	rear    int    // 指向队列尾
}

// 添加数据到队列
func (this *Queue) AddQueue(v int) (err error) {
	// 判断队列是否已满
	if this.rear == this.maxSize-1 {
		return errors.New("queue full")
	}
	this.rear++
	this.array[this.rear] = v
	return nil
}

// 取数据
func (this *Queue) GetQueue() (int, error) {
	if this.front == this.rear {
		return -1, errors.New("queue empty")
	}
	this.front++
	return this.array[this.front+1], nil
}

// 显示队列
func (this *Queue) ShowQueue() {
	for i := this.front + 1; i < this.rear+1; i++ {
		fmt.Printf("array[%d]=%d\n", i, this.array[i])
	}
}
```

> 小结：上面代码实现了队列结构，但是没有有效的利用数组的空间（随者取数据前端的空间会被浪费掉），为了解决这个问题我们可以使用链表实现队列
>
> `fmt.Scanln(&v)`：获取从终端输入的值，放到 v 变量

### 数组模拟环形队列

实现思路

- `(tail+ 1) % maxSize = head` ：表示队列已满
- 队列的容量空出一个作为约定
- 初始化：`tail= 0,  head = 0`
- 数组值的个数：`(tail + maxSize - head) % maxSize`

```go
type circleQueue struct {
	maxSize    int
	array      [4]int
	head, tail int
}

// 入队列
func (this *circleQueue) push(v int) (err error) {
	if this.IsFull() {
		return errors.New("queue full")
	}
	// tail 在队列尾部、不含值
	this.array[this.tail] = v
	this.tail = (this.tail + 1) % this.maxSize
	return
}

// 出队列
func (this *circleQueue) pop() (ret int, err error) {
	if this.IsEmpty() {
		return 0, errors.New("queue empty")
	}
	ret = this.array[this.head]
	this.head = (this.head + 1) % this.maxSize
	return
}

// 显示队列
func (this *circleQueue) ListQueue() {
	size := this.Size()
	if size == 0 {
		fmt.Println("队列为空")
	}
	tempHead := this.head
	for i := 0; i < size; i++ {
		fmt.Printf("arr[%d]=%d", tempHead, this.array[tempHead])
		tempHead = (tempHead + 1) % this.maxSize
	}
	fmt.Println()
}

// 判断环行队列是否为满
func (this *circleQueue) IsFull() bool {
	return (this.tail+1)%this.maxSize == this.head
}

// 判断环行队列是否为空
func (this *circleQueue) IsEmpty() bool {
	return this.tail == this.head
}

// 返回环形队列有多少个元素
func (this *circleQueue) Size() int {
	return (this.tail + this.maxSize - this.head) % this.maxSize
}
```

---

## 链表

有序的列表

### 单向链表

一般来说，为了比较好的对单链表进行增删改查的操作，我们会给他设置一个头节点。头节点的作用主要是用来标识链表头，本身这个节点不存放数据。

```go
func main() {
	head := &HeroNode{}
	hero1 := &HeroNode{
		no:       1,
		name:     "宋江",
		nickname: "及时雨",
	}
	hero2 := &HeroNode{
		no:       2,
		name:     "卢俊义",
		nickname: "玉麒麟",
	}
	InsertHeroNode(head, hero1)
	InsertHeroNode(head, hero2)
	ListHeroNode(head)
}

type HeroNode struct {
	no       int
	name     string
	nickname string
	next     *HeroNode // 指向下一个节点
}

// 1.给链表插入节点
// 在单链表的最后一位添加
func InsertHeroNode(head, newHeroNode *HeroNode) {
    // 使用 temp 当做临时变量、head 是不可以变换的
	temp := head
	for {
		if temp.next == nil { // 表示找到最后
			break
		}
		temp = temp.next // 让 temp 不断的指向下一个节点
	}
	// 将 newHeroNode 加入到链表的最后
	temp.next = newHeroNode
}

// 2.显示链表的所有节点信息
func ListHeroNode(head *HeroNode) {
	temp := head
	if temp.next == nil {
		fmt.Println("空链表")
		return
	}
	for {
		fmt.Printf("[%d , %s , %s ] => ", temp.next.no, temp.next.name, temp.next.nickname)
		temp = temp.next
		if temp.next == nil {
            // 跳出 for 循环
			break
		}
	}
}

// 3.按照英雄的 no 顺序从大到小依次加入
func InsertHeroNode1(head, newHeroNode *HeroNode) {
	temp := head
	flag := true
	// 让插入的节点的 no, 和 temp 的下一个节点的 no 比较
	for {
		if temp.next == nil {
			break
            // temp.next.no < newHeroNode.no 从大到小
		} else if temp.next.no > newHeroNode.no {
			break
		} else if temp.next.no == newHeroNode.no {
			flag = false
			break
		}
        temp = temp.next
	}
	if !flag {
		fmt.Println("对不起，已经存在此 no:", newHeroNode.no)
	} else {
        // 把大的放到后面、把自己放到以前大的位置
		newHeroNode.next = temp.next
		temp.next = newHeroNode
	}
}

// 4.删除指定的项
func DelHeroNode(head *HeroNode, id int) {
	temp := head
	flag := false
	// 找到要删除的节点 no, 和 temp 的下一个节点的 no 比较
	for {
		if temp.next == nil {
			break
		} else if temp.next.no == id {
			flag = true
			break
		}
		temp = temp.next
	}
	if flag {
		temp.next = temp.next.next
	} else {
		fmt.Println("要删除的 id 不存在")
	}
}
```

---

### 双向链表

1. 单向链表，查找的方向只能是一个方向，而双向链表可以向前或者向后查找
2. 单向链表不能自我删除，所有前面我们单链表删除时节点，总是找到 temp 的下一个节点来删除

```go
// 1.给双链表添加元素
func InsertHeroNode(head, newHeroNode *HeroNode) {
	temp := head
	for {
		if temp.next == nil { 
			break
		}
		temp = temp.next 
	}
	temp.next = newHeroNode
	newHeroNode.pre = temp
}
```

```go
// 2.逆序打出双向链表、让 temp 定位到最后一个节点
// 正向打印跟单链表一样
func ListHeroNode2(head *HeroNode) {
	temp := head
	if temp.next == nil {
		fmt.Println("空链表")
		return
	}
	for {
		if temp.next == nil {
			break
		}
		temp = temp.next
	}
	for {
		fmt.Printf("[%d , %s , %s ] => ", temp.no, temp.name, temp.nickname)
		temp = temp.pre
		if temp.pre == nil {
			break
		}
	}
}
```

```go
// 3.按照英雄的 no 顺序从大到小依次加入
func InsertHeroNode1(head, newHeroNode *HeroNode) {
	temp := head
	flag := true
	// 让插入的节点的 no, 和 temp 的下一个节点的 no 比较
	for {
		if temp.next == nil {
			break
			// temp.next.no < newHeroNode.no 从大到小
		} else if temp.next.no > newHeroNode.no {
			break
		} else if temp.next.no == newHeroNode.no {
			flag = false
			break
		}
		temp = temp.next
	}
	if !flag {
		fmt.Println("对不起，已经存在此 no:", newHeroNode.no)
		return
	} else {
		newHeroNode.next = temp.next
		newHeroNode.pre = temp
		if temp != nil {
			temp.next.pre = newHeroNode
		}
		temp.next = newHeroNode
	}
}
```

```go
// 4.双向链表的删除
func DelHeroNode(head *HeroNode, id int) {
	temp := head
	flag := false
	for {
		if temp.next == nil {
			break
		} else if temp.next.no == id {
			flag = true
			break
		}
		temp = temp.next
	}
	if flag {
		temp.next = temp.next.next
		if temp.next != nil {
			temp.next.pre = temp
		}
	} else {
		fmt.Println("要删除的 id 不存在")
	}
}
```

---

### 环形链表

单向环形链表

```go
package main

import "fmt"

func main() {
	head := &CircleSingleLinkedList{}
	l1 := &CircleSingleLinkedList{no: 1, name: "tom"}
	InsertItem(head, l1)
	ListAllItem(head)
    head = DelItem(head, 1)
}

type CircleSingleLinkedList struct {
	no   int
	name string
	next *CircleSingleLinkedList
}

// 1.尾部添加
func InsertItem(head, newItem *CircleSingleLinkedList) {
	if head.next == nil {
		head.no = newItem.no
		head.name = newItem.name
		head.next = head
		return
	}
	temp := head
	for {
		if temp.next == head {
			break
		}
		temp = temp.next
	}
	temp.next = newItem
	newItem.next = head
}

// 2.展示链表
func ListAllItem(head *CircleSingleLinkedList) {
	temp := head
	if temp.next == nil {
		fmt.Println("空")
		return
	}
	for {
		fmt.Println(temp.no, temp.name)
		if temp.next == head {
			break
		}
		temp = temp.next
	}
}

// 删除
func DelItem(head *CircleSingleLinkedList, id int) *CircleSingleLinkedList {
	temp := head
	helper := head
	// 空链表
	if temp.next == nil {
		fmt.Println("空链表，不能删除")
		return head
	}
	// 只有一个节点
	if temp.next == head {
		temp.next = nil
		return head
	}
	for {
		if helper.next == head {
			break
		}
		helper = helper.next
	}
	// 有一个以上节点
	flag := true
	for {
		if temp.next == head {
			break
		}
		if temp.no == id {
			// 说明删除的是头节点
			if temp == head {
				head = head.next
			}
			helper.next = temp.next
			fmt.Printf("ID 为：%d 的节点被干掉\n", id)
			flag = false
			break
		}
		temp = temp.next     // 比较
		helper = helper.next // 用来删除
	}
	if flag { // 如果 flag 为 true 没有进行过删除操作
		if temp.no == id {
			helper.next = temp.next
			fmt.Printf("ID 为：%d 的节点被干掉\n", id)
        } else {
            fmt.Printf("没有 ID 等于: %d 的节点\n", id)
        }
	}
	return head
}
```

---

### 约瑟夫问题

使用环形单向链表实现：

```go
package main

import "fmt"

type Boy struct {
	No   int
	Next *Boy
}

func main() {
	first := InitBoy(5)
	ShowBoy(first)
}

// 1.创建环形链表
func InitBoy(num int) *Boy {
	first := &Boy{}
	curBoy := &Boy{}
	if num < 1 {
		fmt.Println("num 的值不对")
		return first
	}
	for i := 1; i <= num; i++ {
		boy := &Boy{
			No: i,
		}
		if i == 1 { // 第一个小孩
			first = boy
			curBoy = boy
			curBoy.Next = boy // 环形
		} else {
			curBoy.Next = boy
			curBoy = boy
			curBoy.Next = first
		}
	}
	return first
}

// 2.显示环形链表[遍历]
func ShowBoy(first *Boy) {
	// 辅助指针
	if first.Next == nil {
		fmt.Println("空链表")
		return
	}
	curBoy := first
	for {
		fmt.Printf("小孩编号=%d ->\n", curBoy.No)
		if curBoy.Next == first {
			break
		}
        curBoy = curBoy.Next
	}
}

// 3.总体逻辑
func PlayGame(first *Boy, startNo int, countNum int) {
	if first.Next == nil {
		fmt.Println("空链表")
		return
	}
	tail := first
	// 让 tail 走到环形链表的最后一位、删除时用到
	for {
		if tail.Next == first { // 最后一项
			break
		}
		tail = tail.Next
	}
	for i := 1; i <= startNo-1; i++ {
		first = first.Next
		tail = tail.Next
	}
	for {
		// 走到要删除的项
		for i := 1; i <= countNum-1; i++ {
			first = first.Next
			tail = tail.Next
		}
		fmt.Printf("小孩编号为 %d 出圈\n", first.No)
		// 出列逻辑
		first = first.Next
		tail.Next = first
		// tail == first 只有一个小孩
		if tail == first {
			break
		}
	}
	fmt.Printf("最后出圈的小孩为 %d 出圈", first.No)
}
```

---

## 排序算法

- 冒泡排序（最慢）
- 选择排序
- 插入排序
- 快速排序（最快）

### 选择排序

选择排序属于内部排序法，是从预排序的数据中，按指定的规则选出某一元素，经过和其他元素重整，再依原则交换位置后达到排序的目的。

```go
func SelectionSort(arr *[5]int) {
	for i := 0; i < len(*arr)-1; i++ {
		// 假设 (*arr)[0] 就是最大值
		max := (*arr)[i]
		maxIndex := i

		// 遍历 1 -> len(arr)-1 的项进行比较
		for j := i + 1; j < len(*arr); j++ {
			if max < (*arr)[j] {
				max = (*arr)[j]
				maxIndex = j
			}
		}
		if maxIndex != i {
			(*arr)[i], (*arr)[maxIndex] = (*arr)[maxIndex], (*arr)[i]
		}
		fmt.Printf("第%d次: %v\n", i+1, *arr)
	}
}
```

---

### 插入排序

```go
func insertSort(arr *[5]int) {
	for i := 1; i < len(arr); i++ {
		insertVal := arr[i]
		insertIndex := i - 1
        // 从大到小
		for insertIndex >= 0 && arr[insertIndex] < insertVal {
			arr[insertIndex+1] = arr[insertIndex]
			insertIndex--
		}
        // 插入
		if insertIndex+1 != i {
			arr[insertIndex+1] = insertVal
		}
		fmt.Println(arr)
	}
}
```

---

### 快速排序

```go
package main

import "fmt"

func main() {
	arr := [6]int{-9, 78, 0, 23, -567, 70}
	QuickSort(0, len(arr)-1, &arr)
	fmt.Println(arr)
}

func QuickSort(left int, right int, arr *[6]int) {
	l := left
	r := right
	pivot := arr[(left+right)/2]
	// 把 pivot 小的数放左边，大的数放右边
	for l < r {
		for arr[l] < pivot {
			l++
		}
		for arr[r] > pivot {
			r--
		}
		if l >= r {
			break
		}
		arr[l], arr[r] = arr[r], arr[l]

		if arr[l] == pivot {
			r--
		}
		if arr[r] == pivot {
			l++
		}
	}
	if l == r {
		l++
		r--
	}
	if left < r {
		QuickSort(left, r, arr)
	}
	if right > l {
		QuickSort(l, right, arr)
	}
}
```



---

### 排序的速度比较

1. 快速排序
2. 插入排序
3. 选择排序

---

## 栈（Stack）

先入后出

**应用场景**

1. 子程序的调用：在跳往子程序前，会先将下个指令的地址存到堆栈中，直到子程序执行完毕后再将地址取出，以回到原来的程序中
2. 处理递归调用：和子程序的调用类似，只是除了存储下一个指令的地址外，也将参数、区域变量等数据存入堆栈中
3. 表达式的转换与求值
4. 二叉树的遍历
5. 图形的深度优先（depth-first）搜索法

6. ...

**案例**

```go
package main

import (
	"errors"
	"fmt"
)

// 栈
type Stack struct {
	MaxTop int    // 表示我们栈最大可以存放数个数
	Top    int    // 随着值得加入变换
	array  [5]int // 数组模拟栈
}

func main() {
	stack := &Stack{
		MaxTop: 5,
		Top:    -1, // -1 表示栈为空
	}
	stack.Push(1)
	stack.Push(2)
	stack.Push(3)
	stack.Push(4)
	stack.Push(5)

	stack.List()
    // arr[4]=5
    // arr[3]=4
    // arr[2]=3
    // arr[1]=2
    // arr[0]=1
    
    v, _ := stack.Pop()
    fmt.Println(v) // 5
}

// 1.入栈
func (this *Stack) Push(val int) (err error) {
	if this.Top == this.MaxTop-1 {
		fmt.Println("stack full")
		return errors.New("stack full")
	}
	this.Top++
	this.array[this.Top] = val
	return
}

// 2.出栈
func (this *Stack) Pop() (val int, err error) {
	if this.Top == -1 {
		fmt.Println("stack empty")
		return -1, errors.New("stack empty")
	}
	val = this.array[this.Top]
	this.Top--
	return val, nil
}

// 3.遍历
func (this *Stack) List() {
	if this.Top == -1 {
		fmt.Println("stack empty")
		return
	}
	for i := this.Top; i >= 0; i-- {
		fmt.Printf("arr[%d]=%d\n", i, this.array[i])
	}
}
```

**实现计算器**

1. 创建两个栈，numStack，operStack
2. numStack 存放数，operStack 操作符
3. `index := 0`
4. exp 计算表达式，是一个字符串
5. 如果扫描发现是一个数字，则直接 numStack
6. 如果发现是一个运算符
   - 如果 operStack 是一个空栈，直接入栈
   - operStack  不是空栈
     + 如果发现 operStack 栈顶得运算符得优先级大于等于当前准备入栈得运算符得优先级，就从符号栈取出 pop 出，并从数栈也 pop 出两个数，进行运算，运算后得结果再重新入栈到数栈
     + 否则运算符直接入栈
   - 如果扫描表达式完毕，依次从符号栈取出符号，让后从数栈取出两个数进行运算，运算后得结果，入数栈，直到符号栈为空

```go
package main

import (
	"errors"
	"fmt"
	"strconv"
)

type Stack struct {
	MaxTop int     // 表示我们栈最大可以存放数个数
	Top    int     // 随着值得加入变换
	array  [20]int // 数组模拟栈
}

func main() {
	// 数栈
	numStack := &Stack{
		MaxTop: 20,
		Top:    -1,
	}
	// 符号栈
	operStack := &Stack{
		MaxTop: 20,
		Top:    -1,
	}
	// 表达式
	exp := "30+3*6-4"
	// 定义一个 index，帮助扫描 exp
	index := 0
	num1, num2, oper, result, keepNum := 0, 0, 0, 0, ""
	for {
		ch := exp[index : index+1]  // 字符串
		temp := int([]byte(ch)[0])  // 字符对应的 ASCiI 值
		if operStack.IsOper(temp) { // 说明是符号
			if operStack.Top == -1 {
				operStack.Push(temp)
			} else {
				if operStack.Priority(operStack.array[operStack.Top]) 
                >= operStack.Priority(temp) {
					num1, _ = numStack.Pop()
					num2, _ = numStack.Pop()
					oper, _ = operStack.Pop()
					result = operStack.Cal(num1, num2, oper)
					numStack.Push(result)
					operStack.Push(temp)
				} else {
					operStack.Push(temp)
				}
			}
		} else { // 说明是数
			keepNum += ch
			if index == len(exp)-1 {
				val, _ := strconv.ParseInt(keepNum, 10, 64)
				numStack.Push(int(val))
			} else {
				if operStack.IsOper(int([]byte(exp[index+1 : index+2])[0])) {
					val, _ := strconv.ParseInt(keepNum, 10, 64)
					numStack.Push(int(val))
					keepNum = ""
				}
			}

		}
		// 继续扫描
		if index+1 == len(exp) {
			break
		}
		index++
	}
	for {
		if operStack.Top == -1 {
			break
		}
		num1, _ = numStack.Pop()
		num2, _ = numStack.Pop()
		oper, _ = operStack.Pop()
		result = operStack.Cal(num1, num2, oper)
		numStack.Push(result)
	}
	// 算法没有问题，表达式也是正确的
	res, _ := numStack.Pop()
	fmt.Println(res)
}

// 1.入栈
func (this *Stack) Push(val int) (err error) {
	if this.Top == this.MaxTop-1 {
		fmt.Println("stack full")
		return errors.New("stack full")
	}
	this.Top++
	this.array[this.Top] = val
	return
}

// 2.出栈
func (this *Stack) Pop() (val int, err error) {
	if this.Top == -1 {
		fmt.Println("stack empty")
		return -1, errors.New("stack empty")
	}
	val = this.array[this.Top]
	this.Top--
	return val, nil
}

// 3.遍历
func (this *Stack) List() {
	if this.Top == -1 {
		fmt.Println("stack full")
		return
	}
	for i := this.Top; i >= 0; i-- {
		fmt.Printf("arr[%d]=%d\n", i, this.array[i])
	}
}

// 判断一个字符是不是一个运算符[+, -, *, /]
func (this *Stack) IsOper(val int) bool {
	if val == 42 || val == 43 || val == 45 || val == 47 {
		return true
	}
	return false
}

// 运算的方法
func (this *Stack) Cal(num1 int, num2 int, oper int) int {
	res := 0
	switch oper {
	case 42:
		res = num2 * num1
	case 43:
		res = num2 + num1
	case 45:
		res = num2 - num1
	case 47:
		res = num2 / num1
	default:
		fmt.Println("运算符错误")
	}
	return res
}

// 返回运算符的优先级
func (this *Stack) Priority(oper int) int {
	res := 0
	if oper == 42 || oper == 47 {
		res = 1
	} else if oper == 43 || oper == 45 {
		res = 0
	}
	return res
}
```

---

## 递归机制

方法或者函数自己调用自己

**可以解决的问题：**

- 8 皇后问题
- 汉诺塔问题
- 阶乘问题
- 迷宫问题
- 球和篮子问题（google 编程大赛）
- 用递归简化栈的代码

**递归时需要遵循的四个规则：**

1. 执行一个函数时，就创建一个新的受保护的独立空间（新函数栈）
2. 函数的局部变量是独立的，不会相互影响
3. 递归必须向退出递归的条件逼近，否则就是无限递归，死龟了:)
4. 当一个函数执行完毕，或者遇到 return，就会返回，遵守谁调用，就将结果返回给谁，同时当函数执行或者返回时，该函数本身也会被系统销毁

**迷宫问题：**

  规则：

1. 如果元素的值为 0，是没有走过的点
2. 如果元素的值为 1，就是墙
3. 如果元素的值为 2，是通路
4. 如果元素的值为 3，表示曾经走过是死路

```go
package main

import "fmt"

func main() {
	// 二维数组模拟迷宫
	var myMap [8][7]int
	// 地图的最上最下设置为1
	for i := 0; i < 7; i++ {
		myMap[0][i] = 1
		myMap[7][i] = 1
	}
	// 地图最左最右设置为1
	for i := 0; i < 8; i++ {
		myMap[i][0] = 1
		myMap[i][6] = 1
	}
	myMap[3][1] = 1
	myMap[3][2] = 1
	for i := 0; i < 8; i++ {
		for j := 0; j < 7; j++ {
			fmt.Print(myMap[i][j], " ")
		}
		fmt.Println()
	}
	// 测试
	SetWay(&myMap, 1, 1)
	fmt.Println("探测完毕的地图")
	for i := 0; i < 8; i++ {
		for j := 0; j < 7; j++ {
			fmt.Print(myMap[i][j], " ")
		}
		fmt.Println()
	}
}

// i, j 对地图的某个点进行测试
func SetWay(myMap *[8][7]int, i, j int) bool {
	if myMap[6][5] == 2 {
		return true
	}
	// 需要继续找
	if myMap[i][j] == 0 {
		// 假设可以通
		myMap[i][j] = 2
		if SetWay(myMap, i+1, j) { // 下
			return true
		} else if SetWay(myMap, i, j+1) { // 右
			return true
		} else if SetWay(myMap, i-1, j) { // 上
			return true
		} else if SetWay(myMap, i, j-1) { // 左
			return true
		} else { // 死路
			myMap[i][j] = 3
			return false
		}
	} else { // 说明是墙
		return false
	}
}
```

---

## 哈希表（散列）Hash Table

使用 hash table（不带表头的链表）来实现一个雇员的管理系统，实现增加、查询（显示所有员工，按 ID 查询）

