# 变量
关键字 `var`, 定义后可以修改。

# 语法规则

- var 关键字

```shell
var 变量名称 [变量类型] =  变量值
    
 # 例子
 var pi      float     =  3.14159
```
   
其中，变量类型为可选，因为编译器可以根据值来推断其类型 (建议指定类型，可以增强语义性)。

- 直接定义

```shell
变量名称 := 变量值

# 例子
pi := 3.14159
```
    
其中，不需要变量类型，因为编译器可以根据值来推断其类型。

## 注意事项

**在函数中定义的变量，定义后必须使用，否则编译时会报错: `Unused variable 'pi'`**

## 同时定义多个变量

```shell
var (
    变量名称 [变量类型] =  变量值
    变量名称 [变量类型] =  变量值
    变量名称 [变量类型] =  变量值
    ...
)
```

### 例子

```go
package main

func main() {
   var (
      pi float64 = 3.14159
      page int = 1
      name = "abc"
   )

   println(pi)
   println(page)
   println(name)
}

// $ go run main.go
// 输出如下 
/**
    +3.141590e+000
    1
    abc
*/
```

# 显式/隐式

1. 浮点类型
    * 显式类型定义： `var pi float64 = 3.14159`
    * 隐式类型定义： `var pi = 3.14159`
2. 整型
    * 显式类型定义： `var page int = 1`
    * 隐式类型定义： `var page = 1`
3. 字符串
    * 显式类型定义： `var name string = "abc"`
    * 隐式类型定义： `var name = "abc"`
4. 其他类型以此类推
