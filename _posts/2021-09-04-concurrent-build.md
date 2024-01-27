---
layout: post
title: "Go build test 并发行为"
date: 2021-09-04T10:21:10+08:00
draft: false
---

go build 和 go test 不同的包中默认并发执行。关闭这个行为可以通过 `-p 1` 参数。例如跑测试的之后
```sh
$ go test -p 1 ./...
```



