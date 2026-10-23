---
title: "Data Module Code の読み方"
---

データモジュールコード（Data Module Code、DMC）は、共通ソースデータベースからデータモジュールを一つに特定して取り出すためのコードです。読み方の芯は一つです。前半は、どの製品の、どの部分の情報かです。後半は、その部分についての、どんな種類の情報かです。

:::message
フィールドの長さの範囲は版が示し、その範囲の中のどれを採るかはプロジェクトが決めます。Issue 7 の第4.3章では、ここに挙げる文字は英大文字と数字です。情報コードは3文字、情報コードのバリアントは1文字、アイテムロケーションコードは英大文字1文字です。モデル識別は2文字から14文字、システム差分は1文字から4文字、アセンブリは2文字または4文字、分解コードは2文字、分解コードのバリアントは1文字から3文字、という範囲です。符号化方針そのものは、プロジェクトの決定点 BRDP-S1-00331 です。この章の表は、その範囲の中の公式サンプルです。別のプロジェクトのコードを、サンプルの桁数だけに当てはめないでください。他の版の範囲は、その版の第4.3章で確認します。
:::

## 二つの部分

DMC は、システムに関する部分と、情報に関する部分に分かれます。

システムに関する部分は、製品と、その製品の中の位置を表します。モデル識別コード（model identification code）、システム差分コード（system difference code）、標準番号体系（Standard Numbering System、SNS）、分解コード（disassembly code）とそのバリアントがここに入ります。SNS は、システム、サブシステム、サブサブシステム、アセンブリという階層です。階層の意味、つまり番号のどの範囲をブレーキ系統と呼ぶかは、プロジェクトが SNS として定義します。仕様は階層の枠を用意し、自転車のブレーキをどの番号にするかは仕様が決めません。

情報に関する部分は、その位置について何を書いたかを表します。情報コード（information code）、そのバリアント、アイテムロケーションコード（item location code）がここに入ります。同じブレーキでも、「どう作られているかの説明」と「取り外しの手順」は、情報コードが違う別の DM になります。

Issue 4.1 のスキーマでは、これらの値は `dmCode` 要素の属性です。属性名は `modelIdentCode`、`systemDiffCode`、`systemCode`、`subSystemCode`、`subSubSystemCode`、`assyCode`、`disassyCode`、`disassyCodeVariant`、`infoCode`、`infoCodeVariant`、`itemLocationCode` です。Issue 4.2 の既定 BREX も、Issue 6 と Issue 7 の自転車サンプルと既定 BREX も、同じ属性名を使っています。Issue 7 の第4.3章は、DMC を標準化された構造の識別子だと書き、要素 `dmCode` に格納する、と述べています。Issue 3.0 以前は、同じ内容を属性ではなく子要素で書く版があります。第11章で、Issue 2.3 のサンプルに現れる要素名と対照します。

訓練用、または人的パフォーマンス技術の DM には、Issue 7 の第4.3章が第三の区画を足します。学習コード（learn code）は3文字で、先頭は `H` または `T` です。学習イベントコード（learn event code）は英大文字1文字です。この区画を含むときの最小長は21文字、含まないときの最小長は17文字、と第4.3章は書いています。自転車の記述サンプルは、この区画を使いません。ファイル名がアイテムロケーションの後ろにさらに文字を持つときは、その版の第4.3.9章と第4.3.10章を見ます。本書は、学習コードの値の表は写しません。

## 公式サンプルのコード

仕様のダウンロードには、自転車のサンプルデータ（Bike sample data set）が含まれます。Issue 4.1 と Issue 5.0 のダウンロード説明は、このデータセットを、架空の製品に関する XML の例だと明記しています。適合性の試験集ではなく、このデータを処理できたことが S1000D への適合を意味するわけでもない、とも書いてあります。Issue 6 と Issue 7 のパッケージにも自転車サンプルは含まれます。それぞれの仕様本文を “fictional” と “conformance test” で検索した範囲では、この注意文そのものは見つかりませんでした。Issue 4.1 と Issue 5.0 の説明にある注意を、Issue 6 または Issue 7 の本文に書いてあることとしては扱いません。サンプルを処理できたことを、適合の証拠にはしません。

Issue 7 の自転車サンプルに現れるコードの一つが、次です。ファイルは `DMC-S1000DBIKE-AAA-D00-00-00-00AA-041A-A_013-00_EN-US.XML` です。

