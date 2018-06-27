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

整个return过程，没有defer之前是，先把在栈中写一个值，这个值被会当作返回值。然后再调用RET指令返回。return xxx语句汇编后是先给返回值赋值，再做一个空的return: \( 赋值指令 ＋ RET指令\)

defer的执行是被插入到return指令之前的

有了defer之后，就变成了 \(赋值指令 + CALL defer指令 + RET指令\)

而在CALL defer函数中，有可能将最终的返回值改写了...也有可能没改写。总之，如果改写了，那么看上去就像defer是在return xxx之后执行的~



看一下代码：

