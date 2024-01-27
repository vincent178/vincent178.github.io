---
layout: post
title: "浅尝 Go 范型"
date: 2021-09-02T13:30:50+08:00
draft: false
---

## 安装 Go master 分支

在 m1 的 Mac 安装非常简单

```bash
git clone git@github.com:golang/go.git
cd src
arch -arm64 ./all.bash
```

执行完没有报错，就已经成功的编译了最新的 go。

## 配置 Goland 

1. 将 sdk path 指向 clone 下来的代码
![add sdk](/go/add_sdk.png)

2. 打开试验性支持 generics
![enable generics](/go/enable_generics.png)

注意：
goimports 还不支持
类型推断还不支持

## 测试代码

`comparable` 是内置的 constrain

```go
func Contains[T comparable](s []T, e T) bool {
	for _, a := range s {
		if a == e {
			return true
		}
	}
	return false
}

Contains([]int{1, 2, 3}, 3)
Contains([]string{"a", "b", "c"}, "c")
```
