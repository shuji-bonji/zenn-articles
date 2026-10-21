---
title: "クラスと Functionality Matrix"
---

この章では、米国国防総省（Department of Defense、DoD）が対話型電子技術マニュアル（Interactive Electronic Technical Manual、IETM）を含む電子技術マニュアル（Electronic Technical Manual、ETM）に与えたクラスと、のちに契約で使われるようになった機能マトリックス（Functionality Matrix）の関係を整理します。クラス番号を一つ決めれば調達仕様が足りる、という理解は、ここで捨ててください。

## 概要

Tri-Service IETM Working Group は、ETM を Class 0 から Class 5 まで定義しました。Class 0 は幅を示すための項であり、ETM ですらありません。Class 5 は、当時まだ本番に耐えない将来の機能を含むよう、意図的に開かれています。定義を書いた Jorgensen は、Class 0 を “a full spectrum” を示すために含めた、と述べています。クラスは能力の並びの上の点です。

実在のシステムは、一つのクラスにきれいに収まらないことが多い、と考えて読むのが安全です。根拠は定義そのものの中にあります。Class 3 と Class 4 は利用者から見て同等に見えることがあり、既存マニュアルの変換物は新規開発向け仕様の機能をすべて持たない、と同じ論文が書いています。

クラス番号は、どの機能が必須かを一文ずつは指定しません。そのため、取得文書は機能マトリックスで機能を選ばせるようになりました。確認できたのは、米陸軍の MIL-STD-40051-1B 附属書 A と、S1000D に付属する機能マトリックスです。

:::message alert
この二つの機能マトリックスは同じ表ではありません。行の定義も、どの契約で効くかも別です。
:::

```mermaid
flowchart LR
  C0["Class 0<br/>ページ画像<br/>ETM ではない"] --> C1["Class 1<br/>索引付きページ画像"]
  C1 --> C2["Class 2<br/>スクロール文書"]
  C2 --> C3["Class 3<br/>線形の構造化 IETM"]
  C3 --> C4["Class 4<br/>階層データベース"]
  C4 --> C5["Class 5<br/>統合 IETIS<br/>将来機能に開く"]
```

## 詳細

### Class 0 から Class 5

次の表は、Jorgensen が Tri-Service の定義として書いた内容を、本書で短縮したものです。原文の条件を削っていないつもりですが、契約に使うときは原文を読んでください。

| クラス | 定義上の呼び名 | データの持ち方 | 利用者から見た動き |
| --- | --- | --- | --- |
| 0 | Non-Electronically-Indexed Page Images。ETM ではない | 電子保管またはオンデマンド印刷のためのページ画像 | 画面でページは見える。オンライン利用のための詳細な索引はない |
| 1 | Electronically Indexed Page Images | ページ画像。自動的な知的索引でページへ移る。例として Navy Advanced Technical Information System（ATIS） | 図書館や調査での閲覧と研究。全文が文字として検索できるとは限らない |
| 2 | Electronic Scrolling Documents | ASCII の文書に知的索引とハイパーテキストを入れる。ページ原稿からの単純な変換が多く、専門家による再執筆は、タグの追加を除けばほとんどない | 文書の中を移動できる。著者の誘導や、内容に応じた NEXT はごく限られるか、ない |
| 3 | Linear Structured IETMs | MIL-D-87269 のタグによる SGML を可能な限り使う。線形の SGML 文書であり、階層型データベースではない。表示は MIL-M-87268 のハイパーテキスト提示 | 著者が作った誘導がある。質問のダイアログや、内容に応じた論理的な NEXT を使える |
| 4 | Hierarchically Structured IETMs | 冗長でない関係データベース、またはオブジェクト指向の階層データベースとして執筆し維持する。実行時データベースへパッケージして、MIL-M-87268、MIL-D-87269、MIL-Q-87270 に沿って対話提示する | 画面は Class 3 と非常に似ることがある。違いは主に原データの管理方法である |
| 5 | Integrated Data-Base IETIS | Class 4 の IETM を、他プロセスのデータと統合した統合電子技術情報システム（Integrated Electronic Technical Information System、IETIS）。エキスパートシステムの規則、診断、コンピュータ管理の訓練などを含む | 当時は未開発、または本番に十分成熟していない機能を含むよう開かれている |

### どれが IETM か

Jorgensen は、IETM と呼ぶには、著者が対話機能を情報の中へ作り込んであることが必要だ、と書いています。例は、プロンプト、利用者からデータを取るダイアログ、利用者が選ぶ分岐です。

- Class 4 は、IETM 仕様に完全に沿い、改訂可能な原データをデータベース管理システムで管理する。
- Class 3 は、とくに原データの改訂可能データベースの点で Class 4 に完全には沿わないが、Class 4 の機能の多くを持つ。利用者には Class 4 と機能的に同等に見えることがある。原データの管理は実質的に異なる。
- Class 5 は Class 4 の機能的な上位集合であり、追加機能を持つが、Class 4 の IETM 仕様には沿う。
- Class 1 と Class 2 は、情報の中に著者の対話を持たない ETM である。Class 2 の対話は、ビューアが文書ファイルに対して提供するもので、著者が入れた対話情報は文書内の別の箇所へのハイパーテキスト参照に限られる。Class 1 は従来の印刷ページの画像であり、Class 2 はページ区切りを外してスクロールする。

