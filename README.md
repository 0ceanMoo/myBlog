# myBlog 運営設計メモ

---

## このリポジトリの目的

技術記事を書いて、ZennとQiitaに公開するための記事管理リポジトリ。

---

## ディレクトリ構成

```
myBlog/
├── drafts/                    ← 素材・ラフ・情報集め（非公開）
│   └── 記事名/
│       ├── material.md        ← 素材メモ・情報整理
│       └── images/            ← 作業中のスクリーンショットなど
│
├── articles/                  ← 公開用の正式記事（Zenn CLI管理）
│   └── 記事名.md
│
├── .github/
│   └── workflows/
│       └── qiita-sync.yml    ← QiitaへのGitHub Actions自動投稿
│
└── package.json               ← Zenn CLI設定
```

### `drafts/` の役割

- 素材集め・ラフ・メモ・スクリーンショット置き場
- 公開しない。Zenn / Qiita には関係ない
- 記事ごとにサブディレクトリを作る
- 散らかしてOK

### `articles/` の役割

- Zenn CLI が管理する正式記事
- ここのMDをGitHubにpushするとZennに自動同期される
- Qiita CLIもここのMDを読んで投稿する
- ファイル名がそのまま記事のスラッグ（URL）になる

---

## 記事を書く流れ

```
1. drafts/記事名/ で素材を集め・ラフを書く（Claude活用）
2. articles/記事名.md に正式記事を書く（Obsidianで編集・スクショ挿入）
3. GitHub に push
4. Zenn に自動同期・Qiita に自動投稿
```

---

## 使うツール

| ツール | 用途 |
|--------|------|
| Claude | 素材集め・構成・下書き |
| Obsidian | 記事の編集・プレビュー（vaultとして開く） |
| Zenn CLI | 記事管理・プレビュー |
| GitHub | バージョン管理・自動投稿のトリガー |
| GitHub Actions | Qiitaへの自動投稿 |

---

## 公開先

| サービス | 連携方法 |
|----------|---------|
| Zenn | GitHubリポジトリ連携（push で自動同期） |
| Qiita | GitHub Actions（push でQiita CLIが自動投稿） |

---

## Obsidian で使うときの注意

- `[[ノート名]]` や `![[ファイル名]]` などのObsidian独自記法は使わない
- 標準Markdownで書けばZenn・Qiitaにそのまま使える

---

## セットアップ手順（初回のみ）

1. Zenn CLI のインストール・初期化
   ```bash
   npm init -y
   npm install zenn-cli
   npx zenn init
   ```
2. Zenn 管理画面でGitHubリポジトリを連携
3. Qiita CLI のインストール・ログイン
   ```bash
   npm install -g @qiita/qiita-cli
   qiita login
   ```
4. GitHub Actions に Qiita 自動投稿ワークフローを追加
5. GitHubにリポジトリを作成してpush
