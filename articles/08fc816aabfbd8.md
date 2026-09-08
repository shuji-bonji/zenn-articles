---
title: "MCPサーバー間連携でPDF仕様書を用語統一して日本語翻訳する"
emoji: "😊"
type: "tech"
topics:
  - "typescript"
  - "pdf"
  - "翻訳"
  - "deepl"
  - "mcp"
published: true
published_at: "2026-02-21 12:01"
---

## はじめに

[前回の記事](https://zenn.dev/shuji_bonji/articles/ab30e8318d8be9)で、PDF仕様書（ISO 32000-2）をLLMが構造的に参照できるMCPサーバー **pdf-spec-mcp** を紹介しました。

今回はその応用編です。pdf-spec-mcp と **DeepL MCP Server** を組み合わせて、1,020ページの英語仕様書を**用語の一貫した日本語で把握する**ワークフローを構築しました。

### この記事で扱うこと

- 仕様書翻訳で用語がブレる問題とその解決策
- pdf-spec-mcp から用語集（グロッサリー）を自動生成する手順
- DeepL MCP にグロッサリーを適用した翻訳の Before / After
- 「AIが用語を抽出し、人間が訳語を決める」という役割分担

## 課題：用語のブレ

PDF仕様書を日本語で理解するために、DeepL で翻訳するとします。精度は高いのですが、**ドメイン固有の用語がブレます**。

実際に ISO 32000-2 セクション 7.3.7 "Dictionary objects" を翻訳してみた結果です。

| 原文 | グロッサリーなし | 問題 |
|------|----------------|------|
| null object | **NULL**オブジェクト / **Null**オブジェクト | PDFの `null` キーワードは小文字 |
| entries | **項目** / エントリ / エントリー | 同じ文書内で訳語が揺れる |
| indirect object | **間接的な**オブジェクト / 間接オブジェクト | 定着した訳と汎用訳が混在 |

`null` は PDF の予約キーワードで、仕様上 **小文字** です。これが `NULL` や `Null` と訳されると、技術的に正しくない文書になります。

さらに深刻だったのが、**文が丸ごと消える**現象です。

```
原文（2文）:
  Dictionary objects are the main building blocks of a PDF file.
  They are commonly used to collect and tie together the attributes
  of a complex object, such as a font or a page of the document,
  with each entry in the dictionary specifying the name and value
  of an attribute.

グロッサリーなしの翻訳結果（2文目が消えている）:
  辞書オブジェクトは、PDFファイルの主要な構成要素です。
  辞書の各項目は、属性の名前と値を指定します。
  ← 「フォントや文書のページなど…」の部分が丸ごと欠落
```

仕様書で文が欠落するのは致命的です。

## 解決策：MCPサーバー間連携でグロッサリーを作る

解決策は**DeepL のグロッサリー機能**です。「この英語はこう訳せ」という対訳辞書を登録しておくと、翻訳時に一貫して適用されます。

問題は「どうやってグロッサリーを作るか」です。

ISO 32000-2 にはセクション3「用語及び定義」に **71個の専門用語** が定義されています。pdf-spec-mcp の `get_definitions` ツールでこれを一括抽出できます。

ここから、2つの MCP サーバーを組み合わせたワークフローが生まれました。

```
pdf-spec-mcp                                DeepL MCP
┌────────────────┐                         ┌────────────────┐
│ get_definitions │── 71用語抽出 ──▶        │                │
└────────────────┘                          │                │
        ↓                                   │                │
  分類 & TSV整形（人間の判断）                │                │
        ↓                                   │                │
  create-glossary.sh ──▶ POST /v2/glossaries│                │
                              ↓             │                │
                        glossary_id 取得     │                │
                              ↓             │                │
┌────────────────┐           ↓             │                │
│  get_section   │── 英文 ──▶ translate-text │                │
│  search_spec   │           + glossaryId   │                │
└────────────────┘                         └────────────────┘
        　                        ↓
                          用語統一された日本語翻訳
```

## 手順1：用語を抽出する

Claude Desktop（または他のMCPクライアント）で、pdf-spec-mcp に対して：

```
ツール: get_definitions
パラメータ: なし（全用語取得）
```

71個の定義済み用語が返ってきます。

```json
{
  "definitions": [
    { "term": "null object", "definition": "an object of type null..." },
    { "term": "indirect object", "definition": "an object that is..." },
    { "term": "deprecated", "definition": "a feature that is..." },
    // ... 全71用語
  ]
}
```

## 手順2：用語を分類する（人間の判断）

ここが**最も重要なステップ**です。71用語を2つに分類します。

### そのまま保持（15用語）

略語・固有名で、翻訳不要なもの：

```
ASCII, ASN.1, CAdES, DSA, JPEG, PAdES, PKCS #7, PRC,
RDF, SHA, sRGB, TIFF, UCS, U3D, XMP
```

### 対訳を指定（56用語）

一貫した日本語訳が必要な用語。TSV（タブ区切り）形式で作成します。

```tsv
null object	nullオブジェクト
indirect object	間接オブジェクト
direct object	直接オブジェクト
cross-reference table	相互参照テーブル
deprecated	非推奨
entries	エントリー
content stream	コンテンツストリーム
```

**この分類にはドメイン知識が必要です。** いくつか判断例を挙げます。

| 用語 | AI任せの訳 | 人間の判断 | 理由 |
|------|-----------|-----------|------|
| `null object` | NULLオブジェクト | **nullオブジェクト** | PDFキーワードは小文字 |
| `deprecated` | デプリケーテッド | **非推奨** | 日本語の方が開発者に明確 |
| `FDF file` | FDFファイル | **FDFファイル** | カタカナ読みの方が自然 |
| `tagged PDF` | タグ付きPDF | **Tagged PDF** | 技術用語としてそのまま使う |

「AIが用語を抽出し、ドメイン知識が訳語を決め、DeepLが一貫して適用する」— この三者の役割分担がポイントです。

## 手順3：グロッサリーを登録する

TSVファイルができたら、DeepL API に登録します。

```bash
export DEEPL_API_KEY="your-key-here"
bash create-glossary.sh
```

```
📡 DeepL Pro API を使用
📝 PDF Spec ISO32000-2 EN-JA（56エントリ）を登録中...

✅ 登録成功!
   glossary_id: 342b99bd-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   name: PDF Spec ISO32000-2 EN-JA
   entry_count: 56
```

スクリプトは Free / Pro の API エンドポイントを自動判定します。

:::message
現在、DeepL MCP Server にはグロッサリーの**読み取り**ツール（`list-glossaries` 等）はありますが、**作成**ツールがありません。そのため、このステップではシェルスクリプトで直接 API を呼ぶ必要があります。

この点について [Issue #35](https://github.com/DeepLcom/deepl-mcp-server/issues/35) を提出しています。
:::

## 手順4：グロッサリー付きで翻訳する

登録した `glossary_id` を使って、DeepL MCP の `translate-text` を呼びます。

```
ツール: translate-text
パラメータ:
  text: <pdf-spec-mcp の get_section で取得した英文>
  sourceLangCode: "en"
  targetLangCode: "ja"
  glossaryId: "342b99bd-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

実際の使い方としては、Claude に対して以下のように指示するだけです：

> 「pdf-spec-mcp で 7.3.7 節を取得して、DeepL MCP でグロッサリー付きで日本語に翻訳して」

Claude が `get_section` → `translate-text`（+ glossaryId）を順に呼び出し、用語統一された日本語で返してくれます。

## Before / After 比較

### テストケース 1：null キーワードの一貫性

**原文（セクション 7.3.7 第1段落より）：**
> A dictionary entry whose value is null (see 7.3.9, "Null object") shall be treated the same as if the entry does not exist.

**❌ グロッサリーなし：**
> 値が**NULL**である辞書**項目**（7.3.9「**Null**オブジェクト」参照）は、その項目が存在しないのと同じように扱われるものとする。

- `null` が `NULL` と `Null` の2種類に
- `entries` が `項目` に

**✅ グロッサリーあり：**
> 値が**null**である辞書項目（7.3.9「**null**オブジェクト」参照）は、その項目が存在しないのと同じように扱われるものとする。

- `null` が一貫して小文字
- PDF仕様のキーワード表記に準拠

### テストケース 2：文の欠落防止

**原文（セクション 7.3.7 第2段落）：**
> Dictionary objects are the main building blocks of a PDF file. They are commonly used to collect and tie together the attributes of a complex object, such as a font or a page of the document, with each entry in the dictionary specifying the name and value of an attribute.

**❌ グロッサリーなし：**
> 辞書オブジェクトは、PDFファイルの主要な構成要素です。辞書の各項目は、属性の名前と値を指定します。

⚠️ 第2文（「フォントや文書のページなど…」）が**丸ごと欠落**。

**✅ グロッサリーあり：**
> 辞書オブジェクトはPDFファイルの主要な構成要素です。辞書オブジェクトは、フォントや文書のページなど、複雑なオブジェクトの属性を集めて結びつけるために一般的に使用され、辞書の各項目は属性の名前と値を指定します。

✅ 完全な翻訳、情報欠落なし。

### 改善まとめ

| 観点 | ❌ グロッサリーなし | ✅ グロッサリーあり | 影響 |
|------|-------------------|-----------------|------|
| `null` キーワード | `NULL` / `Null`（不統一） | `null`（統一） | PDF仕様のキーワード表記に準拠 |
| "entries" の訳 | 項目（汎用語） | エントリー（PDF用語） | 用語の一貫性 |
| 文の欠落 | 第2文が**丸ごと消える** | 完全に翻訳 | 情報欠損の防止 |
| "abbreviated" の訳 | 略される | 省略される | より正確な訳語 |

## なぜグロッサリーで文の欠落が防げたのか

これは予想外の効果でした。グロッサリーは本来、用語の訳を固定するための機能です。なぜ文の欠落防止に繋がったのか。

推測ですが、グロッサリーによって翻訳モデルの解釈が安定したことで、文構造の把握が改善された可能性があります。用語の曖昧さが減ることで、翻訳エンジンが構文解析に集中できるようになった — という仮説です。

DeepL の内部動作は公開されていないので断言はできませんが、実用上、グロッサリーの適用が翻訳品質を用語面以外でも向上させる事例として報告しておきます。

## セットアップ

### 前提条件

- [pdf-spec-mcp](https://www.npmjs.com/package/@shuji-bonji/pdf-spec-mcp) が設定済み
- [DeepL MCP Server](https://www.npmjs.com/package/deepl-mcp-server) が設定済み
- DeepL API キー（[Free でも OK](https://www.deepl.com/pro#developer)）

### MCP 設定例（Claude Desktop）

```json
{
  "mcpServers": {
    "pdf-spec": {
      "command": "npx",
      "args": ["-y", "@shuji-bonji/pdf-spec-mcp"],
      "env": {
        "PDF_SPEC_DIR": "/path/to/pdf-specs"
      }
    },
    "deepl": {
      "command": "npx",
      "args": ["-y", "deepl-mcp-server"],
      "env": {
        "DEEPL_API_KEY": "your-api-key"
      }
    }
  }
}
```

### グロッサリー登録

```bash
# リポジトリからファイル一式を取得
git clone https://github.com/shuji-bonji/pdf-spec-mcp.git
cd pdf-spec-mcp/examples/translation-glossary

# グロッサリー登録
export DEEPL_API_KEY="your-key-here"
bash create-glossary.sh
```

グロッサリー（TSVファイル）、登録スクリプト、詳細なドキュメントはすべて以下にまとめてあります。

👉 [pdf-spec-mcp/examples/translation-glossary](https://github.com/shuji-bonji/pdf-spec-mcp/tree/main/examples/translation-glossary)

## 他のドメインへの応用

このワークフローの考え方は PDF 仕様に限りません。

1. **MCP サーバーで仕様書の定義済み用語を抽出する**
2. **ドメイン知識で対訳を決定する**
3. **DeepL グロッサリーに登録する**
4. **グロッサリー付きで翻訳する**

API ドキュメント、法律文書、医療規格 — ドメイン固有の用語が多い文書ほど、グロッサリーの効果は大きくなります。

pdf-spec-mcp の `get_definitions` に相当する「構造的な用語抽出」ができれば、同じアプローチが適用できます。すでにMCPサーバーとして [rfcxml-mcp](https://github.com/shuji-bonji/rfcxml-mcp)（RFC仕様）などでも `get_definitions` ツールを実装しているので、同様の連携が可能です。

## まとめ

```
pdf-spec-mcp           人間の判断            DeepL MCP
 用語を抽出     →    訳語を決定     →    一貫して適用
（71用語自動）     （56用語を分類）     （翻訳時に強制）
```

- **MCPサーバー間連携**で、仕様書参照→用語抽出→翻訳を一気通貫で行える
- **グロッサリー**により、用語の一貫性だけでなく文の欠落防止にも効果があった
- **人間のドメイン知識**は依然として不可欠 — AIは用語を抽出するが、「nullは小文字」「deprecatedは非推奨」と判断するのは人間

### リンク

@[card](https://github.com/shuji-bonji/pdf-spec-mcp)
- **翻訳ワークフロー example**: https://github.com/shuji-bonji/pdf-spec-mcp/tree/main/examples/translation-glossary
@[card](https://www.npmjs.com/package/@shuji-bonji/pdf-spec-mcp)
@[card](https://www.npmjs.com/package/deepl-mcp-server)

### 関連

- [前回の記事：PDF仕様書をLLMが「引ける」MCPサーバーを作った](https://zenn.dev/shuji_bonji/articles/ab30e8318d8be9)
- [DeepL Bridges での共有](https://www.deepl-bridges.com/developers/made-with-deepl/post/from-bulk-translation-to-terminology-consistency-using-deepl-glossaries-dsXXCaWo92axmXs)
- [DeepL MCP Server へのグロッサリー書き込みツール提案（Issue #35）](https://github.com/DeepLcom/deepl-mcp-server/issues/35)