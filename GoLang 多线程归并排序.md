```go
//GoLang 多线程归并排序
package main

import (
	"fmt"
	"math/rand"
)

func Multi_MergeSort(a []int) {
	ch := make(chan int, 1)
	defer close(ch)
	Merge_Sort(a, 0, len(a)-1, ch)
	<-ch
}
func Merge_Sort(a []int, l, r int, c chan int) {
	if l < r {
		ch := make(chan int, 2)
		defer close(ch)
		mid := (l + r) / 2
		go Merge_Sort(a, l, mid, ch)
		go Merge_Sort(a, mid+1, r, ch)
		<-ch
		<-ch
		Merge(a, l, r, mid)
	}
	c <- 1
}
func Merge(a []int, l, r, mid int) {
	arr := make([]int, 0)
	i, j := l, mid+1
	for k := l; k <= r; k++ {
		if i > mid {
			arr = append(arr, a[j])
			j++
		} else if j > r {
			arr = append(arr, a[i])
			i++
		} else if a[i] > a[j] {
			arr = append(arr, a[j])
			j++
		} else {
			arr = append(arr, a[i])
			i++
		}
	}
	for i, v := range arr {
		a[l+i] = v
	}
}
func main() {
	list := make([]int, 0)
	for i := 0; i < 10; i++ {
		list = append(list, rand.Int()%100)
	}
	for _, v := range list {
		fmt.Print(v, " ")
	}
	fmt.Println("")
	Multi_MergeSort(list)
	for _, v := range list {
		fmt.Print(v, " ")
	}
}
```

