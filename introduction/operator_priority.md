# 运算符优先级

## 从低到高

1. `||`
2. `&&`
3. `<-` (通道操作符，后面会讲到，暂时先忽略)
4. `==` `!=` `<` `<=` `>` `>=`
5. `+` `-` `|` `^`
6. `*` `/` `%` `<<` `>>` `&` `&^`
7. `^` `!`

## 最佳实践

通过使用括号来提升某个表达式的运算优先级，提高代码可读性。

### 例子
```go
package main

func main() {
	println(((1+2)*3+3)/4 + 5)
}

// $ go run main.go
// 输出如下 
/**
    8
*/
```