---
title: "付録：入手したパッケージの観察記録"
---

この付録は、著者が S1000D の[ダウンロードサイト](https://users.s1000d.org/)から利用条件に同意して入手したパッケージを開き、そこで確認した細部をまとめたものです。本文では概念の説明を優先し、ファイル名、日付、発行番号、仕様本文とファイルの食い違いといった細部は、この付録に集めました。

実務で S1000D のファイルを扱うときに、同じ食い違いや表記のゆれに出会う可能性があります。そのときの手がかりとして使ってください。ここに書いたのは、入手したパッケージの観察であり、すべての配布物に当てはまるとは限りません。

## パッケージの中身

### Issue 4.1 と Issue 5.0（説明ページで確認）

| Issue | ダウンロードサイトの説明ページが同梱物として挙げているもの |
| --- | --- |
| 4.1 | 仕様書、機能マトリックス、既定 BREX、単位、SNS の例、Business Rule Decision Point の索引、XML スキーマ、スキーマ文書、データ辞書、パッチ。自転車サンプルは別項目として説明されている |
| 5.0 | 仕様書、自転車サンプル、ビジネスルールのテンプレート、データ辞書、既定 BREX、SNS の例、前付けのサンプル、機能マトリックス、Project Decision Point の索引、単位、XML スキーマ、パッチ |

Issue 5.0 の説明ページは、パッチ 5.0.A がスキーマの呼び出し行の誤りを直し、5.0.B がデータ辞書に欠けていた属性の定義を補った、とも書いています。

### Issue 6（12 ファイル）

| 中身 | パッケージ内のファイル名 |
| --- | --- |
| 仕様書 | `S1000D Issue 6 Specification.zip`（中のファイルは `Specification/S1000D Issue 6.PDF`） |
| 機能マトリックス | `S1000D Functionality Matrix.zip`（中のファイルは `S1000D Functionality Matrix.xls`。ZIP の名前に Issue 番号がない） |
| 前付けのサンプル | `S1000D Issue 6 Front Matter Samples.zip` |
| 自転車サンプル | `S1000D Issue 6 Bike Sample Data Set.zip` |
| XML スキーマ | `S1000D Issue 6 XML Schema Package.zip` |
| BRDP の索引 | `S1000D Issue 6 BRDP Index.zip`（中のファイルは `S1000D Issue 6 BRDP Index.xlsx`） |
| ビジネスルールのテンプレート | `S1000D Issue 6 BR Template.zip` |
| データ辞書 | `S1000D Issue 6 Data Dictionary.zip` |
| 単位 | `S1000D Issue 6 Unit of Measure.zip` |
| 既定 BREX | `S1000D Issue 6 Default BREX.zip` |
| SNS の例 | `S1000D Issue 6 Example SNS.zip` |
| パッチ | `S1000D Issue 6 Patches.zip` |

### Issue 7（11 ファイル）

ファイル名に Patch という語はありません。

| 中身 | パッケージ内のファイル名 |
| --- | --- |
| 仕様書 | `S1000D Issue 7.pdf` |
| 機能マトリックス | `S1000D Issue 7 Functionality Matrix.zip`（中のファイルは `S1000D Issue 7 Functionality Matrix.xls`） |
| 前付けのサンプル | `S1000D Issue 7 Front Matter Samples.zip` |
| 自転車サンプル | `S1000D Issue 7 Bike Samples.zip` |
| XML スキーマ | `S1000D Issue 7 XML Schema Package.zip` |
| BRDP の索引 | `S1000D Issue 7 Business Rules Decision Point Index.zip`（中のファイルは `S1000D Issue 7 BRDP Index.xlsx`） |
| ビジネスルールのテンプレート | `S1000D Issue 7 Business Rules Template.zip` |
| データ辞書 | `S1000D Issue 7 Data Dictionary .zip`（`Dictionary` と `.zip` の間に空白がある） |
| 単位 | `S1000D Issue 7 Unit of Measure.zip` |
| 既定 BREX | `S1000D Issue 7 Default BREX.zip` |
| SNS の例 | `S1000D Issue 7 Example SNS.zip` |

Issue 6 と Issue 7 の XML スキーマパッケージには XSD のファイルがあり、DTD のファイルはありません。

## 日付の読み方

パッケージの中には、いくつもの日付があります。版の発行日として使えるのは、仕様書の表紙の日付です。

| 日付の種類 | Issue 6 | Issue 7 |
| --- | --- | --- |
| 仕様書の表紙 | 2024-06-19（“Issue No. 6”） | 2026-07-16（“Issue No. 7”） |
| 章のフッター（例） | 第7.3.1.4章は 2024-03-07、第4.10.2.4章は 2023-10-06 | 第4.2章は 2020-01-09 |
| SNS の例の ZIP の中の PDF のアーカイブ上の時刻 | すべて 2017-01-30（例：`DMC-AE-A-08-03-0100-00A-040A-A_004-00_EN-US.pdf`） | 同じ時刻の PDF が入っている |
| 単位の ZIP のアーカイブ上の時刻 | `DocumentInfo.xsd`、`Units.xsd`、`Units.xsl` は 2007-03-19、`S1000DUnits.xml` は 2024-05-24 | — |

章のフッターは、その章が最後に変更された日付です。アーカイブ上の時刻は、ファイルが作られた日付を示すだけです。どちらも、パッケージの発行日ではありません。オンラインのデータ辞書のページに付いている日付も、辞書のページの日付であり、表紙の発行日とは限りません。

Issue 6 の自転車サンプルの ZIP の中のディレクトリ名は `Bike Data Set for Release number 6 R2` です。この “R2” は、表紙の番号とは別の文字列です。

## 版番号の表記のゆれ

### パッチの位

パッチの位について、資料によって説明が違います。

| 資料 | 説明 |
| --- | --- |
| ダウンロードサイト | パッチは版番号の 3 番目の位の文字。例は Issue 4.1.A。Issue 4.1 より前は数字（4.0.1、4.0.2 など） |
| Issue 6 と Issue 7 の第7.3.1.4章 | パッチは版番号の 2 番目の位の英字。例は Issue 6.A |

### Issue 6 のパッケージの中の表記

同じ Issue 6 のパッケージの中に、次の表記が並んでいます。

| 場所 | 表記 |
| --- | --- |
| 仕様書の表紙 | “Issue No. 6” |
| 第7.3.1.4章の例 | “Issue 6.A” |
| パッチのフォルダ名 | “Patch 6.0.A” |
| パッチの中の BREX のディレクトリ名 | “S1000D_6.A_ BREX” |
| データ辞書の README の見出し | “Issue 6-01” と “Issue 6A”（README の日付は 2025-11-14） |

仕様本文を “6.0.A” で検索した範囲では、その文字列は見つかりませんでした。本書では、版の識別には表紙の番号を使い、これらの表記を一つにまとめていません。

### 第7.3.1.4章の後方互換の記述

Issue 6 と Issue 7 の第7.3.1.4章は、どちらも「この版の CPF のうち、後方互換に影響するものは無かった」と書いています。これは、ダウンロードサイトが説明する「メジャー改訂には後方互換がない」という一般規則を取り消すものではありません。

## 既定 BREX

既定 BREX の `dmCode` の属性は、`systemDiffCode` 以外は次の三つのファイルで同じです。

```text
modelIdentCode       = S1000D
systemDiffCode       = F（Issue 4.2 の複製）、H（Issue 6）、J（Issue 7）
systemCode           = 04
subSystemCode        = 1
subSubSystemCode     = 0
assyCode             = 0301
disassyCode          = 00
disassyCodeVariant   = A
infoCode             = 022
infoCodeVariant      = A
itemLocationCode     = D
```

| 項目 | Issue 4.2 の複製 | Issue 6 の同梱ファイル | Issue 7 の同梱ファイル |
| --- | --- | --- | --- |
| DMC | `S1000D-F-04-10-0301-00A-022A-D` | `S1000D-H-04-10-0301-00A-022A-D` | `S1000D-J-04-10-0301-00A-022A-D` |
| ファイル名 | `DMC-S1000D-F-04-10-0301-00A-022A-D_001-00_EN-US.XML` | `DMC-S1000D-H-04-10-0301-00A-022A-D_001-00_EN-US.XML` | `DMC-S1000D-J-04-10-0301-00A-022A-D_001-00_EN-US.XML` |
| 標題（`infoName`） | “Default business rules exchange data module” | “Business rules exchange” | “Business rules exchange” |
| 方針文 | “S1000D at Issue 4.2” | “S1000D-SC-2016-017-002-00 Steering Committee TOR” | “S1000D-SC-2016-017-002-00 Steering Committee TOR” |
| `issueDate` | — | 2024-06-19 | 2026-07-16 |
| スキーマの宣言 | `http://www.s1000d.org/S1000D_4-2/xml_schema_flat/brex.xsd` | `http://www.s1000d.org/S1000D_6/xml_schema_flat/brex.xsd` | `http://www.s1000d.org/S1000D_7/xml_schema_flat/brex.xsd` |

Issue 4.2 のファイルは、公式パッケージの複製として GitHub に置かれているものです。Issue 6 と Issue 7 のファイルは、自分自身を、それぞれ `H` と `J` として参照しています。スキーマの宣言の URL は、執筆時に再取得していません。

### Issue 6 のパッチに含まれる後の発行

同じ DMC（`S1000D-H-04-10-0301-00A-022A-D`）の後の発行が、`S1000D Issue 6 Patches.zip` の中にもあります。

| 場所 | 発行番号 | 作業中番号 | 日付 |
| --- | --- | --- | --- |
| パッケージ本体 | `001` | `00` | 2024-06-19 |
| パッチのディレクトリ `S1000D_6.A_ BREX` | `002` | `00` | 2025-10-26 |
| データ辞書のパッチ側（ファイル名は `_002-01`） | `002` | `01` | 2025-09-05 |

パッチのフォルダ `Patch 6.0.A` の中のデータ辞書の README は、このパッチが属性 `termType` の値 `tt05` を除く、と書いています。同じフォルダには正誤表の PDF がありますが、本書ではその本文を使っていません。

## ビジネスルールのテンプレート（情報コード 024）

| 項目 | Issue 6 | Issue 7 |
| --- | --- | --- |
| DMC | `S1000D-H-04-10-0100-00A-024A-D` | `S1000D-J-04-10-0100-00A-024A-D` |
| `infoName` | “Business rules” | “Business rules” |
| スキーマの宣言 | `brdoc.xsd` | `brdoc.xsd` |
| `issueDate` | 2024-06-19 | 2026-07-16 |
| 識別子の発行番号・作業中番号 | `001`・`00` | `001`・`00` |
| ファイル名 | `DMC-S1000D-H-04-10-0100-00A-024A-D_000-01_EN-US.xml` | — |
| `brexDmRef` が指す既定 BREX | `H`（Issue 6 の既定 BREX） | `H`（Issue 6 の既定 BREX と同じ並び） |

Issue 6 のテンプレートは、ファイル名の `000-01` と、識別子の `001`・`00` が一致しません。同じ ZIP には `DMC-S1000D-H-04-10-0102-00A-024A-D_001-00_EN-US.PDF` もありますが、アセンブリが `0102` で、`0100` の XML とは別の DMC です。この PDF の本文は、本書では使っていません。

## 仕様本文とファイルの食い違い

次の二つは、仕様本文と同梱ファイルが食い違っている点です。本書は、どちらが誤記かを決めずに、両方を記録しています。プロジェクトの BREX を作るときは、その版の章と、参照する既定 BREX の両方を確認してください。

1. **BREX のアセンブリコード**：Issue 6 と Issue 7 の第4.10.2.4章は、SNS をサブサブシステムより下には適用せず、BREX のアセンブリコードは `00` または `0000` とする、と書いています。一方、同梱の既定 BREX の `assyCode` は `0301` です。
2. **Issue 7 のテンプレートの参照先**：Issue 7 のテンプレートの `brexDmRef` は、`systemDiffCode` が `H` の BREX（Issue 6 の既定 BREX と同じ並び）を指しています。同じパッケージの既定 BREX は `J` です。

## 自転車サンプルの発行情報

| DMC | `techName` / `infoName` | Issue 6 | Issue 7 |
| --- | --- | --- | --- |
| `S1000DBIKE-AAA-D00-00-00-00AA-041A-A` | “Bicycle” / “Description of how it is made” | 発行番号 `012`、2024-06-19、`http://www.s1000d.org/S1000D_6/xml_schema_flat/descript.xsd` | 発行番号 `013`、2026-07-16、`http://www.s1000d.org/S1000D_7/xml_schema_flat/descript.xsd` |
| `S1000DBIKE-AAA-DA1-00-00-00AA-041A-A` | “Brake system” / “Description of how it is made” | 発行番号 `010`、2024-06-19 | 発行番号 `011` |
| `BRAKE-AAA-DA1-00-00-00AA-041A-A` | “Brake system” / “Description of how it is made” | 発行番号 `004`、2024-06-19 | 発行番号 `005` |
| `S1000DBIKE-AAA-D00-00-00-00AA-121A-A` | “Bicycle” / “Pre-operation procedures (crew)” | 発行番号 `011`、2024-06-19、`proced.xsd` | 発行番号 `012`、`proced.xsd` |
| `S1000DBIKE-AAA-D00-00-00-00AA-258A-A` | “Bicycle” / “Other procedures to clean” | 発行番号 `011`、2024-06-19、`proced.xsd` | 発行番号 `012`、`proced.xsd` |
| `S1000DBIKE-AAA-D00-00-00-00AA-022A-D`（プロジェクト用 BREX） | “Mountain bicycle” / “Business rules exchange” | 発行番号 `011`、2024-06-19、`brexDmRef` は `H` | 発行番号 `012`、`brexDmRef` は `J` |

スキーマの宣言の URL は、執筆時に再取得していません。

Issue 4.1 と Issue 5.0 の説明ページは、自転車サンプルを、架空の製品の XML の例であり、適合性の試験集ではないと書いています。Issue 6 と Issue 7 の仕様本文を “fictional” と “conformance test” で検索した範囲では、同じ注意の文は見つかりませんでした。

## 出典

- 著者が入手した S1000D Issue 6 のパッケージ（上の 12 ファイル。仕様書の第4.10.2.4章、第7.3.1.4章、既定 BREX、ビジネスルールのテンプレート、自転車サンプル、`Patch 6.0.A`）。入手先は https://users.s1000d.org/
- 著者が入手した S1000D Issue 7 のパッケージ（上の 11 ファイル。仕様書の第4.2章、第4.10.2.4章、第7.3.1.4章、既定 BREX、ビジネスルールのテンプレート、自転車サンプル）。入手先は https://users.s1000d.org/
- S1000D. Issue 4.1 の説明. https://users.s1000d.org/ProductDescription.aspx?ProductID=16
- S1000D. Issue 5.0 の説明. https://users.s1000d.org/ProductDescription.aspx?ProductID=34
- S1000D. Downloads（メジャー、マイナー、パッチの説明）. https://users.s1000d.org/
- Issue 4.2 の既定 BREX（公式パッケージの複製）. https://github.com/kibook/S1000D/blob/master/csdb/DMC-S1000D-F-04-10-0301-00A-022A-D_001-00_EN-US.XML