```text
S1000DBIKE-AAA-D00-00-00-00AA-041A-A
```

この XML の `techName` は “Bicycle”、`infoName` は “Description of how it is made” です。発行日の要素は 2026-07-16、スキーマの宣言は `http://www.s1000d.org/S1000D_7/xml_schema_flat/descript.xsd` です。この URL を、本書の執筆時に再取得してはいません。情報コード `041` の意味を、このサンプルの標題から読みます。全版の情報コード表の写しは、本書には置きません。表は各版の第8.4章と、既定の BREX にあります。

Issue 6 の自転車サンプルにも、同じ DMC のファイル `DMC-S1000DBIKE-AAA-D00-00-00-00AA-041A-A_012-00_EN-US.XML` があります。`dmCode` の属性、`techName`、`infoName` は Issue 7 のファイルと同じです。発行番号は `012`、作業中番号は `00`、`issueDate` は 2024-06-19、スキーマの宣言は `http://www.s1000d.org/S1000D_6/xml_schema_flat/descript.xsd` です。この URL も、再取得してはいません。下のハイフンの読みは、Issue 7 の第4.3章の説明です。Issue 6 のファイルは、同じ属性値を持っています。

Issue 7 の第4.3章は、DMC を図に示した位置のハイフンで示し、ハイフンは属性の値には含めない、と書いています。図の一つの区切りが、一つの属性とは限りません。このコードの切れ目は、次のとおりです。属性名は Issue 6 と Issue 7 の `dmCode` に現れる名前で、Issue 4.1 と Issue 4.2 で確認した名前と同じです。

| 部分 | Issue 6 と Issue 7 の属性名 | 値 | この例での読み |
| --- | --- | --- | --- |
| システム | modelIdentCode | S1000DBIKE | サンプルの自転車という製品 |
| システム | systemDiffCode | AAA | その製品の中の差分。サンプルではこの3文字 |
| システム | systemCode | D00 | SNS のシステム。3文字 |
| システム | subSystemCode | 0 | サブシステム。次の1文字と組で `00` と書かれる |
| システム | subSubSystemCode | 0 | サブサブシステム |
| システム | assyCode | 00 | アセンブリ。この例では2文字 |
| システム | disassyCode | 00 | 分解コード。次のバリアントと組で `00AA` と書かれる |
| システム | disassyCodeVariant | AA | 分解コードのバリアント。この例では2文字 |
| 情報 | infoCode | 041 | 情報の種類。この DM の標題は「どのように作られているかの説明」 |
| 情報 | infoCodeVariant | A | 情報コードのバリアント。`041` と組で `041A` と書かれる |
| 情報 | itemLocationCode | A | Issue 7 の第4.3.8章では、製品に装着された品目に関する情報 |

文字列を左から読むと、`S1000DBIKE`、`AAA`、`D00`、`00`、`00`、`00AA`、`041A`、`A` です。4番目の `00` はサブシステム `0` とサブサブシステム `0`、5番目の `00` はアセンブリ、`00AA` は分解コードとバリアント、`041A` は情報コードとバリアントです。SNS を空白なしでつなぐと `D000000`（`D00` + `0` + `0` + `00`）になります。

同じ Issue 7 のサンプルには、モデル識別 `S1000DBIKE`、システム `DA1`、情報コード `041` の記述 DM もあります。`techName` は “Brake system”、`infoName` は先の自転車全体の DM と同じ “Description of how it is made” です。階層が違えば、同じ情報コードでも別の DM です。サンプルには、モデル識別が `BRAKE` の別の記述 DM もあります。`S1000DBIKE` のブレーキと、`BRAKE` という製品は、同じファイルではありません。Issue 6 のサンプルにも、同じ二つの DMC があります。`S1000DBIKE` 側の発行番号は `010`、`BRAKE` 側の発行番号は `004`、`issueDate` はどちらも 2024-06-19 です。標題は Issue 7 のファイルと同じです。

## 同じ概念でも桁が違う公式の例

Issue 4.2 の既定 BREX として流通しているファイルの DMC は、次の属性を持っています。方針文は “S1000D at Issue 4.2” です。このファイルは公式サイトそのものではなく、公式パッケージの複製です。

