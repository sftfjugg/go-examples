# 概述

阅读本小节之前，建议先阅读 [数组](array.md) 小节。

**`切片` 是对数组的一个连续片段的引用**。片段可以是整个数组，也可以是数组的一部分 (例如数组的第 3 个元素到第 8 个元素)。
所以 `切片` 是一个引用类型，改变切片的值也就改变了底层数组的值。

一个数组可以被多个 `切片` 引用，它们之间共享数组的数据。**当数组或者任一 `切片` 数据改变时，会影响到其他切片的数据**。
切片的优点在于节省了存储数据的空间 (当然切片本身还是需要占用存储空间的)，只需引用数据即可，所以使用频率非常高。

# 长度和容量

**切片有两个属性，分别是 `长度` 和 `容量`。**

长度表示切片当前有多少个元素，容量表示切片最多可以有多少容量，两者的关系是: `长度 <= 容量` 。

# 语法规则

## 声明

```shell
var 变量名称 []数据类型

# 例子
var sli []int
```

可以看到，和数组的语法规则几乎一致，唯一的差异在于切片不需要指明长度。

## 声明及初始化

```shell
var 变量名 = make([]int, 长度, 容量)   // 容量参数可以省略

# 例子
var sli = make([]int, 5, 10)
```

# 获取值/改变值

```go
package main

import "fmt"

func main() {
	var s []int // 声明一个整型切片

	fmt.Printf("切片长度 = %d, 容量 = %d\n", len(s), cap(s))

	var s2 = make([]int, 3, 10) // 声明并初始化一个整型切片

	fmt.Printf("切片长度 = %d, 容量 = %d\n", len(s2), cap(s2))

	s2[0] = 100 // 为切片第 1 个元素赋值
	s2[1] = 200 // 为切片第 2 个元素赋值
	s2[2] = 300 // 为切片第 3 个元素赋值

	println(s2[0]) // 输出切片第 1 个元素
	println(s2[1]) // 输出切片第 2 个元素
	println(s2[2]) // 输出切片第 3 个元素
}

// $ go run main.go
// 输出如下 
/**
  切片长度 = 0, 容量 = 0
  切片长度 = 3, 容量 = 10
  100
  200
  300
*/
```

# 多个切片引用一个数组

```go
package main

import "fmt"

func main() {
	arr := [...]int{1, 2, 3, 4, 5}
	s := arr[0:5]  // 切片 1
	s2 := arr[0:3] // 切片 2

	fmt.Println("修改前")
	fmt.Println(arr) // 输出数组所有元素
	fmt.Println(s)   // 输出切片 1 所有元素
	fmt.Println(s2)  // 输出切片 2 所有元素

	s[0] = 100  // 修改切片 1 第一个元素
	s2[1] = 200 // 修改切片 2 第二个元素

	fmt.Println("修改后")
	fmt.Println(arr) // 输出数组所有元素
	fmt.Println(s)   // 输出切片 1 所有元素
	fmt.Println(s2)  // 输出切片 2 所有元素
}

// $ go run main.go
// 输出如下 
/**
  修改前
  [1 2 3 4 5]
  [1 2 3 4 5]
  [1 2 3]
  修改后
  [100 200 3 4 5]
  [100 200 3 4 5]
  [100 200 3]
*/
```

# 追加值

调用 `append` 函数

```go
package main

import "fmt"

func main() {
	var s []int // 声明一个整型切片

	fmt.Printf("切片长度 = %d, 容量 = %d\n", len(s), cap(s))

	s = append(s, 100)
	s = append(s, 200)

	fmt.Printf("切片长度 = %d, 容量 = %d\n", len(s), cap(s))
}

// $ go run main.go
// 输出如下 
/**
  切片长度 = 0, 容量 = 0
  切片长度 = 2, 容量 = 2
*/
```

# 区间数据

通过 `索引` 位置，可以灵活地获取切片的区间数据。

```go
package main

import "fmt"

func main() {
	var s = make([]int, 3, 10)

	s[0] = 100 // 为切片第 1 个元素赋值
	s[1] = 200 // 为切片第 2 个元素赋值
	s[2] = 300 // 为切片第 3 个元素赋值

	fmt.Printf("%v\n", s[1:3])      // 输出切片的第 2 个和第 3 个元素
	fmt.Printf("%v\n", s[2:5])      // 输出切片的第 3 个到第 5 个元素
	fmt.Printf("%v\n", s[:])        // 输出切片的所有元素, 以 len() 为准
	fmt.Printf("%v\n", s[0:cap(s)]) // 输出切片的所有元素, 以 cap() 为准
}

// $ go run main.go
// 输出如下 
/**
  [200 300]
  [300 0 0]
  [100 200 300]
  [100 200 300 0 0 0 0 0 0 0]
*/
```

上述只列出了简单的用法，读者可以调整 `[]` 中的索引位置，体验更多的用法。

# 遍历切片

## 普通循环

```go
package main

import "fmt"

func main() {
	var s = make([]int, 3, 10)

	s[0] = 100 // 为切片第 1 个元素赋值
	s[1] = 200 // 为切片第 2 个元素赋值
	s[2] = 300 // 为切片第 3 个元素赋值

	for i := 0; i < len(s); i++ {
		fmt.Printf("index = %d, val = %d\n", i, s[i])
	}
}

// $ go run main.go
// 输出如下 
/**
  index = 0, val = 100
  index = 1, val = 200
  index = 2, val = 300
*/
```

## range 循环

```go
package main

import "fmt"

func main() {
	var s = make([]int, 3, 10)

	s[0] = 100 // 为切片第 1 个元素赋值
	s[1] = 200 // 为切片第 2 个元素赋值
	s[2] = 300 // 为切片第 3 个元素赋值

	for i, v := range s {
		fmt.Printf("index = %d, val = %d\n", i, v)
	}
}

// $ go run main.go
// 输出如下 
/**
  index = 0, val = 100
  index = 1, val = 200
  index = 2, val = 300
*/
```

# 小结

- 切片是对数组的一个连续片段的引用
- 一个数组可以被多个切片引用，它们之间共享数组的数据，当数组或者任一切片数据改变时，会影响到其他切片的数据