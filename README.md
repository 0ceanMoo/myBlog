# myBlog 運営設計メモ

---

## このリポジトリの目的

技術記事を書いて、ZennとQiitaに公開するための記事管理リポジトリ。
`articles/` のMDファイルを編集してmainにマージすると、ZennとQiitaに自動投稿される。

---

## ディレクトリ構成

```
myBlog/
├── drafts/                      ← 素材・ラフ・情報集め（非公開）
│   └── 記事名/
│       ├── material.md          ← 素材メモ・情報整理
│       └── images/              ← 作業中のスクショなど
│
├── articles/                    ← 公開用の正式記事（ZennとQiita共通）
│   └── 記事名.md
│
├── images/                      ← 公開用画像（Zenn・Qiita両方で使う）
│   └── filename.png
│
├── .github/
│   └── workflows/
│       └── qiita-publish.yml   ← QiitaへのGitHub Actions自動投稿
│
├── .qiita-mapping.json          ← Qiita記事IDの管理ファイル（自動更新）
└── package.json                 ← Zenn CLI設定
```

### `drafts/` の役割

- 素材集め・ラフ・メモ・スクリーンショット置き場
- 公開しない。Zenn / Qiita には関係ない
- 記事ごとにサブディレクトリを作る。散らかしてOK

### `articles/` の役割

- ZennとQiitaに投稿する正式記事
- ファイル名がそのまま記事のスラッグ（URL）になる
- mainにpushされるとZennに自動同期、GitHub ActionsがQiitaにも投稿する

### `images/` の役割

- Zenn・Qiita両方で使う公開用画像を置く
- 記事内で `/images/filename.png` と書くとZennでそのまま使える
- Qiitaへの投稿時はGitHub ActionsがGitHub raw URLに自動変換する

---

## ブランチ運用方針

| ブランチ | 役割 |
|---------|------|
| 作業ブランチ（名前は自由） | 執筆・編集作業用 |
| `main` | 公開済み記事の管理。マージ＝公開のトリガー |

作業ブランチで執筆 → `main` にPR → マージしたタイミングでZenn・Qiitaに同時公開される。

---

## 記事を書く流れ

```
1. mainから作業ブランチを切る
2. drafts/記事名/ で素材を集め・ラフを書く（Claude活用）
3. articles/記事名.md に正式記事を書く（Obsidianで編集・スクショ挿入）
4. images/ に画像を置く（必要な場合）
5. GitHubにpush（作業ブランチ）
6. mainにPRを作成してマージ
7. Zennに自動同期・Qiitaに自動投稿
```

---

## フロントマターの書き方

記事ファイルの先頭に以下の形式で書く。ZennとQiitaのフィールドを1ファイルに共存させる。

```yaml
---
title: "記事タイトル"
emoji: "😸"        # Zenn用
type: "tech"       # Zenn用: tech（技術記事）or idea（アイデア）
topics:            # Zenn用タグ（1〜5個）
  - topic1
  - topic2
published: false   # Zenn用: false=下書き、true=公開
tags:              # Qiita用タグ（必須）
  - tag1
  - tag2
private: false     # Qiita用: false=公開、true=限定公開
---
```

**記事ファイルには `id` や `updated_at` は書かない。**
QiitaのIDは `.qiita-mapping.json` で管理する。

---

## 画像の扱い

### 記事への埋め込み方

```markdown
![説明](/images/filename.png)
```

### 動作

| 環境 | 挙動 |
|------|------|
| Zennローカルプレビュー（`npx zenn preview`） | `/images/` をそのまま参照できる |
| Zenn本番 | `/images/` をそのまま参照できる |
| Qiita（GitHub Actions経由） | `https://raw.githubusercontent.com/0ceanMoo/myBlog/main/images/` に自動変換 |

### 注意

- 画像は必ず先にGitHubにpushしてからでないとQiitaで表示されない
- ローカルでQiita向けのプレビューはできない（raw URLはpush前に存在しないため）

---

## Qiita ID管理の仕組み

`.qiita-mapping.json` でスラッグとQiita記事IDを対応管理している。

```json
{
  "記事スラッグ": "QiitaのID"
}
```

- 初回投稿時にGitHub Actionsが自動でIDを追記する
- 記事ファイル（`articles/`）は書き換えない
- このファイルはgit管理対象なのでpull後に最新の状態になる

---

## GitHub Actionsワークフローの仕組み

`articles/` に変更があって `main` にpushされたときに動作する。

```
1. articles/*.md を public/ にコピー
2. .qiita-mapping.json からQiita IDを注入
3. 記事内の /images/ を GitHub raw URL に置換
4. qiita publish --all を実行
5. 新しいIDがあれば .qiita-mapping.json を更新してcommit
```

Zennへの同期はGitHubリポジトリ連携により自動で行われる（Zenn側がmainを監視）。

---

## 使うツール

| ツール | 用途 |
|--------|------|
| Claude | 素材集め・構成・下書き |
| Obsidian | 記事の編集・プレビュー（vaultとして開く） |
| Zenn CLI | ローカルプレビュー（`npx zenn preview`） |
| GitHub | バージョン管理・自動投稿のトリガー |
| GitHub Actions | Qiitaへの自動投稿 |

---

## Obsidianで使うときの注意

- `[[ノート名]]` や `![[ファイル名]]` などのObsidian独自記法は使わない
- 標準Markdownで書けばZenn・Qiitaにそのまま使える

---

## セットアップ手順（初回のみ）

1. GitHubにリポジトリを作成してpush
2. Zenn CLI のインストール・初期化
   ```bash
   npm init -y
   npm install zenn-cli
   npx zenn init
   ```
3. Zenn管理画面でGitHubリポジトリを連携
4. Qiita CLI のインストール・ログイン
   ```bash
   npm install -g @qiita/qiita-cli
   qiita login
   ```
   スコープは `read_qiita`・`write_qiita` を選択
5. GitHubリポジトリのSecretsに `QIITA_TOKEN` を登録
   - Settings → Secrets and variables → Actions → New repository secret

---

## 2台目以降のMacでのセットアップ

```bash
git clone https://github.com/0ceanMoo/myBlog.git
cd myBlog
npm install
```

`package.json` から依存関係が復元されるので `npm init` や `npx zenn init` は不要。

### Qiita CLIの再ログイン

Qiitaのトークンはマシンごとにローカル保存されるため、新しいMacでは再度ログインが必要。

```bash
npm install -g @qiita/qiita-cli
qiita login
```

ブラウザが開くので、スコープ `read_qiita`・`write_qiita` を選択してトークンを発行・入力する。
トークンの有効期限はない（自分で削除しない限り使い続けられる）。
