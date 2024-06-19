---
title: "[Git]VSCode　Devcontainer内からGit操作をする手順"
emoji: "🐥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Devcontainer", "git", "frontend"]
published: true
---

こんにちは。mycom です。

環境作成のために VSCode の Devcontainer を使って コンテナでの開発をしてみましたが、、コンテナ内から git の ssh 接続がうまくできず push などできない状態になりました。ちょっと沼ったので、次回のためにメモを残しておきます。

# 環境

Windows の WSL を介して Ubuntu 20.04 LTS を使用しています。

- VSCode: 1.90.1
- Ubuntu: 20.04

# 概要

結論をいうと、ssh エージェントを起動して秘密鍵を登録することで、コンテナ内で git の操作ができるようになります。

> The Dev Containers extension provides out of the box support for using local Git credentials from inside a container. In this section, we'll walk through the two supported options.

どうやら devcontainer では、ローカルの git のクレデンシャルを使うことができるようです。
基本的には、ホスト側の ssh エージェントを使って、コンテナ内で git を使うことができるようです。
そのため、コンテナ内に ssh の秘密鍵をコピーするといった危険なことはする必要がないです。

GitHub への ssh 接続が済んでいる人はエージェントを起動するのみで、コンテナ内で git を使うことができるようになります。

# ホスト側の作業

[WSL2 のインストールから GitHub への SSH 接続まで
](https://zenn.dev/claustra01/articles/d5b31d0ffa0eb2)

- ssh キー生成

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

- ssh-agent を起動

```bash
eval "$(ssh-agent -s)"
```

- ssh エージェントに秘密鍵を登録していない場合は、以下のコマンドで登録する

```bash
ssh-add ~/.ssh/id_ed25519
```

GitHub アカウントへの公開鍵の登録は下記を参考

[GitHub に公開鍵を登録する](https://docs.github.com/ja/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

## 補足

ssh-agent を毎回起動するのは面倒なので、ターミナルを開くたびに自動で ssh-agent が起動するように設定すると便利です。

以下は.bashrc に追記する例です。

```bash: ~/.bashrc
if [ -z "$SSH_AUTH_SOCK" ] ; then
  eval `ssh-agent -s`
  ssh-add ~/.ssh/id_ed25519
  ssh-add -L
fi
```

# Devcontainer 内

- SSH 接続テストを行う

```bash
$ ssh -T git@github.com
```

無事通れば成功。
おそらく push 等も成功するはず。

# 参考ページ

https://zenn.dev/perilla/scraps/351f43bfcd13ec

https://docs.github.com/ja/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

https://blog.systemi.co.jp/sharing-ssh-key-with-container/

https://code.visualstudio.com/remote/advancedcontainers/sharing-git-credentials
