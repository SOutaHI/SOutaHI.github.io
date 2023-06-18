---
title: 型アノテーションを自動化する
date: 2023-06-18 10:00:00 +0900
categories: [Python, AutoTypeAnnotation]
tags: [python, auto type anotation]
pin: false
---

## 概要
- 型を明示しておくことにより、Doumentを書く時やコードレビューする際にコードを説明する時などに理解しやすくする
- 理解しやすくなれば、理解するための時間が短縮できる

- 型アノテーションを自動化し、FormatterやLinterのように使用したい
    - コード量が多くなってくると、アノテーションのための時間も増えてしまう
    - アノテーションには時間をかけず、アルゴリズム/ロジックの方に時間を使いたい

## TypeAnnotaion用のコード
- ROSの公式の[サンプルコード](http://wiki.ros.org/ja/ROS/Tutorials/WritingPublisherSubscriber%28python%29)を使用する
    - talker.py

        ```py
        #!/usr/bin/env python
        import rospy
        from std_msgs.msg import String

        def talker():
            pub = rospy.Publisher('chatter', String, queue_size=10)
            rospy.init_node('talker', anonymous=True)
            r = rospy.Rate(10) # 10hz
            while not rospy.is_shutdown():
                str = "hello world %s"%rospy.get_time()
                rospy.loginfo(str)
                pub.publish(str)
                r.sleep()

        if __name__ == '__main__':
            try:
                talker()
            except rospy.ROSInterruptException: pass
        ```

    - main.py

        ```py
        #!/usr/bin/env python
        from talker import talker

        if __name__ == '__main__':
            talker()
        ```

## 自動型アノテーションツール
### [MonkeyType](https://github.com/Instagram/MonkeyType)
- Instagramが公開している静的型アノテーション生成ライブラリ
- install MonkeyType

```bash
pip install MonkyType
```

- MonkeyTypeは各モジュールのAnnotation結果をSQLlite形式のデータで出力する

- 実行

``` bash
# run　でAnotationを実行
$ monkeytype run main.py

# stub でAnotation結果を確認
$ monkeytype stub talker
def talker() -> None: ...

# applyでAnnotation結果を上書き
$ monkeytype apply talker
#!/usr/bin/env python
# license removed for brevity
import rospy
from std_msgs.msg import String

def talker() -> None:
    pub = rospy.Publisher('chatter', String, queue_size=10)
    rospy.init_node('talker', anonymous=True)
    r = rospy.Rate(10) # 10hz
    while not rospy.is_shutdown():
        str = "hello world %s"%rospy.get_time()
        rospy.loginfo(str)
        pub.publish(str)
        r.sleep()
```

## 型チェックツール
### MyPy
- mypyは型チェックのライブラリ
- install mypy

```bash
pip install mypy
```

- zshの場合は次の2行を.zshrcに追加する

```bash
export PATH="$PATH:/home/sota/.local/bin"
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh
```

- 実行結果
    - 事前にcatkin_wsのsetup.zshを読み込ませたが、rospyを認識できていない

``` bash
$ mypy talker.py
talker.py:3: error: Cannot find implementation or library stub for module named "rospy"
talker.py:3: note: See https://mypy.readthedocs.io/en/stable/running_mypy.html#missing-imports
talker.py:4: error: Cannot find implementation or library stub for module named "std_msgs.msg"
Found 2 errors in 1 file (checked 1 source file)
```

- findに失敗したLibraryを無視するオプション (--ignore-missing-imports)+ all optional error checking flag(--strict)を付けて実行

``` bash
$ mypy --ignore-missing-imports --strict talker.py
talker.py:6: error: Function is missing a return type annotation
talker.py:6: note: Use "-> None" if function does not return a value
Found 1 error in 1 file (checked 1 source file)
```

- Functionの返り値の型が無いというエラーがでおり、"-> None" を使えと言われているので、付けて再実行すると、型チェックをパスする

```bash
$ mypy --ignore-missing-imports --strict talker.py
Success: no issues found in 1 source file
```


### [Pyannotate](https://github.com/dropbox/pyannotate/tree/master)
- DropBoxが公開しているPEP-484準拠の自動型アノテーションライブラリ
- 最終更新が2年前であるため、使いづらいかも


## 参考
- PyannotateとMokeyTypeの比較
    - https://qiita.com/t2y/items/f16a30cb7c4aec8332c2
- MyPy
    - https://mypy-lang.org/
    - https://mypy.readthedocs.io/en/stable/getting_started.html
- TypeAnnotation in python
    - https://towardsdatascience.com/type-annotations-in-python-d90990b172dc
- Type Annotations in attrs
    - https://www.attrs.org/en/stable/types.html
- UnderStanding type annotation in Python
    - https://blog.logrocket.com/understanding-type-annotation-python/
