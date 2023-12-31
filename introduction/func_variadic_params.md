# 概述

在函数的最后一个参数的数据类型之前加上省略号 `...` ，表示该参数的数据类型是 `变长类型`，
调用该函数时可以传递任意数量 `( 0 - N )` 的该类型的参数。

**一个函数只能有一个变长参数，且变长参数必须为最后一个参数**。

# 例子

## 传递一个参数

```go
package main

import "fmt"

func sum(numbers ...int) int {
	total := 0
	for _, num := range numbers {
		total += num
	}
	return total
}

func main() {
	fmt.Printf("1 = %d\n", sum(1))
}

// $ go run main.go
// 输出如下 
/**
  1 = 1
*/
```

## 传递多个参数

```go
package main

import "fmt"

func sum(numbers ...int) int {
	total := 0
	for _, num := range numbers {
		total += num
	}
	return total
}

func main() {
	fmt.Printf("1 + 2 + 3 = %d\n", sum(1, 2, 3))
}

// $ go run main.go
// 输出如下 
/**
  1 + 2 + 3 = 6
*/
```

## 传递切片参数

如果要传递 `切片类型` 数据作为函数参数，只需要在参数后面加上省略号 `...`。

```go
package main

import "fmt"

func sum(numbers ...int) int {
	total := 0
	for _, num := range numbers {
		total += num
	}
	return total
}

func main() {
	numbers := []int{1, 2, 3}
	fmt.Printf("1 + 2 + 3 = %d\n", sum(numbers...)) // 切片变量后面加 ... 即可
}

// $ go run main.go
// 输出如下 
/**
  1 + 2 + 3 = 6
*/
```

## 不传递任何参数

```go
package main

import "fmt"

func sum(numbers ...int) int {
	total := 0
	for _, num := range numbers {
		total += num
	}
	return total
}

func main() {
	fmt.Printf("不传递任何参数 = %d\n", sum())
}

// $ go run main.go
// 输出如下 
/**
  不传递任何参数 = 0
*/
```