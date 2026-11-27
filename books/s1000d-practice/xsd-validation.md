---
title: "XSD で確かめる"
---

「[DM と XSD・BREX の関係](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/dm-xsd-brex)」で見たとおり、DM は自分が従う XML スキーマ（XSD）を指しています。この章では、その XSD で DM を検証します。確かめる 4 つの段階のうち、整形式と XSD の段階を扱います。要素の名前、並びの順、必須の要素、属性の値の型を機械が確かめます。

XSD（XML Schema Definition）は、XML の文書に書ける要素と属性、その順と個数、値の型を定めるための言語です。W3C が勧告として定めた標準で、スキーマそのものも XML で書きます。S1000D は、DM の種類ごとに XSD のファイルを用意しています。たとえば手順の DM には `proced.xsd`、説明の DM には `descript.xsd` を使います。

XSD は、仕様パッケージの XML Schema Package に入っています。入手と置き方は「[仕様パッケージを用意する](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/spec-package)」で説明しました。

## どのスキーマで検証するか

検証に使うスキーマは DM 自身が指定しています。ルート要素の `xsi:noNamespaceSchemaLocation` です。

```xml
<dmodule xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:noNamespaceSchemaLocation="http://www.s1000d.org/S1000D_7/xml_schema_flat/proced.xsd">
```

この値は URL の形をしていますが、検証のたびに、この URL からスキーマを取ってくるわけではありません。URL の末尾のファイル名（`proced.xsd`）と S1000D の Issue（`S1000D_7`）を見て、コンピューターに置いた仕様パッケージの中の、同じ名前のスキーマを使います。

Issue が違うスキーマでは検証できません。Issue 7 の DM を Issue 4.1 のスキーマで検証すると、名前が変わった要素がすべてエラーになります。

## `xml_schema_flat` は 2 つあります

仕様パッケージを展開したフォルダーの中に、`xml_schema_flat/` という名前のフォルダーが 2 つ見つかります。検証に使えるのは片方だけです。

| 場所 | 内容 | 使えるか |
| --- | --- | --- |
| XML Schema Package の中 | スキーマの本体。ほかのスキーマを読み込む指定（`xs:import`）が、同じフォルダーのファイル名で書かれている | 使える |
| Data Dictionary の中 | データ辞書の表示用の写し。33 本すべてが、本体と内容が違う。`xs:import` が、インターネット上の URL で書かれている | 使えない |

データ辞書の側を使うと、検証ツールがインターネットからスキーマを取りに行こうとして、失敗します。検証には、XML Schema Package の側を指定してください。

## XSD が見つけるもの

券売機の手順の DM に、わざと誤りを 2 つ入れて、検証します。

1. 発行日の月を `13` にする。
2. 手順の段落の後ろにスキーマに無い要素 `memo` を足す。

```xml
<issueDate year="2026" month="13" day="25"/>
```

```xml
<proceduralStep id="scr-station">
  <title>STEP2</title>
  <para>到着駅を選びます。</para>
  <memo>文言を確認中</memo>
  …
```

s1000d-verify-mcp の [`validate_schema`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/stack/verify/validate-schema/) の結果は、`invalid` で、エラーが 2 件です。

| 行 | 場所 | エラーの文 |
| --- | --- | --- |
| 15 | `…/dmAddressItems/issueDate` | Element 'issueDate', attribute 'month': [facet 'maxInclusive'] The value '13' is greater than the maximum value allowed ('12'). |
| 66 | `…/mainProcedure/proceduralStep/memo` | Element 'memo': This element is not expected. Expected is one of ( proceduralStep, proceduralStepAlts ). |

エラーの文は、検証のライブラリ（libxml2）が出す英語の文です。読み方を 2 件で説明します。

**1 件目**は値の型の誤りです。`month` 属性は 1 から 12 までの数と決まっています。`facet 'maxInclusive'` は、上限の決まりに当たった、という意味です。

**2 件目**は並びの誤りです。`This element is not expected` は、その場所にその要素を置けない、という意味です。続く `Expected is one of ( … )` が、その場所に置ける要素の一覧です。

