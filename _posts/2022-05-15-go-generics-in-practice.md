---
layout: post
title: "Go Generics in Practice"
date: 2022-05-15T07:00:29+08:00
draft: false 
tags: ['go']
---

Record the work and findings when refactoring http://github.com/vincent178/gocsv with generics.
<!--more-->

## Generics

Go team filed the [change proposal](https://go.googlesource.com/proposal/+/refs/heads/master/design/43651-type-parameters.md) to add generics support on 01/12/2021, this is released in version [1.18](https://go.dev/blog/go1.18) on 03/15/2022. 
Despite the decision to use `[T]` instead of `<T>` which is more common in the industry and design with [Gcshape](https://github.com/golang/proposal/blob/master/design/generics-implementation-dictionaries-go1.18.md) instead of [Monomorphization](https://en.wikipedia.org/wiki/Monomorphization) which causes [discussion](https://planetscale.com/blog/generics-can-make-your-go-code-slower) around performance downgrade when using generics. 

This is still super existing, generics means we could deal with concrete type but remain type restriction and functionality duplication, you know the frustration that deals with `interface{}` everywhere and type conversion.

## Result

I had a simple [tool](https://github.com/vincent178/goutils/blob/master/csv2struct.go) to parse CSV to a struct, before generics, I have to pass the result as an argument to get the type information:
```go
type Person struct {
	Name      string `csv:"Name"`
	Age       uint   `csv:"Age"`
	Height    int    `csv:"Height"`
	IsTeacher bool   `csv:"Is Teacher"`
}

var p Person
goutils.CsvMapToStruct(data, &p)
```

After [refactoring](https://github.com/vincent178/gocsv), now it looks like this:
```go
persons, err := gocsv.Read[Person](data) // ✅ persons now have concrete type
```

I'm pretty satisfied with the result, though it has some limitations which I will elaborate on below.

## Tricks

The trick I use to initialize with type parameter:
```go
func p[T any]() T {
  var t T // ✅ alloc memory to hold data
  // do anything
  return t
}
```

But you can't do like:
```go
func p[T any]() T {
  t := T{} // ❌ compile error: invalid composite literal struct T
  // do anything
  return t
}
```
Because `T` is not a struct, doing so the compiler will yield an error `invalid composite literal struct T`.

## Limitations

The Go generics implementation still has some limitations compared to other languages like Rust.

* Type inference

You might notice that we have to specify the type when calling `gocsv.Read[Person]`, and you can't do below either:
```go
var persons []Person
persons, err = gocsv.Read(data)
```

That's because Go can't do type infer based on the return value. 

For comparision, Rust don't need specify type when calling `walk`, it can infer type based on return value p:
```rust
#[derive(Default)]
struct Person {
    name: String,
}

fn walk<T: Default>() -> T {
    let t = T::default();
    t
}

fn main() {
    let p: Person;

    p = walk(); // ✅ infer type based on p

    println!("{}", p.name);
}
```

* Any struct

Go provide a handy built-in type parameter `any`, but there's no such type parameter for `struct`. In my implementation of gocsv, I have to pass `any` as type parameter, and use `reflect` doing runtime check on arguments instead of compiler time check.
```go
// func Read[T any](r io.Reader, options ...func(*option)) ([]T, error)
rv := reflect.ValueOf(out)
if rv.Kind() != reflect.Struct {
  return nil, errors.New(fmt.Sprintf("invalid generic type %v", rv.Kind()))
}
```

* Generic method

Go supports generic function, but not allow generics [method](https://stackoverflow.com/questions/8263546/whats-the-difference-of-functions-and-methods-in-go).  
```go
type Person {
  Name string
}

func (*Person) Walk[T Animal](t T) {} // ❌ compile error: method must have no type parameter
```

Go only allow put type parameter in the struct level, which might not be best fit for all the cases, in the above case, the generic type `Animal` is only used in `Walk` method, and have to move to struct level seems a bit wired. 

Again, Rust can do this easily:
```rust
#[derive(Default)]
struct Person {
    name: String,
}

trait Animal {
    fn name(&self) -> String;
}

impl Person {
    fn walk<T: Animal>(t: T) {
        println!("walk {} now!",  t.name())
    }
}
```

## Summerize

Generic gives us the way to specify type constraints and work with concrete type instead of `interface`, this is super powerful, and I'm looking forward to future improvements in generics.

## References
* https://go.googlesource.com/proposal/+/refs/heads/master/design/43651-type-parameters.md
* https://go.dev/blog/go1.18
* https://github.com/golang/proposal/blob/master/design/generics-implementation-dictionaries-go1.18.md
* https://en.wikipedia.org/wiki/Monomorphization
* https://planetscale.com/blog/generics-can-make-your-go-code-slower
* https://stackoverflow.com/questions/8263546/whats-the-difference-of-functions-and-methods-in-go
