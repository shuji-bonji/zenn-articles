---
title: "CSDB をフォルダーと Git で持つ"
---

ここまでの章では、DM を 1 件ずつ、または数件の組で扱ってきました。この章では CSDB の全体を扱います。何がそろっていれば納められるか、作る予定のものと実際にあるものをどう突き合わせるかを説明します。

CSDB（共通ソースデータベース）は、プロジェクトの DM、図、パブリケーションモジュールなどをすべて入れて管理する場所です。S1000D は、CSDB に入れるものの構造を決めていますが、CSDB をどう作るかは決めていません（Chap 4.2）。商用の CSDB 製品を使うプロジェクトもあれば、ファイルを置いたフォルダーで始めるプロジェクトもあります。この章は後者の方法を説明します。

:::message
この章のフォルダーと Git による持ち方は、S1000D の決まりではなく、著者が取っている方法です。版の状態の管理、権限、承認の流れのような、CSDB 製品が持つ機能は、含みません。小さなプロジェクトや、学習のための方法として読んでください。
:::

## フォルダーを CSDB と見なす

S1000D が決めているファイル名（「[ファイル名から中身を見分ける](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/file-names)」）でファイルを置いたフォルダーは、そのまま CSDB として扱えます。ファイル名に、種類、識別のコード、版、言語が入っているので、フォルダーの中を探すだけで、目的のものが見つかるためです。

この本の例をすべて行った後の券売機の CSDB です。

```text
csdb/
├── DMC-KBWORKS-A-00-00-00-00A-022A-D_001-00_JA-JP.XML    組織の BREX
├── DMC-KENBAIKI-A-00-00-00-00A-00PA-D_001-00_JA-JP.XML   PCT
├── DMC-KENBAIKI-A-00-00-00-00A-00WA-D_001-00_JA-JP.XML   ACT
├── DMC-KENBAIKI-A-00-00-00-00A-022A-D_001-00_JA-JP.XML   プロジェクトの BREX
├── DMC-KENBAIKI-A-10-20-00-00A-040A-D_001-00_JA-JP.XML   説明
├── …
├── DMC-KENBAIKI-A-10-20-00-01A-520A-D_001-00_JA-JP.XML   手順（発行済みの版）
├── DMC-KENBAIKI-A-10-20-00-01A-520A-D_001-01_JA-JP.XML   手順（作業中の版）
├── …
├── PMC-KENBAIKI-K0001-7BTJ7-00_001-00_JA-JP.XML          パブリケーションモジュール
├── COM-KENBAIKI-K0001-2026-00001-Q_JA-JP.XML             コメント
└── DML-KENBAIKI-K0001-S-2026-00001_001-00.XML            データ管理リスト
```

フォルダーを分ける必要はありません。種類ごと、システムごとにフォルダーを分けると、人が一覧を見たときに分かりやすくなります。その場合は、S1000D Stack の設定（`csdbDirs`）に、すべてのフォルダーを書きます。

## 版ごとに、ファイルを残す

同じ DMC の複数の版のファイルが同じフォルダーに並びます。「紙幣ユニットを取り出す」の DM は、日本語の版が 3 つあります。

```text
DMC-KENBAIKI-A-10-20-00-05A-520A-D_001-00_JA-JP.XML
DMC-KENBAIKI-A-10-20-00-05A-520A-D_002-00_JA-JP.XML
DMC-KENBAIKI-A-10-20-00-05A-520A-D_003-00_JA-JP.XML
```

英語の版も合わせて、版と言語で並べます。

