SkipList

```go
// skip
package main

import (
	"fmt"
	"math/rand"
	"time"
)

const MaxLevel int = 32

type SkipListNode struct {
	data int
	next []*SkipListNode
}

type SkipList struct {
	head   *SkipListNode
	tail   *SkipListNode
	length int
	level  int
	rand   *rand.Rand
}

func main() {
	sl := NewSkipList(6)
	for i := 0; i < 100; i++ {
		sl.Insert(i)
	}
	sl.PrintSkipList()
	fmt.Println(sl.Search(2))
	sl.Remove(2)
	fmt.Println(sl.Search(2))
	sl.PrintSkipList()
}

func (list *SkipList) randomlevel() int {
	level := 1
	for level < list.level && list.rand.Uint32()&0x1 == 1 {
		level++
	}
	return level
}
func NewSkipList(level int) *SkipList {
	list := &SkipList{}
	list.level = level
	list.head = &SkipListNode{next: make([]*SkipListNode, level)}
	list.tail = &SkipListNode{}
	list.rand = rand.New(rand.NewSource(time.Now().UnixNano()))
	for i := range list.head.next {
		list.head.next[i] = list.tail
	}
	return list
}

func (list *SkipList) Search(key int) bool {
	node1 := list.head
	for i := len(node1.next) - 1; i >= 0; i-- {
		for {
			node2 := node1.next[i]
			if node2 == list.tail || node2.data > key {
				break
			} else if node2.data == key {
				return true
			} else {
				node1 = node2
			}
		}
	}
	return false
}
func (list *SkipList) Insert(key int) {
	level := list.randomlevel()
	update := make([]*SkipListNode, level, level)
	node1 := list.head
	for i := level - 1; i >= 0; i-- {
		for {
			node2 := node1.next[i]
			if node2 == list.tail || node2.data > key {
				update[i] = node1
				break
			} else if node2.data == key {
				return
			} else {
				node1 = node2
			}
		}
	}
	NewNode := &SkipListNode{key, make([]*SkipListNode, level, level)}
	for i, node := range update {
		node.next[i], NewNode.next[i] = NewNode, node.next[i]
	}
	list.length++
}
func (list *SkipList) Remove(key int) bool {
	node1 := list.head
	update := make([]*SkipListNode, list.level, list.level)
	var target *SkipListNode
	for i := len(node1.next) - 1; i >= 0; i-- {
		for {
			node2 := node1.next[i]
			if node2 == list.tail || node2.data > key {
				break
			} else if node2.data == key {
				update[i] = node1
				target = node2
				break
			} else {
				node1 = node2
			}
		}
	}
	if target != nil {
		for i, node1 := range update {
			if node1 != nil {
				node1.next[i] = target.next[i]
			}
		}
		list.length--
		return true
	}
	return false
}
func (list *SkipList) PrintSkipList() {
	node1 := list.head
	for i := list.level - 1; i >= 0; i-- {
		fmt.Printf("Level %d:\n", i)
		node2 := node1.next[i]
		for {
			if node2 == list.tail {
				break
			} else {
				fmt.Printf("%d ", node2.data)
				node2 = node2.next[i]
			}
		}
		fmt.Println()
	}
}
```

