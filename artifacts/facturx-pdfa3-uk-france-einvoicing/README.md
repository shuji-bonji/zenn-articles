# Factur-X 検証の証跡

Zenn 記事 **[「PDFは請求書ではない」と英国は言い、フランスは「PDFが請求書だ」と決めた](../../articles/facturx-pdfa3-uk-france-einvoicing.md)** で使ったファイル一式。

記事中の数字（veraPDF 146/146 PASS、106/106 PASS）はこのファイルで測ったものです。

> **English summary** — Test specimens for a Japanese article about hybrid e-invoices.
> `03-facturx-candidate.pdf` is a PDF/A-3b document with an embedded CII XML
> (`AFRelationship = Alternative`). `07-consistent-divergent.pdf` is **deliberately
> inconsistent between its two faces**: the page renders 1 200,00 EUR while the embedded
> XML says 12 000,00 EUR — yet the XML is arithmetically self-consistent, so EN 16931
> Schematron rules such as BR-CO-15 would not catch it. veraPDF 1.30.0 judges both
> COMPLIANT for PDF/A-3b (146/146 rules).
> Feel free to use these as counter-examples when building invoice validators.

---

## ⚠️ 04〜07 は意図的に壊した検体です

`04` `05` `06` `07` は、**人が見る金額と機械が読む金額が 10 倍食い違う請求書**です。

- ページ上の印字：**1 000,00 / 200,00 / 1 200,00 EUR**
- 埋め込み XML：**10 000,00 / 2 000,00 / 12 000,00 EUR**

**壊れていることが要点の検体**であって、正しい Factur-X の見本ではありません。請求書の雛形として流用しないでください。

一方で、検証ツールを書く人にとっては価値のある反例です。PDF/A の検証を通ってしまう不整合の実物なので、テストフィクスチャとして自由に使ってください。作り方は記事本文に全部書いてあるので、このファイルを伏せても何も守れません。だから公開しています。

## ファイル

| ファイル | 内容 |
|---|---|
| `factur-x.xml` | CII (UN/CEFACT CrossIndustryInvoice)。`urn:cen.eu:en16931:2017` を宣言。税込 1 200,00 EUR |
| `factur-x-divergent.xml` | **合計欄だけ** 12 000,00 に改変。→ XML 内部の算術が壊れる（1 000 + 200 ≠ 12 000） |
| `factur-x-consistent.xml` | **明細・税額・合計をすべて 10 倍**。XML 内部の算術は閉じたまま、PDF 面とだけ食い違う |
| `01-invoice-base.pdf` | 請求書 1 ページ（tagged, `lang=fr-FR`, A4）。印字は 1 200,00 EUR |
| `02-invoice-attached.pdf` | 01 に `factur-x.xml` を `AFRelationship=Alternative` で添付 |
| `03-facturx-candidate.pdf` | 02 を PDF/A-3b の器に載せたもの（**整合している方**） |
| `04-divergent-attached.pdf` | 01 に `factur-x-divergent.xml` を添付 |
| `05-divergent-candidate.pdf` | 04 を PDF/A-3b の器に載せたもの（**第 1 版・XML 内部が矛盾**） |
| `06-consistent-attached.pdf` | 01 に `factur-x-consistent.xml` を添付 |
| `07-consistent-divergent.pdf` | 06 を PDF/A-3b の器に載せたもの（**本命・両面とも内部整合、互いに矛盾**） |

### 05 と 07 の違いが重要です

`05` は合計欄だけを 10 倍にしたため、**XML の中だけで矛盾しています**。

```
LineTotalAmount       1 000.00
TaxBasisTotalAmount   1 000.00
TaxTotalAmount          200.00
GrandTotalAmount     12 000.00   ← 1 000 + 200 ≠ 12 000
```

EN 16931 の **BR-CO-15**（税込合計 = 税抜合計 + 消費税額）に反するので、Schematron は PDF を一度も見ることなくこれを落とせます。つまり `05` では「どの層も検出しない」ことの証明になりません。

`07` は明細から合計まですべて 10 倍にしてあります。

```
単価 5 000.00 × 数量 2      = 10 000.00   ✓
課税標準 10 000.00 × 20 %   =  2 000.00   ✓
10 000.00 + 2 000.00        = 12 000.00   ✓  BR-CO-15 を満たす
```

**XML だけを見れば完璧に整合。PDF のページだけを見ても完璧に整合。矛盾は 2 つの面のあいだにしか存在しません。**

## 実測結果

検証環境:

```
veraPDF 1.30.0
Built: Wed Jun 03 13:47:00 JST 2026
（Homebrew formula は 1.30.2、同梱 jar は gui-1.30.0.jar）
```

ルール数（PDF/A-3b = 146、PDF/UA-1 = 106）はビルドと Validation Profile のリビジョンに依存します。