```html-embed
<div class="ig">
  <div class="ig-cap">「紙幣ユニットを取り出す」の DM<span>DMC は 5 つとも KENBAIKI-A-10-20-00-05A-520A-D</span></div>
  <div class="ig-grid">
    <div class="ig-h"></div><div class="ig-h">JA-JP</div><div class="ig-h">EN-US</div>
    <div class="ig-v">001-00</div><div class="ig-c ig-on">…_001-00_JA-JP.XML</div><div class="ig-c ig-off">無い</div>
    <div class="ig-v">002-00</div><div class="ig-c ig-on">…_002-00_JA-JP.XML</div><div class="ig-c ig-on">…_002-00_EN-US.XML</div>
    <div class="ig-v">003-00</div><div class="ig-c ig-on">…_003-00_JA-JP.XML</div><div class="ig-c ig-on">…_003-00_EN-US.XML</div>
  </div>
  <div class="ig-note">縦が版、横が言語と国です。色の付いたます 1 つが、ファイル 1 つです。新しい版を足しても、前の版のファイルは残ります。</div>
</div>
<style>
  .ig {
    --bg: #f8fafc; --text: #0f172a; --sub: #475569; --line: #cbd5e1; --on: #dbeafe; --ont: #1e40af;
    box-sizing: border-box; padding: 16px; border-radius: 8px;
    background: var(--bg); color: var(--text);
    font-family: system-ui, -apple-system, "Hiragino Sans", "Noto Sans JP", sans-serif; font-size: 13px; line-height: 1.5;
  }
  [data-theme="dark"] .ig {
    --bg: #0f172a; --text: #e2e8f0; --sub: #94a3b8; --line: #334155; --on: #1e3a8a; --ont: #dbeafe;
  }
  .ig * { box-sizing: border-box; }
  .ig-cap { font-weight: 700; margin-bottom: 10px; }
  .ig-cap span { display: block; font-weight: 400; font-size: 11px; color: var(--sub); }
  .ig-grid { display: grid; grid-template-columns: 64px 1fr 1fr; gap: 6px; align-items: stretch; }
  .ig-h, .ig-v { font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace; font-weight: 700; font-size: 12px; color: var(--sub); display: flex; align-items: center; }
  .ig-h { justify-content: center; }
  .ig-c { padding: 8px 10px; border-radius: 6px; font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace; font-size: 12px; word-break: break-all; }
  .ig-on { background: var(--on); color: var(--ont); font-weight: 600; }
  .ig-off { border: 1px dashed var(--line); color: var(--sub); font-family: inherit; text-align: center; }
  .ig-note { margin-top: 10px; font-size: 11px; color: var(--sub); }
  @media (max-width: 520px) {
    .ig { padding: 10px; }
    .ig-grid { grid-template-columns: 52px 1fr 1fr; }
    .ig-c { padding: 6px; font-size: 10px; }
  }
</style>
```

:::message
3 つの版は、版の状態（`issueType`）と更新の理由が違います。`002` は、机上の検証を終えて品質保証の状態だけを変えた版で、`issueType` は `status`、更新の理由の種類は `urt01` です。`003` は、紙幣ユニットの番号を記録する手順を足した版で、`issueType` は `changed`、更新の理由の種類は `urt02` です。足した手順には、変更の印が付いています。
:::

新しい版を作っても、前の版のファイルを削除しません。理由は 3 つあります。

- 前の版を使って作られた出版物が既に配られています。その出版物が、どの DM から作られたかを後から確かめられる必要があります。
- 版を指定した参照が、前の版を指していることがあります。
- 変更の印は前の発行済みの版からの差を表します。前の版が無いと、何からの差かを確かめられません。

## Git で持つときの考え方

ファイル名に版が入っているので、Git の履歴と、DM の版は、別のものになります。

| | DM の版 | Git の履歴 |
| --- | --- | --- |
| 単位 | DM 1 件ごと | フォルダーの全体 |
| 記録するもの | 発行した内容と、発行までの作業の段階 | ファイルへの変更のすべて |
| 前の状態 | 別のファイルとして残る | 履歴をさかのぼって取り出す |

著者は次の決まりで扱っています。

| ファイル | Git での扱い |
| --- | --- |
| 発行済みの版（`inWork` が `00`） | 一度コミットしたら、変えない。変更が要るときは、新しい版のファイルを足す |
| 作業中の版（`inWork` が `01` 以降） | 同じファイルを上書きしてコミットする。作業の途中の経過は、Git の履歴に残る |
| 図のファイル | DM と同じフォルダーに置く。大きなファイルが多いときは、大きなファイルのための仕組み（Git LFS など）を使う |

発行済みの版のファイルに差分が出ていないことは、コミットの前に確かめられます。差分が出ていたら、発行済みの版を誤って書き換えています。

この持ち方では、DM を直すツールが、直した場所だけを書き換えることが、大事になります。XML を読み込んで書き出し直すツールは、直していない行の空白や引用符まで変えます。そうなると、Git の差分が、ファイルの全体に広がり、何を直したのかを差分から読めなくなります。「[DM を直し、改版する](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/revise-data-module)」の「実装するときの注意」で、その実測を書きました。

## データ管理リスト（DML）

DML は CSDB オブジェクトの一覧を XML にしたものです。決まりは Chap 4.5 にあります。DML には、種類が 3 つあります（`dmlCode` の `dmlType` 属性）。

