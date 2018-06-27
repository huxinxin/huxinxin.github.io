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
    runtime.GOMAXPROCS(1)
    go funcA()
    go funcB()
    time.Sleep(10 * time.Second)
}

```

执行结果：



