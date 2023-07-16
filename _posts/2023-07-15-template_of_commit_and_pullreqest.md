---
title: CommitとPull Requestのテンプレートを設定する
date: 2023-07-15 10:00:00 +0900
categories: [Git]
tags: [git]
pin: false
---


## 概要
- CommitメッセージとPull requestのメッセージをテンプレート化する
- 複数人で開発する際に、フォーマットに沿ったメッセージであれば、レビュー等のコードの共有の効率が向上する

## 参考
- [リポジトリ用のプルリクエストテンプレートの作成](https://docs.github.com/ja/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository)
- [Pull Requestのテンプレートを作って効率よくレビューしよう！](https://dev.classmethod.jp/articles/pull-request-template/)
- [GitHub pull request template](https://axolo.co/blog/p/part-3-github-pull-request-template)

## Commitテンプレートの設定
-　.git/.commit_templateに次の内容を記載する

``` bash
# Overview (Uncomment one of the following templates)
#✨ feat:
#  └  A new feature
#🐞 fix:
#  └  A bug fix
#📚 doc:
#  └  Documentation only changes
#🗑️   wastebasket:
#  └  remove ununeeded feature
#🚧  construction:
#  └  WIP(Work In Progress)
#💄 style:
#  └  Changes that do not affect the meaning of the code
#    (white-space, formatting, missing semi-colons, etc)
#🔨 refactor:
#  └  A code change that neither fixes a bug nor adds a featur
#🚀 perf:
#  └  A code change that improves performance
#🚨 test:
#  └  Adding missing or correcting existing tests
#👷 chore:
#  └  Changes to the build process or auxiliary tools and libraries
```

- /.gitconfigにテンプレートを設定する

``` bash
[commit]
    template = ~/.git/.commit_template
```

## Pull requestのテンプレートの設定
- pull requestのテンプレートは、リポジトリに設定する
    - [Github Docs - リポジトリ用のプルリクエストテンプレートの作成](https://docs.github.com/ja/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository)

- 例えば、次のような内容を含むと良さそう

```　md
## Describe your changes

## Issue ticket number and link

## Checklist before requesting a review
- [ ] I have performed a self-review of my code
- [ ] If it is a core feature, I have added thorough tests.
- [ ] Do we need to implement analytics?
- [ ] Will this be part of a product update? If yes, please write one phrase about this update.

```