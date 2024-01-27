---
layout: post
title: "Rust 学习笔记（1）"
date: 2022-05-08T19:53:59+08:00
tags: ['rust']
draft: false
images: ['/img/rust-logo.jpg']
---

学习Rust的第一天，关于内存管理和所有权。
<!--more-->

## 栈和堆 Stack and Heap 

数据分配在栈或者堆上。  
* 栈占用连续的内存，是一种先进后出的数据结构，储存在栈上的数据要求必须是已知且固定大小。  
* 堆不要求连续的内存，可以存放可变长度的数据。  

数据保存到栈上比保存到堆上更高效，保存到栈上可以直接在栈顶操作，对比保存到堆上涉及到更多的操作：
1. 内存分配器寻找合适的内存位置
2. 标记该内存位置已占用

访问栈上的数据比访问在堆上的更高效，栈的内存是连续的，处理器可以非常快速的访问这样的内存，对比堆上的数据是不连续的，而且，访问堆上的数据必须通过保存了内存地址的指针访问，涉及更多的操作。  

## 内存管理 Memory Management

内存管理通常是指管理由内存分配器分配给堆使用的内存。常用的由三种方法：
* Java，Go等语言用[垃圾回收 GC](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science))
* C，C++等语言需要手动分配及释放
* Rust 用所有权机制

## 作用域 Scope

在 Rust 中，作用域是指由 `{ ... }` 组成一段代码片段。如果变量在某个作用域中定义，那么在作用域以外，这个变量将无法访问。

```rust
{                      // s is not valid here, it’s not yet declared
    let s = "hello";   // s is valid from this point forward

    // do stuff with s
}                      // this scope is now over, and s is no longer valid
```

## 所有权 Ownership

所有权规则：
* 指向值的变量作为所有人。
* 同一时间只能有一个所有人。
* 当变量出作用域时，对应的值将被自动释放。

### 释放

```rust
{
    let s = String::from("hello"); // s 的值保存在堆上

    // do stuff with s
}                                  // 作用域结束，s 被自动释放
```

### 转移

`String` 的内存布局如下图所示：
![mem layout](/img/string_mem_layout.png)

当s1赋值给s2的时候，s1和s2同时指向同一个值：
![string shadow clone](/img/string_shadow_clone.png)

当s1和s2作用域结束时，根据所有权规则，s1和s2对应的值都会被释放。但同一段内存释放两次会出错，为了保证内存安全，Rust 在s1赋值给s2的时候会将所有权转移给s2：
![ownership move](/img/ownership_move.png)

同时s1将无法访问：
```rust
let s1 = String::from("hello");
let s2 = s1; // s1 对应的值所有权转移到了 s2，s1 后面将无法访问

println!("{}", s1);
```
![move compile error](/img/rust_compile_error_1.png)

## 参考资料
* https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html