| 観測点 | 03（整合） | 05（XML 内部が矛盾） | 07（両面とも内部整合） |
|---|---|---|---|
| catalog `/AF` | あり | あり | あり |
| `AFRelationship` | `Alternative` | `Alternative` | `Alternative` |
| `/Names /EmbeddedFiles` | あり | あり | あり |
| `/Type /EmbeddedFile` の `Subtype` | `/text#2Fxml` | 同左 | 同左 |
| `Params` / `ModDate` | あり | あり | あり |
| **veraPDF `pdfa-3b`** | **COMPLIANT 146/146** | **COMPLIANT 146/146** | **COMPLIANT 146/146** |
| XMP `fx:` 拡張スキーマ | **無し** | **無し** | **無し** |
| XML 内部の算術（BR-CO-15） | 閉じている | **破れている** | 閉じている |
| PDF 面と XML 面の金額 | 一致 | 1 200 vs 12 000 | **1 200 vs 12 000** |

`03-facturx-candidate.pdf` は XMP に `pdfuaid:part=1` も宣言していたため `pdfua-1` でも測定 → **COMPLIANT 106/106**。

`validate_clauses`（ISO 32000-1/-2 条項制約）→ violations 0 / notDecided 1（`given.isSubset`）。

## 検証層の分担

| 層 | 見るもの | 本 PoC では |
|---|---|---|
| ① PDF/A-3b（veraPDF） | 器。フォント埋め込み・ICC・XMP・OutputIntent | **実測（146/146 PASS）** |
| ② Factur-X 構造 | XMP の `fx:`、ファイル名、`AFRelationship` | **未実行**（`fx:` が無いので落ちるはず） |
| ③ XSD（CII D22B） | XML の形。要素の有無・型・出現回数 | **未実行** |
| ④ EN 16931 Schematron | XML の中身。合計＝明細の和、税率と税額の対応 | **未実行** |

**①〜④のどれも、PDF のページに印字された金額と埋め込み XML の値が一致するかは見ません。**①は器しか見ない。②は XMP とファイル名しか見ない。③④は XML の中で完結する検査で、PDF をレンダリングしません。

ただしこれは**各層が何を対象にしているかからの推論**であって、②③④を実際に走らせた結果ではありません。②③④を実行して層ごとの判定を並べれば、この PoC はもう一段強くなります（→ 未実施）。

## 結論

1. **ISO 32000-2 の要求は満たせている。** `attach_file` は Filespec / EmbeddedFile を 14.13.2 と Table 43 のとおりに書く（`Subtype` の MIME = shall、`Params`/`ModDate` = should、`/AF` と名前ツリーの両方）
2. **しかし Factur-X 仕様には適合していない。** XMP に `urn:factur-x:pdfa:CrossIndustryDocument:invoice:1p0#`（prefix `fx`）の拡張スキーマと `DocumentType` / `DocumentFileName` / `Version` / `ConformanceLevel` が要る
3. **PDF 面と XML 面の食い違いを検出する層が、既存のどこにも無い。** PDF/A-3 は器の規格、Factur-X 検証は構造の検査、EN 16931 は XML 内部の業務ルール。ハイブリッド形式固有の失敗モードは、どの層の担当でもない

## family 側の宿題

`mcps/_facturx-poc/README.md` に分離しました。要点だけ:

1. **`set_xmp_extension`（pdf-writer）** — `pdfaid` / `pdfuaid` 以外の XMP 拡張スキーマを書く口が無い。Factur-X の `fx:` がこの穴に落ちる
2. **`validate_clauses` の第 4 ドメイン = embedded-files / associated-files** — ISO 32000-2 14.13.2 と Table 43 から shall/should がそのまま写像できる T1 の領域
3. **ハイブリッド文書の意味論的整合** — ISO 32000 に写像できない（EN 16931 のセマンティクス）。family のスコープ外

## 再現手順

```
create_table_pdf   → 01-invoice-base.pdf         (tagged, lang=fr-FR, A4)
attach_file        → 02-invoice-attached.pdf     (name=factur-x.xml,
                                                  mimeType=text/xml,
                                                  relationship=Alternative)
ensure_pdfa        → 03-facturx-candidate.pdf    (flavour=pdfa-3b)
validate_conformance                             (flavour=pdfa-3b)
```

04/05 は 01 を起点に `factur-x-divergent.xml`、06/07 は `factur-x-consistent.xml` で同じ手順。

## チェックサム

```
608531607eba994514439ffe775a81fb  factur-x.xml
d1c16528bc97057ab558f1179851bf13  factur-x-divergent.xml
567d3fcb10e00874913192aea1ee035a  factur-x-consistent.xml
9e94d2df720b9be5f3982784dfda2b56  01-invoice-base.pdf
92b9a6fadfc4f178a2ca0994754011f3  02-invoice-attached.pdf
ee3b778837d4fb86930439fe3d0c529b  03-facturx-candidate.pdf
0533abd01f6b91728264b3b45ce34f5d  04-divergent-attached.pdf
5f110b541f0a410613a6d4e18072ae87  05-divergent-candidate.pdf
f86d75cbb304624dcd2626c1e0c53b6b  06-consistent-attached.pdf
4e0528edaf4e55cbb60ed996ebcb2c89  07-consistent-divergent.pdf
```

架空の事業者（Atelier Rive Gauche SARL / Comptoir Lumière SAS）と架空の VAT 番号を使っています。実在の企業・番号とは関係ありません。
