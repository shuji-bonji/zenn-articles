---
title: 'PDFの署名が「本物か」をLLMが検証できるMCPサーバーを作った'
emoji: '🔏'
type: 'tech'
topics: ['ai', 'pdf', 'typescript', 'mcpサーバー', '電子署名']
published: true
---

## はじめに

これまでの記事で、PDF仕様書を「引く」**pdf-spec-mcp** と、PDFの内部構造を「読む」**pdf-reader-mcp** を紹介しました。

https://zenn.dev/shuji_bonji/articles/ab30e8318d8be9

https://zenn.dev/shuji_bonji/articles/463c33c2174d6c

pdf-reader-mcp には `inspect_signatures` という署名フィールドの**構造**を解析するツールがあります。/ByteRange や /SubFilter、署名理由を取り出せます。しかし、前回の記事を書いた時点から積み残していた問いがあります。

**「で、この署名は有効なのか？」**

構造が正しく見えても、署名後に中身が書き換えられているかもしれない。証明書が失効しているかもしれない。そもそも誰でも作れる自己署名証明書かもしれない。これらは構造を眺めるだけでは判定できず、**暗号学的な検証**が必要です。

そこで作ったのが **pdf-verify-mcp** です。

https://github.com/shuji-bonji/pdf-verify-mcp

## pdf-verify-mcp とは

PDF の**真正性（authenticity）**を検証する MCP サーバーです。「中身に何があるか」（reader）、「仕様は何を要求するか」（spec）に続く、PDF family の第3の層 —「**それは本物か**」に答えます。

```
┌─────────────────────────────────────────┐
│         AI Agent（Claude等）             │
│  「この契約書、署名後に改ざんされてない？」   │
└──────────────┬──────────────────────────┘
               │ MCP Tool Call
               ▼
┌─────────────────────────────────────────┐
│          pdf-verify-mcp                 │
│  ・ByteRange ダイジェスト再計算 × CMS照合   │
│  ・CMS/PKCS#7 署名値の暗号学的検証         │
│  ・信頼チェーン評価・失効確認（OCSP/CRL）    │
│  ・RFC 3161 タイムスタンプ検証             │
│  ・増分更新解析・DocMDP 認証違反検知        │
└──────────────┬──────────────────────────┘
               │ verdict + 根拠
               ▼
┌─────────────────────────────────────────┐
│           署名済みPDFファイル              │
└─────────────────────────────────────────┘
```

### pdf-reader-mcp の inspect_signatures との違い

| 比較項目                           | pdf-reader-mcp `inspect_signatures` | pdf-verify-mcp `verify_signatures`    |
| ---------------------------------- | ----------------------------------- | ------------------------------------- |
| 署名フィールド構造（ByteRange 等） | ✅                                  | ✅                                    |
| ダイジェスト照合（改ざん検知）     | ❌                                  | ✅ ByteRange を再計算して CMS と照合  |
| 署名値の暗号検証                   | ❌                                  | ✅ pkijs + WebCrypto                  |
| 信頼チェーン評価                   | ❌                                  | ✅ trust_anchors 指定で CA まで検証   |
| 失効確認                           | ❌                                  | ✅ 埋め込み OCSP/CRL + オンライン照会 |
| タイムスタンプ検証                 | ❌                                  | ✅ RFC 3161 完全検証                  |
| PAdES レベル判定                   | ❌                                  | ✅ B-B / B-T / B-LT / B-LTA           |

役割分担は明確で、reader は「構造」、verify は「真正性」。従来 `inspect_signatures` の説明文に書いていた「有効性の判定は検証ツールの領分」という宿題を、実装で回収した形です。

## 5つのツール

### 1. `verify_signatures` — 署名の暗号学的検証

中核ツールです。実際に署名済み PDF を検証した結果（抜粋）:

```markdown
## 1. Signature1

- Verdict: **VALID**
- Trust: **not_evaluated** — No trust anchors provided
- Revocation: **unknown** — No embedded revocation information found
- SubFilter: ETSI.CAdES.detached
- Covers entire file: yes
- Digest match (ByteRange vs messageDigest): **yes**
- Signature cryptographically verified: **yes**
- Digest algorithm: SHA-256
- Signer: CN=pdf-verify-mcp test
  - Self-signed: yes
```

同じ PDF の中身を 1 バイトでも書き換えると、こうなります。

```markdown
- Verdict: **INVALID**
- Digest match (ByteRange vs messageDigest): **no**
- Note: ByteRange digest does not match the CMS messageDigest
  — the signed bytes were altered.
```

#### 「valid」は「信用してよい」ではない

ここが本ツールで一番伝えたいことです。上の例の verdict は VALID ですが、Trust は **not_evaluated**。つまり「署名時点からバイト列が変わっていない」ことは証明されても、**誰が署名したかは何も保証されていません**（実際この署名者は自己署名のテスト証明書です）。

署名者の身元まで検証するには、信頼する CA 証明書を渡します。

