---
layout: post
title: "记录关于 interface 一个愚蠢 bug"
date: 2021-08-23T23:54:52+08:00
draft: false
---

go 中所有变量都是值传递，包括定义为接口的变量。下面例子是我的 bug 的抽象：
```go
var a interface{} = 1

b := a // b = 1

a = 2 // b = 1
```
