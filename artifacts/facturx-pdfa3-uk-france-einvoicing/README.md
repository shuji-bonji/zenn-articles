# Factur-X 検証の証跡

Zenn 記事 **[「PDFは請求書ではない」と英国は言い、フランスは「PDFが請求書だ」と決めた](../../articles/facturx-pdfa3-uk-france-einvoicing.md)** で使ったファイル一式。

記事中の数字（veraPDF 146/146 PASS、106/106 PASS）はこのファイルで測ったものです。

> **English summary** — Test specimens for a Japanese article about hybrid e-invoices.
> `03-facturx-candidate.pdf` is a PDF/A-3b document with an embedded CII XML
> (`AFRelationship = Alternative`). `05-divergent-candidate.pdf` is **deliberately
> inconsistent**: the page renders 1 200,00 EUR while the embedded XML says 12 000,00 EUR.
> Both are judged COMPLIANT by veraPDF 1.30.0 for PDF/A-3b (146/146 rules).
> Feel free to use these as counter-examples when building invoice validators.

---

## ⚠️ 04 と 05 は意図的に壊した検体です

`04-divergent-attached.pdf` と `05-divergent-candidate.pdf` は、**人が見る金額と機械が読む金額が 10 倍食い違う請求書**です。

- ページ上の印字：**1 200,00 EUR**
- 埋め込み XML の `GrandTotalAmount` / `DuePayableAmount`：**12 000,00 EUR**

**壊れていることが要点の検体**であって、正しい Factur-X の見本ではありません。請求書の雛形として流用しないでください。

一方で、検証ツールを書く人にとっては価値のある反例です。PDF/A の検証を通ってしまう不整合の実物なので、テストフィクスチャとして自由に使ってください。作り方は記事本文に全部書いてあるので、このファイルを伏せても何も守れません。だから公開しています。

## ファイル

| ファイル | 内容 |
|---|---|
| `factur-x.xml` | CII (UN/CEFACT CrossIndustryInvoice)。`urn:cen.eu:en16931:2017` を宣言。税込 1 200,00 EUR |
| `factur-x-divergent.xml` | 上記の合計額 2 行のみ 12 000,00 EUR に改変 |
| `01-invoice-base.pdf` | 請求書 1 ページ（tagged, `lang=fr-FR`, A4）。印字は 1 200,00 EUR |
| `02-invoice-attached.pdf` | 01 に `factur-x.xml` を `AFRelationship=Alternative` で添付 |
| `03-facturx-candidate.pdf` | 02 を PDF/A-3b の器に載せたもの（**整合している方**） |
| `04-divergent-attached.pdf` | 01 に `factur-x-divergent.xml` を添付 |
| `05-divergent-candidate.pdf` | 04 を PDF/A-3b の器に載せたもの（**食い違う方**） |

## 実測結果

検証環境:

```
veraPDF 1.30.0
Built: Wed Jun 03 13:47:00 JST 2026
（Homebrew formula は 1.30.2、同梱 jar は gui-1.30.0.jar）
```

ルール数は veraPDF のビルドと Validation Profile のリビジョンに依存します。

| 観測点 | 03（整合） | 05（不整合） |
|---|---|---|
| catalog `/AF` | あり | あり |
| `AFRelationship` | `Alternative` | `Alternative` |
| `/Names /EmbeddedFiles` | あり | あり |
| `/Type /EmbeddedFile` の `Subtype` | `/text#2Fxml` | 同左 |
| `Params` / `ModDate` | あり | あり |
| **veraPDF `pdfa-3b`** | **COMPLIANT 146/146** | **COMPLIANT 146/146** |
| veraPDF `pdfua-1` | COMPLIANT 106/106 | — |
| XMP `fx:` 拡張スキーマ | **無し** | **無し** |
| PDF 面と XML 面の金額 | 一致 | **1 200 vs 12 000** |

### 読み方

1. **ISO 32000-2 の要求は満たしている。** Filespec と EmbeddedFile は 14.13.2 と Table 43 のとおり（`Subtype` の MIME = shall、`Params`/`ModDate` = should、`/AF` と名前ツリーの両方）
2. **しかし Factur-X としては不完全。** XMP に `urn:factur-x:pdfa:CrossIndustryDocument:invoice:1p0#`（prefix `fx`）の拡張スキーマと `DocumentType` / `DocumentFileName` / `Version` / `ConformanceLevel` が要る。受信側はまずここを見るので、無いと Factur-X と認識されない
3. **PDF 面と XML 面の食い違いを検出する層がどこにも無い。** veraPDF が見ているのは PDF/A-3b の 146 ルール、つまり「自己完結して開けるか」の側。ハイブリッド形式固有の失敗モードはその 146 に入っていない

### 未検証であること

- 生成した CII XML を **EN 16931 の Schematron にかけていません**。`urn:cen.eu:en16931:2017` は宣言であって、測っていません（支払手段 BG-16 など足りない BT があります）
- **ISO 19005（PDF/A）の原文は参照していません。** PDF/A について書いたことはすべて PDF Association の Application Note 002 経由です。よってここで言えるのは「veraPDF はこう判定した」までで、「ISO 19005 に適合する」ではありません
- フランスのフォーマット要件の一次資料（DGFiP spécifications externes、AFNOR XP Z12-012）は参照していません

## 再現手順

記事本文の手順そのままです。使ったのは [pdf-writer-mcp / pdf-verify-mcp](https://www.npmjs.com/~shuji-bonji) ですが、やっていることは素の PDF 操作なので pikepdf でも iText でも同じです。

```
create_table_pdf   → 01-invoice-base.pdf      (tagged, lang=fr-FR, A4)
attach_file        → 02-invoice-attached.pdf  (name=factur-x.xml,
                                               mimeType=text/xml,
                                               relationship=Alternative)
ensure_pdfa        → 03-facturx-candidate.pdf (flavour=pdfa-3b)
validate_conformance                          (flavour=pdfa-3b)
```

04 / 05 は 01 を起点に `factur-x-divergent.xml` で同じ手順。

XML の差分はこの 2 行だけです。

```diff
-        <ram:GrandTotalAmount>1200.00</ram:GrandTotalAmount>
-        <ram:DuePayableAmount>1200.00</ram:DuePayableAmount>
+        <ram:GrandTotalAmount>12000.00</ram:GrandTotalAmount>
+        <ram:DuePayableAmount>12000.00</ram:DuePayableAmount>
```

なお 03 と 05 は金額以外にも差があります（添付の `/Desc`、`/Size`、`ModDate`）。PDF のページ内容と生成手順は同一です。

## チェックサム

```
608531607eba994514439ffe775a81fb  factur-x.xml
d1c16528bc97057ab558f1179851bf13  factur-x-divergent.xml
9e94d2df720b9be5f3982784dfda2b56  01-invoice-base.pdf
92b9a6fadfc4f178a2ca0994754011f3  02-invoice-attached.pdf
ee3b778837d4fb86930439fe3d0c529b  03-facturx-candidate.pdf
0533abd01f6b91728264b3b45ce34f5d  04-divergent-attached.pdf
5f110b541f0a410613a6d4e18072ae87  05-divergent-candidate.pdf
```

架空の事業者（Atelier Rive Gauche SARL / Comptoir Lumière SAS）と架空の VAT 番号を使っています。実在の企業・番号とは関係ありません。