```text
modelIdentCode       = S1000D
systemDiffCode       = F
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

ファイル名に現れる文字列は `S1000D-F-04-10-0301-00A-022A-D` です。`10` はサブシステム `1` とサブサブシステム `0`、`0301` は4文字のアセンブリ、`00A` は分解コードと1文字のバリアントです。

Issue 6 のパッケージに同梱されている既定 BREX は、次の属性です。ファイル名は `DMC-S1000D-H-04-10-0301-00A-022A-D_001-00_EN-US.XML` です。`techName` は “S1000D”、`infoName` は “Business rules exchange” です。方針文は “S1000D-SC-2016-017-002-00 Steering Committee TOR” です。`issueDate` は 2024-06-19、発行番号は `001`、作業中番号は `00` です。スキーマの宣言は `http://www.s1000d.org/S1000D_6/xml_schema_flat/brex.xsd` です。

```text
modelIdentCode       = S1000D
systemDiffCode       = H
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

Issue 7 のパッケージに同梱されている既定 BREX は、次の属性です。ファイル名は `DMC-S1000D-J-04-10-0301-00A-022A-D_001-00_EN-US.XML` です。`techName` は “S1000D”、`infoName` は “Business rules exchange” です。方針文は “S1000D-SC-2016-017-002-00 Steering Committee TOR” であり、Issue 4.2 の複製にあった “S1000D at Issue 4.2” ではありません。`issueDate` は 2026-07-16 です。スキーマの宣言は `http://www.s1000d.org/S1000D_7/xml_schema_flat/brex.xsd` です。