:::message alert
「PDF は Class 2 である」とは言えません。画像だけの PDF は Class 0 または Class 1 に近く、文字化されハイパーリンクを持つ線形の PDF は Class 2 に近い、という対応が限界です。クラスはファイル形式の別名ではありません。
:::

同じ論文は、公式の 3 仕様が正式に規定するのは Class 4 であり、軍は新規システムでは Class 4 の標準を要求しつつ、既存システムでは Class 1、2、3 を収容する必要がある、と述べています。費用の大きい Class 4 より、別のクラスの方が妥当な場合がある、という判断を定義の側が認めている、ということです。

### 機能マトリックスが必要になる理由

クラスは典型的な組み合わせに名前を付けたものです。ある IETM が注釈を持つか、部品発注とつながるか、動的な診断を持つかは、クラス番号だけでは契約に書けません。Class 3 と Class 4 が画面上は似る、という定義自体が、番号では成果物を特定しきれないことを示しています。

確認できた機能マトリックスは、少なくとも次の二つです。

1. 米陸軍の MIL-STD-40051-1B 附属書 A。線形と非線形の IETM の機能と、技術内容の選択表を定める。この附属書は規格の必須部分です。機能の具体的な水準は、附属書 A の機能マトリックスに従う、と本文が指示しています。マトリックスの目的は、IETM 事業が、産業界に通る言葉で要求を定義できるようにすることです。完成した機能マトリックスと内容選択表は、契約、作業記述書、その他の契約文書に含まれたときに契約上の拘束力を持ちます。
2. S1000D に付属する機能マトリックス。米空軍の MIL-STD-3048A は、これを「IETM とページ指向の出版物の両方について、必要な機能水準を事業が指定するための調達ツール」と定義しています。機能は、理論、診断、部品など、技術出版物のデータの種類ごとに別々に指定できます。S1000D Issue 5.0 のダウンロード一式の説明にも、Functionality matrix が含まれています。

MIL-STD-40051-1B の表は、機能ごとに複雑さの係数を、線形の列と非線形の列で持ちます。この係数は、その IETM 全体のクラス番号ではありません。確認できた例を挙げます。

| 機能 | 線形の列 | 非線形の列 | 区分 |
| --- | --- | --- | --- |
| Dynamic Diagnostics | 適用なし（NA） | 5 | Diagnostics and Prognostics |
| Prognostics | NA | 5 | Diagnostics and Prognostics |
| System Simulation | 4 | 4 | Diagnostics and Prognostics |

区分として表に並んでいるものには、Access、Annotation、Delivery and Distribution、Diagnostics and Prognostics、External Processes、Graphics、Linking、Navigation and Tracking、Printing、Special Content、Updates、User Operation Mode があります。配送媒体（CD-ROM、DVD、ネットワーク）、注釈、印刷、外部プロセス（不具合報告、部品発注、整備データ収集など）が、クラスという言葉より具体的な行になっています。

:::message alert
S1000D のマトリックスと陸軍のマトリックスを、同じチェックリストとして混ぜないでください。どちらも「番号一つでは機能が指定できない」という問題への回答ですが、行の定義と、どの規格の契約で効くかは別です。マトリックス上の数字は、その IETM 全体のクラス番号ではありません。
:::

クラスという言葉が廃止された、と本書では書きません。1994 年の定義は今もクラスを説明するための一次資料です。その後の取得規格が、クラス番号に加えて、またはクラス番号の代わりに、機能の一覧で要求を書かせている、というのが確認できた事実です。

## 出典

- Eric L. Jorgensen. *The Interactive Electronic Technical Manual Overview: "Setting the Stage"*. https://www.navsea.navy.mil/Portals/103/Documents/NSWC_Carderock/Cals94j.pdf
- Department of Defense. MIL-STD-40051-1B. 附属書 A（IETM の機能、表示、内容選択）。ASSIST から 2026-10-14 に取得されたテキスト。規格自身が、使用前に現行版かを確認するよう注意している。
- Department of Defense. MIL-STD-3048A(USAF). 用語 3.2.11（Functionality matrix）。https://everyspec.com/MIL-STD/MIL-STD-3000-9999/download.php?spec=MIL-STD-3048A.054327.pdf
- S1000D Issue 5.0 のダウンロード一式の説明（Functionality matrix を含む）. https://users.s1000d.org/

## この章のポイント

- Class 0 から Class 5 は、Tri-Service が spectrum 上の点として定義したものである。Class 0 は ETM ではなく、Class 5 は将来の機能に開かれている。
- IETM と呼べるのは、著者の対話機能を持つ Class 3 以上である。Class 3 と Class 4 は画面が似ていても、原データの管理が違う。PDF の形式名とクラスは一致しない。
- 機能の要否は Functionality Matrix で指定する。陸軍の MIL-STD-40051 の表と、S1000D の表は別物である。マトリックス上の数字は、IETM 全体のクラス番号ではない。
