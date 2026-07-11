---
title: 'PDF 2.0 Errata Collection 3 が出たので pdf-spec-mcp を対応させた'
emoji: '📝'
type: 'tech'
topics: ['PDF', 'ISO', 'TypeScript', 'MCPサーバー', 'AI']
published: true
---

## はじめに

[以前の記事](https://zenn.dev/shuji_bonji/articles/ab30e8318d8be9)で、PDF仕様書（ISO 32000-2）をLLMが構造的に参照できるMCPサーバー **pdf-spec-mcp** を紹介しました。

2026年6月、PDF Association から [PDF 2.0 Errata Collection 3（EC3）](https://pdfa.org/pdf-2-0-errata-collection-3-now-available/) が公開されました。pdf-spec-mcp が参照する仕様書PDFそのものが更新されたことになります。

本記事では、EC3で実際に何が変わったのかの調査結果と、それに伴う pdf-spec-mcp v0.3.0 の対応、そして対応中に見つけた「自動検出の先勝ちロジックの罠」と「errata が注釈で実装されている問題」について書きます。

### この記事で扱うこと

- Errata Collection 3 の概要と、EC2からの実際の差分（全11ファイルを検証）
- ファイル名パターンによる仕様書自動検出が壊れるポイント
- `readdir` 順に依存した「先勝ち」ロジックの罠と修正
- errata 本文が PDF 注釈で実装されているため、テキスト抽出では見えない問題

## Errata Collection 3 とは

ISO 32000-2:2020（PDF 2.0）は、Adobe・Apryse・Foxit のスポンサードにより [PDF Association から無償で入手](https://pdfa.org/sponsored-standards/)できます。この無償版には、ISO承認済み＋業界承認済みの正誤表（errata）が注釈として適用されており、そのまとまりが「Errata Collection」です。

EC3 は EC2 の**完全な置き換え**（追加パッチではない）で、内容は以下のとおりです。

| 項目          | 内容                                                                   |
| ------------- | ---------------------------------------------------------------------- |
| 基準日        | 2026年6月1日                                                           |
| errata 修正数 | **356件**（766編集として実装）                                         |
| ISO承認率     | 766編集中655（86%、EC2では54%）                                        |
| 追補ページ    | 図表の差し替え等、注釈で表現できない修正を**20ページ**として巻末に追加 |
| 対象文書      | ISO 32000-2 本体、ISO/TS 32001、ISO/TS 32002                           |

## 実際に何が変わったのか検証する

ダウンロードし直したバンドル一式（11ファイル）を、手元のEC2世代のファイルとハッシュ・メタデータ・ページ数で比較しました。

| ファイル                            | 旧            | 新（EC3バンドル）   | 判定                |
| ----------------------------------- | ------------- | ------------------- | ------------------- |
| ISO 32000-2                         | EC2版 1,020頁 | **EC3版 1,023頁**   | ✅ 更新             |
| ISO/TS 32001:2022                   | 13頁          | **14頁**（EC3表記） | ✅ 更新             |
| ISO/TS 32002:2022                   | 13頁          | **14頁**（EC3表記） | ✅ 更新             |
| ISO/TS 32003 / 32004 / 32005        | —             | —                   | ❌ 内容同一         |
| PDF-Declarations、AN001〜003、WTPDF | —             | —                   | ❌ ハッシュ完全一致 |

面白いのは TS 32003〜32005 です。ファイルサイズは旧版と異なるのに、ページ数・タイトル・内容は同一でした。スポンサー版PDFはダウンロード時に利用者名のスタンプが埋め込まれるため、**バイト列は毎回変わる**のです。「ファイルが変わった＝内容が変わった」とは限らない、という PDF らしい話でした。公式アナウンスの "Other sponsored ISO publications are unchanged since Errata Collection 2" とも一致します。

つまり、実質の更新は **3ファイルだけ** です。

## pdf-spec-mcp が壊れるポイント

pdf-spec-mcp は環境変数 `PDF_SPEC_DIR` のディレクトリをスキャンし、ファイル名パターンで最大17種の仕様書を自動検出します。EC3ファイルを配置すると、2つの問題が起きることが分かりました。

### 問題1：primary spec が検出されない

v0.2.x のパターンは EC2 のファイル名に固定されていました。

```typescript
// v0.2.x — EC2固定
{
  pattern: /ISO_32000-2_sponsored-ec2\.pdf$/i,
  id: 'iso32000-2',
  ...
}
```

EC3 のファイル名は `ISO_32000-2_sponsored_EC3.pdf`（区切りが `-ec2` → `_EC3` に変化）なので、どのパターンにもマッチしません。primary spec（`iso32000-2`）が未登録になり、**デフォルトspecを使う全ツールがエラーになります**。

### 問題2：「先勝ち」ロジックの罠

TS 32001/32002 のパターンは `/ISO_TS_32001.*\.pdf$/i` と緩いので、EC3ファイル名もマッチします。一見問題なさそうですが、旧ファイルを消さずに共存させると罠が発動します。

registry は同一IDに対して「先にスキャンされたファイルが勝つ」実装で、スキャン順は `readdir` の返す順（ほぼASCII順）でした。

```
ISO_TS_32001-2022_sponsored.pdf      ← '.' (0x2E)
ISO_TS_32001-2022_sponsored_EC3.pdf  ← '_' (0x5F)
```

`.pdf` の `.`（0x2E）は `_`（0x5F）より小さいので、**旧版が先に登録され、EC3が黙って無視されます**。エラーも警告も出ないのでタチが悪い。32000-2 本体も同様で、`-ec2.pdf` の `-`（0x2D）は `_EC3.pdf` の `_` より小さく、両方置くと必ず旧版が勝ちます。

「ファイル名の辞書順」という実装の都合が「どちらの仕様書を正とするか」を決めてしまっている状態です。

## v0.3.0 での修正

### パターンを世代別に分離し、配列順＝優先度にする

パターンを「EC3優先、EC2フォールバック」の2エントリに分けました。同じ `iso32000-2` IDを持ちますが、タイトルはそれぞれの世代を正しく名乗ります。

```typescript
// v0.3.0 — EC3を先に（優先）、EC2を後に（フォールバック）
{
  pattern: /ISO_32000-2_sponsored[-_]ec3\.pdf$/i,
  id: 'iso32000-2',
  title: 'ISO 32000-2:2020 (PDF 2.0) with Errata Collection 3',
  ...
},
{
  pattern: /ISO_32000-2_sponsored[-_]ec2\.pdf$/i,
  id: 'iso32000-2',
  title: 'ISO 32000-2:2020 (PDF 2.0) with Errata Collection 2',
  ...
},
```

### スキャン順を readdir 順からパターン優先度順に変更

ファイル一覧をパターンのインデックスでソートしてから登録するように変更しました。これで「配列の順序＝優先度」が保証されます。

```typescript
// discoverSpecs() — ファイルをパターン優先度順に処理
const prioritized = pdfFiles
  .map((filename) => ({
    filename,
    patternIndex: SPEC_PATTERNS.findIndex((p) => p.pattern.test(filename)),
  }))
  .sort((a, b) => a.patternIndex - b.patternIndex);
```

この2つの修正により、挙動は次のようになります。

| 環境           | 登録される spec                                  |
| -------------- | ------------------------------------------------ |
| EC3 のみ       | EC3（Errata Collection 3 として）                |
| EC2 のみ       | EC2（Errata Collection 2 として正しく名乗る）    |
| EC3 + EC2 共存 | **EC3 が優先**（ファイル名の辞書順に依存しない） |

将来 EC4 が出たら、パターンを1エントリ追加するだけで済みます。

### 動作確認

npx 経由（Claude Desktop 設定）での実機確認結果です。

```jsonc
// list_specs（抜粋）
{
  "id": "iso32000-2",
  "title": "ISO 32000-2:2020 (PDF 2.0) with Errata Collection 3",
  "filename": "ISO_32000-2_sponsored_EC3.pdf"
}

// get_structure
{
  "totalPages": 1023,      // EC2: 1020 → EC3: 1023
  "totalSections": 988,    // EC2: 985 → EC3: 988
  "sections": [
    // ...
    { "title": "Additional errata pages", "page": 1004 }  // EC3の追補20ページ
  ]
}
```

追補20ページ（"Additional errata pages"）もアウトラインに現れ、`get_section` / `search_spec` / `compare_versions` にリグレッションはありませんでした。

## 落とし穴：errata 本文はテキスト抽出では見えない

対応中に一つ重要な事実に気づきました。EC3 の errata 修正766編集の内訳は、公式アナウンスによると次のとおりです。

| 実装方法               | 件数 |
| ---------------------- | ---- |
| テキスト置換注釈       | 322  |
| テキスト挿入注釈       | 200  |
| 付箋（sticky note）    | 124  |
| 取り消し線（削除）注釈 | 113  |
| ファイル添付           | 6    |

つまり errata の本文は、ページのコンテンツストリームではなく **PDF 注釈（アノテーション）として実装されています**。pdfjs-dist の `getTextContent()` はページ本文を抽出するAPIなので、**注釈の中身は返ってきません**。pdf-spec-mcp で条文を取得しても、そこに適用された errata 修正は見えないのです（これはEC2の頃からの制約です）。

実際に、姉妹プロジェクトの [pdf-reader-mcp](https://github.com/shuji-bonji/pdf-reader-mcp) で EC3 のあるページを覗いてみると：

```jsonc
// inspect_annotations({ pages: "40" })
{
  "totalAnnotations": 13,
  "byType": {
    "Link": 9,
    "Popup": 2,
    "Caret": 1, // ← テキスト挿入 errata
    "Text": 1, // ← 付箋 errata
  },
}
```

7.3.4節（String objects）のページに、挿入注釈と付箋が確認できます。

ここから、2つのMCPサーバーを組み合わせた errata 確認ワークフローが成立します。

```
pdf-spec-mcp                       pdf-reader-mcp
┌──────────────────┐              ┌──────────────────────┐
│ get_section      │─ 条文取得 ─▶ │                      │
│ （ページ範囲付き）│              │ inspect_annotations  │
└──────────────────┘              │ （該当ページの       │
        ↓                         │   errata注釈を確認） │
   「7.3.4節は p.40」─────────────▶└──────────────────────┘
                    ↓
        条文 + 適用された errata 修正の両方を把握
```

「spec で条文を引き、reader で同じページの errata 注釈を確認する」— 仕様書の本文と正誤表を突き合わせる作業が、MCPツール2回の呼び出しで済みます。

なお、注釈が読めない問題の恒久対応（`get_section` の結果に errata 注釈を含める等）は今後の課題として検討中です。

## まとめ

- **EC3 は EC2 の完全置き換え**。ただし実際に内容が変わったのは ISO 32000-2 本体と TS 32001/32002 の3ファイルだけ。スポンサー版はDL時スタンプでバイト列が毎回変わるため、ハッシュ比較だけでは内容差分は判定できない
- **ファイル名の辞書順に依存した自動検出は罠になる**。`-`（0x2D）と `_`（0x5F）の大小関係で旧版が黙って勝つ、という分かりにくい不具合だった。v0.3.0 でパターン優先度順に修正
- **errata は注釈実装のためテキスト抽出では見えない**。pdf-reader-mcp の `inspect_annotations` との連携で補完できる

pdf-spec-mcp v0.3.0 は npm で公開済みです。`PDF_SPEC_DIR` に EC3 のファイルを置くだけで、EC3 を primary spec として利用できます。

### リンク

- pdf-spec-mcp: https://github.com/shuji-bonji/pdf-spec-mcp
- npm: https://www.npmjs.com/package/@shuji-bonji/pdf-spec-mcp
- pdf-reader-mcp: https://github.com/shuji-bonji/pdf-reader-mcp
- PDF 2.0 Errata Collection 3 now available（PDF Association）: https://pdfa.org/pdf-2-0-errata-collection-3-now-available/
- errata の全リスト: https://pdf-issues.pdfa.org/

### 関連記事

- [PDF 2.0 Errata Collection 3 が公開された — 何が変わったのか](https://zenn.dev/shuji_bonji/articles/pdf20-errata-collection-3)
- [PDF仕様書をLLMが「引ける」MCPサーバーを作った](https://zenn.dev/shuji_bonji/articles/ab30e8318d8be9)
- [MCPサーバー間連携でPDF仕様書を用語統一して日本語翻訳する](https://zenn.dev/shuji_bonji/articles/08fc816aabfbd8)
