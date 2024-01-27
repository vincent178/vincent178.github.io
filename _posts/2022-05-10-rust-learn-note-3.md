---
layout: post
title: "Rust 学习笔记（3）"
date: 2022-05-10T06:40:17+08:00
draft: false
tags: ['rust']
images: ['/img/rust-logo.jpg']
---

学习Rust的第三天，可变引用和Vector。
<!--more-->

## 可变引用 Mutable Reference

如果试图修改一个引用变量，编译会抛错：
```rust
fn main() {
    let s = String::from("hello");

    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world"); // 编译错误 ❌
}
```

将引用改成可变引用，编译就会通过：
```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world"); // 编译通过 ✅
}
```

可变引用同样有同一时间只有一个的限制：
```rust
fn main() {
    let mut s = String::from("hello");

    let r = &mut s; // 第一个可变引用

    s.push_str("a"); // 第二个可变引用，编译错误 ❌

    r.push_str("b");
}
```

第二个可变引用由下图来解释（注意第一个参数）：
![fn mut self](/img/rust_mut_self.png)
函数调用发生了对于 `s` 变量的可变引用。

但交换了 `s`和 `r` 的函数调用后编译可以通过✅。
```rust
fn main() {
    let mut s = String::from("hello");

    let r = &mut s;

    r.push_str("b");

    s.push_str("a"); // 编译通过 ✅
}
```
`r` 的作用域在完成最后一次调用后提前结束了，因此在后面的 `s` 可以正常的进行可变引用。

## 悬空引用 Dangling References

Rust 不允许悬空引用
```rust
fn dangle() -> &String {
    let s = String::from("hello");

    &s
} // s 的引用值被释放，s 变成悬空引用，编译错误 ❌
```

## Vector

创建空 Vector，创建的时候可以声明类型：
```rust
let v: Vec<i32> = Vec::new();
```

创建有初始值的 Vector：
```rust
let v = vec![1, 2, 3];
```

更新 Vector：
```rust
let mut v = Vec::new();

v.push(5);
v.push(6);
```

读取 Vector：
```rust
let v = vec![1, 2, 3, 4, 5];


let third = &v[2]; // 访问非法索引 panic ❌
let third = v.get(2); // 访问非法索引 ✅
```

遍历 Vector：
```rust
let v = vec![100, 32, 57];

for i in &v {
  println!("{}", i);
}
```

遍历更新 Vector：
```rust
let v = vec![100, 32, 57];

for i in &mut v {
  *i += 50; // 解引用 i
}
```

