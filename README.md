# Zenn CLI

- [📘 How to install ZENN-CLI](https://zenn.dev/zenn/articles/install-zenn-cli)
- [📘 How to use](https://zenn.dev/zenn/articles/zenn-cli-guide)

# 記事作成

markdown テンプレートを作成

```
$ npx zenn new:article
```

記事の md ファイルは articles ディレクトリにファイルを入れる

# プレビュー

```
$ npx zenn preview # プレビュー開始
```

# 記事公開

- テンプレートの published を true にする

```
---
// 記事作成コマンドで作られるテンプレート

title: "" # 記事のタイトル
emoji: "😸" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: [] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---
ここから本文を書く
```

- リポジトリにプッシュされると自動的にデプロイが開始される。

# 公開予約

以下のように'published_at'のパラメーターを追加してプッシュ

```
published: true # trueを指定する
published_at: 2050-06-12 09:03 # 未来の日時を指定する
```

# 記事の更新

- ファイルを更新してプッシュするだけ

# 記事の削除

※ファイルを削除しても zenn 上から削除されない。
ファイル削除　＆zenn のダッシュボードから削除
