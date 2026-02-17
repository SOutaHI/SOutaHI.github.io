---
title: Ubuntu 24.04のキーボードレイアウトで日本語配列とUS配列を切り替える 
date: 2026-02-15 15:45:00 +0900
categories: [Ubuntu]
tags: [ubuntu 24.04, mozc]
pin: false
---


## 環境
| 項目 | 内容 |
|------|------|
| CPU | 11th Gen Intel(R) Core(TM) i9-11900H @ 2.50GHz |
| OS | Ubuntu 24.04.4 LTS |


## 問題
- 日本語配列のキーボードが搭載されているノートPCにおいてUS配列のキーボードを使用したい
- 日本語配列のレイアウトとUS配列のレイアウトを切り替えられるようにする

## 手順
1. Mozcをインストールする
    ``` bash 
    $ sudo apt update && sudo apt install ibus-mozc
    ```
2. Mozcの設定を開き、IMEのActivationとDeactivationのKeymapを変更する
    1. Desktopのヘッダーの言語マークをクリックし、Tools > Properties > General > Keymap > Customizeを選択する
    2. 次の表の用に変更する
        | Mode | Key | Command |
        |------|-----|---------|
        | Precomposition | {好きなKey} | Deactive IME |
        | DirectInput | {好きなKey} | Active IME |

3. 使用するキーボードのレイアウトに合わせてレイアウトを変更する
    - 日本語配列を使用する場合
        ```bash
        $ settings set org.gnome.desktop.input-sources sources "[('ibus', 'mozc-jp'), ('xkb', 'jp')]"
        $ setxtbmap jp 
        ```
    - US配列を使用する場合
        ```bash
        $ settings set org.gnome.desktop.input-sources sources "[('ibus', 'mozc-jp'), ('xkb', 'us')]"
        $ setxkbmap us
        ```
4. IBusの再起動
- 以下のコマンドを実行し、IBusを再起動すると設定が反映されます。
    ```bash
    killall ibus-daemon && ibus-daemon -dx
    ```

## 詳細
### IME
- Input Method Editorの略。日本語や中国をを入力するためのソフトウェアのカテゴリー名。

