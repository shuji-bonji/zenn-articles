---
title: "適用性で出し分ける"
---

同じ製品にも、機種の違いや、設置の仕方の違いがあります。違いごとに別の DM を作ると、共通の部分を何度も書くことになります。S1000D は、1 つの DM の中に「この手順は、この機種だけ」という条件を機械が読める形で書けるようにしています。この仕組みを適用性（applicability）と呼びます。

この章では、券売機の CSDB に、機種と設置場所の違いを足して、適用性を書き、読みます。決まりは、仕様書の Chap 3.9.5.3（DM の中の適用性）と Chap 4.14（適用性の管理）にあります。

## 例の設定

券売機に、次の違いがあるとします。

| 違い | 値 | 意味 |
| --- | --- | --- |
| 機種 | `MV-50` | 硬貨とカードだけを受け付ける |
| | `MV-60` | 紙幣も受け付ける |
| 設置場所 | `indoor` | 屋内 |
| | `outdoor` | 屋外。前面扉に、雨よけのカバーが付いている |

この違いによって、DM の内容が 2 か所で変わります。

- 「紙幣ユニットを取り出す」の DM は、紙幣ユニットを持つ `MV-60` にだけ当てはまる。
- 「前面扉を開ける（係員）」の DM の中の「雨よけのカバーを先に外す」という手順は、屋外の券売機にだけ当てはまる。

1 つ目は DM の全体、2 つ目は DM の中の手順 1 つ、という違いがあります。

## ACT、PCT、`applic`

適用性は次の 3 つでできています。

```mermaid
flowchart LR
  ACT["ACT（適用性相互参照表）<br>製品の属性の定義<br>機種、設置場所"]
  PCT["PCT（製品相互参照表）<br>実在する製品ごとの値<br>製造番号 K60-0002 は MV-60、屋外"]
  DM["DM<br>applic（条件の文）"]
  DM -->|"applicCrossRefTableRef"| ACT
  ACT -->|"productCrossRefTableRef"| PCT
```

| 名前 | 何を書くか | 情報コード |
| --- | --- | --- |
| ACT | 製品を区別する属性と、取れる値 | `00W` |
| PCT | 実在する製品 1 台ごとの属性の値 | `00P` |
| DM の中の `applic` | どの値の製品に当てはまるか | — |

ACT と PCT も DM です。CSDB の中に 1 つのファイルとして置きます。このほかに、改修の適用の前後のような、製品の状態を定義する CCT（条件相互参照表、情報コード `00Q`）があります。この章では使いません。

## ACT を書く

ACT（`DMC-KENBAIKI-A-00-00-00-00A-00WA-D`）の内容の部分です。

```xml
<content>
  <applicCrossRefTable>
    <productAttributeList>
      <productAttribute id="serial" productIdentifier="primary">
        <name>製造番号</name>
        <displayName>製造番号</displayName>
        <descr>筐体の背面の銘板に書かれた番号</descr>
      </productAttribute>
      <productAttribute id="model">
        <name>機種</name>
        <displayName>機種</displayName>
        <descr>MV-50 は硬貨とカードだけを受け付ける。MV-60 は紙幣も受け付ける</descr>
        <enumeration applicPropertyValues="MV-50|MV-60"/>
      </productAttribute>
      <productAttribute id="site">
        <name>設置場所</name>
        <displayName>設置場所</displayName>
        <descr>屋内か屋外か</descr>
        <enumeration applicPropertyValues="indoor|outdoor"/>
      </productAttribute>
    </productAttributeList>
    <productCrossRefTableRef>
      <dmRef><!-- PCT（情報コード 00P）の DMC --></dmRef>
    </productCrossRefTableRef>
  </applicCrossRefTable>
</content>
```

| 要素・属性 | 内容 |
| --- | --- |
| `productAttribute` の `id` | 属性の名前。DM の `applic` から、この名前で指す |
| `productIdentifier="primary"` | 製品 1 台を特定する属性であること。この例では、製造番号 |
| `enumeration` の `applicPropertyValues` | 取れる値。縦線の記号で区切って並べる |

## PCT を書く

PCT（`DMC-KENBAIKI-A-00-00-00-00A-00PA-D`）の内容の部分です。券売機が 3 台あるとします。

