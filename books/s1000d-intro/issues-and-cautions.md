---
title: "版の違いと注意点"
---

S1000D を読むときに最初に決めるのは、どの Issue の話かです。メジャー版の間に、後方互換はありません。要素名と属性名の体系は、Issue 3.0 以前と Issue 4.0 以降で大きく変わります。どの版で納品するかは、仕様が新しいから、では決まりません。プロジェクトの要求で決まります。

## 版番号の公式の説明

ダウンロードサイトは、版番号を次のように説明しています。

> Major revisions of S1000D are indicated by change of the first position of the issue number; for example, change from issue 3.0 to 4.0. A major revision is not backward compatible and transformation of information will require some degree of human intervention.
>
> Minor revisions are indicated by change of the second position of the issue number; for example, change from issue 2.0 to 2.1. A minor revision is backward compatible and information can be transformed by automated processes without human intervention.
>
> Patches are indicated by addition of a letter in the third position of the issue number; for example issue 4.1.A. This indicates a release of minor corrections and bug fixes. No new technical functionality is introduced with patches.

日本語にすると、次のとおりです。

- メジャー改訂は、版番号の最初の位が変わることです。例は Issue 3.0 から 4.0 です。後方互換はなく、情報の変換には人の介在が必要です。
- マイナー改訂は、二番目の位が変わることです。例は Issue 2.0 から 2.1 です。後方互換があり、人の介在なしの自動的な変換で情報を移せます。
- パッチは、三番目の位の文字です。例は Issue 4.1.A です。細かい修正と不具合の修正であり、新しい技術的な機能は入りません。パッチは、対応するメジャーまたはマイナーのパッケージに含まれます。

同じページは、Issue 4.1 より前のパッチは、三番目の位が数字だった、と注意しています。Issue 4.0.1 や 4.0.2 が、この注意に当たります。

2026年10月23日時点の製品一覧は、新しい側を “Issue 6”“Issue 7” と表示しています。上の説明の例は 4.1.A や 5.0 のような形です。著者が入手した Issue 6 の表紙は “Issue No. 6”、日付は 2024-06-19 です。Issue 7 の表紙は “Issue No. 7”、日付は 2026-07-16 です。一覧の表示と、説明文の例と、入手したパッケージの表紙の番号が並ぶので、表紙の番号をその版の識別として使います。“6.0” や “7.0” を、一覧の表示から作りません。

Issue 7 の第7.3.1.4章は、パッチを版番号の二番目の位の英字で示す、と書き、例を Issue 6.A としています。同じ章の範囲は、前の版とこの版の、仕様本文と DM の XML スキーマの間の後方非互換の要約です。この章は、当該 Issue には後方互換の問題を持つ CPF は無かった、と書いています。これは、ダウンロードサイトが説明する「メジャー改訂には後方互換がない」という一般規則を取り消す文ではありません。また、パッチの位の説明が、ダウンロードサイトの「三番目の位の文字、例は 4.1.A」と、第7.3.1.4章の「二番目の位の英字、例は Issue 6.A」で違います。

Issue 6 の第7.3.1.4章も、パッチを版番号の二番目の位の英字で示す、と書き、例を Issue 6.A としています。第1.1節は、範囲を、前の版とこの版の、仕様そのものと DM の XML スキーマの間の後方非互換の要約に限る、と書いています。同じ章は、この版の CPF のうち後方互換に影響するものは無かった、と書いています。章のフッターの日付は 2024-03-07 です。表紙の 2024-06-19 として読みません。この章も、ダウンロードサイトの一般規則を取り消す文としては読みません。

:::message alert
同じ Issue 6 のパッケージの中に、次の表記が並んでいます。表紙は “Issue No. 6”。第7.3.1.4章の例は “Issue 6.A”。パッチのフォルダ名は “Patch 6.0.A”。その中の BREX のディレクトリ名は “S1000D_6.A_ BREX”。データ辞書の README の見出しは “Issue 6-01” と “Issue 6A” で、README に書かれた日付は 2025-11-14 です。仕様本文を “6.0.A” で検索した範囲では、その文字列は見つかりませんでした。これらの表記を一つにまとめません。ダウンロードサイトの 4.1.A という例へも、機械的に読み替えません。Issue 7 の “Issue 6.A” という例も、同じ読み替えの対象にしません。
:::

## 名前の体系が変わった箇所

Issue 3.0 から Issue 4.0 は、公式の説明がメジャー改訂の例に使っている境界です。この境界で、識別情報の書き方の名前が変わります。

Issue 3.0 の自転車サンプル（たとえば `DMC-S1000DBIKE-AAA-D00-00-00-00AA-121A-A_005-00.XML`）では、識別情報が `dmaddres/dmc/avee/` の下の子要素として書かれています。`avee` の子要素は次のとおりです。Issue 2.3 の自転車サンプルを印刷した公開物にも、同じパスの名前が現れます。

