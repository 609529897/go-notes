# Go 函数式编程和数据并行

## Map

```go
func main() {
	fmt.Println(Map([]int{1, 2, 3}, func(i int) string {
		return strconv.FormatInt(int64(i), 2)
	}))
}

// Map
func Map(ints []int, lambda func(int) string) []string {
	ret := make([]string, len(ints))
	for i := range ret {
		ret[i] = lambda(ints[i])
	}
	return ret
}
```

---

## Reduce / Fold

## Filter

## Unfold

