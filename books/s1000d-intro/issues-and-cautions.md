---
title: "版の違いと注意点"
---

S1000D を読むときに最初に確認するのは、どの版（Issue）の話かです。メジャー版の間には後方互換がなく、要素名と属性名の体系も、Issue 3.0 以前と Issue 4.0 以降で大きく変わっています。どの版で納品するかは、新しい版かどうかではなく、プロジェクトの要求で決まります。

この章では、版番号の読み方、版によって名前が変わった箇所、版を扱うときの注意点を説明します。

## 版番号の読み方

ダウンロードサイトは、版番号を次のように説明しています。

> Major revisions of S1000D are indicated by change of the first position of the issue number; for example, change from issue 3.0 to 4.0. A major revision is not backward compatible and transformation of information will require some degree of human intervention.
>
> Minor revisions are indicated by change of the second position of the issue number; for example, change from issue 2.0 to 2.1. A minor revision is backward compatible and information can be transformed by automated processes without human intervention.
>
> Patches are indicated by addition of a letter in the third position of the issue number; for example issue 4.1.A. This indicates a release of minor corrections and bug fixes. No new technical functionality is introduced with patches.

| 改訂の種類 | 番号の変わり方 | 内容 |
| --- | --- | --- |
| メジャー改訂 | 1 番目の位が変わる（例：Issue 3.0 → 4.0） | 後方互換がなく、情報の変換には人の作業が必要 |
| マイナー改訂 | 2 番目の位が変わる（例：Issue 2.0 → 2.1） | 後方互換があり、人の作業なしに自動で変換できる |
| パッチ | 3 番目の位に文字が付く（例：Issue 4.1.A） | 小さな修正と不具合の修正で、新しい機能は入らない。対応する版のパッケージに含まれる |

同じページは、Issue 4.1 より前のパッチは 3 番目の位が数字だったと注意しています。Issue 4.0.1 や 4.0.2 がその例です。

新しい版の番号の書き方には、注意が必要です。2026 年 10 月 23 日時点の製品一覧は、新しい版を “Issue 6”、“Issue 7” と表示しており、仕様書の表紙も “Issue No. 6”、“Issue No. 7” です。本書では、表紙の番号を版の識別に使い、一覧の表示から “6.0” や “7.0” のような番号は作りません。

また、Issue 6 と Issue 7 の仕様書の第7.3.1.4章は、パッチを「版番号の 2 番目の位の英字」で示すと書き、例を Issue 6.A としています。ダウンロードサイトの説明（3 番目の位、例は 4.1.A）とは違います。このように、版番号の表記は資料によってゆれています。具体的な表記のゆれは、付録にまとめています。

:::message
Issue 6 と Issue 7 の第7.3.1.4章は、「この版の CPF のうち、後方互換に影響するものは無かった」とも書いています。これは、「メジャー改訂には後方互換がない」という一般の規則を取り消すものではありません。版を移行するときは、変換に人の確認が必要だという前提で計画してください。
:::

## 名前の体系が変わった箇所

Issue 3.0 から Issue 4.0 への改訂は、ダウンロードサイトがメジャー改訂の例に挙げているものです。この境界で、DMC などの識別情報の書き方が大きく変わりました。

Issue 3.0 の自転車サンプル（たとえば `DMC-S1000DBIKE-AAA-D00-00-00-00AA-121A-A_005-00.XML`）では、識別情報を `dmaddres/dmc/avee/` の下に、短い省略名の子要素として書いています。Issue 2.3 の自転車サンプルを印刷した公開物にも、同じパスの名前が現れます。

Issue 4.0 以降では、同じ情報を `identAndStatusSection` の下の `dmCode` 要素に、意味が分かるキャメルケースの属性として書きます。Issue 4.0 の自転車サンプルも、`identAndStatusSection` と `dmCode` を使っています。Issue 4.1、4.2、6、7 のサンプルや既定 BREX では、`identAndStatusSection`、`dmAddress`、`dmIdent`、`dmCode` の順に入れ子になっています。

| 概念 | Issue 3.0 のサンプルの要素名 | Issue 4.0 以降の属性名 |
| --- | --- | --- |
| モデル識別 | `modelic` | `modelIdentCode` |
| システム差分 | `sdc` | `systemDiffCode` |
| システム | `chapnum` | `systemCode` |
| サブシステム | `section` | `subSystemCode` |
| サブサブシステム | `subsect` | `subSubSystemCode` |
| アセンブリ | `subject` | `assyCode` |
| 分解コード | `discode` | `disassyCode` |
| 分解コードのバリアント | `discodev` | `disassyCodeVariant` |
| 情報コード | `incode` | `infoCode` |
| 情報コードのバリアント | `incodev` | `infoCodeVariant` |
| アイテムロケーション | `itemloc` | `itemLocationCode` |

BREX を参照する要素名にも、同じ境界があります。Issue 3.0 の自転車サンプルの DM は `brexref`、Issue 4.0 の自転車サンプルの DM は `brexDmRef` を使っています。PM の `brexDmRef` は、Issue 4.0 の `pm.xsd` にはなく、Issue 4.1 の自転車サンプルの PM にはあります。

:::message alert
Issue 4.0 以降のサンプルを、Issue 3.0 のスキーマで検査することはできません。その逆もできません。変換ツールがファイルを出力しても、公式の説明のとおり、メジャー改訂をまたぐ変換には人の確認が必要です。
:::

### マークアップの世代