| 概念 | Issue 3.0 のサンプルの要素名 |
| --- | --- |
| モデル識別 | `modelic` |
| システム差分 | `sdc` |
| システム | `chapnum` |
| サブシステム | `section` |
| サブサブシステム | `subsect` |
| アセンブリ | `subject` |
| 分解コード | `discode` |
| 分解コードのバリアント | `discodev` |
| 情報コード | `incode` |
| 情報コードのバリアント | `incodev` |
| アイテムロケーション | `itemloc` |

Issue 4.1 のスキーマを参照する公開テンプレート、Issue 4.2 の既定 BREX、Issue 6 の自転車サンプルと既定 BREX、Issue 7 の自転車サンプルと既定 BREX では、同じ概念が `dmCode` の属性です。Issue 6 のサンプルが宣言しているスキーマの位置は `http://www.s1000d.org/S1000D_6/xml_schema_flat/` の下、Issue 7 のサンプルは `http://www.s1000d.org/S1000D_7/xml_schema_flat/` の下です。これらの URL を、本書の執筆時に再取得してはいません。

| 概念 | Issue 4.1 / 4.2 / 6 / 7 の属性名 |
| --- | --- |
| モデル識別 | `modelIdentCode` |
| システム差分 | `systemDiffCode` |
| システム | `systemCode` |
| サブシステム | `subSystemCode` |
| サブサブシステム | `subSubSystemCode` |
| アセンブリ | `assyCode` |
| 分解コード | `disassyCode` |
| 分解コードのバリアント | `disassyCodeVariant` |
| 情報コード | `infoCode` |
| 情報コードのバリアント | `infoCodeVariant` |
| アイテムロケーション | `itemLocationCode` |

外側の構造も違います。Issue 3.0 のパスは `dmaddres`、`dmc`、`avee` です。Issue 4.1、Issue 4.2、Issue 6、Issue 7 のサンプルは `identAndStatusSection`、`dmAddress`、`dmIdent`、`dmCode` です。短い省略名の子要素から、意味の取れるキャメルケースの属性へ、という変わり方は、少なくとも Issue 6 と Issue 7 のサンプルまで続いています。Issue 4.0 の自転車サンプルも、`identAndStatusSection` と `dmCode` の属性を使っています。

参照の要素名にも、同じ境界があります。Issue 3.0 の自転車サンプルの DM は `brexref`、Issue 4.0 の自転車サンプルの DM は `brexDmRef` を使っています。PM の `brexDmRef` は、Issue 4.0 の `pm.xsd` にはなく、Issue 4.1 の自転車サンプルの PM にはあります。

:::message alert
Issue 4.0 以降のサンプルを、Issue 3.0 のスキーマで検査することはできません。逆も同じです。変換ツールがファイルを出力しても、公式の説明どおり、メジャー改訂の変換には人の確認が必要です。
:::

マークアップ言語の世代についても、二次資料がダウンロード物の構成を次のようにまとめています。Issue 3.0 までは SGML の DTD と XML を含み、Issue 4.0 以降は XML スキーマである、という表です。一次の確認は、入手したパッケージに DTD があるか、XSD だけかを見ることです。Issue 6 と Issue 7 の XML スキーマパッケージの一覧には XSD があり、DTD のファイルはありませんでした。これは、この二つのパッケージの観察です。二次資料の各日付を、表紙の発行日としては採用しません。Issue 6 の表紙の日付は 2024-06-19、Issue 7 の表紙の日付は 2026-07-16 です。章ごとのフッターには、それより前の日付が残っています。Issue 6 では、第7.3.1.4章のフッターが 2024-03-07、第4.10.2.4章のフッターが 2023-10-06 です。Issue 7 では、たとえば第4.2章のフッターが 2020-01-09 です。フッターの日付を、パッケージの発行日として読みません。

## マイナー改訂とパッチで油断しない

マイナー改訂は後方互換があり、自動変換できる、というのが公式の説明です。自動変換できることと、プロジェクトの BREX や出版の見た目が変わらないことは、別です。マイナー版でも、情報コードの追加、スキーマの任意項目の追加、説明文の明確化は起きます。納品の版は、マイナー番号まで契約と一致させます。

パッチは新しい機能を入れない、というのが公式の説明です。Issue 5.0 の説明ページは、パッチ 5.0.A がスキーマの呼び出し行の誤りを直し、5.0.B がデータ辞書に欠けていた属性の定義を補った、と書いています。機能が増えていなくても、検査の結果はパッチの前後で変わり得ます。使っているパッケージが、どのパッチを含んだ状態かを記録します。

