# 问题

Go语言里的Goroutine是抢占式调度还是非抢占式调度？

# 代码

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

由执行结果可知funcA和funcB交替执行。所以Goroutine应该是抢占式的调度，不会有某个Goroutine长期占有CPU使其他Goroutine得不到执行的情况发生。

