---
title: zshでコマンド予測補完機能を有効化する
date: 2023-07-08 10:00:00 +0900
categories: [Zsh]
tags: [zsh]
pin: false
---

## 概要
- コマンドのすべてを毎回すべて入力する手間を減らしたい
- zshで、コマンド予測補完系のプラグインをインストールする

## 参考
- [n1snt/Oh my ZSH with zsh-autosuggestions zsh-syntax-highlighting zsh-fast-syntax-highlighting and zsh-autocomplete.md](https://gist.github.com/n1snt/454b879b8f0b7995740ae04c5fb5b7df)

## 環境
- zsh + oh-my-zsh

## コマンド入力予測補完
- 次のプラグインをインストールする
    - ターミナルのコマンド履歴に基づいてコマンド候補を表示、入力補完する
        - zsh-autosuggestions
        - zsh-autocomplete
    - コマンドの色付け
        - zsh-syntax-highlighting
        - zsh-fast-syntax-highlighting

### プラグインのインストール
- 次のコマンドで各プラグインをインストールする

``` bash
sudo apt install zsh-autosuggestions zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
git clone https://github.com/zdharma-continuum/fast-syntax-highlighting.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/fast-syntax-highlighting
git clone --depth 1 -- https://github.com/marlonrichert/zsh-autocomplete.git $ZSH_CUSTOM/plugins/zsh-autocomplete
```

### zshrcにプラグインを記載
- oh-my-zshのpluginに上記のプラグインを追加する

``` bash
plugins=(git zsh-autosuggestions zsh-syntax-highlighting fast-syntax-highlighting zsh-autocomplete)
```

## コマンド検索機能
- 標準のCtrl + Rでは探しづらい場合が多々あり、検索機能をリッチにしたい
- fzf(fuzzy finder)をインストールする

### fzfのインストール
- 次のコマンドでインストールする

``` bash
git clone https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```

### zshrcにfzfの読み込みコマンドを記載する
- 読み込みコマンド以外に、関連する関数も定義しておくと便利

``` bash
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh

```

## zshrcの設定内容
- 上記２つの機能の追加と、oh-my-zshの設定をまとめておく

``` bash
# oh my zsh
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="agnoster"
plugins=(git zsh-autosuggestions zsh-syntax-highlighting fast-syntax-highlighting zsh-autocomplete)

source $ZSH/oh-my-zsh.sh

[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh

```
