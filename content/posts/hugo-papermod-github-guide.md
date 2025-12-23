---
title: "Hugo + PaperMod + GitHub Pagesでブログを公開する完全ガイド"
slug: "hugo-papermod-github-pages-guide"
description: "HugoとPaperModを使ってGitHub Pagesでブログを公開する方法を初心者向けに詳しく解説します。"
date: 2025-12-22T10:00:00+09:00
draft: false
tags: ["Hugo", "PaperMod", "GitHub Pages", "ブログ構築"]
---

# 🚀 Hugo + PaperMod + GitHub Pagesでブログを公開する完全ガイド

Hugoは高速で柔軟な静的サイトジェネレーターであり、PaperModは洗練されたテーマとして人気です。本記事では、**Hugo + PaperMod + GitHub Pages**を使って、初心者でも簡単にブログを公開する方法を詳しく解説します。

---

## 📚 目次

1. ✅ 前提条件
2. 🛠 Hugoサイトの新規作成
3. 🎨 PaperModテーマを導入
4. ✍ 初回記事の作成
5. 🔍 ローカルでプレビュー確認
6. 🌐 GitHubリポジトリの準備
7. 🤖 GitHub Actionsで自動デプロイ設定
8. ✅ 公開ブログを確認
9. 🔗 公開URLのルール
10. 💡 補足ポイント
11. ⚠ よくあるエラーと対処法

---

## ✅ 前提条件

- **Git** と **Hugo（Extended版）** がインストール済み
- **GitHubアカウント**を持っている

---

## 🛠 Hugoサイトの新規作成

```shell
hugo new site blog --format yaml  # 新しいHugoサイトを作成し、設定ファイルをYAML形式に
cd blog
git init  # Gitリポジトリを初期化
```

---

## 🎨 PaperModテーマを導入

```shell
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod  # PaperModテーマを追加
```

`hugo.yaml`に以下を追加：

```yaml
theme: ["PaperMod"]
```

> **注意**: GitHub Actionsでテーマを反映させるため、`actions/checkout` の `submodules: true` を必ず設定してください。

---

## ✍ 初回記事の作成

```shell
hugo new posts/my-first-post.md  # 初回記事ファイルを作成
```

記事ファイルを編集：

```yaml
---
title: "My First Post"
date: 2025-12-22T10:00:00+09:00
draft: false
---
Hugo & PaperModでの初投稿です！
```

---

## 🔍 ローカルでプレビュー確認

```shell
hugo server -D  # ローカルサーバーを起動し、ドラフト記事も表示
```

`http://localhost:1313/`でデザインと本文を確認。

---

## 🌐 GitHubリポジトリの準備

1. GitHubで新リポジトリ（例：`blog`）を作成。
2. 以下を実行：

```shell
git remote add origin https://github.com/username/blog.git  # リモートリポジトリを追加
git branch -M main  # メインブランチに切り替え
git add .
git commit -m "初回投稿の準備"
git push -u origin main
```

---

## 🤖 GitHub Actionsで自動デプロイ設定

`.github/workflows/deploy.yml`を作成：

```yaml
name: Deploy Hugo site
on:
  push:
    branches: [ "main" ]
permissions:
  contents: write
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: true
          fetch-depth: 0
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: '0.153.1'
          extended: true
      - name: Build
        run: hugo --minify
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
          publish_branch: gh-pages
          enable_jekyll: false
          force_orphan: true
```

---

## ✅ 公開ブログを確認

`https://username.github.io/blog/` にアクセスし、記事が表示されれば成功！

---

## 🔗 公開URLのルール

- **ユーザーサイト**：リポジトリ名が `username.github.io` → `https://username.github.io/`
- **プロジェクトサイト**：その他のリポジトリ名 → `https://username.github.io/repository-name/`
`baseURL`を`hugo.yaml`に設定：

```yaml
baseURL: "https://username.github.io/blog/"
```

---

## 💡 補足ポイント

- `public/`フォルダは`.gitignore`に追加。
- PaperModのカスタマイズは`hugo.yaml`の`[params]`で調整可能。
- 独自ドメインを設定する場合は`baseURL`を変更し、GitHub Pagesで「Custom domain」を設定。
- **OGP/Twitterカード設定例**：

```yaml
params:
  images: ["images/og-image.png"]
  socialShare: true
  ShowShareButtons: true
  ShowReadingTime: true
```

---

## ⚠ よくあるエラーと対処法

### 1. テーマが反映されない

- 原因：`hugo.yaml` に `theme: ["PaperMod"]` が設定されていない。
- 対処：設定を確認し、`hugo server` で再起動。

### 2. 記事が表示されない

- 原因：`draft: true` のまま。
- 対処：`draft: false` に変更し、再ビルド。

### 3. GitHub Pagesで404エラー

- 原因：`baseURL` が正しく設定されていない。
- 対処：公開URLに合わせて `hugo.yaml` を修正。

### 4. GitHub Actionsが失敗する

- 原因：YAMLのインデントミスやトークン設定不備。
- 対処：`.github/workflows/deploy.yml` を再確認し、`github_token` が設定されているか確認。

---

## ✅ 最終チェック
- RSSフィード確認：`https://username.github.io/blog/index.xml`
- OGP確認：公開ページの `<meta property="og:image"...>` が意図した画像を指しているか

---
