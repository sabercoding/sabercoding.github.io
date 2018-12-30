---
layout: post
title: "rusty_sr-图片变高清"
date: 2017-03-28 03:03:00.000000000 +09:00
tags: ""
---
![LogoNN](https://raw.githubusercontent.com/millardjn/rusty_sr/master/docs/logo_nn.png)![LogoLin](https://raw.githubusercontent.com/millardjn/rusty_sr/master/docs/logo_lin.png)![Logo](https://raw.githubusercontent.com/millardjn/rusty_sr/master/docs/logo_rs.png)

一个Rust超分辨率工具，当给予低分辨率图像时，利用深度学习推断出相应的高分辨率图像。
使用附带的训练有素的神经网络来升高您的图像，或轻松地训练您自己的专门的神经网络！

随意提出一般性讨论或提出任何问题的问题。

## 使用
升级图像:  
`rusty_sr.exe <INPUT_FILE> <OUTPUT_FILE>`  

推荐使用PNG输出文件。
更多的选项：  
`rusty_sr.exe --help`  
`rusty_sr.exe train --help`  

## 搭建
获取rust编译器（rustc）使用[rustup]（https://rustup.rs）。为了最好的性能编译使用环境变量 `RUSTFLAGS="-C target-cpu=native" `和构建发布模式 `cargo build --release`。
或在一行中： `cargo rustc --release -- -C target-cpu=native`.  

## 事例
Set14 Cartoon  
![CartoonLowRes](https://raw.githubusercontent.com/millardjn/rusty_sr/master/docs/cartoon_nn.png)![Cartoon](https://raw.githubusercontent.com/millardjn/rusty_sr/master/docs/cartoon_rsa.png)

Set14 Butterfly  
![ButterflyLowRes](https://raw.githubusercontent.com/millardjn/rusty_sr/master/docs/butterfly_nn.png)![Butterfly](https://raw.githubusercontent.com/millardjn/rusty_sr/master/docs/butterfly_rs.png)

Bank Lobby (test image for [Neural Enhance](https://github.com/alexjc/neural-enhance))  
CC-BY-SA @benarent  
![BankLowRes](https://raw.githubusercontent.com/millardjn/rusty_sr/master/docs/bank_nn.png)![Bank](https://raw.githubusercontent.com/millardjn/rusty_sr/master/docs/bank_rs.png)

## 注意
以高噪声或者jpeg文物拍摄高分辨率图像可能会产生较差的结果。输入和输出颜色空间名义上是sRGB。

## License
MIT


# Mac下实践
```
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
git clone https://github.com/millardjn/rusty_sr.git
cd rusty_sr
cargo rustc --release -- -C target-cpu=native
cd target/release      //这里面就生成了一个rusty_sr可以执行文件
./rusty_sr butterfly_nn.png butt.png //时间老长老长了。。。。。。。。
```