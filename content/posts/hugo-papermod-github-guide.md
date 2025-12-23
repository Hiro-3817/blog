---
title: "🚀 Hugo + PaperMod + GitHub Pagesでブログ公開【初心者向け完全ステップガイド】"
slug: "hugo-papermod-github-pages-guide"
description: "Hugo + PaperMod + GitHub Pagesを使って、ゼロからブログを公開するまでを完全ステップで解説。自動デプロイ（GitHub Actions）、公開URLの設定、OGP、よくあるエラーの回避までこの1本でOK。"
date: 2025-12-22T10:00:00+09:00
lastmod: 2025-12-23T10:00:00+09:00
draft: false
categories: ["Web開発", "サイト構築"]
tags: ["Hugo", "PaperMod", "GitHub Pages", "GitHub Actions", "静的サイト", "SEO"]
author: "HNEST"
images: ["images/og-image.png"]
---

> **このガイドのゴール**：Hugo + PaperMod を使ったブログを GitHub Pages に公開し、以降は記事を push するだけで自動更新できる状態にする。

---

## ✅ 前提条件（必要な環境）
- **Git** と **Hugo（Extended版）** をインストール済み
- **GitHubアカウント**を保有
- コマンドラインが使える（macOS/Linux/WSL いずれもOK）

> **Extended版が必要な理由**：PaperModなど一部テーマはSCSSのコンパイルにExtended版を使います。

---

## 🛠️ Hugoサイトの新規作成
```shell
hugo new site blog --format yaml   # 新規Hugoサイト（設定ファイルをYAMLで作成）
cd blog
git init                            # Gitリポジトリを初期化
```

---

## 🎨 PaperModテーマの導入
最も簡単なのは **Git Submodule** でテーマを取り込む方法です。

```shell
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

`hugo.yaml` にテーマを指定します：

```yaml
theme: ["PaperMod"]
```

> **注意**：GitHub Actions でビルドする際は `actions/checkout` の `submodules: true` を必ず設定してください（後述）。

> **代替案**：Hugo Modulesでテーマを管理する方法もあります（`hugo mod init` → `module.imports`にPaperModを指定）。慣れるまではSubmoduleが分かりやすいです。

---

## 📝 Hugoの基本設定（`hugo.yaml`）
最低限の設定例を示します。後から拡張できます。

```yaml
baseURL: "https://username.github.io/blog/"  # 後で必ず自分の公開URLに合わせて変更
languageCode: "ja-jp"
title: "My Hugo Blog"
theme: ["PaperMod"]
paginate: 10
outputs:
  home: ["HTML", "RSS", "JSON"]
params:
  defaultTheme: "auto"            # ダーク/ライト切替
  ShowShareButtons: true
  ShowReadingTime: true
  ShowCodeCopyButtons: true
  showtoc: true                    # 記事内目次
  images: ["images/og-image.png"] # OGP用
  author: "HNEST"
menu:
  main:
    - identifier: archives
      name: Archives
      url: /archives/
      weight: 10
    - identifier: tags
      name: Tags
      url: /tags/
      weight: 20
```

---

## ✍ 初回記事の作成とローカル確認
記事を作成します：

```shell
hugo new posts/my-first-post.md
```

作成されたファイル（`content/posts/my-first-post.md`）を編集：

```yaml
---
title: "My First Post"
date: 2025-12-22T10:00:00+09:00
draft: false
---
Hugo & PaperModでの初投稿です！
```

ローカルでプレビュー：

```shell
hugo server -D    # ドラフト記事も表示して動作確認
```

ブラウザで `http://localhost:1313/` にアクセスして、レイアウトと本文を確認します。

---

## 🌐 GitHubリポジトリの準備と初回 push
GitHubで新規リポジトリ（例：`blog`）を作成し、ローカルから初回pushします。

```shell
git remote add origin https://github.com/username/blog.git
git branch -M main                   # デフォルトブランチをmainに変更
git add .
git commit -m "初回投稿の準備"
git push -u origin main
```

> `.gitignore` に `public/` を追加しておきましょう（ビルド成果物はGit管理しない）。

---

## 🤖 GitHub Actions で自動デプロイ
`gh-pages` ブランチにビルド成果物を自動公開するワークフロー例です。

`.github/workflows/deploy.yml` を作成：

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
          submodules: true   # PaperModをSubmoduleで使う場合は必須
          fetch-depth: 0
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: '0.153.1'  # 使用するHugoのバージョンを固定（Extended版）
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

