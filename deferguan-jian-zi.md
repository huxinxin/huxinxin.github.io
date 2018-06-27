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

funcA

a= 6

funcB

a= 5

