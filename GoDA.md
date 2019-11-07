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

