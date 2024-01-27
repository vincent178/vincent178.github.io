---
layout: post
title: "Go 调用包私有方法"
date: 2021-09-16T15:37:57+08:00
draft: false
---

Go 定义公有方法和私有方法的在于方法的首字母大小写。通常情况下，Go 不允许调用其他 package 的私有方法。当然，使用`go:linkname`，调用私有方法也是可以做到的。

使用的时候有几个注意点:
1. 必须import `unsafe` package
2. 注释方式 `//go:linkname localname packagename`

举个例子

```go
import (
	"fmt"

	_ "runtime"
	_ "unsafe"
)

//go:linkname dolockOSThread runtime.dolockOSThread
func dolockOSThread()

func main() {
	dolockOSThread()

	fmt.Println("Hello world")
}
```

如果用的是非标准库的私有方法，packagename 必须写完整的名字。比如要用 testify 的私有方法，需要写成 `github.com/stretchr/testify/assert.<funcname>`

