---
title: zshrcに定義している関数
date: 2023-07-08 10:00:00 +0900
categories: [Zsh]
tags: [zsh]
pin: false
---

## 概要

- zshrcに定義している関数をメモとして追加していく

## zshrcに定義している関数
### git cloneでリポジトリ名だけ指定し、クローンを実行する
- githubを開いて、clone用のURLをコピーする手間を減らす

``` bash
function gc() {
  git clone git@github.com:SOutaHI/$1.git
}

```

### gitのブランチ変更
- 入力するコマンドを減らす

``` bash
function gs() {
  git switch -c $1
}

```
- aliasの方がいいかも

``` bash
alias gc='git switch -c'
```
