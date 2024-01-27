---
layout: post
title: "Rust 学习笔记（5）"
date: 2022-05-12T13:20:24+08:00
draft: false
tags: ['rust']
images: ['/img/rust-logo.jpg']
---

学习Rust的第五天，泛型。
<!--more-->

## 泛型

Rust 使用 `<T>` 表示类型参数，定义泛型结构体及函数如下：
```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}
```

## Monomorphization

Rust 采用在编译时，将泛型代码转化成具体的类型代码，因此没有运行时的性能影响。

将原始的代码：
```rust
let integer = Some(5);
let float = Some(5.0);
```

编译成：
```rust
enum Option_i32 {
    Some(i32),
    None,
}

enum Option_f64 {
    Some(f64),
    None,
}

fn main() {
    let integer = Option_i32::Some(5);
    let float = Option_f64::Some(5.0);
}
```

## 类型约束 Trait

使用 `trait` 关键词定义类型约束：
```rust
pub trait Summary {
    fn summarize(&self) -> String;
}
```

结构体实现 Trait 的约束：
```rust
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}
```

Trait 默认实现：
```rust
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}
```

Trait 作为参数：

* 作为参数的类型
```rust
pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```

* 作为泛型的类型约束：
```rust
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

类型约束操作符 `+`，类型需要同时满足操作符左右的约束：
```rust
pub fn notify<T: Summary + Display>(item: &T) {}
```

类型约束 `where` 用于用于复杂的类型约束条件：
```rust
fn some_function<T, U>(t: &T, u: &U) -> i32
    where T: Display + Clone,
          U: Clone + Debug {}
```

类型约束下的方法定义：
```rust
use std::fmt::Display;

struct Pair<T> {
    x: T,
    y: T,
}

impl<T: Display + PartialOrd> Pair<T> {
    fn cmp_display(&self) {
        if self.x >= self.y {
            println!("The largest member is x = {}", self.x);
        } else {
            println!("The largest member is y = {}", self.y);
        }
    }
}
```

