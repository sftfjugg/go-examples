# 概述

包通过 `导出` 机制控制 [变量](variables.md)、[结构体](struct.md)、[函数](func.md) 等数据可见性。

只有 1 个简单的规则: **首字母大写，可导出，首字母小写，不可导出。** 也就是说，Go 的访问控制只有两种模式:
- 包内可见 
- 全局可见

# 例子

```go
package hello

var (
	privateName string // 只能包内访问
	PublicName  string // 包内，包外都可以访问
)
```