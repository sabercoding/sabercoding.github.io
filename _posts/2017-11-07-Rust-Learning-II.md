---
layout: article
title: "rust学习之二"
date: 2017-11-07 02:29:00.000000000 +09:00
tags: rust
---

>昨天参加团建没有看这些，之后继续补上。开始学习第二章的猜猜看游戏。

## 猜猜看游戏流程

1. 生成一个随机数
2. 输入一个数
3. 比较输入数与随机数
4. 如果相等则猜对了，否则继续步骤2

## 代码

```rust
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => {
                println!("You win!");
                break;
            }
        }
    }
}
```

## 解读

直接亮代码来解读吧。

### extern
#### extern crate rand;

声明使用`rand`外部依赖。

>记住`crate`是一个Rust代码的包。我们正在构建的项目是一个二进制 crate，它生成一个可执行文件。rand`crate`是一个库`crate`，库`crate`可以包含任意能被其他程序使用的代码。

可以理解成每个外部依赖都可以理解为在一个库`crate`里，所以就需要在依赖前加上它。

`rand`里面有生成随机数的方法。如何添加此依赖？这时候cargo就派上大用场了。只需要编辑`Cargo.toml`文件，在你再次`build`的时候就会自动去下载你需要的依赖。

```
[dependencies]

rand = "0.3.14"
```

还能更深层次的理解上一次遇到的`Cargo.lock`文件，此文件能确保构建可以重现。一个项目会有很多依赖，但是如何来确保每个人的依赖都是一致的呢，lock文件就是为此而生的。Cargo第一次构建项目的时候，会去寻找到符合你的依赖版本，并生成lock文件。之后再构建的时候就会使用lock里指定的文件来构建项目。

### use
#### use std::io;

很多语言都是有使用的use这个关键字，rust也不例外。此处是声明引用std里的io库。std::io 库提供很多 io 相关的功能，比如接受用户输入。

#### use std::cmp::Ordering;

引入`std::cmp::Ordering`这个类型，这个类型在调用比较两值方法cmp时会有一个对此类型枚举匹配，`Less`、`Greater`和`Equal`。

#### use rand::Rng;

引入`Rng`这个trait，它定义了随机数生成器应实现的方法;

### main

#### `let` 和 `let mut`

`let`和`let mut`都是在定义变量。但是`let mut`是可变变量，`let`是不可变变量。感觉这是为了性能做的考虑，在底层的初始化应该是不一样的。

同时在使用引用时，`let`定义的可以直接使用，`let mut`定义的也是要加个`mut`。
```rust
let a = 1;
add(&a);

let mut b = 1;
add(&mut b);
```

#### rand::thread_rng().gen_range(1, 101)

`rand`依赖里的`thread_ran()`函数，是随机数生成器。再调用这个生成器里的`gen_range`方法来生成一个在两参数之间的随机数。这个随机数包含下限但不包含上限。

#### loop{}

`loop`实现无限循环。可以使用`break`退出，`continue`跳过重新继续。

#### String::new()

初始化一个string类型的变量。

#### io::stdin().read_line(&mut guess).expect("Failed to read line");

调用`io`的关联函数`stdin`，它返回了一个终端标准输入的一个句柄。再使用`read_line()`函数，回车触发，读取这行内容到引用的可变变量`guess`。

`read_line()`函数除了复制变量，还会返回一个`io::Result`。它的成员是`Ok`或`Err`,`Ok`表示操作成功，内部包含成功时产生的值。Err意味着操作失败，包含失败的前因后果。

我们可以使用`expect`方法，当返回时Err的时候就抛出，我们事先定义的错误信息。

#### let guess: u32

rust支持对变量隐藏，用一个新值来隐藏`guess`之前的值。这个功能常用在需要转换值类型之类的场景，它允许我们复用`guess`变量的名字，而不是被迫创建两个不同变量，诸如`guess_str`和`guess`之类。

#### guess.trim().parse()

比较两个数时，rust自己会对变量进行类型判断。前面为了获取输入，赋值`guess`为string类型，而随机数则是int类型。直接比较会报`guess`类型错误，所以需要进行类型转换。将`guess`转成int，使用`parse()`能直接string转int。`trim`函数是为了过滤掉输入时需回车触发导致多出一个回车符。


#### match {}

```rust
 match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };
```
此处也是上面讲的`Result`，`parse()`也是会有一个`Result`返回。因为使用`expect`并不是很优雅的输出。

```shell
$ ./target/debug/hello_cargo
Guess the number!
The secret number is: 67
Please input your guess.
gg
thread "main" panicked at "Please type a number!: ParseIntError { kind: InvalidDigit }", /checkout/src/libcore/result.rs:906:4
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```

而且，我们这不还需要继续猜数字嘛。`Err(_)`是结果错误时返回要进行的操作，`continue`就能让它重新继续来猜。

#### cmp

```rust
match guess.cmp(&secret_number) {
    Ordering::Less    => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal   => {
        println!("You win!");
        break;
    }
}
```

使用`cmp`比较两个数，`cmp`结果会返回一个`Ordering`类型，我们可以事先定义这个三个可能的类型之后的操作。继续使用`match {}`来进行结果操作。会遍历定义的结果返回，如果匹配则执行。

如果只是一行代码的就不需要`{}`。

至此，猜猜看就完成啦。
