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
		} else {
			end = guess - 1
		}
	}
	return -1
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

**数组模拟队列**

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
	return
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

**数组模拟环形队列**

实现思路

- (tail+ 1) % maxSize = 0 ：表示队列已满
- 队列的容量空出一个作为约定
- 初始化：tail= 0,  head = 0
- 数组值的个数：(tail + maxSize - head) % maxSize

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
    // 使用 temp 当在临时变量、head 是不可以变换的
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
		return
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
		// 把大的放到后面、把自己放到以前大的位置
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
		if temp.next != nil {
			temp.next.pre = temp
		}
	} else {
		fmt.Println("要删除的 id 不存在")
	}
}
```