Issue 6 の `S1000D Issue 6 Patches.zip` は、フォルダ `Patch 6.0.A` を含みます。その中のデータ辞書 README の概要は、このパッチが属性 `termType` の値 `tt05` を除く、と書いています。同じフォルダには正誤表の PDF があります。正誤表の本文は、本書の記述には使っていません。既定 BREX の本体は、発行番号 `001`、作業中番号 `00`、日付 2024-06-19 です。同じ DMC が、ディレクトリ `S1000D_6.A_ BREX` では発行番号 `002`、作業中番号 `00`、日付 2025-10-26 としてあり、データ辞書のパッチ側ではファイル名 `_002-01`、識別子は発行番号 `002`、作業中番号 `01`、日付 2025-09-05 です。本体の `001` を、パッチ側の発行に置き換えて読みません。`002`/`00` と `002`/`01` も、一つの発行情報にまとめません。

## 納品の版はプロジェクトが決める

新しい Issue が公開されていることは、進行中の契約の版が上がったことを意味しません。契約が Issue 4.1 なら、納品物は Issue 4.1 のスキーマと、そのプロジェクトの BREX に対して作ります。Issue 5.0 の要素を混在させません。

版が契約に書いていない場合に、供給側が新しい版を選んで納品を始めることは、仕様の要求ではありません。版を決めるのはプロジェクトの要求です。決まるまでは、要素名のレベルで文書を作り始められません。学習のために新しい版を読むことと、納品の版を決めることは、第10章で分けたとおり、別の行為です。

複数の版が一つの CSDB に共存するかも、仕様の概要は決めていません。共存させるなら、版をまたいだ DMC の一意性、変換の責任、どの版を IETP が見るかは、プロジェクトのルールに書く事項です。

## この章でしないこと

次のことは、本書の範囲外です。

- Issue ごとの要素名の全対照表。対照は、入手したパッケージのスキーマで作ります。
- 特定の変換ツールの操作。メジャー改訂に人の介在が必要だ、という公式の説明以上の手順は書きません。
- 「新しい版の方が正しい」という評価。正しい版は、契約が指定した版です。

## 出典

- S1000D. Downloads（メジャー、マイナー、パッチの説明）. https://users.s1000d.org/
- S1000D. Product list（Issue 6、Issue 7 を含む一覧）. https://users.s1000d.org/ProductList.aspx
- S1000D. Issue 5.0 の説明（パッチ 5.0.A、5.0.B の内容）. https://users.s1000d.org/ProductDescription.aspx?ProductID=34
- Issue 2.3 の自転車サンプル出版物の印刷（`dmaddres/dmc/avee/modelic` などのパス。Docuneering 社による印刷）. https://www.docuneering.com/s1000d/demo/pdf/s1000d-issue-2.3/PMC-S1000DBIKE-X1234-00023-00_002-00_EN-US.pdf
- Issue 4.1 のスキーマを参照する公開テンプレート. https://github.com/oxygenxml/S1000D/blob/master/templates/Data%20Module.xml
- Issue 4.2 の既定 BREX. https://github.com/kibook/S1000D/blob/master/csdb/DMC-S1000D-F-04-10-0301-00A-022A-D_001-00_EN-US.XML
- 著者が入手した S1000D Issue 3.0 のパッケージ（自転車サンプル `DMC-S1000DBIKE-AAA-D00-00-00-00AA-121A-A_005-00.XML` の `dmaddres/dmc/avee` と `brexref`）。入手先は https://users.s1000d.org/
- 著者が入手した S1000D Issue 4.0 と Issue 4.1 のパッケージ（Issue 4.0 の自転車サンプルの `brexDmRef` と `pm.xsd`、Issue 4.1 の自転車サンプルの PM）。入手先は https://users.s1000d.org/
- Wikipedia. *S1000D*（マークアップ言語の世代の二次的なまとめ。日付の採用は本書ではしていない）. https://en.wikipedia.org/wiki/S1000D
- 著者が入手した S1000D Issue 6 のパッケージ（表紙は Issue No. 6、日付は 2024-06-19。第7.3.1.4章、第4.10.2.4章、サンプルの要素名、スキーマパッケージ、`Patch 6.0.A`）。入手先は https://users.s1000d.org/
- 著者が入手した S1000D Issue 7 のパッケージ（表紙は Issue No. 7、日付は 2026-07-16。第7.3.1.4章、サンプルの要素名、スキーマパッケージ）。入手先は https://users.s1000d.org/

## この章のポイント

- メジャー改訂は後方互換がなく、ダウンロードサイトの例は Issue 3.0 から 4.0 である。変換には人の介在が必要である。Issue 6 と Issue 7 の第7.3.1.4章が「この版の CPF に後方互換の問題は無かった」と書くことは、この一般規則の取消ではない。
- この境界で、識別情報は `modelic` や `chapnum` のような子要素から、`modelIdentCode` や `systemCode` のような属性へ変わっている。Issue 6 と Issue 7 のサンプルも後者の名前を使っている。
- 納品の版はプロジェクトの要求で決まる。新しい Issue の公開は、契約の版を更新しない。パッチの位の説明は、ダウンロードサイト、Issue 6 と Issue 7 の第7.3.1.4章、Issue 6 のパッケージ内のフォルダ名で違う。表紙の番号を使う。