| `dmlType` | 種類 | 内容 |
| --- | --- | --- |
| `c` | 完全な DMRL | プロジェクトで作る予定の DM と PM の全体の一覧 |
| `p` | 部分の DMRL | 作る予定の一覧の一部分。協力会社ごとの担当分など |
| `s` | CSL | CSDB に、いま実際にあるものの一覧 |

DMRL（Data Management Requirement List）は、予定の一覧です。まだ作っていない DM も載ります。CSL（CSDB Status List）は現状の一覧です。データを受け渡しした相手と、同じものを持っているかを確かめるのに使います。

DML の 1 行は `dmlEntry` です。

```xml
<dmlEntry dmlEntryType="n">
  <dmRef>
    <dmRefIdent>
      <dmCode modelIdentCode="KENBAIKI" … infoCode="040" infoCodeVariant="A" itemLocationCode="D"/>
      <issueInfo issueNumber="001" inWork="00"/>
      <language languageIsoCode="ja" countryIsoCode="JP"/>
    </dmRefIdent>
  </dmRef>
  <responsiblePartnerCompany enterpriseCode="K0002"/>
</dmlEntry>
```

| 要素・属性 | 内容 |
| --- | --- |
| `dmRef` / `pmRef` | その行が指す DM・PM。版と言語を書ける |
| `dmlEntryType` | DML の前の版と比べた、その行の種類。`n` は新しい行、`c` は変わった行、`d` は削除した行 |
| `issueType` | その行が指す DM・PM の版の状態 |
| `responsiblePartnerCompany` | 責任を持つ企業 |

## DML と CSDB を突き合わせる

DML に書いてあるものと、CSDB に実際にあるものが、合っているかを確かめます。s1000d-verify-mcp の [`reconcile_dml`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/stack/verify/reconcile-dml/) は、DML の行ごとに、次のどれかを返します。

| 状態 | 意味 |
| --- | --- |
| `found` | その DM・PM が CSDB にある。DML が版を書いているときは、その版がある |
| `issue-mismatch` | DMC は同じものがあるが、DML が書いた版が無い |
| `not-found` | CSDB に無い |

券売機の CSDB と、テストのために食い違いを入れた DML（4 行）を突き合わせた結果です。

| 行 | DML が書いているもの | 状態 | 理由 |
| --- | --- | --- | --- |
| 1 | 説明の DM、`001-00` | `found` | — |
| 2 | プロセスの DM、`002-00` | `issue-mismatch` | The DML lists issue 002-00; the CSDB has 001-00 |
| 3 | システムの番号が `99` の説明の DM | `not-found` | Data module KENBAIKI-A-99-00-00-00A-040A-D is not in the CSDB |
| 4 | プロジェクトの BREX、`001-00` | `found` | — |

逆の向きの結果も返ります。CSDB にあるのに、DML に載っていない DM・PM です。この例では、13 件（DM が 12 件、PM が 1 件）が、載っていないと返りました。

結果の読み方は DML の種類で変わります。

| DML の種類 | `not-found` の意味 | DML に載っていないものの意味 |
| --- | --- | --- |
| DMRL（予定の一覧） | まだ作っていない。予定どおりなら、問題ではない | 予定に無いものを作っている。予定に足すか、作るのをやめるかを決める |
| CSL（現状の一覧） | 一覧と実際が合っていない。受け渡しで、届いていないものがある | 一覧が古い。または、相手に渡していないものがある |

## 納める前に確かめること

CSDB の全体を納める前に確かめる観点をまとめます。この本の各章で見たことの一覧です。

| 観点 | 確かめること | 扱った章 |
| --- | --- | --- |
| ファイル名 | ファイル名と、中身の DMC・版・言語が一致している | [ファイル名から中身を見分ける](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/file-names) |
| XSD | すべての DM・PM の検証の状態が `valid`（`schema-not-found`・`not-run` は通ったことにならない）。使ったスキーマが XML Schema Package の側にあり、その Issue が納める Issue と同じ | [XSD で確かめる](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/xsd-validation) |
| BREX | すべての DM・PM が、BREX の検査を通り、状態が `incomplete` でない。検査に、既定 BREX が入っている | [BREX で確かめる](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/brex-validation) |
| 人が確かめるルール | BREX の機械で検査できない取り決めをレビューで確かめた | [BREX で確かめる](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/brex-validation) |
| 参照 | DM・PM・図への参照の先が、すべてある | [参照と図をたどる](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/references-and-graphics) |
| 版 | 発行するものの `inWork` が `00`。版の状態、更新の理由、変更の印がそろっている | [DM を直し、改版する](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/revise-data-module) |
| 適用性 | 製品ごとに読んで、出る手順が意図どおり | [適用性で出し分ける](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/applicability) |
| PM | 載せるべき DM が、すべて PM に載っている | [PM でまとめる](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/publication-module) |
| DML | DML と CSDB が合っている | この章 |
| 品質保証 | `qualityAssurance` が、プロジェクトの求める段階になっている | [識別・状態の部分を読む](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/ident-and-status) |

