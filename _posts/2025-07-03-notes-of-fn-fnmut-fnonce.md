---
layout: post
title: "Rust学习笔记——Rust 中的 FnOnce、FnMut 与 Fn Traits"
tag: "rust-note"
---

笔记参考了互联网上的一些帖子、《The Rust Programming Language》等。

<!--more-->

# 一、`FnOnce`、`FnMut`、`Fn` Traits 是什么？

`FnOnce`、`FnMut`、`Fn`是Rust中自动为闭包（closure）实现的调用操作符，用以细化一个闭包的调用行为。

1. 其中以`FnOnce` Trait解释的闭包只能调用一次。
2. 以`FnMut` Trait解释的闭包能调用多次，并且可以修改捕获变量。
3. 以`Fn` Trait解释的闭包能够调用多次，并且不能修改捕获变量。

可以理解为一个闭包的基础形态就是`FnOnce`，对于捕获变量，采取`move T`, `&mut T` 还是 `&T` 的方式捕获类型为`T`的变量取决于编写的代码如何使用捕获变量。
对于`FnOnce`与`FnMut`，这三种捕获方式都是允许的；而对于`Fn`，只允许采用`&T`的方式捕获变量。

# 二、`FnOnce`、`FnMut`、`Fn` Traits 的定义

**在用户的视角里**，`FnOnce`、`FnMut`、`Fn` Traits 可以看作有以下定义。

```rust
pub trait FnOnce<Args: Tuple> {
    extern "rust-call" fn call_once(self, args: Args) -> Self::Output;
}

pub trait FnMut<Args: Tuple>: FnOnce<Args> {
    extern "rust-call" fn call_mut(&mut self, args: Args) -> Self::Output;
}

pub trait Fn<Args: Tuple>: FnMut<Args> {
    extern "rust-call" fn call(&self, args: Args) -> Self::Output;
}
```

`FnOnce`、`FnMut`、`Fn` Traits具备“继承关系”，即`Fn : FnMut : FnOnce`。

直观而言，当我们调用一个闭包时：

```rust
fn call_a_closure(f: FnOnce(i32) -> i32) -> i32{
    f(114514)
}
```

可以看作为以下代码（这个代码是语法正确的）：

```rust
fn call_a_closure(f: FnOnce(i32) -> i32) -> i32{
    FnOnce::call_once(f, (114514))
}
```

一个闭包调用时需要的参数有两部分，一部分为捕获变量，一部分为调用语句上指定的参数。当我们拿到一个类型为闭包的变量时，实际上在这个变量里便记录了捕获变量与指向对应函数体的指针。而`FnOnce::call_once(f, (114514))`中便显示表明了这两部分参数。

在特殊情况下，即闭包不捕获任何变量时，我们可以将一个闭包变量的类型转化为函数指针，如以下代码所示。

```rust
fn cast_closure_to_fn_ptr() -> fn(i32) -> i32 {
    |x| {
        if (x == 114514) { 1919180 } else { 114514 }
    }
}
```
