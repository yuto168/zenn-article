---
title: "[Nuxt]API経由で文字コードがShift_JISのCSVファイルをダウンロードする"
emoji: "🐿️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Nuxt, JavaScript, vue]
published: true
---

案件で API 経由で Shift_JIS の CSV ファイルをダウンロードする必要がありましたが、その際に大分はまってしまいました。
他の記事に API 経由で Shift_JIS の CSV ファイルをダウンロードする記事があまり見当たらなかったのもあり、備忘録として残しておきます。

# 動作環境

- Nuxt3
- TypeScript

# はまったポイント

レスポンスは下記の想定です。

Shift_JIS でエンコードされた CSV ファイルをダウンロードする想定です。

```
<!-- API レスポンスヘッダ -->

content-disposition: attachment; filename="hoge.csv"
content-type: text/csv;
```

API を$fetch()で叩いて、CSV ファイルをダウンロードする処理を実装しました。

```ts : composables/csv.ts
/**CSVテキスト取得 */
const fetchCsv = async () => {
  const { data, error, pending } = await useAsyncData(() =>
    $fetch("/xxxx/xxxxx", {
      method: "POST",
    })
  );
};

/**ファイルダウンロード */
const downloadCsv = async () => {
  const { data } = await fetchCsv();
  const blob = new Blob([data.value], { type: "text/csv" });

  //　ダウンロード実行(今回の本筋ではない)
  const url = window.URL.createObjectURL(blob);
  const a = document.createElement("a");
  a.href = url;
  a.download = "sample.csv";
  a.click();
  window.URL.revokeObjectURL(url);
};
```

ダウンロードはうまくいきましたが、 CSV ファイルを開いてみると、文字化けしていました。

# 問題点

どうやら fetch()の Response や Blob は テキストを UTF-8 として扱っているようです。
そのため、何も考えずにデータを Blob に持たせてダウンロード実行しても、文字化けしてしまいました。

Shift_JIS でエンコードされた CSV ファイルをダウンロードする場合は、
バイナリとしてデータを受け取り、それを Blob に渡す必要があります。

# 解決策

以下のように response は バイナリ で受け取るよう設定し、それを Blob に渡すことで
エンコーディングの設定を保持したままダウンロードすることができます。

```ts
/**CSVテキスト取得 */
const fetchCsv = async () => {
  const { data, error, pending } = await useAsyncData(() =>
    $fetch("/xxxx/xxxxx", {
      method: "POST",
      responseType: "arrayBuffer", // バイナリデータとして受け取る
    })
  );
};

/**ファイルダウンロード */
const downloadCsv = async () => {
  const { data } = await fetchCsv(); // data.value = ArrayBuffer

  /**
   * ちなみにテキストをそのまま使う場合は、以下のように明示的にShift_JISでデコードする
   * const decoder = new TextDecoder("Shift_JIS");
   * const text = decoder.decode(data.value);
   */

  const blob = new Blob([data.value], { type: "text/csv" });

  const url = window.URL.createObjectURL(blob);
  const a = document.createElement("a");
  a.href = url;
  a.download = "sample.csv";
  a.click();
  window.URL.revokeObjectURL(url);
};
```

UTF-8 以外のエンコーディングのファイルをダウンロードする場合は、注意が必要なので忘れずにしたいですね。
