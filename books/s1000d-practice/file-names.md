---
title: "ファイル名から中身を見分ける"
---

CSDB のフォルダーを開くと、長い名前のファイルが並んでいます。S1000D は、ファイルでデータを受け渡すときの名前の付け方を決めているので、名前を読めば、中身を開かなくても、種類、識別のコード、版、言語が分かります。この章では券売機の CSDB にあるファイルの名前を読みます。

名前の付け方は、仕様書の Chap 7.5.1（ファイルによる受け渡し）にあります。データモジュールコード（DMC）の各部分の意味は、Chap 4.3 と、入門の「[Data Module Code の読み方](https://zenn.dev/shuji_bonji/books/s1000d-intro/viewer/data-module-code)」で説明しています。この章では DMC の説明は繰り返しません。

## 先頭の文字で、種類が分かる

ファイル名の先頭の 3 文字が、CSDB に入れるもの（CSDB オブジェクト）の種類を表します。

| 先頭 | 種類 | 券売機の CSDB にあるか |
| --- | --- | --- |
| `DMC-` | データモジュール | ある（16 ファイル） |
| `PMC-` | パブリケーションモジュール | ある（1 ファイル） |
| `COM-` | コメント | ある（1 ファイル） |
| `ICN-` | 図、動画などのファイル | ある（6 ファイル。PNG の図が 5 つ、STEP の 3D のモデルが 1 つ） |
| `DML-` | データ管理リスト | 「CSDB をフォルダーと Git で持つ」で足す |
| `DDN-` | 納入票 | 無い |
| `IMF-` | ICN のメタデータ | 無い |
| `UPF-` | データ更新ファイル | 無い |

## データモジュールのファイル名

手順の DM の名前を区切って読みます。

```text
DMC-KENBAIKI-A-10-20-00-01A-520A-D_001-00_JA-JP.XML
```

```html-embed
<div class="fn">
  <div class="fn-cap">データモジュールのファイル名</div>
  <div class="fn-row">
    <div class="fn-seg fn-kind"><code>DMC</code><span>種類</span></div>
    <div class="fn-sep">-</div>
    <div class="fn-seg fn-dmc"><code>KENBAIKI-A-10-20-00-01A-520A-D</code><span>DMC（どの製品の、どの部位の、どの種類の情報か）</span></div>
    <div class="fn-sep">_</div>
    <div class="fn-seg fn-issue"><code>001-00</code><span>版</span></div>
    <div class="fn-sep">_</div>
    <div class="fn-seg fn-lang"><code>JA-JP</code><span>言語と国</span></div>
    <div class="fn-sep">.XML</div>
  </div>
  <div class="fn-cap fn-cap2">DMC の内訳</div>
  <div class="fn-grid">
    <div class="fn-part"><code>KENBAIKI</code><span>モデル識別コード</span></div>
    <div class="fn-part"><code>A</code><span>システム差異コード</span></div>
    <div class="fn-part"><code>10</code><span>システム</span></div>
    <div class="fn-part"><code>20</code><span>サブシステムとサブサブシステム</span></div>
    <div class="fn-part"><code>00</code><span>組立</span></div>
    <div class="fn-part"><code>01A</code><span>分解コードとバリアント</span></div>
    <div class="fn-part fn-hl"><code>520A</code><span>情報コードとバリアント</span></div>
    <div class="fn-part"><code>D</code><span>アイテムロケーションコード</span></div>
  </div>
  <div class="fn-cap fn-cap2">版の内訳</div>
  <div class="fn-grid fn-grid2">
    <div class="fn-part fn-issue"><code>001</code><span>発行番号（issueNumber）</span></div>
    <div class="fn-part fn-issue"><code>00</code><span>作業中番号（inWork）。00 は発行済み</span></div>
  </div>
</div>
<style>
  .fn {
    --bg: #f8fafc; --text: #0f172a; --sub: #475569; --line: #cbd5e1; --card: #ffffff;
    --c1: #e0e7ff; --c1t: #3730a3; --c2: #dbeafe; --c2t: #1e40af;
    --c3: #dcfce7; --c3t: #166534; --c4: #fef3c7; --c4t: #92400e;
    box-sizing: border-box; padding: 16px; border-radius: 8px;
    background: var(--bg); color: var(--text);
    font-family: system-ui, -apple-system, "Hiragino Sans", "Noto Sans JP", sans-serif; font-size: 13px; line-height: 1.5;
  }
  [data-theme="dark"] .fn {
    --bg: #0f172a; --text: #e2e8f0; --sub: #94a3b8; --line: #334155; --card: #1e293b;
    --c1: #312e81; --c1t: #e0e7ff; --c2: #1e3a8a; --c2t: #dbeafe;
    --c3: #14532d; --c3t: #dcfce7; --c4: #78350f; --c4t: #fef3c7;
  }
  .fn * { box-sizing: border-box; }
  .fn code { font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace; font-size: 14px; font-weight: 600; background: none; padding: 0; color: inherit; }
  .fn-cap { font-weight: 700; margin-bottom: 8px; }
  .fn-cap2 { margin-top: 14px; }
  .fn-row { display: flex; flex-wrap: wrap; align-items: stretch; gap: 4px; }
  .fn-seg { display: flex; flex-direction: column; gap: 4px; padding: 8px 10px; border-radius: 6px; }
  .fn-seg span, .fn-part span { font-size: 11px; color: inherit; opacity: .9; }
  .fn-sep { display: flex; align-items: flex-start; padding-top: 8px; color: var(--sub); font-family: ui-monospace, Menlo, Consolas, monospace; font-weight: 600; }
  .fn-kind { background: var(--c1); color: var(--c1t); }
  .fn-dmc { background: var(--c2); color: var(--c2t); flex: 1 1 260px; }
  .fn-issue { background: var(--c3); color: var(--c3t); }
  .fn-lang { background: var(--c4); color: var(--c4t); }
  .fn-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 6px; }
  .fn-grid2 { grid-template-columns: repeat(2, 1fr); }
  .fn-part { display: flex; flex-direction: column; gap: 2px; padding: 6px 8px; border-radius: 6px; background: var(--card); border: 1px solid var(--line); }
  .fn-part.fn-issue { background: var(--c3); border-color: transparent; }
  .fn-hl { border-color: var(--c2t); border-width: 2px; }
  @media (max-width: 520px) {
    .fn { padding: 10px; }
    .fn-grid { grid-template-columns: repeat(2, 1fr); }
    .fn code { font-size: 12px; }
  }
</style>
```

| 部分 | 値 | XML の中の場所 |
| --- | --- | --- |
| DMC | `KENBAIKI-A-10-20-00-01A-520A-D` | `dmCode` 要素の属性 |
| 版 | `001-00`（発行番号と作業中番号） | `issueInfo` 要素の `issueNumber` と `inWork` |
| 言語と国 | `JA-JP` | `language` 要素の `languageIsoCode` と `countryIsoCode` |

DMC の中の `520A` は、情報コード（[`infoCode`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/s1000d/issue-7/info-code/)）の `520` と、情報コードバリアント（[`infoCodeVariant`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/s1000d/issue-7/info-code-variant/)）の `A` です。末尾の `D` は、アイテムロケーションコード（[`itemLocationCode`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/s1000d/issue-7/item-location-code/)）です。

ファイル名では言語と国を大文字で書きます。XML の中の `languageIsoCode` は小文字（`ja`）です。

### 同じ DMC のファイルが複数あってよい

DMC が同じでも、版か言語が違えば、別のファイルです。券売機の CSDB では、「紙幣ユニットを取り出す」の DM が 5 ファイルあります。

```text
DMC-KENBAIKI-A-10-20-00-05A-520A-D_001-00_JA-JP.XML
DMC-KENBAIKI-A-10-20-00-05A-520A-D_002-00_JA-JP.XML
DMC-KENBAIKI-A-10-20-00-05A-520A-D_002-00_EN-US.XML
DMC-KENBAIKI-A-10-20-00-05A-520A-D_003-00_JA-JP.XML
DMC-KENBAIKI-A-10-20-00-05A-520A-D_003-00_EN-US.XML
```

日本語の版が 3 つと、そのうち 2 つの版に対応する英語の版です。DMC は、「どの製品の、どの部位の、どの種類の情報か」を表すコードで、版と言語は含みません。1 つの DM を特定するには、DMC、版、言語の 3 つが要ります。

## パブリケーションモジュールのファイル名

```text
PMC-KENBAIKI-K0001-7BTJ7-00_001-00_JA-JP.XML
```

| 部分 | 値 | `pmCode` 要素の属性 |
| --- | --- | --- |
| モデル識別コード | `KENBAIKI` | `modelIdentCode` |
| 発行者のコード | `K0001` | `pmIssuer` |
| 出版物の番号 | `7BTJ7` | `pmNumber` |
| 巻 | `00` | `pmVolume` |

版と、言語と国の書き方は、DM と同じです。

## コメントのファイル名

```text
COM-KENBAIKI-K0001-2026-00001-Q_JA-JP.XML
```

| 部分 | 値 | `commentCode` 要素の属性 |
| --- | --- | --- |
| モデル識別コード | `KENBAIKI` | `modelIdentCode` |
| 送り手のコード | `K0001` | `senderIdent` |
| 年 | `2026` | `yearOfDataIssue` |
| 連番 | `00001` | `seqNumber` |
| コメントの種類 | `Q` | `commentType`（XML の中では小文字の `q`） |

コメントには版がありません。ファイル名にも `_001-00` に当たる部分がありません。

## データ管理リストのファイル名

「[CSDB をフォルダーと Git で持つ](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/csdb-and-dml)」で使う DML の名前です。

```text
DML-KENBAIKI-K0001-S-2026-00001_001-00.XML
```

| 部分 | 値 | `dmlCode` 要素の属性 |
| --- | --- | --- |
| モデル識別コード | `KENBAIKI` | `modelIdentCode` |
| 送り手のコード | `K0001` | `senderIdent` |
| リストの種類 | `S` | `dmlType`（XML の中では小文字の `s`） |
| 年 | `2026` | `yearOfDataIssue` |
| 連番 | `00001` | `seqNumber` |

DML には版がありますが言語と国はありません。

## 図のファイル名

図や動画は DM の XML の中には入りません。ICN（Information Control Number）という番号を名前にした、別のファイルです。

```text
ICN-K0001-00002-001-01.PNG
```

これは、企業コード（CAGE コード）を先頭に置く書式です（Chap 4.4）。企業コード（`K0001`）、その企業の中での番号（`00002`）、版（`001`）、機密区分（`01`）の順に並びます。ICN には、モデル識別コードを先頭に置く、もう 1 つの書式もあります。

拡張子（`.PNG`）は ICN の一部ではありません。DM が ICN からファイルを見つける仕組みは、「[参照と図をたどる](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/references-and-graphics)」で説明します。

## 確かめ方

### ファイル名と中身が一致しているか

ファイル名の DMC・版・言語は XML の中の値の写しです。写しなので、食い違うことがあります。ファイルをコピーして名前だけを変えた場合や、中身の版だけを上げた場合です。

食い違ったファイルは、名前で探したときと、中身で探したときで、別の DM として見つかります。CSDB に入れる前に、次の 3 つが一致しているかを確かめます。

| ファイル名の部分 | XML の中の場所 |
| --- | --- |
| DMC | `dmIdent/dmCode` の属性 |
| 版 | `dmIdent/issueInfo` の `issueNumber` と `inWork` |
| 言語と国 | `dmIdent/language` の `languageIsoCode` と `countryIsoCode` |

### CSDB に何があるかを一覧にする

s1000d-reader-mcp の [`list_data_modules`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/stack/reader/list-data-modules/) は、CSDB のフォルダーにある DM を中身から読んだ値で一覧にします。券売機の CSDB で、DM と PM を対象にした結果です。

| スキーマ | 件数 |
| --- | --- |
| `proced`（手順） | 10 |
| `descript`（説明） | 2 |
| `brex` | 2 |
| `process` | 1 |
| `fault`（故障） | 1 |
| `pm` | 1 |

一覧の各行には、DMC、版、言語、題名、スキーマの名前、S1000D の Issue が入ります。スキーマの名前と S1000D の Issue は、ファイル名には無い情報で、XML の中から読んだものです。読み方は次の章で説明します。

## 実装するときの注意

### 配布物の見本には名前と中身が合わないものがある

S1000D の配布物のデータ辞書に入っている見本のファイルには、次のものがあります。

- 版の場所に版ではなく、例の通し番号が入っているもの
- ファイル名の DMC と、中身の DMC が違うもの

説明のための見本で、CSDB の形をしていません。配布物のフォルダーを CSDB と一緒に探す道具は、これを DM として扱わないようにする必要があります。s1000d-core は、DMC で DM を探すときに、中身の DMC がファイル名と一致するファイルを優先します。

### 大文字と小文字

ファイル名は大文字で、XML の中の値は小文字のものがあります（言語、コメントの種類、DML の種類）。ファイル名と中身を比べるときは、この 3 つについて、大文字と小文字の違いを無視します。DMC はファイル名でも XML の中でも大文字です。

### 拡張 DMC

公式サンプルには、`DME-` で始まる名前の DM が 2 件あります。DMC の前に、作成者を表す部分を足した、拡張 DMC の DM です。`DMC-` で始まるファイルだけを数えると、これを見落とします。s1000d-core は、種類として数えますが、拡張 DMC の分解には対応していません。

## この章のポイント

- ファイル名の先頭が種類を表します。DM は `DMC-`、パブリケーションモジュールは `PMC-`、コメントは `COM-`、データ管理リストは `DML-`、図は `ICN-` です。
- DM を 1 つに特定するには、DMC、版、言語の 3 つが要ります。DMC が同じファイルが複数あって構いません。
- ファイル名は XML の中の値の写しです。CSDB に入れる前に、一致しているかを確かめます。

## 出典

- 著者が入手した S1000D Issue 7 のパッケージ（仕様書の Chap 4.3、Chap 4.4、Chap 7.5.1、XML スキーマの `pm.xsd`・`comment.xsd`・`dml.xsd`、Data Dictionary の見本、Bike Samples）。入手先は https://users.s1000d.org/
