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

