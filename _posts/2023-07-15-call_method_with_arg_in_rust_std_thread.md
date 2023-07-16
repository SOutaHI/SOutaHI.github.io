---
title: Rustのstd::threadの中でCallする関数に引数を渡す
date: 2023-07-15 10:00:00 +0900
categories: [Rust, Thread]
tags: [rust, thread]
pin: false
---


## 概要
- rustのstd::threadの中でCallする関数に、引数を渡す
- std::threadの中で、引数を渡すため、渡す引数が他の関数から参照可能な状態になっていると、コンパイラが通らない
    - 引数がイミュータブルな変数になっている場合には、問題ない
    - 引数がミュータブルな変数になっている場合には、上記の問題が発生する

## 参考
- [rust doc - チャンネルと所有権の転送](https://doc.rust-jp.rs/book-ja/ch16-02-message-passing.html)
- [rust doc - 所有権とムーブ](https://doc.rust-jp.rs/rust-by-example-ja/scope/move.html)

## rustのstd::threadの中でCallする関数に引数を渡す
- 例えば、次のようなコードになっているとする

``` rust
std::thread::spawn(|| {
        let rt = tokio::runtime::Runtime::new().unwrap();
        rt.block_on(async {
            // test_proc()に、引数を渡したい
            crate::test_proc().await;
        });
    });
```

- このとき、std::thread::spawnの中でCallしている関数に引数を渡したいとする
- 単に引数を渡してしまうと、Linterに引っかかってしまう

``` rust
std::thread::spawn(|| {
        let rt = tokio::runtime::Runtime::new().unwrap();
        rt.block_on(async {
            // some argsがThread Safeでないというエラーが発生する
            crate::test_proc(some_args).await;
        });
    });
```

- これは、渡している引数がThread Safeな引数になっていないため派生する
- thread safeな引数にするために、moveによりsome_argsの所有者を変更する


``` rust
// moveを追加
std::thread::spawn( move || {
        let rt = tokio::runtime::Runtime::new().unwrap();
        rt.block_on(async {
            // test_proc()に、引数を渡したい
            crate::test_proc(some_args).await;
        });
    });
```