```xml
<content>
  <productCrossRefTable>
    <product>
      <assign applicPropertyIdent="serial" applicPropertyType="prodattr" applicPropertyValue="K50-0001"/>
      <assign applicPropertyIdent="model" applicPropertyType="prodattr" applicPropertyValue="MV-50"/>
      <assign applicPropertyIdent="site" applicPropertyType="prodattr" applicPropertyValue="indoor"/>
    </product>
    <product>
      <assign applicPropertyIdent="serial" applicPropertyType="prodattr" applicPropertyValue="K60-0001"/>
      <assign applicPropertyIdent="model" applicPropertyType="prodattr" applicPropertyValue="MV-60"/>
      <assign applicPropertyIdent="site" applicPropertyType="prodattr" applicPropertyValue="indoor"/>
    </product>
    <product>
      <assign applicPropertyIdent="serial" applicPropertyType="prodattr" applicPropertyValue="K60-0002"/>
      <assign applicPropertyIdent="model" applicPropertyType="prodattr" applicPropertyValue="MV-60"/>
      <assign applicPropertyIdent="site" applicPropertyType="prodattr" applicPropertyValue="outdoor"/>
    </product>
  </productCrossRefTable>
</content>
```

`product` が製品 1 台です。`assign` が属性 1 つへの値の割り当てです。`applicPropertyType` の `prodattr` は、ACT の属性であることを示します。

:::message
券売機のプロジェクトの BREX は、使ってよい情報コードに `00W` と `00P` を入れています。入れていないと、ACT と PCT を足したときに、BREX の検査で違反になります。プロジェクトで新しい種類の DM を使い始めるときは、BREX を先に直します。
:::

## DM の全体の適用性

「紙幣ユニットを取り出す」の DM を、`MV-60` だけに当てはまるようにします。識別・状態の部分の `applic` を次のように書きます。

```xml
<dmStatus issueType="status">
  <security securityClassification="01"/>
  <responsiblePartnerCompany enterpriseCode="K0002"/>
  <originator enterpriseCode="K0002"/>
  <applicCrossRefTableRef>
    <dmRef><!-- ACT（情報コード 00W）の DMC --></dmRef>
  </applicCrossRefTableRef>
  <applic>
    <displayText>
      <simplePara>MV-60</simplePara>
    </displayText>
    <assert applicPropertyIdent="model" applicPropertyType="prodattr" applicPropertyValues="MV-60"/>
  </applic>
  …
</dmStatus>
```

| 要素 | 内容 |
| --- | --- |
| `applicCrossRefTableRef` | この DM が使う ACT への参照 |
| `displayText` | 人が読む文 |
| `assert` | 機械が読む条件。「属性 `model` の値が、`MV-60` である」 |

`displayText` と `assert` は、同じことを人と機械のそれぞれに向けて書いたものです。2 つが食い違っていても XSD の検証は通ります。「人が読む文は `MV-60`、条件は `MV-50`」という DM は、画面の表示と、実際の出し分けが、逆になります。

条件が複数あるときは、`assert` を `evaluate` で囲んで、`and` か `or` で結びます。

## DM の中の一部の適用性

「前面扉を開ける（係員）」の DM は、DM の全体としては、どちらの機種にも当てはまります。その中の手順 1 つだけを屋外の券売機に限ります。書く場所は 2 つです。

内容の部分の先頭に、条件の文を置きます。条件の文には、名前（`id`）を付けます。

```xml
<content>
  <referencedApplicGroup>
    <applic id="app-outdoor">
      <displayText>
        <simplePara>屋外に設置した券売機</simplePara>
      </displayText>
      <assert applicPropertyIdent="site" applicPropertyType="prodattr" applicPropertyValues="outdoor"/>
    </applic>
  </referencedApplicGroup>
  <procedure>
    …
```

当てはめたい要素に `applicRefId` 属性で、その名前を書きます。

```xml
<proceduralStep id="stp-open-1-3" applicRefId="app-outdoor">
  <para>雨よけのカバーを先に外す。</para>
</proceduralStep>
```

条件の文を 1 か所に置いて、名前で指すので、同じ条件を複数の手順から使えます。

要素に `applicRefId` が無いときは、いちばん近い親の `applicRefId` が、その要素にも当てはまります。どの親にも無ければ、DM の全体の適用性だけが当てはまります。要素は、要素の適用性と DM の全体の適用性の両方を満たすときに当てはまります。

:::message
発行済みの DM に適用性を足すときは、「[DM を直し、改版する](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/revise-data-module)」の手順で、作業中の版を始めます。この章の例の 2 つの DM も、作業中の版（「紙幣ユニットを取り出す」は `003-01`、「前面扉を開ける（係員）」は `001-01`）として作っています。更新の理由の種類は、適用性だけの変更を表す `urt04` です。
:::

## 読む

s1000d-reader-mcp の [`read_data_module`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/stack/reader/read-data-module/) に、製品の属性の値を渡すと、DM の全体と、要素ごとに、当てはまるかどうかが返ります。状態は 3 つです。

| 状態 | 意味 |
| --- | --- |
| `applicable` | 当てはまる |
| `notApplicable` | 当てはまらない |
| `undetermined` | 判定できない。渡した製品に、条件が使う属性の値が無い、など |

「紙幣ユニットを取り出す」の DM の全体の結果です。

