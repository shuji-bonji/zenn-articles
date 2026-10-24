---
title: "S1000D とは何か"
---

S1000D は、技術マニュアルなどの技術刊行物を、「データモジュール」という小さな情報の単位に分けて作り、共通の保管場所で管理し、必要な単位を集めて紙、PDF、画面の出版物として出すための国際仕様です。欧州の航空宇宙産業で始まり、現在は欧州と米国の三つの団体が共同で維持しています。

この本は、S1000D を初めて読む人のための入門です。契約に「S1000D で納品」と書いてあるものの、まだ仕様書を開いていない技術文書の担当者を読者に想定しています。本書は、S1000D の公式サイト、ダウンロードページの説明、著者が入手した仕様のパッケージをもとに、どの版にも共通する中心概念を説明します。要素名やスキーマは版によって変わるので、特定の版の名前を出すときは、その版を明記します。

## 公式の定義

[ダウンロードサイト](https://users.s1000d.org/)が掲げている S1000D の標題は、次のとおりです。

> International specification for technical publications using a common source database

日本語にすると「共通ソースデータベースを用いて技術刊行物を作るための国際仕様」です。情報の単位を作り、それを共通の保管場所で管理し、必要な単位を集めて刊行物として出す、という一連の方法を定めています。本書は、この標題を S1000D の中心として説明を進めます。

Issue 6 と Issue 7 の仕様書の表紙にも、同じ英文の標題が書かれています。Issue 7 の第1.1章は、S1000D を技術刊行物の調達と作成のための国際仕様だと説明し、これまでの適用の経験から、その原理は技術以外の刊行物にも使えると述べています。第1.2章は、民間または軍の事業で、製品のライフサイクルを支える技術文書の計画、管理、作成、交換、配布、利用を対象にしています。空・陸・海のビークルや装備は、仕様の中で “the Product” と呼ばれます。

[公式の概要ページ](https://s1000d.org/?page_id=101)も、S1000D は技術刊行物を中心にしつつ、手続きと管理を必要とする事業であれば技術以外の刊行物にも使える、と書いています。ただし、使えることと、どの事業でも同じ効果が得られることは別です。公式の[利点のページ](https://s1000d.org/?page_id=57)も、利点が得られるかどうかは、導入の方針や基盤などいくつかの要因によって変わると書いています。

## 作っている組織と対象の範囲

S1000D は、当初、欧州航空宇宙防衛工業会（Aerospace, Security and Defence Industries Association of Europe、ASD）が作りました。当時の名称は AECMA です。現在は、次の三つの組織と、その会員、顧客が共同で作っています。

- ASD
- 米国航空宇宙工業会（Aerospace Industries Association of America、AIA）
- ATA e-Business Program

この三つの組織が、S1000D Council と S1000D Steering Committee を構成しています。それぞれの役割は[第2章](https://zenn.dev/shuji_bonji/books/s1000d-intro/viewer/history-and-organization)で説明します。

なお、Issue 7 の第1.1章は、共同の作成者を Air Transport Association of America（ATA）と書いていますが、仕様書の表紙の発行者（Publishers）は ATA e-Business Program です。公式サイトはこの二つを別の組織として紹介しているので、本書でも区別して扱います。詳しくは[第2章](https://zenn.dev/shuji_bonji/books/s1000d-intro/viewer/history-and-organization)で説明します。

対象の範囲について、公式の概要ページは、Issue 2 から陸上と海上の装備が加わったと書いています。その後も範囲は広がり、軍用と民用の装備のほか、建設、方針や手続き、法務文書、組織の文書などにも使えると列挙しています。ダウンロードサイトの案内も、現在の用途として、防衛（陸・海・空）、民間航空、建設、船舶を挙げています。Issue 7 の第1.1章も、Issue 2 から陸と海が加わったことを、概要ページと同じように書いています。

つまり S1000D は、航空機の整備手順だけを扱う仕様ではありません。範囲が広がった経緯と、S1000D が今注目されている理由は、[第12章](https://zenn.dev/shuji_bonji/books/s1000d-intro/viewer/why-now)で説明します。一方で、建設や法務の文書を S1000D で作る具体的な手順は、本書の範囲外です。本書は、どの分野にも共通する中心概念を扱います。

## IETP と IETM

S1000D が定めているのは、情報の作り方、管理の仕方、出版物の作り方です。画面で対話的に表示する出版物は、紙や PDF と並ぶ出力の一つで、S1000D では対話型電子技術出版物（Interactive Electronic Technical Publication、IETP）と呼びます。

米国の文書では、ほぼ同じものを対話型電子技術マニュアル（Interactive Electronic Technical Manual、IETM）と呼びます。ただし、二つの語が近いものを指していても、契約が同じものを納品物として求めているとは限りません。IETP と IETM の定義の対応と、IETP とパブリケーションモジュール（Publication Module、PM）の関係は、[第7章](https://zenn.dev/shuji_bonji/books/s1000d-intro/viewer/publication-and-ietp)で説明します。米国 DoD のクラス（Class 0 から 5。IETM と呼べるのは Class 3 以上）については、別の本[『IETM入門 — 概念から S1000D まで』](https://zenn.dev/shuji_bonji/books/ietm-overview/viewer/classes)で説明しています。

## この本の読み方

S1000D を読むときは、最初に次の四つを分けて考えると混乱しません。

1. **仕様が定めること**：データモジュールの考え方、識別のコード、共通ソースデータベースという保管の要求、出版物の組み立て方です。
2. **版が定めること**：要素名、属性名、スキーマ、情報コードの表、フィールドの桁数です。メジャー版の間には後方互換がありません。[第11章](https://zenn.dev/shuji_bonji/books/s1000d-intro/viewer/issues-and-cautions)で説明します。
3. **プロジェクトが定めること**：どの版で納品するか、標準番号体系の付け方、仕様が選択肢として残している項目のどれを選ぶかです。このうち機械で検査できる形にしたものが BREX（Business Rules Exchange）です。[第8章](https://zenn.dev/shuji_bonji/books/s1000d-intro/viewer/brex)で説明します。
4. **ツールが行うこと**：共通ソースデータベースの機能は、仕様では定められていません。本書では、編集ソフトやビューアの製品は推奨しません。

契約書を読むときに最初に確認するのは、「どの Issue（版）か」です。版が決まらないと、要素名の話を始められません。

## 出典

- S1000D. *About S1000D*. https://s1000d.org/?page_id=101
- S1000D. *Benefits*. https://s1000d.org/?page_id=57
- S1000D. *S1000D Organizational Structure*. https://s1000d.org/?page_id=61
- S1000D. Downloads（標題と用途の案内）. https://users.s1000d.org/
- S1000D. Product list（無償提供と、掲載されている Issue の一覧）. https://users.s1000d.org/ProductList.aspx
- 著者が入手した S1000D Issue 6 のパッケージ（表紙は Issue No. 6、日付は 2024-06-19）。入手先は https://users.s1000d.org/
- 著者が入手した S1000D Issue 7 のパッケージ（表紙は Issue No. 7、日付は 2026-07-16。第1.1章、第1.2章）。入手先は https://users.s1000d.org/
- IETM のクラス分け. https://zenn.dev/shuji_bonji/books/ietm-overview

## この章のポイント

- S1000D は、共通ソースデータベースを用いて技術刊行物を作り、管理し、発行するための国際仕様です。
- 対象は航空から始まり、Issue 2 から陸と海に広がりました。公式の概要ページは、装備以外の文書にも使えると書いています。
- IETP は、紙や PDF と並ぶ出力の一つです。どの版で、どのルールで納品するかは、仕様ではなくプロジェクトの要求で決まります。