s1000d-verify-mcp の [`verify_csdb`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/stack/verify/verify-csdb/) は、CSDB の DM と PM をまとめて検証します。この本の例をすべて行った後の券売機の CSDB の結果です。

| 項目 | 件数 |
| --- | --- |
| 検証した DM・PM | 23 |
| XSD の検証が通ったもの | 23 |
| BREX の検査が通ったもの | 23 |
| 指す先の無い参照を持つもの | 0 |

図のファイルを入れ忘れると、その図を指す DM が「指す先の無い参照を持つもの」に数えられます。実際に納めるときはこの数を 0 にします。

BREX の検査は、件数のほかに、何層を検査したかも見ます。この結果では、DM と PM の検査に既定 BREX が入っています。「[BREX で確かめる](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/brex-validation)」で見たとおり、既定 BREX が入っていない検査でも件数は同じように出るので、件数だけでは区別できません。

:::message alert
この一覧のうち、ツールで確かめられる項目がすべて通っても、S1000D に準拠している、という判定にはなりません。納め先のビジネスルールに合っているかは、納め先との取り決めに沿って、人が確かめます。
:::

## 相手に渡すとき

CSDB のデータを協力会社や顧客に渡すときは、渡すファイルの組に、納入票（DDN）を付けます。DDN は、送り手、受け手、渡すファイルの名前の一覧を書いた XML です。受け取った側は、DDN の一覧と、届いたファイルを突き合わせて、足りないものが無いかを確かめます。`reconcile_dml` は DDN も受け付けます。

## 実装するときの注意

### CSDB をフォルダーに決め打ちにしない

CSDB 製品は、DM をファイルではなく、データベースに持っています。フォルダーを読む処理をプログラムのあちこちに書くと、CSDB 製品の中のデータを扱えません。s1000d-core は、CSDB からの読み出しを 1 つのインターフェース（`CsdbSource`）の後ろに置いています。フォルダーを読む実装はその 1 つです。

### DML は PM を載せないことがある

公式サンプルの DML は、108 行すべてが DM で、PM の行がありません。CSDB には PM が 4 件あります。突き合わせると、この 4 件が、DML に載っていないものとして返ります。DML に PM を載せない運用がある、と読めます。載っていないものをすぐに誤りとして扱わず、プロジェクトの運用を確かめます。

### 版の食い違いを無いことと分ける

DML が書いた版が無く、別の版がある、という状態は、DM が無い状態とは、対処が違います。前者は、DML か CSDB のどちらかが古い、という問題です。s1000d-core は、この 2 つを別の状態として返し、版が食い違うときは、CSDB にある最新の版を添えます。公式サンプルの DDN では、184 件のうち 1 件が、版の食い違いでした。

### 値を解釈しない

`dmlType`、`dmlEntryType`、`issueType` の値は、書かれたまま返します。「この行は削除された DM だから、CSDB に無くて正しい」のような判断は、s1000d-verify-mcp ではなく、利用する側が行います。

## この章のポイント

- S1000D のファイル名でファイルを置いたフォルダーは、CSDB として扱えます。前の版のファイルは消さずに残します。
- Git で持つときは、発行済みの版のファイルを変えず、新しい版のファイルを足します。
- DML は、作る予定の一覧（DMRL）と、現状の一覧（CSL）に分かれます。CSDB と突き合わせた結果の読み方は種類で変わります。
- 納める前の確認は、ツールで確かめられる項目と、人が確かめる項目の両方がそろって終わります。

## 出典

- 著者が入手した S1000D Issue 7 のパッケージ（仕様書の Chap 4.2、Chap 4.5、Chap 7.5.1、XML スキーマの `dml.xsd`・`ddn.xsd`、Bike Samples）。入手先は https://users.s1000d.org/
