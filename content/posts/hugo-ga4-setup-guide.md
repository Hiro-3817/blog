---
title: "🚀 Hugo × GA4（Google Analytics）連携完全ガイド｜初心者でもできるアクセス解析の導入と設定手順"
slug: "hugo-ga4-setup-guide"
description: "Hugo と Google Analytics 4（GA4）を連携し、アクセス解析を開始するための完全ガイド。GA4の初期設定、Measurement IDの取得、Hugoへの導入、PaperModでの注意点、extend_head.htmlを使った確実な実装方法まで丁寧に解説します。"
date: 2025-12-25T13:00:00+09:00
lastmod: 2025-12-26T11:00:00+09:00
draft: false
categories: ["Web開発", "アクセス解析"]
tags: ["Hugo", "GA4", "Google Analytics", "PaperMod", "ブログ構築", "初心者向け"]
author: "HNEST"
images: ["images/og-image.png"]
---

## 🎯 この記事で得られること

このガイドを読めば、次のことができるようになります。

- GA4（Google Analytics 4）の仕組みと役割が理解できる  
- GA4 の初期設定（プロパティ作成〜Measurement ID取得）ができる  
- Hugo（特に PaperMod）と GA4 を正しく連携できる  
- GitHub Pages で公開したブログのアクセス解析を開始できる  

> **重要**：PaperMod は GA4 の `measurement_id` を自動で読み込まないため、  
> **extend_head.html に GA4 スニペットを追加する方法が最も確実**です。

---

## 📘 1. GA4（Google Analytics 4）とは？

GA4 は Google が提供するアクセス解析ツールで、  
Web サイトのユーザー行動を可視化できます。

### GA4 で分かること

- アクセス数（PV）
- ユーザー数
- 滞在時間
- 流入元（検索・SNS など）
- ページごとの閲覧数
- スマホ / PC の割合
- 国・地域別アクセス

> **補足**：GA4 は旧 Google Analytics（UA）とは仕組みが異なり、  
> 計測には **Measurement ID（G-XXXXXXX）** を使用します。

---

## 🌐 2. GA4 プロパティを作成する

### 🪜 手順

1. Google Analytics にアクセス  
   https://analytics.google.com/

2. 左下の **管理（Admin）** を開く

3. 「プロパティを作成」をクリック  
   - プロパティ名：任意（例：My Blog）  
   - タイムゾーン：日本  
   - 通貨：JPY

4. 「データストリーム」→ **ウェブ** を選択

5. Web ストリームの URL を設定  
   - URL：`https://username.github.io`  
   - パス：`/repository-name/`（任意）

6. Measurement ID（例：`G-SV452C9JE3`）を控える

> **注意**：GA4 の URL 設定は **ドメイン部分にパスを含めない**のが正解です。  
> 例：`https://username.github.io/repository-name/` は NG

---

## 🧩 3. Hugo（PaperMod）への GA4 導入方法

PaperMod には GA4 の設定項目がありますが、  
**現状では measurement_id を設定しても GA4 スニペットが出力されません。**

そのため、最も確実な方法は **extend_head.html に GA4 のコードを直接追加する方法**です。

---

## 🛠️ 4. extend_head.html を使った GA4 の確実な導入方法

### 📁 ファイルを作成する

Hugo プロジェクト内に以下のファイルを作成します。

```text
layouts/partials/extend_head.html
```

### 📝 GA4 のスニペットを追加する

以下のコードをそのまま貼り付けます。

```html
<!-- Google Analytics GA4 -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-SV452C9JE3"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-SV452C9JE3');
</script>
```

> **補足**：Measurement ID（G-XXXXXXX）は必ず自分のものに置き換えてください。

---

## 🧪 5. 動作確認（ローカル & 本番）

### 🔍 ローカルで確認

```shell
hugo --minify
```

生成された `public/index.html` を開き、  
以下の文字列が含まれているか確認します。

- `gtag(`
- `googletagmanager`
- `G-SV452C9JE3`

### 🌐 本番で確認（GitHub Pages）

1. GitHub に push  
2. GitHub Actions が成功しているか確認  
3. GA4 の「リアルタイム」レポートを開く  
4. 自分のブログにアクセス  
5. 1 アクティブユーザーとして表示されれば成功

---

## ⚠️ 6. よくあるつまずきポイント

### ❌ measurement_id を hugo.yaml に書いても GA4 が動かない  
→ PaperMod が GA4 を自動出力しないため

### ❌ GA4 の URL に `/repository-name/` を含めてしまう  
→ ドメイン欄にはパスを含めない

### ❌ GitHub Pages のキャッシュで反映が遅れる  
→ 数分待つ or 強制リロード（Ctrl+Shift+R）

### ❌ GitHub Actions が失敗している  
→ Actions のログを確認

---

## 📚 参考リンク

- Google Analytics 公式  
  https://analytics.google.com/
- GA4 の Measurement ID について  
  https://support.google.com/analytics/answer/1008080
- Hugo 公式  
  https://gohugo.io/
- PaperMod テーマ  
  https://github.com/adityatelange/hugo-PaperMod

---

## 🚀 拡張案（次のステップ）

- GA4 のイベント計測（外部リンククリック・スクロール率）
- Google Search Console の導入
- PaperMod の SEO 最適化（OGP / meta タグ）
- サイトマップの送信
- 独自ドメインの設定
- Giscus / Utterances でコメント機能を追加

---

## 🎉 まとめ

- GA4 はアクセス解析の必須ツール  
- Measurement ID を取得するだけで導入可能  
- PaperMod は measurement_id を自動出力しないため  
  **extend_head.html に GA4 スニペットを追加するのが最も確実**  
- ローカルと本番で動作確認すれば安心して運用できる  

---