```
verify_signatures({
  file_path: "/path/to/contract.pdf",
  trust_anchors: ["/path/to/ca-cert.pem"],
  check_revocation: "online"
})
```

`check_revocation: "online"` を指定すると、証明書の AIA 拡張から OCSP レスポンダへ問い合わせ、CRL 配布点も照会します。中間証明書が PDF に埋め込まれていない場合も、AIA caIssuers を辿ってチェーンを補完します（古い署名済み請求書などで実際によくあるケースです）。

### 2. `verify_integrity` — 署名後の改ざん検知

PDF は「増分更新」という仕組みで、既存バイト列を保ったままファイル末尾に変更を追記できます。署名後の追記は合法（連署や LTV データ追加）ですが、**変更一切禁止（DocMDP P=1）で認証された文書への追記は認証違反**です。

認証後に増分更新を加えた PDF の実行結果:

```markdown
## Certification (DocMDP)

- Permission: 1 — No changes permitted
- Violated by later changes: **yes**

## Changes after signing

- Signature1: 83 byte(s) added after signed range
```

「署名は暗号学的に有効。しかし認証は破られている」— この2つを区別して報告できるのがポイントです。

### 3. `detect_pades_level` — PAdES ベースラインレベル判定

長期署名（LTV）の観点で、署名が B-B / B-T / B-LT / B-LTA のどのレベルかを判定します。

```markdown
- PAdES: yes — level **B-B**
- Evidence: signature timestamp=no, DSS=no, VRI=no, document timestamp=no
```

B-B 止まりの署名は、署名者証明書が失効・期限切れになった時点で検証不能になります。10年保存が必要な文書でこれを見つけたら、LTV 化（B-LT / B-LTA）が必要というサインです。

単に DSS 辞書の有無を見るだけでなく、**DSS 内の失効情報が実際に署名者証明書をカバーしているか**まで検証します。「宣言上は B-LT だが実データが欠けている」ファイルを B-T に降格させます。

### 4. `identify_conformance` — PDF/A・PDF/UA 宣言の識別

XMP メタデータから PDF/A（pdfaid）と PDF/UA（pdfuaid）の宣言を読み取ります。あくまで「宣言」の識別で、実際に適合しているかは次のツールの仕事です。

### 5. `validate_conformance` — PDF/A 適合検証（ハイブリッドエンジン）

「PDF/A-2b と宣言している PDF が、実際に適合しているか」を検証します。実行結果:

```markdown
- Flavour: PDF/A-2b
- Engine: verapdf
- Result: **NOT COMPLIANT**
- Rules: 144 checked, 140 passed, 4 failed

## Violations

- **ISO 19005-2:2011 6.1.3-1** (6.1.3): The file trailer dictionary
  shall contain the ID keyword ...
- **ISO 19005-2:2011 6.2.4.3-4** (6.2.4.3): DeviceGray shall only be
  used if ... a PDF/A OutputIntent is present
```

エンジンは2段構えです。

| エンジン   | 条件                                 | 結果の扱い                                                                                              |
| ---------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------- |
| veraPDF    | インストール済みなら自動検出して委譲 | 権威的（compliant: true/false）                                                                         |
| 内蔵ルール | veraPDF がないとき                   | ~15 の高価値ルールのサブセット。違反発見は確定的だが、「違反なし」は**認証ではない**（compliant: null） |

内蔵エンジンが「全ルール通過」を `true` ではなく `null` で返すのは意図的な設計です。サブセット検査で「適合」を名乗るのは誠実ではないからです。

## 技術的な設計判断

### ByteRange ダイジェストの独立検証

PDF 署名の基本構造は「/Contents（署名データ）を除いたファイル全体のハッシュに署名する」というものです。

```
|←── ByteRange[0,1] ──→|←─ /Contents ─→|←── ByteRange[2,3] ──→|
[ 署名対象バイト列 前半  ][  署名データ    ][ 署名対象バイト列 後半  ]
```

pdf-verify-mcp は pkijs の検証 API に丸投げせず、**ByteRange のバイト列からダイジェストを自前で再計算し、CMS の messageDigest 属性と独立に照合**します。「署名検証が失敗した」だけでは改ざんなのか形式非対応なのか分かりませんが、ダイジェスト照合を分離しておくと「署名対象のバイト列そのものが変わっている（=改ざんの可能性）」を特定できます。verdict が invalid / indeterminate を区別できるのはこの分離のおかげです。

### レガシー署名への現実対応 — MD5 と OpenSSL 3

古い署名済み PDF（実在する某社の請求書など）には MD5 ベースの署名が残っています。WebCrypto は MD5 をサポートしないため、pkijs の検証パスに乗りません。かといって「未対応」で突き返すと現実の文書が検証できないので、MD5/SHA-1 系は `node:crypto` にフォールバックし、検証結果に**弱いダイジェストである旨の警告**を必ず添えます。

