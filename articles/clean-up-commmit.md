---
title: "【git】rebaseでプルリク作る前のコミット整理を効率化したい"
emoji: "🐿️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [git]
published: false
---

# 背景

プルリクを出す前、作業中に作ったコミットを整理する必要があります。

しかし整理している途中、"この変更、前のコミットに含めるべきやったな"って感じでミスがあった場合、いちいち reset するのは手間だしストレスたまります。

git rebase を使えばコミットを一気に整理できるので、その手順を備忘録としてまとめます。

# 対象

- git rebase になじみが薄い人

# 複数のコミットを一つにまとめたい

## ケース

すでにコミット済みの commit2 に commit3 をまとめればよかった....(直前の commit だとリセットすりゃ楽ですが例ということで勘弁。。。)

![alt text](/images/image.png)

## 手順

```sh
git log --oneline
```

![alt text](/images/image-1.png)

commit2~3 をまとめたいので commit1 に移動

```sh
git rebase --i {{コミット番号}}
```

コマンドの結果が出ます。
この時コメントでその後のコマンドの詳細が載っています。

```sh
pick 7dfbbc5 commit2
pick c5e4e0a commit3

# Rebase 6904133..c5e4e0a onto 6904133 (2 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
......
```

今回のケースでは commit2 と commit3 をまとめたいので、Squash を使います。

コメントにも書かれている通りで

```sh
# s, squash <commit> = use commit, but meld into previous commit
```

"指定したコミットをそのまま直前のコミット"に取り込むという意味です。

commmit3 を commit2 に取り込みたいので、次のように編集します。

vim での編集に慣れていない人はこちらが参考になります。
https://qiita.com/hide/items/5bfe5b322872c61a6896

```sh
pick 7dfbbc5 commit2
s c5e4e0a
```

コマンド結果が表示されます。
ここでコメントを変更できます。

そのままでよければ上書き保存します。

```
# This is a combination of 2 commits.
# This is the 1st commit message:

commit2

# This is the commit message #2:

commit3

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Fri Apr 12 10:30:07 2024 +0900
#
# interactive rebase in progress; onto 6904133
```

これで commit3 の内容が commit2 にまとめられます。

![alt text](/images/image-2.png)

# ２つ以上の commit をまとめたい

## ケース

commit3 と commit4 って commit2 にまとめるべきやったな...

![alt text](/images/image-3.png)

## 手順

上述の git reset までは同じ

```sh
pick 7dfbbc5 commit2
pick c5e4e0a commit3
pick c8129f1 commit4
```

commit2 以外はすべて squash します。

```sh
pick 7dfbbc5 commit2
s c5e4e0a
s c8129f1
```

まとめられました。

![alt text](/images/image-4.png)

今までは Source Tree 等の GUI を使って reset ばかりやっていましたが、作業効率を上げるにはコマンドの方が早いですね。
