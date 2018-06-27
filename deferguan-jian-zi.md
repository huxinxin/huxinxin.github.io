# 一、执行顺序先进后出

```
package main

import "fmt"

func funcA() {
    defer fmt.Println("defer test A")
    defer fmt.Println("defer test B")
    defer fmt.Println("defer test C")
    defer fmt.Println("defer test D")
}

func main() {
    funcA()
}
```

运行结果：

defer test D

defer test C

defer test B

defer test A

# 二、当defer被声明时，其参数就会被实时解析

```
package main

import "fmt"

func main() {
    funcA()
    funcB()
}

func funcA() {
    a := 5
    defer func() {
        fmt.Println("funcA a=", a)
    }() 
    a++ 
}
func funcB() {
    a := 5
    defer func(a int) {
        fmt.Println("funcB a=", a)
    }(a)
    a++ 
}
```

执行结果：

funcA a= 6

funcB a= 5

# 三、defer在return之前执行

这是官方文档中明确说明的，然而需要注意的是：

* go返回值的方式跟C是不一样的，为了支持多值返回，go是用栈返回值的，而C是用寄存器。
* return XXX并不是一个原子指令

先看一下代码：



