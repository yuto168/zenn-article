---
title: ""
emoji: "👏"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

Ruby Silver の取得に向けて学習していく中でハッシュのメソッドの種類が多く困惑しそうなので、直感的にわかりにくいものや戻り値が困惑するものを中心にまとめます。
自分と同じく Ruby に触れて間もない方に役立てば幸いです。

# 環境

Ruby: 3

# 破壊的変更

## delete

指定したキーの値を削除

戻り値

- 対象が存在：キーに対応する値
- 対象が不在：nil

```ruby
animal = {dog: "犬", cat:"猫", bird: "鳥"}
delete(dog)
# => 犬

animal
# => {dog: "犬", cat:"猫"}
```

## shift

先頭のプロパティを取り除く

戻り値

- 対象が存在：削除対象のキーと値を要素に持つ配列
- 対象が不在：nil

```ruby
animal = {dog: "犬", cat:"猫", bird: "鳥"}
animal.shift
# => ["dog": "犬"]

animal
# => {cat:"猫", bird: "鳥"}
```
