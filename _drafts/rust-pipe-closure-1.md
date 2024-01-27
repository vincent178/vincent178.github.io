---
title: "Rust Pipeline (1)"
date: 2022-12-04T07:01:16+08:00
draft: false
tags: ['rust']
images: ['/img/rust-logo.jpg']
---

A pipeline is a series of functions where every function has a single parameter as input, the function's output is the succeeding function's input, as well as types for strong typed languages. The goal is to build a such pipeline in Rust, and the final result looks like:

```rust
fn main() {
    SyncPipelineRunner::new(|s: &str| s.to_string()) // closure
        .pipe(capitalize_first_letter) // function
        .pipe(|mut s| { 
            s.push_str(" world");
            s
        })
        .pipe(|s| s.len()) // return usize
        .pipe(|l| println!("total len is {}", l))
        .exec("hello"); // input is &str => total len is 11
}

fn capitalize_first_letter(s: String) -> String {
    s[0..1].to_uppercase() + &s[1..]
}    
```

<!--more-->

`SyncPipelineRunner` has three methods, `new`, `pipe`, and `exec`. Let's start with `new` with accept a closure as argument.

## Closure

A closure is anonymous function which can capture variables from their environment. A simple example closure in Rust:
```rust
let add = |x, y| x + y;
```

To evalulate it, we can call it like a regular function:
```rust
let result = add(1, 2);
println("{}", result); // => 3
```

## Closure in Depth

A closure type is approximately equivalent to a struct which contains the captured variables.[^1]

```rust
let add = |x, y| x + y;
```

generates a closure type roughly like the following:
```rust
struct AddClosure {
}

impl FnOnce for AddClosure {
  type Output = i32;

  fn call_once(self, a: i32, b: i32) -> i32 {
    a + b
  }
}
```

the call to `add` works as:
```rust
AddClosure{}.call_once(1, 2);
```

So, closure is not a magic but syntax sugar, and it all implment `FnOnce`.

## Stored Closure

`SyncPipelineRunner` should have a field which stored closure passed by `new` function. 
```rust
pub struct SyncPipelineRunner<I, O> {
    f: FnOnce(I) -> O,
}
```

If we compile above code, we get the following error.
```rust
error[E0782]: trait objects must include the `dyn` keyword
 --> src/main.rs:6:8
  |
6 |     f: FnOnce(I) -> O,
  |        ^^^^^^^^^^^^^^
  |
help: add `dyn` keyword before this trait
  |
6 |     f: dyn FnOnce(I) -> O,
  |        +++

For more information about this error, try `rustc --explain E0782`.
error: could not compile `rust-pipe-closure-2` due to previous error
```

The error message is helpful, add `dyn` keyword, 
```rust
pub struct SyncPipelineRunner<I, O> {
    f: dyn FnOnce(I) -> O,
}
```
and now compiler is happy.


* https://stevedonovan.github.io/rustifications/2018/08/18/rust-closures-are-hard.html
* https://medium.com/swlh/demystifying-closures-futures-and-async-await-in-rust-part-1-closures-97e531e4dc50#406c
* https://levelup.gitconnected.com/demystifying-closures-futures-and-async-await-in-rust-part-2-futures-abe95ab332a2
* https://stackoverflow.com/questions/58354633/cannot-use-impl-future-to-store-async-function-in-a-vector
* https://www.bitfalter.com/async-closures
* https://bnz-digital.github.io/fp/inductive/composition/


[^1]: https://doc.rust-lang.org/reference/types/closure.html
