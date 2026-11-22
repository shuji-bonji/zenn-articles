---
title: "付録 A：レビューと comment DM"
---

DM をレビューして見つけた指摘は、メールや表計算のファイルに書くこともできます。S1000D は、指摘そのものを、CSDB に入れる XML として書く形を用意しています。コメント（comment）です。指摘が、対象の DM と同じ場所で、同じ仕組みで管理されます。

決まりは仕様書の Chap 4.6（コメント）にあります。この付録では券売機の CSDB にあるコメントを読みます。

## コメントの形

コメントは DM ではありません。ルート要素は `comment` で、スキーマは `comment.xsd` です。識別・状態の部分と、内容の部分を持つ点は、DM と同じです。

### 識別の部分

```xml
<commentAddress>
  <commentIdent>
    <commentCode modelIdentCode="KENBAIKI" senderIdent="K0001" yearOfDataIssue="2026"
      seqNumber="00001" commentType="q"/>
    <language languageIsoCode="ja" countryIsoCode="JP"/>
  </commentIdent>
  <commentAddressItems>
    <commentTitle>レビューの指摘 3 件</commentTitle>
    <issueDate year="2026" month="11" day="05"/>
    <commentOriginator>
      <dispatchAddress>
        <enterprise>
          <enterpriseName>KBWORKS</enterpriseName>
        </enterprise>
        <dispatchPerson>
          <lastName>評価</lastName>
          <firstName>太郎</firstName>
        </dispatchPerson>
        <address>
          <city>東京</city>
          <country>JP</country>
        </address>
      </dispatchAddress>
    </commentOriginator>
  </commentAddressItems>
</commentAddress>
```

| 要素・属性 | 内容 |
| --- | --- |
| `commentCode` | コメントを特定するコード。送り手、年、連番、種類でできている |
| `commentType` | コメントの種類。`q` は指摘（問い）、`i` は途中の回答、`r` は最終の回答（Chap 4.6.1） |
| `commentTitle` | 題名 |
| `commentOriginator` | 指摘した人と、その所属・連絡先 |

コメントには版（`issueInfo`）がありません。指摘への回答は、指摘を書き直すのではなく、種類が `i` か `r` の別のコメントとして書きます。

DM と違って、指摘した人の名前を書く場所があります。コメントを CSDB に入れて、相手に渡すときは、個人の名前が含まれることに注意します。

### 状態の部分

```xml
<commentStatus>
  <security securityClassification="01"/>
  <commentPriority commentPriorityCode="cp01"/>
  <commentRefs>
    <dmRefGroup>
      <dmRef>
        <dmRefIdent>
          <dmCode modelIdentCode="KENBAIKI" … disassyCode="04" … infoCode="520" …/>
          <issueInfo issueNumber="001" inWork="00"/>
          <language languageIsoCode="ja" countryIsoCode="JP"/>
        </dmRefIdent>
      </dmRef>
    </dmRefGroup>
  </commentRefs>
  <brexDmRef>…</brexDmRef>
</commentStatus>
```

| 要素・属性 | 内容 |
| --- | --- |
| `commentPriority` の `commentPriorityCode` | 優先度。値の意味は、プロジェクトが決める |
| `commentRefs` | 指摘の対象。DM、PM などへの参照 |

対象への参照は**版と言語を書いています**。本文からの参照が、版を書かないことが多いのと、対照的です。指摘は、「この版の、この場所」についてのものだからです。対象の DM が改版された後に、このコメントを読む人が、どの版への指摘だったかを分かるようにします。

### 内容の部分

```xml
<commentContent>
  <simplePara>R-3 [BREX手動] KB-00100: 手順 3（id stp-open-3）「作業が終わったら扉を閉めて鍵をかける。」は、扉を閉める操作と鍵をかける操作の 2 つが 1 つのステップに読める。確認してください。</simplePara>
</commentContent>
```

内容は段落（`simplePara`）の並びです。この例のコメントには、段落が 3 つあり、指摘が 1 つずつ書かれています。上はその 3 つ目です。

内容の部分には、対象の DM の中の場所を構造として指す仕組みがありません。場所は文の中に書きます。この例は、手順の番号と、`id`（`stp-open-3`）と、対象の文を書いています。`id` を書いておくと、手順の番号が変わっても、場所が分かります。

## 指摘の出どころを書き分ける

この例のコメントの 3 件は出どころが 2 種類あります。

| 指摘 | 出どころ | 内容 |
| --- | --- | --- |
| R-1、R-2 | 道具の検査 | 図と 3D のモデルのファイルが、CSDB に無い |
| R-3 | BREX の人が確かめるルール（KB-00100） | 1 つの手順に 2 つの操作が入っている |

:::message
このコメントは、例の CSDB に図のファイルを入れる前のレビューの記録です。R-1 と R-2 は、その後、図と 3D のモデルのファイルを入れて解消しています。コメントは、書いた時点の指摘を残すものなので、ファイルは直していません。
:::

指摘の先頭に `[機械]`、`[BREX手動]` のような印を付けて、出どころを書き分けています。出どころが分かると、指摘を受けた側が、どう確かめればよいかが分かります。道具の検査による指摘は、直した後に、同じ検査をもう一度行えば、直ったかどうかが分かります。人の判断による指摘は指摘した人とのやり取りが要ります。

レビューの観点は大きく 3 つに分けられます。

| 観点 | 確かめること | 道具で確かめられるか |
| --- | --- | --- |
| 構造 | XSD、BREX の機械で検査できるルール、参照、適用性 | できる |
| 取り決め | BREX の機械で検査できないルール（`nonContextRules`） | できない。ルールの一覧を見て、人が確かめる |
| 文章 | 用語が、プロジェクトの用語集と合っているか。書き方の決まりに沿っているか | 一部だけ |

## 確かめ方

コメントも XSD と BREX で確かめます。BREX の検査は、コメントの `commentStatus` の中の BREX への参照をたどります。既定 BREX には、コメントのスキーマだけに当てはまるルールがあります。

コメントについて、人が確かめる点は、次のとおりです。

- 対象への参照が指摘した版を指しているか。
- 指摘の文だけで、対象の場所が分かるか。
- 1 つの段落に、指摘が 1 つになっているか。

## 道具

S1000D Stack の Skill `s1000d-review` は、上の 3 つの観点で DM を確かめて、指摘の一覧を作ります。その中から、利用者が選んだ指摘を s1000d-writer-mcp の [`create_comment`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/stack/writer/create-comment/) で、コメントの XML にします。この付録の例はその出力です。

## この付録のポイント

- コメントは、レビューの指摘を CSDB に入れる XML として書く形です。DM ではなく、版もありません。
- 対象への参照は版と言語を書きます。どの版への指摘かを残すためです。
- 対象の DM の中の場所は文の中に書きます。手順の番号だけでなく、`id` を書いておきます。
- 指摘は、道具の検査によるものか、人の判断によるものかを書き分けます。

## 出典

- 著者が入手した S1000D Issue 7 のパッケージ（仕様書の Chap 4.6、XML スキーマの `comment.xsd`、既定 BREX）。入手先は https://users.s1000d.org/