### Mozc
- Googleが開発したIME（日本語入力システム）。
- IBusからの入力を日本語に変換し、IBusに戻している。
- Mozcはインストールすると、必要なときにIBusの子プロセスとして実行される。
    - pstreeでmozcを確認すると、mozc_serverとmozc_redererがibus-engine-mozcの子プロセスとして実行されている。
        ```bash
        $ pstree -p | grep -A 200 mozc
               |               |-ibus-daemon(36094)-+-ibus-dconf(36113)-+-{ibus-dconf}(36119)
               |               |                    |                   |-{ibus-dconf}(36120)
               |               |                    |                   |-{ibus-dconf}(36126)
               |               |                    |                   `-{ibus-dconf}(36130)
               |               |                    |-ibus-engine-moz(36141)-+-mozc_renderer(36409)-+-{mozc_renderer}(36410)
               |               |                    |                        |                      |-{mozc_renderer}(36411)
               |               |                    |                        |                      `-{mozc_renderer}(36412)
               |               |                    |                        |-mozc_server(36148)-+-{mozc_server}(36150)
               |               |                    |                        |                    `-{mozc_server}(36151)
        ```
    - ibus-daemonはsystemd --userが実行しており、D-Busサービスとして登録されている
        ```bash 
        $ pstree -aps $(pgrep ibus-daemon)
        systemd,1 splash
          └─systemd,2674 --user
              └─ibus-daemon,36094 -drx
        ```
    - Mozcを再起動したい場合は、ibus-daemonをKillして、再度ibus-daemonを実行する。
        - ibus-daemonのオプションのdはデーモン実行指定、xはX window Systemの入力メソッドを有効化するオプション。
            ```bash
            $ killall ibus-daemon && ibus-daemon -dx
            ```

### キーボードレイアウト
- キーボードレイアウトはX11やWaylandが管理している。
    - 現在使用しているディスプレイサーバーは環境変数から確認できる。
        ```bash
        $ echo $XDG_SESSION_TYPE
        x11
        ```
    - システムで設定されているレイアウトはlocalectlから確認できる。
        ```bash
        $ localectl status
        System Locale: LANG=en_US.UTF-8
        VC Keymap: (unset)         
        X11 Layout: jp
        X11 Model: pc105
        ```
    - 実際に動いているX11のセッションはsetxkbampから確認できる。
        ```bash
        $ setxkbmap -query
        rules:      evdev
        model:      pc105+inet
        layout:     us,us
        variant:    ,
        ```
    - 上記のように、システムで設定しているレイアウトとX11のセッションで設定されているレイアウトが異なる場合には、GNOMEが設定を書き換えている可能性がある。GNOMEが管理している入力ソースのリストからGNOMEが設定を書き換えているかできる。
        - 以下の実行例では、xcbがキーボードレイアウトのタイプであり、これを'us'に設定している。
            ```bash
            gsettings get org.gnome.desktop.input-sources sources
            [('ibus', 'mozc-jp'), ('xkb', 'us')]
            ```

- キーボードレイアウトを変更したい場合には、Mozcが設定しているレイアウトを変更し、Mozcを再起動する
    - レイアウトを切り替える。
        ```bash
        # 現在のレイアウトの確認
        $ gsettings set org.gnome.desktop.input-sources sources
        # レイアウトの変更（日本語配列に設定）
        $ settings set org.gnome.desktop.input-sources sources "[('ibus', 'mozc-jp'), ('xkb', 'jp')]"
        # レイアウトの変更（英語配列に変更）
        $ settings set org.gnome.desktop.input-sources sources "[('ibus', 'mozc-jp'), ('xkb', 'us')]"
        ```
    - Mozcを再起動する。
        ```bash
        $ killall ibus-deamon && ibus-daemon -dx
        ```
    - 日本語配列からUS配列に変更する際に上手く設定できない場合には、setxkbmapからUS配列に変更することもできる
        ```bash
        $ setxkbmap us
        ````

- 以下のようなスクリプトを用意して、aliasを登録しておくと切り替えが容易になる。
    - キーボードレイアウト切り替えスクリプト
        ```bash:change_keyboard_layout.sh
        #!/bin/bash
        set -e

        TARGET_LAYOUT_LIST=("us" "jp")
        TARGET_LAYOUT=$1

        check_current_layout() {
            CURRENT_LAYOUT=$(setxkbmap -query | grep layout | awk '{print $2}')
            echo "Current keyboard layout: $CURRENT_LAYOUT"
        }

        # check TARGET_LAYOUT is set
        if [ -z "$TARGET_LAYOUT" ] || [[ ! " ${TARGET_LAYOUT_LIST[@]} " =~ " ${TARGET_LAYOUT} " ]]; then
            echo "TARGET_LAYOUT is not set. Please set it to the desired keyboard layout (us or jp)."
            exit 1;
        fi

        # set keyboard layout
        check_current_layout
        echo "Setting keyboard layout to $TARGET_LAYOUT..."
        gsettings set org.gnome.desktop.input-sources sources "[('ibus', 'mozc-jp'),('xkb', '$TARGET_LAYOUT')]"
        setxkbmap $TARGET_LAYOUT
        killall ibus-daemon && ibus-daemon -dx

        # confirm current layout
        check_current_layout
        ```
    - ~/.bashrcにaliasとして登録
        ```bash
        alias change_keyboard_layout='{スクリプトまでのPATH}/change_keyboard_layout.ch'
        ```
    - 実行例
        ```bash
        # US配列に切り替え
        change_keyboard_layout us
        # 日本語配列に切り替え
        change_keyboard_layout jp
        ```