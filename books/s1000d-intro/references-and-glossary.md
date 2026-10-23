---
title: "参考情報と用語集"
---

この章は、本書で使った公式の入り口と、初出の用語を一箇所に集めます。定義は、各章で出典とともに説明した範囲に限定します。版によって意味の細部が変わる語は、その旨を付けます。

## 公式の入り口

| 用途 | URL |
| --- | --- |
| 公式サイトの入り口 | https://s1000d.org/ |
| 概要、DM と CSDB の定義、歴史 | https://s1000d.org/?page_id=101 |
| 組織（Council、Steering Committee、作業グループ） | https://s1000d.org/?page_id=61 |
| 利点（実現は実装の要因に依存する、と書いてあるページ） | https://s1000d.org/?page_id=57 |
| 変更提案（CPF） | https://s1000d.org/?page_id=37 |
| 外部リンク（S シリーズ、ATA Spec 1000BR、STE100） | https://s1000d.org/?page_id=44 |
| オンラインのデータ辞書 | https://s1000d.org/?page_id=1003 |
| 仕様のダウンロード | https://users.s1000d.org/ |
| 掲載されている Issue の一覧 | https://users.s1000d.org/ProductList.aspx |

Issue ごとの同梱物は、一覧の Description から開く説明ページで確認します。本書が本文を確認した説明ページは、Issue 4.1（https://users.s1000d.org/ProductDescription.aspx?ProductID=16）と Issue 5.0（https://users.s1000d.org/ProductDescription.aspx?ProductID=34）です。Issue 6 は、著者が入手したパッケージの12ファイルを確認しました。Issue 7 は、著者が入手したパッケージの11ファイルを確認しました。Issue 6 と Issue 7 の説明ページの本文は、確認していません。

仕様の本文、スキーマ、既定 BREX、自転車サンプルの一次の配布元は、上のダウンロードサイトです。本書の章末にある GitHub の URL は、公式パッケージのファイルが第三者のリポジトリに置かれているものを、属性名の確認に使ったものです。公式の配布そのものではありません。

## 用語集

**適用（applicability）**
その情報がどの装備のどの構成に当てはまるかを、データとして持たせること。公式の概要は、識別・状態の部分が適用を管理すると書いている。粒度と条件の属性は、版とプロジェクトで決まる。

**ATA e-Business Program**
S1000D の三つのスポンサーの一つ。民間航空の情報交換の規格を担う。Airlines for America（A4A）とは、公式の外部リンクでは別に紹介されている。

**ATA Spec 1000BR**
S1000D を実装するための、民間航空のビジネスルール。S1000D そのものではない。

**自転車サンプル（Bike sample data set）**
仕様のダウンロードに含まれる自転車の XML 例。Issue 4.1 と Issue 5.0 の説明は、架空の製品であり適合性の試験集ではないと明記している。Issue 6 と Issue 7 のパッケージにも含まれ、記述 DM の `infoName` は “Description of how it is made” である。Issue 6 と Issue 7 の仕様本文からは、架空であることと試験集ではないことという注意文は、検索した範囲では見つからなかった。

**BREX（Business Rules Exchange）**
プロジェクトまたは組織のルールのうち、XML の構造の使い方に関するものを、機械可読な DM として交換する仕組み。概要が情報の種類の一つに挙げている。情報コード `022` とアイテムロケーションコード `D` は、Issue 4.2 の複製と、Issue 6 および Issue 7 の第4.10.2.4章および同梱の既定 BREX が書いている。既定 BREX の `systemDiffCode` は、Issue 4.2 の複製では `F`、Issue 6 の同梱ファイルでは `H`、Issue 7 の同梱ファイルでは `J` である。Issue 6 と Issue 7 の第4.10.2.4章はアセンブリコードを `00` または `0000` とし、同梱ファイルの `assyCode` は `0301` である。この差は解消していない。

**ビジネスルール（business rules）**
仕様の枠の中で、プロジェクトが採用する値、必須、禁止を決めたもの。人が読む文書と、BREX の両方が関係する。BREX に検査式として書けない規則は、人の読む文書の側に残る。

**BRDP（Business Rules Decision Point）**
仕様が、プロジェクトに決定を求めている点。Issue 4.1 のパッケージ説明は Business Rule Decision Point index、Issue 5.0 は Project Decision Point Index という名称で、索引の同梱を挙げている。Issue 6 のパッケージでは、ファイル名が `S1000D Issue 6 BRDP Index.xlsx`、Issue 7 では `S1000D Issue 7 BRDP Index.xlsx` である。

