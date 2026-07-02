---
title: テスト記事
emoji: "🧪"
type: "tech"
topics:
  - test
published: false
tags:
  - テスト
private: true
---

## Zenn記法変換テスト

ZennからQiitaへの記法変換が正しく動作するか確認するためのテスト記事です。

### :::message の変換確認

以下はZennの `:::message` 記法です。Qiitaでは `:::note` に変換されるはずです。

:::message
これは通常のメッセージです。Qiitaでは note スタイルで表示されるはずです。
:::

### :::message alert の変換確認

以下はZennの `:::message alert` 記法です。Qiitaでは `:::note warn` に変換されるはずです。

:::message alert
これは警告メッセージです。Qiitaでは warn スタイルで表示されるはずです。
:::

### 通常のコンテンツ

変換処理が通常のテキストに影響しないことも確認します。

```bash
echo "hello world"
```