暗号化 PDF も同様です。RC4 暗号化（R2–R4）の古い PDF はまだ大量に存在しますが、OpenSSL 3 は RC4 を無効化しているため、**RC4 は純 JS で実装**しました。AES-256（R6）の鍵導出（ISO 32000-2 Algorithm 2.A/2.B）も含め、権限暗号化 PDF は空パスワードで自動復号、リーダーパスワード付きは `password` パラメータで復号できます。

なお署名の /Contents は暗号化対象外（ISO 32000-1 §7.6.2）なので、**復号に失敗しても署名検証自体は正常に動きます**。復号は署名理由やフィールド名などのメタデータ回復のためです。誤ったパスワードは /U エントリとの照合で検出し、文字化けしたメタデータを返さないようにしています。

### テストフィクスチャは暗号ごと自前生成

署名済み PDF・改ざん済み PDF・CA チェーン・CRL・タイムスタンプトークンといったテスト素材は、すべて pkijs + WebCrypto で**インメモリ生成**しています。バイナリ資産をリポジトリに置かない方針は pdf-reader-mcp と同じですが、今回は「自己署名証明書の発行 → CMS 署名の構築 → ByteRange の計算」まで含めてコードで再現しています。改ざんテストは「署名済み PDF の署名対象バイトを 1 バイト書き換える」関数で作ります。

```typescript
const identity = await createTestIdentity(); // 証明書+鍵ペア生成
const signed = await createSignedPdf(identity); // 署名済みPDF構築
const tampered = tamperSignedPdf(signed); // 1バイト改ざん
```

暗号化まわりの E2E テストには qpdf を使い、RC4 / AES-128 / AES-256 の各暗号化 PDF を CI 上で生成して回帰テストしています。

## PDF family の中での位置づけ

これで family の3層が揃いました。

```
pdf-spec-mcp    「仕様は何を要求するか」（正典層）
pdf-reader-mcp  「中身に何があるか」    （実体層）
pdf-verify-mcp  「それは本物か」        （真正性層）
```

3つは互いに依存せず、それぞれ単独で動きます。verify は reader なしで署名検証が完結しますし、逆も然り。連携が必要な場面 —「取引先から届いた署名済み PDF の受入監査」「電帳法対応の長期保存チェック」のような複数ツールをまたぐ手順 — は、MCP サーバーを増やすのではなく **Skill（手順書）** として記述する方針にしています。この設計判断の話はまた別の記事で書く予定です。

## セットアップ

npx で即実行できます。Claude Desktop の `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "pdf-verify": {
      "command": "npx",
      "args": ["-y", "@shuji-bonji/pdf-verify-mcp"]
    }
  }
}
```

Claude Code:

```bash
claude mcp add pdf-verify -- npx -y @shuji-bonji/pdf-verify-mcp
```

オプション設定:

| 環境変数                   | 用途                                                                |
| -------------------------- | ------------------------------------------------------------------- |
| `PDF_VERIFY_TRUST_ANCHORS` | 信頼する CA 証明書のディレクトリ（_.pem / _.crt / _.cer / _.der）   |
| `PDF_VERIFY_VERAPDF`       | veraPDF 実行ファイルのパス（PATH と Homebrew の既知パスは自動検出） |

## まとめ

pdf-verify-mcp は「PDF の真正性を LLM が検証できるようにする」MCP サーバーです。

- **改ざん検知** — ByteRange ダイジェストの独立照合、増分更新解析、DocMDP 認証違反の検出
- **署名者の検証** — 信頼チェーン評価、埋め込み/オンライン失効確認、AIA によるチェーン補完
- **長期署名** — RFC 3161 タイムスタンプ完全検証、LTV データの実在検証込みの PAdES レベル判定
- **PDF/A 検証** — veraPDF 委譲 + 内蔵ルールのハイブリッド。「違反なし ≠ 認証」を正直に返す設計
- **現実の PDF への対応** — MD5 レガシー署名、RC4/AES 暗号化 PDF の復号

そして繰り返しになりますが、**trust_anchors なしの「valid」は暗号学的完全性のみの主張**です。ツールの説明文にもレスポンスにもこの注意を明記しています。LLM に検証を任せるからこそ、「何を検証して、何を検証していないか」を機械可読に返すことが大切だと考えています。

### リンク

- **GitHub**: https://github.com/shuji-bonji/pdf-verify-mcp
- **npm**: https://www.npmjs.com/package/@shuji-bonji/pdf-verify-mcp

### 関連記事

- [PDF仕様書をLLMが「引ける」MCPサーバーを作った](https://zenn.dev/shuji_bonji/articles/ab30e8318d8be9)
- [既存のPDF系MCPサーバーが「読めない」ものを読むMCPサーバーを作った](https://zenn.dev/shuji_bonji/articles/463c33c2174d6c)
- [pdf-spec-mcp が PDF 2.0 Errata Collection 3 に対応した](https://zenn.dev/shuji_bonji/articles/pdf-spec-mcp-ec3-support)
- [PDF 2.0 Errata Collection 3 とは何か](https://zenn.dev/shuji_bonji/articles/pdf20-errata-collection-3)