| 渡した製品 | 結果 |
| --- | --- |
| `K50-0001`（`MV-50`、屋内） | `notApplicable` |
| `K60-0002`（`MV-60`、屋外） | `applicable` |

「前面扉を開ける（係員）」の手順 1.3「雨よけのカバーを先に外す」の結果です。

| 渡した製品 | 結果 | 理由 |
| --- | --- | --- |
| `K50-0001`（`MV-50`、屋内） | `notApplicable` | — |
| 機種だけ（`MV-60`）。設置場所は渡さない | `undetermined` | product has no value for: site |

2 行目が 3 つ目の状態です。機種しか分からない製品については、屋外かどうかで決まる手順を出すとも出さないとも言えません。

2 つの結果を、表示の例で並べます。表示の例は、著者が描いたものです。実際の見た目は、閲覧ソフトが決めます。

```html-embed
<div class="ap">
  <div class="ap-col">
    <div class="ap-cap">製造番号 K50-0001<span>機種 MV-50、設置場所 indoor</span></div>
    <div class="ap-page">
      <div class="ap-step"><span class="ap-no">1</span>保守鍵で前面扉の鍵を開ける。</div>
      <div class="ap-step ap-sub"><span class="ap-no">1.1</span>鍵を右に回す。</div>
      <div class="ap-step ap-sub"><span class="ap-no">1.2</span>取っ手を引く。</div>
      <div class="ap-step ap-sub ap-na"><span class="ap-no">1.3</span><div>雨よけのカバーを先に外す。<em>notApplicable　屋外に設置した券売機</em></div></div>
      <div class="ap-step"><span class="ap-no">2</span>保守モードに入る。</div>
    </div>
  </div>
  <div class="ap-col">
    <div class="ap-cap">機種だけが分かっている製品<span>機種 MV-60、設置場所は不明</span></div>
    <div class="ap-page">
      <div class="ap-step"><span class="ap-no">1</span>保守鍵で前面扉の鍵を開ける。</div>
      <div class="ap-step ap-sub"><span class="ap-no">1.1</span>鍵を右に回す。</div>
      <div class="ap-step ap-sub"><span class="ap-no">1.2</span>取っ手を引く。</div>
      <div class="ap-step ap-sub ap-un"><span class="ap-no">1.3</span><div>雨よけのカバーを先に外す。<em>undetermined　屋外に設置した券売機（設置場所の値が無い）</em></div></div>
      <div class="ap-step"><span class="ap-no">2</span>保守モードに入る。</div>
    </div>
  </div>
</div>
<style>
  .ap {
    --bg: #f8fafc; --text: #0f172a; --sub: #475569; --line: #cbd5e1; --card: #ffffff;
    --na: #94a3b8; --nabg: #f1f5f9; --un: #b45309; --unbg: #fef3c7;
    box-sizing: border-box; padding: 16px; border-radius: 8px; display: flex; gap: 12px; align-items: stretch;
    background: var(--bg); color: var(--text);
    font-family: system-ui, -apple-system, "Hiragino Sans", "Noto Sans JP", sans-serif; font-size: 13px; line-height: 1.5;
  }
  [data-theme="dark"] .ap {
    --bg: #0f172a; --text: #e2e8f0; --sub: #94a3b8; --line: #334155; --card: #1e293b;
    --na: #64748b; --nabg: #0f172a; --un: #fbbf24; --unbg: #78350f;
  }
  .ap * { box-sizing: border-box; }
  .ap-col { flex: 1 1 0; min-width: 0; display: flex; flex-direction: column; }
  .ap-cap { font-weight: 700; margin-bottom: 8px; }
  .ap-cap span { display: block; font-weight: 400; font-size: 11px; color: var(--sub); }
  .ap-page { flex: 1; background: var(--card); border: 1px solid var(--line); border-radius: 6px; padding: 10px; }
  .ap-step { display: flex; gap: 8px; padding: 4px 6px; margin: 2px 0; border-left: 4px solid transparent; }
  .ap-sub { margin-left: 16px; }
  .ap-no { flex: 0 0 auto; min-width: 22px; font-weight: 700; color: var(--sub); }
  .ap-step em { display: block; font-style: normal; font-size: 11px; font-weight: 700; }
  .ap-na { background: var(--nabg); border-left-color: var(--na); color: var(--na); }
  .ap-na .ap-no { color: var(--na); }
  .ap-un { background: var(--unbg); border-left-color: var(--un); }
  .ap-un em { color: var(--un); }
  @media (max-width: 520px) {
    .ap { flex-direction: column; padding: 10px; }
  }
</style>
```

:::message alert
`undetermined` は、「当てはまらない」ではありません。「分からない」です。判定できなかった手順を当てはまらないものとして隠すと、必要な手順が、作業者に見えなくなります。判定できないときは、人が読む文（`displayText`）を添えて見せ、作業者が判断できるようにします。
:::

