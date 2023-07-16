---
title: GoogleのDIフレームワーク　fruit
date: 2023-07-15 10:00:00 +0900
categories: [Cpp, Dependency Injection, DI]
tags: [cpp, dependency injection, di]
pin: false
---

## 概要
- C++でDIフレームワークとかを探すと, [Hypodermic](https://github.com/ybainier/Hypodermic)とかが出てくる
  - 継続的にサポートされている（2023年7月確認時点）
- 上記以外のフレームワークにgoogleのfruitというフレームワークがあり、メモとして残しておく

## google/fruit
- [google/fruit](https://github.com/google/fruit)
- [google/fruit Wiki](https://github.com/google/fruit/wiki)
- googleのワークスペースで公開されているが、Googleの公式プロジェクトではない
   - また、Googleによる公式なサポートもない
- 作者は、Marco Polettiであり、現在の正規開発者は、彼のみである
- ライセンスは、Apatch-2.0
- fruit/fruit.hをリンクすると、使用できる
  -  自動的にコードは生成されない

### 利点
- コンポーネント内の型のバインディングを変更した後において、 コンポーネントによって公開されるインターフェイスが同じである限り、コンパイルにおいては、コンポーネント自体を再コンパイルするだけで済む
- コンポーネントとそのコンポーネントを使用するインジェクターの再コンパイルは必要ない
- これにより、大規模なプロジェクトのコンパイルが、インジェクションする必要のあるすべてのクラスを含めるアプローチよりも高速になる

### チュートリアル
- https://github.com/google/fruit/wiki/tutorial:-getting-started

<!-- ### サンプルコードの実行
- fruit/exampleのhello_worldを実行する
- コードをクローンする
``` bash
mkdir -p workspace/di/ && cd workspace/di
git clone git@github.com:google/fruit.git
cd ./fruit/example/hello_world

```
- build

``` bash
mkdir -p workspace/di/ && cd workspace/di
git clone git@github.com:google/fruit.git
cd ./fruit/example/hello_world

``` -->





