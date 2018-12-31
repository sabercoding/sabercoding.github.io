---
layout: post
title: "rust学习之一"
date: 2017-11-06 02:28:00.000000000 +09:00
---

>第一次接触rust是因为在GitHub上面看到了一个好玩的项目，rust-sr这个能让图片更加清晰的工具。rust这个新兴的语言在人工智能方面运用的还是很有前景的。so,开始学习学习。

## 教材

我是用的教程是[Rust 程序设计语言（第二版）](https://kaisery.github.io/trpl-zh-cn/ch01-00-introduction.html),刚加第一版的书签时，还没有呢。打算看的时候除了第二版，那就从第二版开始看.

# 介绍

>Rust 是一门着眼于安全、速度和并发的编程语言。其程序设计兼顾底层语言的性能与控制，并不失高级语言强大的抽象能力。其特性适合那些有类 C 语言经验，正在寻找更安全的替代品的开发者；同样适合有着类 Python 语言背景，寻求在不牺牲表现力的前提下，编写更高性能代码的开发者。

# 安装

rust的安装很简单，下载一个shell脚本执行即可。
```
$ curl https://sh.rustup.rs -sSf | sh
info: downloading installer

Welcome to Rust!

This will download and install the official compiler for the Rust programming
language, and its package manager, Cargo.

It will add the cargo, rustc, rustup and other commands to Cargo''s bin
directory, located at:

  /home/saber/.cargo/bin

This path will then be added to your PATH environment variable by modifying the
profile files located at:

  /home/saber/.profile
  /home/saber/.bash_profile

You can uninstall at any time with rustup self uninstall and these changes will
be reverted.

Current installation options:

   default host triple: x86_64-unknown-linux-gnu
     default toolchain: stable
  modify PATH variable: yes

1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
1

info: syncing channel updates for ''stable-x86_64-unknown-linux-gnu''
info: latest update on 2017-10-12, rust version 1.21.0 (3b72af97e 2017-10-09)
info: downloading component ''rustc''
info: downloading component ''rust-std''
 56.7 MiB /  56.7 MiB (100 %)  47.9 MiB/s ETA:   0 s
info: downloading component ''cargo''
info: downloading component ''rust-docs''
info: installing component ''rustc''
info: installing component ''rust-std''
info: installing component ''cargo''
info: installing component ''rust-docs''
info: default toolchain set to ''stable''

  stable installed - rustc 1.21.0 (3b72af97e 2017-10-09)


Rust is installed now. Great!

To get started you need Cargo''s bin directory ($HOME/.cargo/bin) in your PATH
environment variable. Next time you log in this will be done automatically.

To configure your current shell run source $HOME/.cargo/env

$ source $HOME/.cargo/env

```

# Hello World!

貌似每个编程语言的开头学习总会是，输出`Hello World!`。rust也不例外。

简单创建一个脚本main.rs
```
fn main() {
    println!("Hello, world!");
}
```

执行：
```
$ rustc main.rs
$ ./main
Hello, world!
```

这样的运行方式很类似go跟c呀。`rust是一种预编译静态类型语言`，所以需要预编译一下代码。跟PHP就不一样了，`PHP是解释型语言`，它就启动一个解释器，运行时解释代码即可。

# Hello, Cargo!

cargo这玩意用完了感觉还是挺方便的，就是一个脚手架，跟wepback有点类似。就是生成比较简单，到最后自己可以扩展一下分层之类的。

### 创建项目

执行：
```
$ cargo new hello_cargo --bin
     Created binary (application) `hello_cargo` project
```

就会自动生成一个Git项目。

> --.git
> --Cargo.toml
> --src
> -----main.rs

main.rs里面就是上面的hello world。Cargo.toml这个文件的内容是：

```
[package]
name = "hello_cargo"
version = "0.1.0"
authors = ["saber <sabercoding@gail.com>"]

[dependencies]
```

创建命令后面的`--bin`，就是在获取你当前用户的信息，写入到Cargo.toml文件里。
### 执行项目

执行命令：
```
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

直接输出你当前项目的代码

### 编译项目

执行命令：
```
$ cargo build
   Compiling hello_cargo v0.1.0 (file:///home/saber/rust/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.70 secs
```

编译完成后多出来一个文件''Cargo.lock''和一个文件夹''target''。

Cargo 使用 Cargo.lock 来记录程序的依赖。这个项目并没有依赖，所以其内容比较少。事实上，你自己永远也不需要碰这个文件，让 Cargo 处理它就行了。

编译完成后的项目在target里，不加''--release''参数则生成在`debug`文件夹内，否则在''release''文件夹内。执行文件夹内的''hello_cargo''文件即可。