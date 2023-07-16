---
title: localブランチの状態をremoteブランチの状態に一致させる
date: 2023-07-15 10:00:00 +0900
categories: [Git]
tags: [git]
pin: false
---

## 概要

- remote ブランチでマージされたブランチは、local ブランチでも削除したい
- 手動で実施するのではなく、別コマンドと同時に実行するようにする
  - 例えば、pull した時、同時に remote の状態に一致するようにする

## 参考

- [Git のローカルブランチの削除を簡単にした話](https://www.farend.co.jp/blog/2023/01/delete-git-local-branch/)
- [xargs - if condition and echo {}](https://stackoverflow.com/questions/9484368/xargs-if-condition-and-echo)

## マージされたブランチを削除する

- 次のコマンドを実行する
    - 1. リモートのMainブランチをPull
    - 2. マージ済みのブランチ名を取得
    - 3. mainを除いたブランチ名を取得
    - 4. マージ済みのブランチを削除

``` bash
git pull origin main && git branch --merged | grep -v 'main' | xargs git branch -d

```
## 各ブランチの状態を確認した後に、マージされたブランチを削除する
-　念のため、現状のローカルブランチの状態を確認し、問題無い場合には、ブランチを削除する
- zshで関数を定義する

### セットアップ
- zshrcに関数を定義する

``` bash
function clean_local_branches () {

    branches=`git pull origin main && git branch --merged | grep -v 'main' | grep -v 'remote'`
    for branch in $branches
    do
        read -p "remove ${branch} ? (y/N):" yn
        case "$yn" in [yY]*) ;; *) echo "skipped." ; continue ;; esac
        git branch -d ${branch}
    done
}

```

### 実行
- 実行すると、merge済みのブランチごとに、削除するかyn選択し、削除する

``` bash
$ clean_local_branches
remove a ? (y/N):y
Deleted branch a (was 0c600cb).
remove b ? (y/N):y
Deleted branch b (was 0c600cb).
remove c ? (y/N):y
Deleted branch c (was 0c600cb).
remove d ? (y/N):y
Deleted branch d (was 0c600cb).

```