2 件目のエラーには役に立つ情報が入っています。`memo` を置いた場所で、スキーマが置けるとしているのは、`proceduralStep` か `proceduralStepAlts` でした。段落や図は、もう置けない場所だった、ということです。「[説明と手順の内容を読む](https://zenn.dev/shuji_bonji/books/s1000d-practice/viewer/descriptive-and-procedural)」で見た、「手順の中では、段落や図の後に下の階層の手順が来る」という順が、ここに現れています。

:::message
並びの誤りのエラーは、誤りのある要素ではなく、その次の要素の場所で出ることがあります。必須の要素を書き忘れたときは、「次に来た要素は、ここには置けない」というエラーになります。エラーの行だけを見ずに、`Expected is one of` の一覧を見て、何が足りないかを考えます。
:::

## XSD が見つけないもの

XSD の検証が通っても確かめられていないことがあります。

| 見つけないもの | 例 | 確かめる方法 |
| --- | --- | --- |
| プロジェクトが禁じた値 | 機密区分 `05`（スキーマは 2 桁の番号なら通す） | BREX |
| ほかの DM との関係 | 指す先の DM が CSDB にあるか | 参照の確認 |
| ファイル名との一致 | ファイル名は `001-00` なのに、中身は `002-00` | ファイル名と中身を比べる |
| 属性どうしの関係 | 最初の発行（`001-00`）なのに、`issueType` が `changed` | 既定 BREX |
| 内容の適切さ | 警告にすべきものを注意に書いている | 人によるレビュー |

同じ DM の中の `id` を指す属性は XSD が確かめます。指す先の `id` が無いと、XSD のエラーになります。

## 確かめ方

券売機の CSDB の全体を s1000d-verify-mcp の [`verify_csdb`](https://shuji-bonji.github.io/s1000d-stack/ja/reference/stack/verify/verify-csdb/) で検証すると、DM とパブリケーションモジュールの 17 件すべてが、XSD の検証を通ります。

検証の結果では次の 3 つを必ず見ます。

| 見るもの | 理由 |
| --- | --- |
| 状態が `valid` か | `invalid` のほかに、スキーマが見つからなかった状態（`schema-not-found`）と、検証を行わなかった状態（`not-run`）がある。この 2 つは、通ったことにならない |
| 使ったスキーマの場所 | 仕様パッケージの正しい側のフォルダーか |
| S1000D の Issue | DM の Issue と、スキーマの Issue が同じか |

使ったスキーマの場所は、次のように返ります。券売機の手順の DM を検証したときの値です。場所が XML Schema Package の中になっていれば、正しい側を使えています。

```text
…/xml-schema-package/S1000D Issue 7 XML Schema Package/xml_schema_flat/proced.xsd
```

検証の結果を記録に残すときは、「通った」だけでなく、どのスキーマで、いつ検証したかも残します。スキーマの Issue が違えば、同じ DM でも結果が変わるためです。

## 実装するときの注意

### 検証のライブラリの選び方

Issue 7 のスキーマ 33 本は、XSD 1.1 で加わった機能（`xs:assert`、`xs:alternative` など）を使っていません。そのため、XSD 1.0 にだけ対応したライブラリ（libxml2 など）でも検証できます。

JavaScript だけで書かれた、S1000D の規模の XSD を検証できるライブラリは、見つかりませんでした。s1000d-core は、C のライブラリ libxml2 を WebAssembly にしたものを使っています。候補を 2 つ、Issue 7 のスキーマ 33 本と、公式サンプルの DM 106 件で比べました。

| 観点 | `xmllint-wasm` 5.3.0 | `libxml2-wasm` 0.7.2 |
| --- | --- | --- |
| 判定 | 106 件すべて適合。入れた誤り 3 種を検出 | 同じ |
| 106 件の検証にかかった時間 | 7.8 秒 | 0.27 秒 |
| スキーマの扱い | 呼び出すたびに、スキーマを読み込み直す | 読み込んだスキーマを使い回せる |

判定は同じで、速さが約 30 倍違いました。CSDB の全体を検証する使い方では、同じスキーマで多くの DM を検証するので、スキーマを使い回せる方を選びました。

### スキーマのフォルダーを名前で決め打ちにしない

仕様パッケージの内側の zip を展開したフォルダーの名前は、利用者によって違います。s1000d-core は、フォルダーの名前を決めずに、`brex.xsd` があるフォルダーをスキーマのフォルダーとして探します。ただし、データ辞書のフォルダーの下は除きます。`xml_schema_flat/` が 2 つあり、データ辞書の側は検証に使えないためです。

### `xs:import` を解決する

S1000D の各スキーマは、共通のスキーマ（`xml.xsd`、`xlink.xsd`、`rdf.xsd`）を `xs:import` で読み込みます。読み込み先を解決できないと、スキーマの読み込みの段階で失敗します。

WebAssembly の中からは、ファイルシステムが直接は見えません。`libxml2-wasm` では、同じフォルダーのスキーマを名前と中身の組として登録しておくと、`xs:import` がそこから解決されます。この登録の機能は、まだ安定版ではない扱い（alpha）なので、s1000d-core は、検証の処理を 1 つのインターフェースの後ろに置いています。機能の形が変わっても、直す場所が 1 か所で済むようにするためです。

データ辞書の側の `xml_schema_flat/` が使えない理由も、`xs:import` です。読み込み先がインターネット上の URL で書かれていて、ライブラリがネットワークに接続しようとして失敗します。

### メモリーを解放する

`libxml2-wasm` の文書と検証器のオブジェクトは、使い終わったら、明示して解放する必要があります。s1000d-core は、検証器をスキーマごとに 1 つ作って使い回し、文書は、検証のたびに解放しています。

### DOCTYPE の宣言は別のパーサーで読む

`libxml2-wasm` には、DOCTYPE の `ENTITY` 宣言と `NOTATION` 宣言を列挙する機能がありません。図への参照を解決するための宣言の読み取りは、別のパーサー（`@xmldom/xmldom`）で行っています。XSD の検証と、宣言の読み取りで、同じ XML を 2 回読むことになります。

## この章のポイント

- 検証に使うスキーマは DM のルート要素が指しています。URL から取ってくるのではなく、コンピューターに置いた同じ名前のスキーマを使います。
- `xml_schema_flat/` は 2 つあります。使えるのは XML Schema Package の中のものです。
- 並びの誤りのエラーは、`Expected is one of` の一覧を見て、その場所に何が置けるかを読みます。
- XSD の検証は構造だけを確かめます。プロジェクトのルール、ほかの DM との関係、内容の適切さは、別の方法で確かめます。
- 検証の結果は、状態、使ったスキーマの場所、S1000D の Issue の 3 つを見ます。

## 出典

- 著者が入手した S1000D Issue 7 のパッケージ（XML Schema Package、Data Dictionary、Bike Samples）。入手先は https://users.s1000d.org/
- libxml2-wasm. https://github.com/jameslan/libxml2-wasm
- xmllint-wasm. https://github.com/notengrafik/xmllint-wasm
