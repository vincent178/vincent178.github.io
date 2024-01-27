---
layout: post
title: "Rust 学习笔记（2）"
date: 2022-05-09T12:57:17+08:00
draft: false
tags: ['rust']
images: ['/img/rust-logo.jpg']
---

学习Rust的第二天，继续所有权以及引用。
<!--more-->

## 所有权 Ownership

变量作为参数传入函数中也会发生所有权转移：
```rust
fn main() {
    let s = String::from("hello");

    takes_ownership(s);             // 此处发生所有权转移

    println!(s);                    // 编译错误，s 没有所有权
} 

fn takes_ownership(some_string: String) { // 所有权转移到 some_string 变量
    println!("{}", some_string);
} // some_string 出了作用域，对应的值释放
```

函数返回也会发生所有权转移：
```rust
fn main() {
    let s1 = gives_ownership();     // 所有权转移到 s1 变量 
} // s1 出了作用域，对应的值释放

fn gives_ownership() -> String {             
    let some_string = String::from("yours");

    some_string                              // 返回变量 some_string，所有权转移出函数
} // some_string 出了作用域，但没有所有权，对应的值不会释放
```

前面提到值的释放，背后就是对于拥有该值所有权的变量调用了`drop`函数：
```rust
pub fn drop<T>(_x: T) {}
```

## 拷贝和克隆 Copy & Clone

栈中的数据拷贝操作不会影响所有权，完成拷贝后各自拥有一份数据。
```rust
let x = 5; // x 的值存在栈中
let y = x; // y 拷贝了 x 的值，新 PUSH 了 5 进入栈上

println!("x = {}, y = {}", x, y); // 编译通过 ✅
```

堆上的数据可以通过克隆操作实现数据拷贝，变量各自拥有一份数据。
```rust
let s1 = String::from("hello"); // s1 的值存在堆中
let s2 = s1.clone();            // s2 拷贝了 s1 的值，存在另外一个堆上的地址

println!("s1 = {}, s2 = {}", s1, s2); // 编译通过 ✅
```

## 引用 Reference

引用是指变量在不转移所有权的情况下引用值：
* 引用标识符 `&`
* 解引用标识符 `*`

```rust
let s1 = String::from("hello");

let s = &s1;
let r = &s;

println!("{}, {}", s, r); // 编译通过 ✅
```    

![reference](/img/rust_reference.png)

函数同样也适用：
```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len); // 编译通过 ✅
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

## 参考资料
* https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html