```text
modelIdentCode       = S1000D
systemDiffCode       = J
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

Issue 4.2 の複製では `systemDiffCode` が `F`、Issue 6 の同梱ファイルでは `H`、Issue 7 の同梱ファイルでは `J` です。他の `dmCode` の属性は、この三つの既定 BREX で同じです。`F` を Issue 6 や Issue 7 の既定として使わないでください。`H` を Issue 7 の既定として使わないでください。

自転車サンプルの `systemDiffCode` は3文字、既定 BREX のそれは1文字です。アセンブリは、前者が2文字、後者が4文字です。どちらも Issue 7 の第4.3章が示す範囲の中です。範囲の中のどれを採るかが、プロジェクトの符号化方針です。Issue 6 の第4.3章の長さの範囲は、本書では写していません。

Issue 6 と Issue 7 の既定 BREX は、BREX の情報コードは `022`、アイテムロケーションコードは `D` である、と書き、Chap 4.10.2.4 Para 2 を引いて検査しています。同じ二つの値は、Issue 6 と Issue 7 の第4.10.2.4章の本文にもあります。`022` は「説明」のコードではありません。ビジネスルールを交換する DM のコードです。BREX は第8章で説明します。

:::message alert
Issue 6 の第4.10.2.4章は、SNS をサブサブシステムより下には適用しない、と書き、BREX のアセンブリコードを `00` または `0000` とする、とも書いています。同じパッケージの既定 BREX の `assyCode` は `0301` です。Issue 7 の第4.10.2.4章も、アセンブリコードを `00` または `0000` とし、同梱の既定 BREX の `assyCode` は `0301` です。本書はこの差を、どちらかが誤記だと決めて解消しません。プロジェクトの BREX を書くときは、その版の章と、参照する既定 BREX の両方を見ます。
:::

## 情報コードの読み方

情報コードは3文字です。先頭の文字が大まかな族を表し、残りの文字が族の中の種類を表します。プロジェクトは、仕様が許す位置に限り、独自のコードを足せます。足せる範囲の条件は版の情報コードの章にあります。

本書が公式の配布物で確認した例は、次のとおりです。意味は、その DM の標題または、Issue 7 の章の文から読んだものであり、情報コード表の全文の写しではありません。

- `041`: Issue 6 と Issue 7 の自転車サンプルが “Description of how it is made” という `infoName` を付けている記述の DM
- `022`: Issue 6 と Issue 7 の第4.10.2.4章と、同梱の既定 BREX が、BREX データモジュールの情報コードだと書いている値。Issue 4.2 の複製も同じ値を検査している
- `024`: Issue 6 と Issue 7 のビジネスルールのテンプレート。`infoName` は “Business rules”。第8章で、既定 BREX との違いを説明します。それより前のすべての版で確認したわけではありません

族の全表、たとえば先頭が `0` のとき何を意味し、`4` のとき何を意味するかは、入手した版の表で確認します。表を記憶してからサンプルを読む必要はありません。サンプルの DM を一つ開き、情報コードを表で引く、という順で足ります。

アイテムロケーションコードは、Issue 7 の第4.3.8章では英字1文字です。同じ章が挙げる値は、次のとおりです。

| 値 | Issue 7 の第4.3.8章の説明 |
| --- | --- |
| A | 製品に装着された品目に関する情報 |
| B | 製品から取り外された主要アセンブリに装着された品目に関する情報 |
| C | 作業台の上の品目に関する情報。製品から外したかどうかは問わない |
| D | A、B、C の三つすべてに関する情報。他の組み合わせは許されない |
| T | 訓練の DM に関する情報。学習コードを使う場合の例外は、同じ章が第4.3.9章と第4.3.10章へ回している |

自転車の記述サンプルは `A`、既定 BREX は `D` です。他の版の符号表が同じとは限りません。

## ファイル名と DMC を混ぜない

CSDB 上のファイル名は、DMC より長いことがあります。Issue 7 の既定 BREX のファイル名は、次の形です。

```text
DMC-S1000D-J-04-10-0301-00A-022A-D_001-00_EN-US.XML
```

`DMC-` は接頭辞です。アンダースコアの後ろの `001-00` は発行番号と作業中番号、`EN-US` は言語です。DMC は、アンダースコアより前の `S1000D-J-04-10-0301-00A-022A-D` です。発行が進んでも、言語が増えても、DMC は変わりません。変わるのは発行情報と言語です。自転車の記述サンプルでは、Issue 7 の同じ位置が `_013-00_EN-US`、Issue 6 では `_012-00_EN-US` です。発行番号が違っても、DMC の属性は変わりません。ファイル名の発行情報と、XML の `issueInfo` が食い違うファイルもあります。Issue 6 の情報コード `024` のテンプレートは、ファイル名が `_000-01` で、識別子は発行番号 `001`、作業中番号 `00` です。ファイル名を、識別子の代わりに採用しません。

## 出典

- S1000D. *About S1000D*（DMC を識別子として保管し取り出す）. https://s1000d.org/?page_id=101
- S1000D. Issue 4.1 のダウンロード説明（Bike sample は架空の製品であり、適合性試験集ではない）. https://users.s1000d.org/ProductDescription.aspx?ProductID=16
- S1000D. Issue 5.0 のダウンロード説明（Bike sample を同梱物として説明）. https://users.s1000d.org/ProductDescription.aspx?ProductID=34
- Issue 4.1 のスキーマを参照する公開テンプレート（`dmCode` の属性名）. https://github.com/oxygenxml/S1000D/blob/master/templates/Data%20Module.xml
- Issue 4.2 の既定 BREX. https://github.com/kibook/S1000D/blob/master/csdb/DMC-S1000D-F-04-10-0301-00A-022A-D_001-00_EN-US.XML
- 著者が入手した S1000D Issue 6 のパッケージ（第4.10.2.4章、既定 BREX、自転車サンプル、情報コード `024` のテンプレート）。入手先は https://users.s1000d.org/
- 著者が入手した S1000D Issue 7 のパッケージ（第4.3章、第4.3.8章、第4.10.2.4章、既定 BREX、自転車サンプル）。入手先は https://users.s1000d.org/
- 自転車サンプルのコードと標題が印刷されている公開の出版物（Docuneering 社による印刷。Issue 7 の XML で同じ DMC と標題を確認したあとも、この PDF の表紙からは元の Issue 番号を特定できない）. https://www.docuneering.com/s1000d/demo/pdf/PMC-S1000DBIKE-C3002-LOAP1-00_000-02_EN-US.pdf

## この章のポイント

- DMC は、どの製品のどの部分か、という部分と、どんな種類の情報か、という部分に分かれる。訓練の DM には、Issue 7 では学習コードの区画が加わる。
- Issue 6 と Issue 7 の自転車サンプル `S1000DBIKE-AAA-D00-00-00-00AA-041A-A` と、既定 BREX は、同じ種類のフィールドを違う桁数で持っている。既定 BREX の `systemDiffCode` は、Issue 4.2 の複製では `F`、Issue 6 では `H`、Issue 7 では `J` である。Issue 7 の第4.3章の範囲の説明を、Issue 6 の章の写しとしては使っていない。
- ハイフンは、Issue 7 では図の区切りであり、属性値には含まれない。一つの区切りが一つの属性とは限らない。発行番号、作業中番号、言語は DMC に含めない。