## 確かめ方

### 属性と値が ACT にあるか

`assert` が使う属性の名前（`applicPropertyIdent`）と値が、ACT に定義されているかを確かめます。属性の名前を書き間違えた `assert` は、どの製品を渡しても、判定できません。

s1000d-reader-mcp の [`describe_applicability`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/stack/reader/describe-applicability/) は、1 件の DM について、適用性にかかわる情報をまとめて返します。「前面扉を開ける（係員）」の結果です。

| 項目 | 値 |
| --- | --- |
| ACT の属性 | `serial`（製品を特定する属性）、`model`（`MV-50`・`MV-60`）、`site`（`indoor`・`outdoor`） |
| PCT の製品 | 3 台 |
| DM の中の条件の文 | DM の全体（「MV-50、MV-60」）、`app-outdoor`（「屋外に設置した券売機」） |
| 条件の文が使う属性 | `model`、`site` |
| そのうち、PCT が値を割り当てていない属性 | 無し |

最後の行が空でないときは、PCT のどの製品を渡しても、`undetermined` になる条件があります。

### `applicRefId` が指す先があるか

`applicRefId` が指す `id` を持つ `applic` が、DM の中にあるかを確かめます。s1000d-verify-mcp の参照の確認が、これを数えます。

### 製品ごとに読んで確かめる

PCT の製品を 1 台ずつ渡して、DM を読みます。その製品の作業者に見える手順が意図したとおりかを人が確かめます。適用性の誤りは、XML を眺めても見つけにくく、製品を決めて読んだ結果を見ると、見つけやすくなります。

## 実装するときの注意

### `applicRefId` が指す先は 2 通りある

`applicRefId` は、上の例のように、同じ DM の `referencedApplicGroup` の中の `applic` を指します。もう 1 つ、別の DM にある条件の文を指す形があります。同じ DM の `referencedApplicGroupRef` の中の `applicRef` を指し、その `applicRef` が、別の DM の条件の文を `applicIdentValue` という値で指します。

公式サンプルでは、`applicRefId` 366 件のうち 64 件が、この 2 つ目の形でした。1 つ目の形だけを実装すると、この 64 件は、指す先が無い参照として報告されます。

2 つ目の形では、条件の式が、読んでいる DM の中にありません。判定には、CSDB の中から、条件の文を持つ DM を見つける必要があります。公式サンプルには、指されている DM が入っていないので、この 64 件は、どの製品を渡しても `undetermined` になります。

### 判定は 3 つの値で行う

条件の判定は、真と偽の 2 つではなく、「分からない」を加えた 3 つの値で行います。`and` と `or` も 3 つの値で計算します。

| 式 | 結果 |
| --- | --- |
| 偽 `and` 分からない | 偽（1 つでも偽があれば、偽と決まる） |
| 真 `and` 分からない | 分からない |
| 真 `or` 分からない | 真（1 つでも真があれば、真と決まる） |
| 偽 `or` 分からない | 分からない |

「分からない」を偽として扱う実装は、判定できない手順を何も知らせずに隠します。

### 値の比較は完全一致にする

公式サンプルの PCT は、ある条件の値を `Pre`・`Post` と書いていますが、その条件の定義（CCT）の列挙は、`PRE` と `POST-001~POST-999` です。大文字と小文字も、書き方も、合っていません。

s1000d-core は値の比較を完全一致のままにしています。大文字と小文字を無視して合ったことにすると、公式サンプルの側の食い違いが、見えなくなるためです。合わない値は合わないと報告します。

### 式を LLM に渡さない

s1000d-reader-mcp は、`assert` と `evaluate` の式を応答に入れません。返すのは、判定の結果、人が読む文、判定できなかった理由です。`and` と `or` の入れ子や、範囲の書き方を LLM が読み違えると、当てはまらない手順を当てはまると答えてしまいます。判定は、決まった手順で計算できるので、s1000d-reader-mcp が行います。

## この章のポイント

- 適用性は、属性を定義する ACT、製品ごとの値を持つ PCT、DM の中の条件の文（`applic`）でできています。
- 条件の文は、人が読む文（`displayText`）と、機械が読む条件（`assert`）の両方を持ちます。2 つが合っているかは人が確かめます。
- DM の一部に当てはめるときは、条件の文に名前を付けて置き、要素の `applicRefId` で指します。
- 判定の結果は、当てはまる、当てはまらない、判定できない、の 3 つです。判定できないものを当てはまらないものとして扱いません。

## 出典

- 著者が入手した S1000D Issue 7 のパッケージ（仕様書の Chap 3.9.5.3、Chap 4.14、XML スキーマの `appliccrossreftable.xsd`・`prdcrossreftable.xsd`・`proced.xsd`、Bike Samples）。入手先は https://users.s1000d.org/
