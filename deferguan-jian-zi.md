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

# 三、defer在return之前执行（而不是return XXX）

这是官方文档中明确说明的，然而需要注意的是：

* go返回值的方式跟C是不一样的，为了支持多值返回，go是用栈返回值的，而C是用寄存器。
* return XXX并不是一个原子指令

整个return过程，没有defer之前是，先把在栈中写一个值，这个值被会当作返回值。然后再调用return。return xxx是先给返回值赋值，再做一个空的return\( 赋值 ＋ return\)

defer的执行是被插入到return指令之前的

有了defer之后，就变成了 \(赋值指令 +  defer + return\)

```
package main

import "fmt"

func funcA() (result int) {
    defer func() {
        result++
    }() 
    return 0
}

func funcB() (result int) {
    t := 2
    defer func() {
        t = t + 2 
    }() 
    return t
}

func funcC() (result int) {
    defer func(r int) {
        r = r + 2 
    }(result)
    return 1
}

func main() {
    fmt.Println(funcA())
    fmt.Println(funcB())
    fmt.Println(funcC())
}
```

执行结果：

1

2

1

根据\(赋值指令 +  defer + return\)的执行过程，可以对代码进行改写

```
func funcA() (result int) {
    result = 0
    func() {
        result++
    }() 
    return 
}
```

```
func funcB() (result int) {
    t := 2
    result = t
    func() {
        t = t + 2 
    }() 
    return
}
```