Wikipedia などの二次資料は、Issue 3.0 までは SGML の DTD と XML を含み、Issue 4.0 以降は XML スキーマになったとまとめています。確実なのは、入手したパッケージに DTD があるか、XSD だけかを見ることです。Issue 3.0 のパッケージには SGML と XML の DTD と XML スキーマがあり、Issue 6 と Issue 7 の XML スキーマパッケージには XSD だけがあって、DTD はありません。

## マイナー改訂とパッチで気を付けること

公式の説明では、マイナー改訂は後方互換があり、自動で変換できます。ただし、自動で変換できることと、プロジェクトの BREX や出版物の見た目が変わらないことは別です。マイナー改訂でも、情報コードの追加、スキーマへの任意の項目の追加、説明の明確化などが起こります。納品の版は、マイナー版の番号まで契約と一致させてください。

パッチは新しい機能を入れない、というのが公式の説明です。しかし、Issue 5.0 の説明ページによると、パッチ 5.0.A はスキーマの呼び出し行の誤りを直し、5.0.B はデータ辞書に欠けていた属性の定義を補っています。Issue 6 のパッチ（`Patch 6.0.A`）も、データ辞書の README によると、属性 `termType` の値 `tt05` を除いています。機能が増えなくても、パッチの前後で検査の結果は変わり得ます。使っているパッケージが、どのパッチまで含んでいるかを記録しておいてください。

## 納品の版はプロジェクトが決める

新しい版が公開されても、進行中の契約の版が上がるわけではありません。契約が Issue 4.1 なら、納品物は Issue 4.1 のスキーマと、そのプロジェクトの BREX に沿って作ります。Issue 5.0 の要素を混ぜてはいけません。

契約に版が書かれていない場合に、供給者が新しい版を選んで作り始めることは、仕様が求めていることではありません。版を決めるのはプロジェクトの要求です。版が決まるまでは、要素名のレベルで文書を作り始められません。学習のために新しい版を読むことと、納品の版を決めることは、第10章で説明したとおり、別のことです。

複数の版のデータを一つの CSDB に置くことについても、仕様の概要ページは何も決めていません。置く場合は、版をまたいだ DMC の一意性、変換の責任、IETP がどの版を表示するかを、プロジェクトのルールに書く必要があります。

## この章の範囲外のこと

- 版ごとの要素名の全体の対照表。使う版のスキーマで作ってください。
- 特定の変換ツールの操作。メジャー改訂には人の作業が必要、という公式の説明以上の手順は書いていません。
- 「新しい版の方が正しい」という評価。正しい版は、契約で指定された版です。

## 出典

- S1000D. Downloads（メジャー、マイナー、パッチの説明）. https://users.s1000d.org/
- S1000D. Product list（Issue 6、Issue 7 を含む一覧）. https://users.s1000d.org/ProductList.aspx
- S1000D. Issue 5.0 の説明（パッチ 5.0.A、5.0.B の内容）. https://users.s1000d.org/ProductDescription.aspx?ProductID=34
- Issue 2.3 の自転車サンプル出版物の印刷（`dmaddres/dmc/avee/modelic` などのパス。Docuneering 社による印刷）. https://www.docuneering.com/s1000d/demo/pdf/s1000d-issue-2.3/PMC-S1000DBIKE-X1234-00023-00_002-00_EN-US.pdf
- Issue 4.1 のスキーマを参照する公開テンプレート. https://github.com/oxygenxml/S1000D/blob/master/templates/Data%20Module.xml
- Issue 4.2 の既定 BREX. https://github.com/kibook/S1000D/blob/master/csdb/DMC-S1000D-F-04-10-0301-00A-022A-D_001-00_EN-US.XML
- 著者が入手した S1000D Issue 3.0 のパッケージ（自転車サンプル `DMC-S1000DBIKE-AAA-D00-00-00-00AA-121A-A_005-00.XML` の `dmaddres/dmc/avee` と `brexref`、SGML と XML の DTD、XML スキーマ）。入手先は https://users.s1000d.org/
- 著者が入手した S1000D Issue 4.0 と Issue 4.1 のパッケージ（Issue 4.0 の自転車サンプルの `brexDmRef` と `pm.xsd`、Issue 4.1 の自転車サンプルの PM）。入手先は https://users.s1000d.org/
- Wikipedia. *S1000D*（マークアップの世代の二次的なまとめ）. https://en.wikipedia.org/wiki/S1000D
- 著者が入手した S1000D Issue 6 のパッケージ（表紙は Issue No. 6、日付は 2024-06-19。第7.3.1.4章、サンプルの要素名、スキーマパッケージ、`Patch 6.0.A`）。入手先は https://users.s1000d.org/
- 著者が入手した S1000D Issue 7 のパッケージ（表紙は Issue No. 7、日付は 2026-07-16。第7.3.1.4章、サンプルの要素名、スキーマパッケージ）。入手先は https://users.s1000d.org/

## この章のポイント

- メジャー改訂には後方互換がなく、変換には人の作業が必要です。ダウンロードサイトは、その例に Issue 3.0 から 4.0 への改訂を挙げています。
- この境界で、識別情報の書き方は、`modelic` や `chapnum` のような子要素から、`modelIdentCode` や `systemCode` のような属性に変わりました。Issue 6 と Issue 7 も後者の書き方です。
- 納品の版は、プロジェクトの要求で決まります。新しい版が公開されても、契約の版は変わりません。版の識別には、仕様書の表紙の番号を使います。