**共通ソースデータベース（Common Source Data Base、CSDB）**
DM を収め、管理する保管場所。公式の概要は、機能を定めていないと明言している。Issue 7 の第4.2章は、設計と実装の規則は定めず、情報オブジェクトのデータ構造だけを定める、と書いている。製品名ではない。

**変更提案（Change Proposal Form、CPF）**
仕様を変更する公式の手続き。Steering Committee が処理し、受け入れられたものが Issue に入る。

**内容の部分（contents section）**
DM の二つの部分の一つ。利用者が作業するため、またはシステムを理解するために必要な情報そのもの。公式の概要の用語。

**データモジュール（Data Module、DM）**
独立した情報の単位。概要は、プラットフォーム、システム、構成品についての記述、手順、または運用のデータを含む、と書いている。Issue 7 の第1.2章は、技術刊行物の中の最小の自己完結した情報の単位、と定義している。DMC を識別子として CSDB に保管し、取り出す。

**データモジュールコード（Data Module Code、DMC）**
DM を取り出す識別コード。どの製品のどの部分か、という部分と、どんな種類の情報か、という部分に分かれる。発行番号、作業中番号、言語は、Issue 4.1、Issue 4.2、Issue 6、Issue 7 のサンプルでは DMC の外にある。Issue 7 では、訓練の DM に学習コードの区画が加わる。Issue 6 の第4.3章の長さの範囲は、本書では写していない。

**既定 BREX（default BREX）**
各 Issue のパッケージに含まれる、仕様側の BREX。プロジェクトの BREX とは別である。

**DIG（Defense Interest Group）**
防衛の顧客を代表する独立したグループ。スポンサーそのものではない。

**識別・状態の部分（identification and status section）**
DM の二つの部分の一つ。CSDB での管理、適用、品質保証、取り出しの制御のための情報を持つ。公式の概要の用語。XML の要素名は版で変わる。

**IETP（Interactive Electronic Technical Publication）**
S1000D が、画面で対話的に表示する出版物に使う語。出力の一つであり、紙や PDF と排他ではない。機能の範囲は、機能マトリックスとプロジェクトの要求で決まる。

**IETM（Interactive Electronic Technical Manual）**
主に米国の文書が使う語。米空軍の MIL-STD-3048A は、S1000D の IETP とほぼ同等だと定義している。契約上の範囲は、その定義だけで決まらない。クラス 1 から 5 の説明は『IETM入門 — 概念から S1000D まで』にある。

**情報コード（information code）**
DMC の情報側にある、情報の種類を表すコード。Issue 7 の第4.3.6章では3文字である。自転車サンプルでは `041` の DM に “Description of how it is made” という `infoName` が付いている。BREX は、Issue 4.2 の複製と、Issue 6 および Issue 7 の第4.10.2.4章で `022`。情報コード `024` は、Issue 6 と Issue 7 のビジネスルールのテンプレートで確認した。全表は版の章にある。

**情報管理番号（Information Control Number、ICN）**
図など、DM とは別に CSDB で管理するオブジェクトの識別子。Issue 7 の第4.4章は、CAGE に基づく方法とモデル識別コードに基づく方法を説明している。桁の表は版のその章にある。

**Issue**
S1000D の版。メジャー、マイナー、パッチの意味はダウンロードサイトが説明している。メジャー版間に後方互換はない。Issue 6 の表紙は “Issue No. 6”、Issue 7 の表紙は “Issue No. 7” である。パッチの位の説明は、ダウンロードサイト、Issue 6 と Issue 7 の第7.3.1.4章、Issue 6 のパッケージ内のフォルダ名で違う。Issue 6 のパッケージは “Issue No. 6”“Issue 6.A”“Patch 6.0.A”“S1000D_6.A_ BREX”“Issue 6-01”“Issue 6A” を、一つの表記にまとめずに残している。

**モデル識別コード（model identification code）**
DMC の先頭で、どの製品またはプロジェクトかを表すコード。自転車サンプルでは `S1000DBIKE`。Issue 4.1 以降の属性名の例は `modelIdentCode`。それより前の版では別の要素名がある。

**パブリケーションモジュール（Publication Module、PM）**
出版物の内容と構造を、DM などへの参照で定義するオブジェクト。記述の DM の一種ではない。概要ページに定義の文はない。Issue 7 の第4.9章は、識別子、状態、内容を持ち、内容は DM、従来の技術出版物、他の PM への参照だと書いている。Issue 4.0 の公開されたスキーマ一覧と、Issue 6 および Issue 7 のスキーマパッケージでは、`pm.xsd` が別ファイルになっている。要素名 `pmEntry` は、Issue 6 と Issue 7 の第4.9.1章で確認した。