> **補足**：ユーザーサイト（`username.github.io`）やプロジェクトサイトのいずれでも使えます。Pagesの設定で公開ブランチを `gh-pages` に指定してください。

---

## 🔗 公開URL（baseURL）の正しい設定
- **ユーザーサイト**：リポジトリが `username.github.io` → 公開URLは `https://username.github.io/`
- **プロジェクトサイト**：それ以外 → 公開URLは `https://username.github.io/repository-name/`

プロジェクトサイトの例：

```yaml
baseURL: "https://username.github.io/blog/"
```

> `baseURL` を誤ると、CSS/JSやリンクが404になります。**必ず自分の公開URLに合わせて修正**してください。

---

## 🖼️ OGP/Twitterカードの設定例
PaperMod では OGP 画像を `params.images` に指定します。共有ボタンや読了時間表示も簡単です。

```yaml
params:
  images: ["images/og-image.png"]
  ShowShareButtons: true
  ShowReadingTime: true
```

> 画像は `static/images/og-image.png` に配置します。Twitterカードはサイトの `<meta property="og:*">` および `name="twitter:*"` をブラウザの検証ツールで確認しましょう。

---

## 💡 公開後の運用Tips
- **記事追加**：`hugo new posts/xxx.md` → 記事編集 → `git commit & push` → 自動で公開。
- **テーマ更新**（Submoduleの更新）：
  ```shell
  git submodule update --remote --merge
  git commit -m "Update PaperMod"
  git push
  ```
- **サイトマップ/robots**：Hugoは `sitemap.xml` を自動生成。インデックス制御は `robots.txt`（必要なら `static/robots.txt` を用意）。
- **RSS**：トップのRSSは通常 `https://username.github.io/blog/index.xml`（プロジェクトサイトの場合）。

---

## ⚠️ よくあるエラーと対処法
### 1. テーマが反映されない
- **原因**：`hugo.yaml` に `theme: ["PaperMod"]` が未設定、またはSubmoduleがチェックアウトされていない。
- **対処**：設定を見直し、`actions/checkout` の `submodules: true` を指定。ローカルでは `git submodule update --init --recursive`。

### 2. 記事が表示されない
- **原因**：`draft: true` のまま、または公開日が未来日。
- **対処**：`draft: false` に変更。未来日なら現在日時より過去に設定する。

### 3. GitHub Pagesで404
- **原因**：`baseURL` が不正、Pagesの公開ブランチ設定ミス。
- **対処**：`hugo.yaml` を公開URLに合わせる。リポジトリの **Settings → Pages** でブランチを `gh-pages` に。

### 4. GitHub Actionsが失敗する
- **原因**：YAMLインデントミス、トークン設定不備、Submoduleの取得失敗。
- **対処**：`.github/workflows/deploy.yml` を再確認。`github_token` が指定されているか、ログ（Actions → 該当ジョブ）で失敗箇所を確認。

### 5. CSS/JSが読み込まれない
- **原因**：`baseURL` の末尾スラッシュ不足、相対パス設定の誤り。
- **対処**：`baseURL` は **末尾にスラッシュ** を付ける（例：`https://username.github.io/blog/`）。

---

## 🔍️ 最終チェックリスト
- [ ] `baseURL` が公開URLに一致
- [ ] `theme: ["PaperMod"]` が設定済み
- [ ] `.gitignore` に `public/` を追加
- [ ] GitHub Pages の公開ブランチが `gh-pages` になっている
- [ ] RSSフィード：`https://username.github.io/blog/index.xml` を開ける
- [ ] OGP画像：`<meta property="og:image" ...>` が意図画像を指す

---

## 🔗 参考リンク
- Hugo公式ドキュメント: https://gohugo.io/
- PaperModテーマ（GitHub）: https://github.com/adityatelange/hugo-PaperMod
- GitHub Pages公式ドキュメント: https://docs.github.com/pages
- peaceiris/actions-hugo: https://github.com/peaceiris/actions-hugo
- peaceiris/actions-gh-pages: https://github.com/peaceiris/actions-gh-pages

---

## 👣 次の一歩
- コメント機能（Giscus/Utterances）の導入
- 独自ドメインの設定（GitHub Pagesの「Custom domain」 + `baseURL` 更新）
- Google Analytics などの計測導入

---
