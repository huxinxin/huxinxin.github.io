# 问题一：抢占式还是非抢占式

```
package main

import (
    "fmt"
    "runtime"
    "time"
)

var a = make(map[int]string)

func funcA() {
    for {
        fmt.Println("goroutine func A")
    }   
}

func funcB() {
    for {
        fmt.Println("goroutine func B")
    }   
}

func main() {
    //线程数设为1，在多线程的情况下，线程之间会抢CPU，这个问题没有意义
    runtime.GOMAXPROCS(1)
    go funcA()
    go funcB()
    time.Sleep(10 * time.Second)
}
```

由运行结果可知funcA和funcB交替执行。所以Goroutine应该是抢占式的调度，不会有某个Goroutine长期占有CPU导致其他Goroutine不能执行的情况发生。

# 问题二：线程安全

```
package main

import (
    "fmt"
    "runtime"
)

var result int = 0 
var chA = make(chan bool)
var chB = make(chan bool)

func funcA() {
    for i := 0; i < 100000; i++ {
        result = result + 1 
    }   
    chA <- true
}
func funcB() {
    for i := 0; i < 100000; i++ {
        result = result + 1 
    }   
    chB <- true
}

func main() {
    runtime.GOMAXPROCS(4)
    go funcA()
    go funcB()
    <-chA
    <-chB
    fmt.Println("result = ", result)
}
```

运行结果：  
result = 102277

多线程下result = result + 1并不是原子操作，需要加锁，没什么问题。

下面把线程数设置为1

```
package main

import (
    "fmt"
    "runtime"
)

var result int = 0 
var chA = make(chan bool)
var chB = make(chan bool)

func funcA() {
    for i := 0; i < 100000; i++ {
        result = result + 1 
    }   
    chA <- true
}
func funcB() {
    for i := 0; i < 100000; i++ {
        result = result + 1 
    }   
    chB <- true
}

func main() {
    runtime.GOMAXPROCS(1)
    go funcA()
    go funcB()
    <-chA
    <-chB
    fmt.Println("result = ", result)
}
```

运行结果：

result = 200000

结论：在单线程下不同Goroutine之间不存在线程安全的问题

