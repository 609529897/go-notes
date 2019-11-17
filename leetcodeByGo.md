# leetcode by go

## Two Sum

```go
package main

import "fmt"

func main() {
	input := []int{2, 7, 8, 4, 9}
	target := 9
	fmt.Println(twoSum(input, target))
}

func twoSum(nums []int, target int) []int {
	dict := make(map[int]int)
	for i := 0; i < len(nums); i++ {
		if v, ok := dict[nums[i]]; ok {
			res := []int{v, i}
			return res
		}
		dict[target-nums[i]] = i
	}
	return nil
}

```

