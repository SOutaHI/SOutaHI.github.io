---
title: 2つのブランチの差分の可視化
date: 2023-07-15 10:00:00 +0900
categories: [Git, VSCode]
tags: [git, vscode]
pin: false
---

## 概要

- ブランチ同士の差分を確認したい時が多々ある
- vscode の diff のように可視化すると確認しやすい
- vscode の gitLens の Extension を使用する

## 参考

- [vscode で git の異なる branch 間の差分を確認する方法](https://qiita.com/nabenabe0928/items/9f36ee4fe1af92c215f6)

## 手順

### 1. SOURCE CONTROL -> BRANCHES を選択する

- BRANCHES の折り畳まれている内容を展開しておく

### 2. BRANCHES のヘッダーの Compare with Working Tree を選択する

- 選択すると、FILE CHANGED や、SEARCH & COMPARE が表示される

![VSCode View](/assets/img/branch_visualization_2023_05_15.png){: .normal }
