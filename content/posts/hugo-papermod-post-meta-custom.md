---
title: "🛠 Hugo × PaperMod｜ブログ冒頭のメタ情報（投稿日・更新日・読了時間・著者）を美しくカスタマイズする完全ガイド"
slug: "hugo-papermod-post-meta-custom"
description: "Hugo + PaperMod のブログ冒頭に表示される「投稿日・更新日・読了時間・著者情報」を日本語化し、アイコン付きで見やすくカスタマイズする方法を初心者向けに丁寧に解説。post_meta.html の上書き、著者リンク、日英切り替え対応まで完全網羅。"
date: 2025-12-26T13:00:00+09:00
lastmod: 2025-12-26T13:00:00+09:00
draft: false
categories: ["Web開発", "サイト構築"]
tags: ["Hugo", "PaperMod", "ブログ構築", "カスタマイズ", "初心者向け"]
author: "HNEST"
images: ["images/og-image.png"]
---

## 🎯 この記事で得られること

この記事では、Hugo + PaperMod を使ったブログの冒頭に表示される **メタ情報（投稿日・更新日・読了時間・著者）** を、次のようにカスタマイズする方法を解説します。

- 📅 投稿日  
- 🔄 更新日  
- ⏱ 読了目安  
- ✍️ 著者（リンク付き）  
- 🇯🇵 日本語 / 🇺🇸 英語の自動切り替え  
- PaperMod のアップデートに強い「上書きレイアウト方式」

> **ポイント**：PaperMod のテーマファイルは直接編集せず、  
> **`layouts/partials/post_meta.html` を上書きする方法が最も安全**です。

---

## 📘 1. PaperMod のメタ情報はどこで生成されている？

PaperMod の記事ページ（single.html）では、メタ情報は次の partial で生成されています。

```text
themes/PaperMod/layouts/partials/post_meta.html
```

このファイルを直接編集すると、テーマ更新時に上書きされてしまうため、  
**プロジェクト側にコピーして上書きするのが正しい方法**です。

---

## 📁 2. post_meta.html をプロジェクト側にコピーする

まずは PaperMod の元ファイルを確認し、プロジェクト側にコピーします。

```shell
# プロジェクトのルートで実行
mkdir -p layouts/partials
cp themes/PaperMod/layouts/partials/post_meta.html layouts/partials/post_meta.html
```

> **補足**：Hugo は「プロジェクト側の layouts を優先」して読み込むため、  
> このコピーがそのまま上書きとして機能します。

---

## 🛠 3. 日本語・英語対応のカスタム post_meta.html を作成する

以下は、メタ情報（投稿日・更新日・読了時間・著者）を、次のようにカスタマイズする **完全版 post_meta.html** です。

- 📅 投稿日  
- 🔄 更新日（変更がある場合のみ表示）  
- ⏱ 読了目安  
- ✍️ 著者（リンク付き）  
- 🇯🇵 日本語 / 🇺🇸 英語の自動切り替え  

```gohtml
{{- /* ===============================
     カスタム Post Meta（日本語/英語対応）
     表示内容：
     📅 投稿日 ｜ 🔄 更新日 ｜ ⏱ 読了目安 ｜ ✍️ 著者（リンク）
     =============================== */ -}}

{{- $lang := .Site.Language.Lang | default "ja" -}}
{{- $date := .Date -}}
{{- $lastmod := .Lastmod -}}
{{- $readingTime := .ReadingTime -}}

{{- $authorName := or .Params.author .Site.Author.name "[UserName]" -}}
{{- $authorURL := or .Params.authorLink .Site.Author.link "#" -}}

<div class="post-meta">

  {{- if eq $lang "ja" -}}
    <span class="post-meta-item">📅 投稿日：{{ $date.Format "2006年1月2日" }}</span>

    {{- if ne $lastmod $date -}}
      <span class="post-meta-separator">｜</span>
      <span class="post-meta-item">🔄 更新日：{{ $lastmod.Format "2006年1月2日" }}</span>
    {{- end }}

    <span class="post-meta-separator">｜</span>
    <span class="post-meta-item">⏱ 読了目安：{{ $readingTime }}分</span>

    <span class="post-meta-separator">｜</span>
    <span class="post-meta-item">✍️ 著者：<a href="{{ $authorURL }}">{{ $authorName }}</a></span>

  {{- else -}}
    <span class="post-meta-item">📅 Published: {{ $date.Format "Jan 2, 2006" }}</span>

    {{- if ne $lastmod $date -}}
      <span class="post-meta-separator">｜</span>
      <span class="post-meta-item">🔄 Updated: {{ $lastmod.Format "Jan 2, 2006" }}</span>
    {{- end }}

    <span class="post-meta-separator">｜</span>
    <span class="post-meta-item">⏱ Reading time: {{ $readingTime }} min</span>

    <span class="post-meta-separator">｜</span>
    <span class="post-meta-item">✍️ Author: <a href="{{ $authorURL }}">{{ $authorName }}</a></span>
  {{- end }}

</div>
```

> **注意**：著者リンクは front matter の `authorLink:` または  
> `config` の `.Site.Author.link` で設定できます。

---

## 🌐 4. 日本語表示にするための Hugo 設定

日本語ブログの場合は、以下を `hugo.yaml` に追加します。

```yaml
languages:
  ja:
    languageName: "Japanese"
    weight: 1
```

> **補足**：これを設定しないと `.Site.Language.Lang` が `"en"` になり、  
> 英語表示になってしまいます。

---

## 🎨 5. メタ情報の見た目を整える（任意）

PaperMod のデザインに馴染むよう、CSS を追加します。

```css
.post-meta {
  font-size: 0.9rem;
  color: var(--secondary);
  display: flex;
  flex-wrap: wrap;
  gap: 0.25rem;
  align-items: center;
}

.post-meta-item {
  white-space: nowrap;
}

.post-meta-separator {
  opacity: 0.6;
}

.post-meta a {
  color: var(--secondary);
  text-decoration: underline;
}
```

---

## 🧪 6. 動作確認

ローカルで確認します。

```shell
hugo server -D
```

- 日本語表示になっている  
- 更新日が正しく表示される  
- 著者名がリンクになっている  

これらが確認できれば成功です。

---

## 📚 参考リンク

- Hugo 公式ドキュメント  
  https://gohugo.io/
- PaperMod テーマ  
  https://github.com/adityatelange/hugo-PaperMod
- Hugo の多言語設定  
  https://gohugo.io/content-management/multilingual/

---

## 🚀 拡張案（次のステップ）

- 著者アイコン（プロフィール画像）を追加する  
- 投稿日と更新日の位置を入れ替える  
- 英語版記事を追加して本格的な多言語ブログにする  
- メタ情報を JSON-LD に変換して SEO を強化する  
- PaperMod のヘッダーにパンくずリストを追加する  

---

これで、ブログの冒頭メタ情報は **美しく・分かりやすく・拡張性の高い形**に仕上がります。  
