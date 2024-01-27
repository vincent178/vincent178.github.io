---
layout: post
title: "Rust 学习笔记（4）"
date: 2022-05-11T05:16:07+08:00
draft: false
tags: ['rust']
images: ['/img/rust-logo.jpg']
---

学习Rust的第四天，错误处理。
<!--more-->

## 基于 Result 的错误处理 

错误处理的核心数据结构：
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

以文件打开为例：
```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");

    let nf = match f {
        Ok(file) => file, // 🆗 返回 std::fs::File
        Err(error) => panic!("Problem opening the file: {:?}", error), // ⭕ 错误程序 panic
    };
}
```

注意： nf 的类型由 Ok 和 Err 的返回值推断，因此两个返回值的类型必须一致：
```rust
let f = File::open("hello.txt");

let nf = match f {
    Ok(file) => file, // nf 类型推断为 std::fs::File
    Err(error) => "not ok" // nf 类型推断为 &str，编译错误 ❌
};
```

`unwrap` 和 `expect` 就是遇到错误panic的语法糖函数：
```rust
let f = File::open("hello.txt").unwrap();
let f = File::open("hello.txt").expect("Failed to open hello.txt"); // expect 可以传入定制的panic消息
```

## 返回错误

函数返回包含错误的 `Result` 的返回值：
```rust
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("hello.txt");

    let mut f = match f {
        Ok(file) => file,
        Err(e) => return Err(e), // ⭕ 提前中止函数并且返回错误
    };

    let mut s = String::new();

    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s), // 🆗 执行成功，返回包含 String 值的 Result
        Err(e) => Err(e), // ⭕ 执行错误，返回包含 Err 的 Result
    }
}
```

`?` 放在返回 `Result` 函数的最后，`Ok` 时返回 T，`Err` 时中止当前作用域的函数，并返回错误。
利用语法糖 `?`，上面的例子重构为：
```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = File::open("hello.txt")?;
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}
```

用 `?` 做链式调用：
```rust
fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();

    File::open("hello.txt")?.read_to_string(&mut s)?;

    Ok(s)
}
```

`?` 也适用于返回 `Option` 函数：
```rust
fn last_char_of_first_line(text: &str) -> Option<char> {
    text.lines().next()?.chars().last() // 如果 next 函数返回 None 将中止整个函数，并且返回 None
}
```