**S シリーズ**
製品支援のための国際仕様の一群。S1000D は技術出版物を担う。S2000M、S3000L、S4000P、S5000F、S6000T は別の仕様であり、SX000i は使い方のガイドである。

**S1000D Council**
ASD、AIA、ATA e-Business Program によるガバナンスの主体。ビジョンと、Steering Committee への方向づけを担う。

**S1000D Steering Committee**
CPF を処理し、仕様の Issue を出す維持の主体。

**スキーマ（schema）**
その Issue の XML が従う構造の定義。種類ごとにファイルが分かれる。Issue 4.0 以降の名前の体系は、Issue 3.0 以前と互換がない。Issue 6 と Issue 7 のサンプルも、Issue 4 以降の名前を使っている。Issue 6 と Issue 7 のスキーマパッケージの一覧に DTD は無かった。

**標準番号体系（Standard Numbering System、SNS）**
製品をシステムからアセンブリまで階層で番号付ける枠。番号の意味はプロジェクトが定義する。DMC のシステム側の中心である。

**システム差分コード（system difference code）**
同じモデル識別の中での違いを表すコード。Issue 7 の第4.3章では1文字から4文字である。自転車サンプルは `AAA`、Issue 6 の既定 BREX は `H`、Issue 7 の既定 BREX は `J`、Issue 4.2 の複製は `F` である。

## 本書が断定しなかったこと

次の項目は、確認した公開情報の範囲では結論を書いていません。

- S1000D を採用すると費用が下がる、という効果。公式の利点のページは可能性と、実現が要因に依存することを書いており、本書は約束として扱っていません。
- 日本の公的機関による、納品仕様としての採用。2022年の調査研究の契約記録以外に、採用を示す公式発表は確認できませんでした。
- Issue 6 と Issue 7 の第4.10.2.4章が BREX のアセンブリコードを `00` または `0000` とし、同梱の既定 BREX の `assyCode` が `0301` であること。この差は解消していない。
- Issue 7 の情報コード `024` のテンプレートが、`systemDiffCode` `H` の BREX（Issue 6 の既定 BREX の DMC と一致する並び）を参照し、Issue 7 の既定 BREX 自身は `J` であること。`H` を `J` の誤記としては読んでいない。
- Issue 6 の情報コード `024` の XML について、ファイル名の `_000-01` と、識別子の発行番号 `001`、作業中番号 `00` のどちらを正とするか。同じ ZIP の `0102` の PDF の本文は読んでいない。
- Issue 6 のパッケージ内の表記 “Issue No. 6”“Issue 6.A”“Patch 6.0.A”“S1000D_6.A_ BREX”“Issue 6-01”“Issue 6A” を、一つの版番号にまとめること。パッチ内の同じ既定 BREX について、発行番号 `002` 作業中番号 `00`（日付 2025-10-26）と、発行番号 `002` 作業中番号 `01`（日付 2025-09-05）と、本体の発行番号 `001`（日付 2024-06-19）を、一つの発行にまとめること。正誤表 PDF の本文は読んでいない。
- Issue 6 と Issue 7 の説明ページの本文。スキーマの URL `http://www.s1000d.org/S1000D_6/` と `S1000D_7/` を、執筆時に再取得したこと。
- Issue 6 と Issue 7 の仕様本文が、自転車サンプルを架空の製品であり適合性試験集ではない、と書いていること。Issue 4.1 と Issue 5.0 の説明ページにはその文がある。Issue 6 と Issue 7 の本文を検索した範囲では見つからなかった。
- 特定の CSDB 製品、編集ソフト、ビューアの適否。

契約の読み始めに戻るときは、Issue、ビジネスルールまたは BREX、納品する出力（紙、PDF、IETP と、機能マトリックスの範囲）の三つの有無を見ます。三つが揃って、初めて要素名の作業に進めます。

## 出典

- 各用語の一次の出典は、第1章から第11章の章末に絶対 URL で挙げた。この章はそれらの索引である。
- S1000D. *About S1000D*. https://s1000d.org/?page_id=101
- S1000D. Downloads. https://users.s1000d.org/
- S1000D. *External Links*. https://s1000d.org/?page_id=44

## この章のポイント

- 一次の文書は、公式サイトと、利用条件に同意して入手する Issue のパッケージである。
- 用語の要素名は版に従属する。この用語集が固定しているのは、概要が説明する概念と、本書が特定の版で確認した名前だけである。
- 作業を始める条件は、Issue、プロジェクトのルール、出力の範囲の三つが契約で分かっていることである。
