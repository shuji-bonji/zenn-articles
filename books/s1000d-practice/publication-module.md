---
title: "PM でまとめる"
---

DM は 1 件ずつが独立した情報の単位です。どの DM をどの順に並べて、1 冊のマニュアルにするかは、DM の中には書かれていません。それを書くのがパブリケーションモジュール（PM）です。

PM の役割と、出力の種類は、入門の「[Publication Module と IETP](https://zenn.dev/shuji_bonji/books/s1000d-intro/viewer/publication-and-ietp)」で説明しました。この章では、券売機の PM の XML を読み、新しい DM を足します。決まりは仕様書の Chap 4.9（出版物の管理）にあります。

## PM の形

PM も、DM と同じく、識別・状態の部分と、内容の部分を持ちます。ルート要素は `pm` で、スキーマは `pm.xsd` です。

### 識別・状態の部分

```xml
<pmAddress>
  <pmIdent>
    <pmCode modelIdentCode="KENBAIKI" pmIssuer="K0001" pmNumber="7BTJ7" pmVolume="00"/>
    <language languageIsoCode="ja" countryIsoCode="JP"/>
    <issueInfo issueNumber="001" inWork="00"/>
  </pmIdent>
  <pmAddressItems>
    <issueDate year="2026" month="10" day="25"/>
    <pmTitle>指定席券売機 操作マニュアル（例）</pmTitle>
  </pmAddressItems>
</pmAddress>
```

DM と違うのは、識別のコードが、DMC ではなく PMC（`pmCode`）であることと、題名が 1 つ（`pmTitle`）であることです。言語、版、発行日は、DM と同じです。`pmStatus` には、DM の `dmStatus` と同じく、機密区分、企業、適用性、BREX への参照、品質保証が入ります。

PM にも版があります。DM を足す、順を変える、といった変更は、PM の改版です。

### 内容の部分

券売機の PM の内容です。参照の中身（DMC の属性と題名）は省いて、どの DM を指しているかをコメントで示します。

```xml
<content>
  <pmEntry>
    <pmEntryTitle>券売機の概要</pmEntryTitle>
    <dmRef><!-- 040A 券売機の概要 --></dmRef>
  </pmEntry>
  <pmEntry>
    <pmEntryTitle>乗換案内からきっぷを買う</pmEntryTitle>
    <dmRef><!-- 130A プロセス --></dmRef>
    <dmRef><!-- 520A STEP1 --></dmRef>
    <dmRef><!-- 520A STEP2 --></dmRef>
    <dmRef><!-- 520A STEP3 --></dmRef>
    <dmRef><!-- 520A STEP3-2 --></dmRef>
  </pmEntry>
  <pmEntry>
    <pmEntryTitle>前面扉を開ける（係員）</pmEntryTitle>
    <dmRef><!-- 520A 前面扉を開ける --></dmRef>
  </pmEntry>
  <pmEntry>
    <pmEntryTitle>きっぷが出てこない</pmEntryTitle>
    <dmRef><!-- 411A 故障探求 --></dmRef>
  </pmEntry>
  <pmEntry>
    <pmEntryTitle>用語</pmEntryTitle>
    <dmRef><!-- 040A 用語 --></dmRef>
  </pmEntry>
</content>
```

| 要素 | 役割 |
| --- | --- |
| `pmEntry` | 目次の 1 つの項目。中に `pmEntry` を入れて、階層を作れる |
| `pmEntryTitle` | 項目の見出し |
| `dmRef` | その項目に入る DM への参照 |
| `pmRef` | ほかの PM への参照。大きな出版物を複数の PM に分けるときに使う |

この PM は、`pmEntry` が 5 つで、DM への参照が 9 件です。並びの順が出版物の中での順になります。

`dmRef` の書き方は、「[参照と図をたどる](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/references-and-graphics)」で見たものと同じです。この PM の参照はどれも版を書いていません。

## PM に載っていない DM

PM と、CSDB にある DM を比べると、PM に載っていない DM があります。

| DM | PM に載っているか |
| --- | --- |
| 説明（2 件）、プロセス、故障探求、きっぷを買う手順（4 件）、前面扉を開ける | 載っている |
| 紙幣ユニットを取り出す | 載っていない |
| BREX（2 件） | 載っていない |

BREX が載っていないのはふつうのことです。BREX はマニュアルの読者に見せる情報ではありません。

「紙幣ユニットを取り出す」が載っていないのは、意図したものか、足し忘れかを確かめる必要があります。DM が CSDB にあっても、PM に載っていなければ、そのマニュアルには出てきません。

## DM を足す

「[DM を作る](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/create-data-module)」で作った、払い戻しの説明の DM を PM に足します。「乗換案内からきっぷを買う」の後ろに新しい `pmEntry` を置きます。

```xml
<pmEntry>
  <pmEntryTitle>きっぷを払い戻す</pmEntryTitle>
  <dmRef>
    <dmRefIdent>
      <dmCode modelIdentCode="KENBAIKI" systemDiffCode="A" systemCode="10"
        subSystemCode="2" subSubSystemCode="0" assyCode="00" disassyCode="02"
        disassyCodeVariant="A" infoCode="040" infoCodeVariant="A" itemLocationCode="D"/>
    </dmRefIdent>
    <dmRefAddressItems>
      <dmTitle>
        <techName>指定席券売機 操作マニュアル（例）</techName>
        <infoName>きっぷの払い戻し</infoName>
      </dmTitle>
    </dmRefAddressItems>
  </dmRef>
</pmEntry>
```

足した後の PM は、`pmEntry` が 6 つ、DM への参照が 10 件になります。

:::message
この例は PM の XML を手で直しています。s1000d-writer-mcp には、PM に DM を 1 件足すための操作が、まだありません。PM の全体を下書きから組み立てる操作（`build_data_module` で、種類を `pm` にする）はあります。

また、この例は、足し方を見せるために、発行済みの PM のファイルをそのまま直しています。実際には、PM も、作業中の版を始めてから直します。
:::

## 確かめ方

### 参照の先がすべてあるか

PM の確かめ方は DM と同じです。XSD、BREX、参照の 3 つを確かめます。s1000d-verify-mcp の [`verify_data_module`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/stack/verify/verify-data-module/) は、PM も受け付けます。DM を足した後の PM の結果です。

| 段階 | 結果 |
| --- | --- |
| XSD | `valid`（`pm.xsd`） |
| BREX | `passed`（3 層、違反 0 件） |
| 参照 | `resolved`（11 件。DM への参照が 10 件と、BREX への参照が 1 件） |

PM で、いちばん大事なのは、参照の確認です。参照の先の DM が CSDB に無いと、目次の項目だけがあって、中身の無いマニュアルになります。

### 目次として読む

s1000d-reader-mcp の [`read_publication_module`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/stack/reader/read-publication-module/) は、PM を目次の木として返します。参照ごとに、CSDB で見つかった DM のファイル、題名、版が添えられます。

足す前の PM を、目次の表示の例にします。表示の例は、著者が描いたものです。実際の見た目は、閲覧の道具が決めます。

```html-embed
<div class="pt">
  <div class="pt-cap">指定席券売機 操作マニュアル（例）<span>目次の表示の例</span></div>
  <div class="pt-page">
    <div class="pt-entry"><b>券売機の概要</b>
      <div class="pt-dm"><span>券売機の概要</span><code>00A-040A</code></div>
    </div>
    <div class="pt-entry"><b>乗換案内からきっぷを買う</b>
      <div class="pt-dm"><span>乗換案内からきっぷを買う</span><code>00A-130A</code></div>
      <div class="pt-dm"><span>乗換案内からきっぷを買う - STEP1</span><code>00A-520A</code></div>
      <div class="pt-dm"><span>乗換案内からきっぷを買う - STEP2</span><code>01A-520A</code></div>
      <div class="pt-dm"><span>乗換案内からきっぷを買う - STEP3</span><code>02A-520A</code></div>
      <div class="pt-dm"><span>乗換案内からきっぷを買う - STEP3-2</span><code>03A-520A</code></div>
    </div>
    <div class="pt-entry"><b>前面扉を開ける（係員）</b>
      <div class="pt-dm"><span>前面扉を開ける（係員）</span><code>04A-520A</code></div>
    </div>
    <div class="pt-entry"><b>きっぷが出てこない</b>
      <div class="pt-dm"><span>きっぷが出てこない</span><code>00A-411A</code></div>
    </div>
    <div class="pt-entry"><b>用語</b>
      <div class="pt-dm"><span>用語</span><code>01A-040A</code></div>
    </div>
  </div>
  <div class="pt-note">太字が pmEntry の見出し、その下の行が dmRef の指す DM の情報名です。右の文字は、DMC の分解コードと情報コードです。</div>
</div>
<style>
  .pt {
    --bg: #f8fafc; --text: #0f172a; --sub: #475569; --line: #cbd5e1; --card: #ffffff; --code: #1d4ed8;
    box-sizing: border-box; padding: 16px; border-radius: 8px;
    background: var(--bg); color: var(--text);
    font-family: system-ui, -apple-system, "Hiragino Sans", "Noto Sans JP", sans-serif; font-size: 13px; line-height: 1.5;
  }
  [data-theme="dark"] .pt {
    --bg: #0f172a; --text: #e2e8f0; --sub: #94a3b8; --line: #334155; --card: #1e293b; --code: #93c5fd;
  }
  .pt * { box-sizing: border-box; }
  .pt-cap { font-weight: 700; margin-bottom: 8px; }
  .pt-cap span { margin-left: 8px; font-weight: 400; font-size: 12px; color: var(--sub); }
  .pt-page { background: var(--card); border: 1px solid var(--line); border-radius: 6px; padding: 6px 12px; column-count: 2; column-gap: 20px; }
  .pt-entry { break-inside: avoid; padding: 5px 0; }
  .pt-entry b { display: block; }
  .pt-dm { display: flex; justify-content: space-between; gap: 8px; margin-left: 12px; padding: 1px 0 1px 8px; border-left: 2px solid var(--line); font-size: 12px; color: var(--sub); }
  .pt-dm span { min-width: 0; }
  .pt-dm code { flex: 0 0 auto; font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace; font-size: 11px; color: var(--code); background: none; padding: 0; }
  .pt-note { margin-top: 8px; font-size: 11px; color: var(--sub); }
  @media (max-width: 520px) {
    .pt { padding: 10px; }
    .pt-page { column-count: 1; }
  }
</style>
```

足す前の PM を読んだ結果の一部です。

| 項目 | 参照の先の DMC | CSDB で見つかった DM の情報名 | 見つかった版 |
| --- | --- | --- | --- |
| 券売機の概要 | `…-00A-040A-D` | 券売機の概要 | `001-00` |
| 乗換案内からきっぷを買う | `…-00A-130A-D` | 乗換案内からきっぷを買う | `001-00` |
| | `…-00A-520A-D` | 乗換案内からきっぷを買う - STEP1 | `001-00` |
| | `…-01A-520A-D` | 乗換案内からきっぷを買う - STEP2 | `001-00` |

PM の参照の中に写した題名は技術名称だけです。情報名は、参照の先の DM から読んで、添えられています。

### 人が見る観点

- 載せるべき DM がすべて載っているか。CSDB の DM の一覧と、PM の参照を比べます。
- 並びの順と、見出しが、読者にとって分かりやすいか。
- 版を書かない参照が、どの版の DM を指すことになるか。同じ DMC の DM が、CSDB に複数の版であるときは、出力のときに、どの版が選ばれるかを確かめます。

## 実装するときの注意

### PM の題名は技術名称として扱う

DM の題名は、技術名称と情報名の 2 つですが、PM の題名は 1 つです。DM と PM を同じ一覧に並べる道具では、PM の題名を、どちらの欄に入れるかを決める必要があります。s1000d-core は技術名称の欄に入れています。

### PM の更新の理由は読んでいない

PM の `pmStatus` にも、更新の理由（`reasonForUpdate`）を書けます。s1000d-core の読み取りは、DM の識別・状態の部分だけを対象にしていて、PM の更新の理由を返しません。PM の中の変更の印は一覧として返します。

### 作業中の版は PM でも始められる

作業中の版を始める操作は PM の XML でも動きます。公式サンプルの PM 4 件で確かめました。ただし、新しい版のファイル名は、DM のときだけ返します。

## この章のポイント

- PM は、どの DM をどの順に並べるかを書きます。項目は `pmEntry`、DM への参照は `dmRef` です。
- CSDB に DM があっても、PM に載っていなければ、マニュアルには出てきません。
- PM の確かめ方で、いちばん大事なのは、参照の先の DM がすべてあるかです。
- PM にも版があります。DM を足すことは PM の改版です。

## 出典

- 著者が入手した S1000D Issue 7 のパッケージ（仕様書の Chap 4.9、XML スキーマの `pm.xsd`、Bike Samples）。入手先は https://users.s1000d.org/
