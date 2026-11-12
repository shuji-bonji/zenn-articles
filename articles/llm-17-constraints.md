---
title: "今更聞けない LLM の 17 の制約 — ハーネスと Claude Code の設定でどこまで対処できるか"
emoji: "🧭"
type: "tech"
topics: ["llm", "claudecode", "ai", "agent", "harness"]
published: true
---

:::message
この記事の Claude Code の機能に関する記述は、2026-11-11 時点の公式ドキュメント（code.claude.com/docs）で確認した内容です。Claude Code は更新が速いため、設定名や挙動は変わる可能性があります。制約そのものの説明（前半）は、製品の更新では変わらない内容です。
:::

## はじめに

この記事は、LLM を業務やコーディングエージェントで使っている開発者が、「LLM はなぜこう失敗するのか」と「その失敗を設定でどこまで抑えられるか」を一続きで確認するための整理です。

筆者は以前、LLM の構造的な制約を 8 つに分けて整理しました。そのうち 3 つ（Context Rot、Instruction Decay、Sycophancy）は、Zenn の記事「[CLAUDE.mdがなくても戦える — LLMの構造的制約を踏まえたプロンプト駆動開発](https://zenn.dev/shuji_bonji/articles/c9d325f1fd7646)」でも取り上げています。その後、エージェントにファイルの編集やコマンドの実行を任せる場面が増え、8 つでは説明しきれない失敗が目立つようになりました。そこでこの記事では、制約を 17 個に広げ、5 つの分類にまとめ直しています。

:::message
**この記事の主張**

Claude Code の設定やハーネスの仕組み（CLAUDE.md、Hooks、サブエージェント、権限ルールなど）は、便利機能として個別に作られたものではなく、LLM の制約への対策として存在しています。制約を知っておくと、次の 3 つができるようになります。

1. **失敗の原因を見分けられる**: 会話が長くなって指示を忘れたのか、知らないことを答えたのかで、取るべき対策が変わります
2. **対策を置く場所を選べる**: 必ず守らせたいことは、LLM に読ませる指示ではなく、LLM の外側で動く仕組みに置く、という判断ができます
3. **ツールが変わっても判断を使い回せる**: 機能名が変わっても、別のツールに移っても、「どの制約に対する、どの種類の対策か」という見方はそのまま使えます
:::

### この記事で扱うこと

- LLM の 17 の制約と、それぞれの根拠となる研究
- 17 の制約を生む原因（アーキテクチャと学習方法）
- ハーネスの 4 要素が、どの制約に効くか
- Claude Code の各機能が、どの制約に効くか

### この記事で扱わないこと

- モデルの学習方法そのものを変える対策（学習データの改善、RLHF の改良など）
- Claude Code 以外のツール（Cursor、Codex など）の設定

## 17 という数について

LLM の制約の数え方に、決まった基準はありません。この記事では「利用者から見て、異なる失敗として現れるもの」を 1 つと数えています。原因の単位で数えると 9 個程度になり、仕組みの単位まで分けると 30 個以上になります。17 は、対策を選ぶときに区別する必要がある単位として選んだ数です。

制約の名前は、論文で使われている英語の名前を主にし、日本語の説明を「17 の制約の説明」の各見出しに添えています。ただし、3（Priority Saturation）と 14（Instruction Decay）は筆者が付けた名前で、研究で広く使われている用語ではありません。同じ現象を、Jaroslawicz ほか（2025）は指示の密度（instruction density）が高いときの遵守率の問題として、Laban ほか（2025）は複数のターンに分けた会話での性能の低下として扱っています。10（Self-Evaluation Bias）と 13（Tokenization Limits）も、複数の研究の内容をまとめて呼ぶための説明的な名前です。

筆者が以前整理した 8 つは、すべてこの 17 に含まれています。以前の整理を読んでいない方は、この表を読み飛ばしても本文の理解に支障はありません。

| 以前の 8 つ | この記事での番号 | 分類 |
|---|---|---|
| [Context Rot][c1] | 1 | ① 空間 |
| [Lost in the Middle][c2] | 2 | ① 空間 |
| [Priority Saturation][c3] | 3 | ① 空間 |
| [Hallucination][c5] | 5 | ② 信頼性 |
| [Sycophancy][c6] | 6 | ② 信頼性 |
| [Knowledge Boundary][c7] | 7 | ② 信頼性 |
| [Prompt Sensitivity][c11] | 11 | ③ 入力 |
| [Instruction Decay][c14] | 14 | ④ 時間 |

新しく加えたのは、4（Context Window Limit）、8（Miscalibration）、9（CoT Unfaithfulness）、10（Self-Evaluation Bias）、12（Non-determinism）、13（Tokenization Limits）、15（Compounding Errors）、16（Prompt Injection）、17（Reward Hacking）の 9 つです。

## 17 の制約と 5 つの分類

17 の制約を、「どこで・いつ現れるか」で 5 つに分けます。

| 分類 | 現れる場面 | 制約 |
|---|---|---|
| ① 空間 | 入力が増えるほど悪化する | [1. Context Rot][c1]、[2. Lost in the Middle][c2]、[3. Priority Saturation][c3]、[4. Context Window Limit][c4] |
| ② 信頼性 | 生成した内容が正しいか | [5. Hallucination][c5]、[6. Sycophancy][c6]、[7. Knowledge Boundary][c7]、[8. Miscalibration][c8]、[9. CoT Unfaithfulness][c9]、[10. Self-Evaluation Bias][c10] |
| ③ 入力 | 書き方と処理の単位に左右される | [11. Prompt Sensitivity][c11]、[12. Non-determinism][c12]、[13. Tokenization Limits][c13] |
| ④ 時間 | 会話や手順が長くなるほど悪化する | [14. Instruction Decay][c14]、[15. Compounding Errors][c15] |
| ⑤ 行為 | エージェントが外部のデータを読み、操作するとき | [16. Prompt Injection][c16]、[17. Reward Hacking][c17] |

表の制約の名前を選ぶと、その制約の説明に移動します。この表と、後半の「17 の制約 × Claude Code の機能」の表は、用語集の目次としても使えます。

①〜④ は、以前の 8 つの制約の分類（コンテキスト系・出力系・入力感受性系・時間軸系）を広げたものです。⑤ は今回新しく立てた分類です。Prompt Injection と Reward Hacking は、チャットで質問に答えるだけなら被害が小さく、エージェントがファイルを書き換えたりコマンドを実行したりするときに初めて大きな問題になります。

エージェントに特有の失敗としては、ほかに、存在しないツールや引数を使おうとする、計画した手順と実際の操作がずれる、といったものもあります。この記事では、これらを Hallucination（5）や Compounding Errors（15）がエージェントの操作の中で現れたものとして扱い、独立した制約としては数えていません。

```html-embed
<div class="c17">
  <div class="card sp"><div class="lb"><span class="ic"><svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 2 21 7v10l-9 5-9-5V7z"/><path d="M3 7l9 5 9-5M12 12v10"/></svg></span><span class="nm">① 空間<small>入力が増えるほど悪化する</small></span></div>
    <ol start="1"><li>Context Rot</li><li>Lost in the Middle</li><li>Priority Saturation</li><li>Context Window Limit</li></ol></div>
  <div class="card rl"><div class="lb"><span class="ic"><svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 2 4 5v6c0 5 3.5 9 8 11 4.5-2 8-6 8-11V5z"/><path d="m8.5 12 2.5 2.5 4.5-5"/></svg></span><span class="nm">② 信頼性<small>生成した内容が正しいか</small></span></div>
    <ol start="5"><li>Hallucination</li><li>Sycophancy</li><li>Knowledge Boundary</li><li>Miscalibration</li><li>CoT Unfaithfulness</li><li>Self-Evaluation Bias</li></ol></div>
  <div class="card ip"><div class="lb"><span class="ic"><svg viewBox="0 0 24 24" aria-hidden="true"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><path d="M14 2v6h6M8 13h8M8 17h5"/></svg></span><span class="nm">③ 入力<small>書き方と処理の単位に左右される</small></span></div>
    <ol start="11"><li>Prompt Sensitivity</li><li>Non-determinism</li><li>Tokenization Limits</li></ol></div>
  <div class="card tm"><div class="lb"><span class="ic"><svg viewBox="0 0 24 24" aria-hidden="true"><circle cx="12" cy="12" r="9"/><path d="M12 7v5l3 3"/></svg></span><span class="nm">④ 時間<small>会話や手順が長くなるほど悪化する</small></span></div>
    <ol start="14"><li>Instruction Decay</li><li>Compounding Errors</li></ol></div>
  <div class="card ac"><div class="lb"><span class="ic"><svg viewBox="0 0 24 24" aria-hidden="true"><path d="M10.3 3.9 1.8 18a2 2 0 0 0 1.7 3h17a2 2 0 0 0 1.7-3L13.7 3.9a2 2 0 0 0-3.4 0z"/><path d="M12 9v4M12 17h.01"/></svg></span><span class="nm">⑤ 行為<small>エージェントが外部を操作するとき</small></span></div>
    <ol start="16"><li>Prompt Injection</li><li>Reward Hacking</li></ol></div>
</div>
<style>
  .c17 { --bg:#ffffff; --fg:#1a1f36; --sub:#5a6178;
         display:flex; flex-direction:column; gap:8px; padding:12px; background:var(--bg); color:var(--fg);
         font-family:-apple-system, BlinkMacSystemFont, "Hiragino Sans", "Noto Sans JP", "Yu Gothic", sans-serif; }
  .sp { --c:#1d4fd8; --t:#f3f6fe; } .rl { --c:#c2570a; --t:#fff8f1; } .ip { --c:#1f7a37; --t:#f2faf4; }
  .tm { --c:#6d35c9; --t:#f8f4fe; } .ac { --c:#c62828; --t:#fef4f4; }
  [data-theme="dark"] .c17 { --bg:#1b1f2a; --fg:#e8eaf0; --sub:#a9afc0; }
  [data-theme="dark"] .sp { --c:#7aa2ff; --t:#1d2540; } [data-theme="dark"] .rl { --c:#ffa45c; --t:#33261a; }
  [data-theme="dark"] .ip { --c:#6fd28a; --t:#1a2e22; } [data-theme="dark"] .tm { --c:#b794ff; --t:#2a2140; }
  [data-theme="dark"] .ac { --c:#ff7b7b; --t:#3a1e22; }
  .card { display:flex; align-items:center; border:2px solid var(--c); background:var(--t); border-radius:10px; padding:7px 12px; }
  .lb { width:240px; flex:none; display:flex; align-items:center; gap:10px; padding-right:10px; border-right:2px dotted var(--c); align-self:stretch; }
  .ic { width:34px; height:34px; flex:none; border-radius:50%; border:2px solid var(--c); display:flex; align-items:center; justify-content:center; }
  .ic svg { width:18px; height:18px; fill:none; stroke:var(--c); stroke-width:2.2; stroke-linecap:round; stroke-linejoin:round; }
  .nm { font-size:16px; font-weight:800; color:var(--c); line-height:1.3; }
  .nm small { display:block; font-size:12px; font-weight:500; color:var(--sub); }
  ol { flex:1; margin:0; padding:0 0 0 14px; list-style:none; display:grid; grid-template-columns:1fr 1fr; gap:1px 10px; }
  li { font-size:13px; font-weight:600; line-height:1.55; white-space:nowrap; }
  li::before { content:counter(list-item) ". "; color:var(--c); font-weight:800; }
  @media (max-width: 640px) {
    .c17 { padding:8px; gap:6px; }
    .card { flex-direction:column; align-items:stretch; padding:6px 10px; }
    .lb { width:auto; border-right:none; border-bottom:2px dotted var(--c); padding:0 0 4px; gap:8px; }
    .ic { width:24px; height:24px; } .ic svg { width:13px; height:13px; }
    .nm { font-size:14px; display:flex; align-items:baseline; gap:6px; }
    .nm small { display:inline; font-size:11.5px; }
    ol { padding:4px 0 0; gap:0 8px; }
    li { font-size:12px; line-height:1.5; white-space:normal; }
  }
</style>
```

## 制約を生む原因

17 の制約は、それぞれ独立に生まれたものではありません。原因までさかのぼると、次の 9 つに整理できます。

| 原因 | 何が起きるか | 主に生む制約 |
|---|---|---|
| 自己注意の計算 | トークンが増えるほど、1 つのトークンに向く注意が薄まる | 1、3 |
| 位置の扱い（位置エンコーディング） | 入力の先頭と末尾に注意が偏る | 2 |
| 次のトークンを予測するという学習目標 | もっともらしい続きを出すことが目的になり、正しいかどうかは目的に含まれない | 5、15 |
| 強化学習による事後学習（RLHF など）と、評価の採点方法 | 同意する応答や、分からなくても答える応答、採点基準を満たすだけの応答が高く評価されやすい | 6、7、8、10、17 |
| 学習データの時点 | 学習データより後の出来事を知らない | 7 |
| トークン化 | 文字ではなくトークン単位で処理する | 13 |
| 推論時の計算とサンプリング | 同じ入力でも出力が変わる | 12 |
| データと指示が同じトークン列に入ること | 読み込んだ文書の中の命令文を、利用者の指示と区別できない | 16 |
| ウィンドウの長さの上限 | 決まった長さを超える入力を受け取れない | 4 |

9 の CoT Unfaithfulnessと 11 の Prompt Sensitivity は、原因の説明がまだ研究で定まっていないため、表に入れていません。

ここから分かるのは、これらの制約は「モデルが新しくなれば消える不具合」ではなく、「今の LLM の作り方から必ず出てくる性質」だということです。程度は新しいモデルで小さくなることがありますが、ゼロにはなりません。そのため、モデルの外側（ハーネス）で抑える必要があります。

## 17 の制約の説明

### ① 空間 — 入力が増えるほど悪化する

#### 1. Context Rot（入力が長いほど品質が下がる）

入力のトークン数が増えるほど、出力の品質が下がる現象です。エラーにならずに品質だけが下がるため、気づきにくいという特徴があります。Chroma の技術レポート（Hong ほか, 2025）は、18 のモデルを評価し、入力が長くなるにつれて性能が均一ではない形で不安定になることを示しました。課題の内容は同じで、関係のない文章を増やしただけでも性能が下がります。

#### 2. Lost in the Middle（中間の情報が使われにくい）

長い入力の中間にある情報が使われにくくなる現象です。Liu ほか（2024）は、必要な情報が入力の先頭か末尾にあるときに性能が高く、中間にあるときに低くなる U 字型の傾向を報告しました。

#### 3. Priority Saturation（指示が多いほど守られにくい）

同時に与える指示の数が増えるほど、1 つ 1 つの指示が守られにくくなる現象です。Jaroslawicz ほか（2025）の IFScale は、同時に与える指示を 10 個から 500 個まで増やし、モデルごとに遵守率が下がっていく様子を測っています。CLAUDE.md に規則を書き足し続けると起きるのは、この現象です。

#### 4. Context Window Limit（一度に受け取れる長さの上限）

モデルが一度に受け取れる長さには上限があり、それを超える入力は受け取れません。Context Rot は上限より手前で品質が下がる問題で、こちらは上限そのものの問題です。Claude Code では、Fable 系、Sonnet 5 以降、Haiku 5.5、Opus 4.6 以降、Sonnet 4.6 が 100 万トークンのコンテキストウィンドウに対応しています（[公式ドキュメント「Explore the context window」](https://code.claude.com/docs/en/context-window)による）。ただし、使えるかどうかは契約しているプランによって異なり、モデルによっては名前に `[1m]` の付いた版を選ぶ必要があります。また、ウィンドウが大きくなっても Context Rot は残ります。

### ② 信頼性 — 生成した内容が正しいか

#### 5. Hallucination（事実と異なる内容を生成する）

事実と異なる内容を、もっともらしく生成する現象です。Kalai ほか（2025）は、学習と評価の手順が「分からないと答えること」より「推測して答えること」を高く評価するため、Hallucination が残り続けると論じています。Xu ほか（2024）は、Hallucination を完全になくすことはできないことを理論的に示しています。

#### 6. Sycophancy（利用者に同調して正確さを損なう）

利用者の意見や思い込みに合わせて、正確さを犠牲にする現象です。Sharma ほか（2023）は、人の評価データの分析から、利用者の見方に一致する応答のほうが好まれやすいことを示しました。人の評価を使って学習する限り、この傾向が学習に組み込まれます。コードレビューを同じモデルに頼んだとき、「問題ありません」と返ってきやすいのはこのためです。

#### 7. Knowledge Boundary（知らないことを知らないと言えない）

自分の知識の範囲外の質問に対して、「分からない」と答えにくい現象です。Pan ほか（2025）は、知らない質問を断れるかどうかを 16 のモデルで測定しています。学習データの時点（Knowledge Cutoff）もここに含めます。Cheng ほか（2024）は、モデルが実際に知っている情報の時点が、公表されているカットオフの時点と大きくずれる場合があることを報告しています。

#### 8. Miscalibration（確信度と正しさが一致しない）

答えが正しいときも間違っているときも、同じように自信のある口調で答える現象です。Kadavath ほか（2022）は、大きなモデルは適切な形式の多肢選択問題では確信度と正答率がよく対応する（較正されている）ことを示しました。一方で、GPT-4 Technical Report（OpenAI, 2023）は、事前学習後のモデルはよく較正されているものの、事後学習（RLHF など）を経ると較正が大きく悪くなることを報告しています。利用者が触れるのは事後学習を経たモデルなので、「自信がありそうな口調」を正しさの手がかりにはできません。

#### 9. CoT Unfaithfulness（思考過程が実際の判断と一致しない）

モデルが表示する思考過程（Chain-of-Thought）が、実際に答えを決めた過程と一致しないことがある現象です。Chen ほか（2025）は、問題文にヒントを埋め込み、モデルがヒントを使って答えたときに、思考過程の中でヒントを使ったと書くかを調べました。書いた割合は、多くの場合 20% を下回っていました。「理由を説明させれば正しさを確かめられる」とは限りません。実務では、エージェントが書いた変更の説明（「〜のため修正しました」）を、変更が正しいことの根拠として扱わないようにします。

#### 10. Self-Evaluation Bias（自分の誤りを自分で見つけにくい）

自分の出力の誤りを、自分で見つけて直すのが苦手な現象です。Huang ほか（2024）は、外部からのフィードバックなしにモデル自身に推論を見直させると、性能が上がらず、下がる場合もあることを示しました。Panickssery ほか（2024）は、モデルが自分の生成した文章を見分けられ、人が同じ品質と判断した文章より自分の文章を高く評価する傾向があることを報告しています。

### ③ 入力 — 書き方と処理の単位に左右される

#### 11. Prompt Sensitivity（書き方で結果が変わる）

意味が同じでも、書き方によって結果が大きく変わる現象です。Sclar ほか（2023）は、few-shot の例の区切り記号や大文字小文字といった書式を変えるだけで、LLaMA-2-13B の正答率が最大 76 ポイント変わることを示しました。ただし Hua ほか（2025）は、観測される感度の多くは評価方法が厳しすぎることによるもので、適切に評価すれば報告より頑健だと論じています。どの程度の影響があるかは、課題と評価方法によって大きく変わります。

#### 12. Non-determinism（同じ入力でも出力が変わる）

同じ入力でも出力が変わる現象です。サンプリングの温度を 0 にしても、同じ出力になるとは限りません。Thinking Machines Lab の He（2025）は、その主な原因として、推論サーバーが複数の利用者の要求をまとめて処理する際のバッチの大きさによって、計算結果がわずかに変わることを挙げています。利用者の側からは、この変動を止められません。

#### 13. Tokenization Limits（文字単位の処理を誤る）

LLM は文字ではなくトークン（数文字のまとまり）を単位に処理するため、文字数を数える、文字の位置を答える、文字列を逆順にするといった処理を間違えることがあります。ただし、原因をトークン化だけに求めることには異論もあります。Uzan と Pinter（2025）の CharBench は、文字を数える課題では、トークンの分かれ方より、単語の長さや実際の文字数のほうが正答率と強く関係していると報告しています。原因がどちらであっても、実務での扱いは同じです。文字数に上限のある文章（タイトルや説明文など）の作成や、桁の多い計算をモデルの出力だけで済ませると、誤りが混じることがあります。

### ④ 時間 — 会話や手順が長くなるほど悪化する

#### 14. Instruction Decay（長い会話で最初の指示が守られなくなる）

長いマルチターンの会話の中で、最初に与えた指示が守られなくなっていく現象です。Laban ほか（2025）は、20 万件以上の会話のシミュレーションで、課題の情報を 1 回でまとめて与えた場合に比べ、複数のターンに分けて少しずつ与えた場合の性能が平均 39% 下がることを示しました。

#### 15. Compounding Errors（手順が多いほど誤りが積み重なる）

手順が多い作業では、各ステップの小さな誤りが積み重なり、作業全体が失敗しやすくなる現象です。Sinha ほか（2025）は、長い作業では手順が進むにつれて 1 ステップあたりの正答率が下がることを示しました。その理由の 1 つとして、それまでの自分の誤りがコンテキストに含まれていると、次のステップでも誤りやすくなること（self-conditioning）を挙げています。この傾向はモデルを大きくしても減らず、思考（thinking）を使うと抑えられると報告されています。

### ⑤ 行為 — エージェントが外部のデータを読み、操作するとき

#### 16. Prompt Injection（読み込んだデータの命令文を実行してしまう）

エージェントが読み込んだ Web ページ、文書、ツールの応答の中に命令文が含まれていると、それを利用者の指示と区別できずに実行してしまう現象です。Greshake ほか（2023）は、攻撃者が LLM に直接入力しなくても、LLM が後で読み込むデータに命令文を仕込むことで、アプリケーションを操作できること（間接 Prompt Injection）を示しました。データと指示が同じトークン列に入るという構造から来ているため、モデルの改良だけでは防ぎきれません。モデル側の対策としては、システムの指示・利用者の入力・ツールの出力の間に優先順位を付けて学習させる Instruction Hierarchy（Wallace ほか, 2024）があります。ただし、これで命令文の混入を完全に防げるわけではないため、ハーネスの側でも対策します。

#### 17. Reward Hacking（目的ではなく評価基準を満たそうとする）

作業の目的ではなく、評価の基準を満たすことを目的にしてしまう現象です。コーディングでは、テストを書き換える、特定のテストケースだけを通すコードを書く、といった形で現れます。テストそのものではなく、テストや lint の設定、実行スクリプトを変えて判定を通す形もあります。また、ツールが返した実際の結果と違う内容（「テストはすべて通りました」など）を報告する形もあり、こちらは Hallucination とも重なります。METR（2025）は、最近のモデルがテストや採点コードを書き換えて高い得点を得ようとした事例を報告しています。MacDiarmid ほか（2025）は、学習中に Reward Hacking を覚えたモデルで、他の望ましくない振る舞いも同時に増えたことを報告しています。

## 制約同士の連鎖

これらの制約は、互いに悪化させ合います。

```mermaid
flowchart LR
  subgraph S1["空間の連鎖"]
    a1["Context Rot"] --> a2["Lost in the Middle"] --> a3["Priority Saturation"]
  end
  subgraph S2["信頼性の連鎖"]
    b3["Knowledge Boundary"] --> b1["Hallucination"]
    b4["Miscalibration"] --> b1
    b1 <--> b2["Sycophancy"]
    b6["Self-Evaluation Bias"] --> b2
  end
  subgraph S3["時間の連鎖"]
    d1["Instruction Decay"]
    d2["Compounding Errors"]
  end
  S1 --> S3
  S2 --> S3
```

- **空間の連鎖**: 入力が増えると注意が薄まり（Context Rot）、中間の情報が使われにくくなり（Lost in the Middle）、指示の遵守率が下がります（Priority Saturation）。
- **信頼性の連鎖**: 知らないことを知らないと言えず（Knowledge Boundary）、自信のある口調のまま（Miscalibration）、誤った内容を生成します（Hallucination）。利用者がそれを信じると、モデルは同調し（Sycophancy）、誤りが固定されます。モデル自身に見直させても、誤りは見つかりにくいままです（Self-Evaluation Bias）。
- **時間の連鎖**: 会話や手順が長くなると、空間と信頼性の問題が積み重なり、Instruction Decay と Compounding Errors として現れます。

③ 入力と ⑤ 行為の制約は、この図に入れていません。Prompt Injection は読み込んだデータから、Reward Hacking は評価の基準から起きるもので、入力の量や会話の長さで悪化するという関係を、この記事では根拠を確認できていないためです。この 2 つは、エージェントがファイルやコマンドを操作するたびに、連鎖とは別に起こりうるものとして扱います。

## ハーネスで対処する

### ハーネスの 4 要素

ハーネスは、LLM の外側に置いて LLM の動作を補う仕組みの総称です。この記事では、次の 4 つの要素に分けて考えます。

| 要素 | 役割 |
|---|---|
| Action（ツール連携） | 外部の API・データベース・ファイル・ブラウザーなどにつなぐ |
| Context（記憶） | 過去の経緯や作業履歴を保持し、必要なときに LLM に渡す |
| Guardrails（安全制御） | 誤った出力や危険な操作を、LLM の外側で検出して止める |
| Orchestration（ループ制御） | 作業を分解し、実行・評価・次の行動の決定を繰り返す |

### 5 つの分類 × 4 つの要素

● は直接効くもの、△ は部分的に効くもの、― は効かないものです。

| 分類 | Action | Context | Guardrails | Orchestration |
|---|---|---|---|---|
| ① 空間 | ― | ● 履歴を外に出し、渡す量を絞る | ― | ● 作業を分けて、コンテキストを分離する |
| ② 信頼性 | ● 外部の情報源で確かめる | △ | ● テスト・型検査・別モデルで検証する | △ 別のエージェントにレビューさせる |
| ③ 入力 | ● 計算や文字数はコードで処理する | △ 決まった手順書を使う | △ 結果を検証で判定する | ― |
| ④ 時間 | ― | ● 要約と成果物のファイル化 | ● 各ステップで検証する | ● 指示を再び渡し、作業を短く分ける |
| ⑤ 行為 | △ 読み込むデータの経路を限る | ― | ● 権限・サンドボックス・人の承認 | △ 危険な操作の前で止める |

### 対策の原則: LLM 自身に検証させない

表の中で最も多くの分類に効くのは Guardrails です。その中心にある原則は、「LLM の出力を、LLM 自身に検証させない」ことです。

Self-Evaluation Bias、Sycophancy、Miscalibration、CoT Unfaithfulnessの 4 つはどれも、「LLM に確認させれば誤りが分かる」という前提を崩します。そのため、検証にはコンパイラ、テスト、型検査のように、LLM の出力に同調しない仕組みを使います。LLM でレビューする場合も、作業したセッションとは別のコンテキストで、できれば別のモデルにレビューさせます。

### ハーネスで対処しきれないもの

ハーネスで制約が消えるわけではありません。次のものは、ハーネスを組んでも残ります。

| 制約 | 残る理由 |
|---|---|
| [Hallucination][c5] | 外部の検証で誤りを止めることはできますが、生成そのものをなくすことはできません。検証できない種類の誤り（テストのない部分、文章の内容など）は残ります |
| [Sycophancy][c6] | 別モデルでのレビューで抑えられますが、レビューするモデルにも同じ傾向があります |
| [Prompt Sensitivity][c11] | 直接の処方はありません。書き方を固定して、ぶれを小さくすることしかできません |
| [Non-determinism][c12] | 推論サーバー側の問題のため、利用者の側からは止められません。出力が変わる前提で、結果を検証で判定します |
| [Prompt Injection][c16] | 権限とサンドボックスで被害を限ることはできますが、命令文の混入そのものは防げません |
| [Reward Hacking][c17] | テストの書き換えは止められても、テストが確認していない入力でだけ誤る実装を機械的に見つけることは難しく、人によるレビューが残ります |

そのため、どの制約についても、最終的な受け入れの判断は、テストの結果と、人による差分の確認を組み合わせて行います。

また、Context（記憶）の設計を誤ると、過去の履歴をすべて読み戻すことで、かえって Context Rot を悪化させます。記憶は「全部渡す」ものではなく、「必要なものだけ選んで渡す」ものです。

## Claude Code での実装（2026-11-11 時点）

ここからは、ハーネスの 4 要素を Claude Code の機能に当てはめます。この章の内容は製品の更新で古くなります。

### 指示として読ませる機能と、LLM の外側で動く機能

Claude Code の機能は、どれだけ確実に効くかで 2 つに分かれます。

```html-embed
<div class="cmp">
  <div class="col ins">
    <div class="hd"><span class="ic"><svg viewBox="0 0 24 24" aria-hidden="true"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><path d="M14 2v6h6M8 13h8M8 17h5"/></svg></span><span class="tt">指示として LLM に<br class="pc">読ませる機能</span></div>
    <div class="chips"><span>CLAUDE.md</span><span>.claude/rules/</span><span>Skills</span></div>
    <p class="key">LLM が読んで従う必要がある</p>
    <ul>
      <li><b class="mk">!</b>入力が増えたり会話が長くなったりすると、守られにくくなる</li>
    </ul>
  </div>
  <div class="vs" aria-hidden="true">VS</div>
  <div class="col out">
    <div class="hd"><span class="ic"><svg viewBox="0 0 24 24" aria-hidden="true"><circle cx="12" cy="12" r="3"/><path d="M12 2v3M12 19v3M2 12h3M19 12h3M4.9 4.9l2.1 2.1M17 17l2.1 2.1M4.9 19.1 7 17M17 7l2.1-2.1"/></svg></span><span class="tt">LLM の外側で<br class="pc">動く機能</span></div>
    <div class="chips"><span>Hooks</span><span>権限ルール</span><span>サンドボックス</span></div>
    <p class="key">LLM の判断に頼らずに動く</p>
    <ul>
      <li><b class="mk">✓</b>会話が長くなっても効き方は同じ</li>
      <li><b class="mk">✓</b>必ず守らせたいことはこちらに置く</li>
    </ul>
  </div>
</div>
<style>
  .cmp { --bg:#ffffff; --fg:#1a1f36; --sub:#5a6178; --line:#d6d9e0; --chip:#eef0f4;
         --ins:#1a1f36; --out:#1d4fd8; --outbg:#f3f6fe;
         display:flex; align-items:stretch; gap:12px; padding:14px; background:var(--bg); color:var(--fg);
         font-family: -apple-system, BlinkMacSystemFont, "Hiragino Sans", "Noto Sans JP", "Yu Gothic", sans-serif; }
  [data-theme="dark"] .cmp { --bg:#1b1f2a; --fg:#e8eaf0; --sub:#a9afc0; --line:#3a4052; --chip:#2a3040;
         --ins:#c9cedb; --out:#7aa2ff; --outbg:#1f2740; }
  .col { flex:1; border:2px solid var(--line); border-radius:12px; padding:14px 16px; }
  .out { border-color:var(--out); background:var(--outbg); }
  .hd { display:flex; align-items:center; gap:10px; padding-bottom:10px; border-bottom:2px solid currentColor; }
  .ins .hd { color:var(--ins); } .out .hd { color:var(--out); }
  .ic { width:34px; height:34px; border-radius:50%; background:currentColor; display:flex; align-items:center; justify-content:center; flex:none; }
  .ic svg { width:19px; height:19px; fill:none; stroke:var(--bg); stroke-width:2.2; stroke-linecap:round; stroke-linejoin:round; }
  .tt { font-size:16px; font-weight:800; line-height:1.35; color:var(--fg); }
  .chips { display:flex; flex-wrap:wrap; gap:6px; margin:12px 0 10px; }
  .chips span { font-size:14px; font-weight:700; padding:4px 10px; border-radius:999px; background:var(--chip); }
  .out .chips span { background:var(--bg); border:1px solid var(--out); }
  .key { margin:0 0 6px; font-size:15px; font-weight:800; }
  ul { list-style:none; margin:0; padding:0; }
  li { display:flex; gap:8px; font-size:14px; line-height:1.55; color:var(--sub); margin-top:4px; }
  .mk { flex:none; width:18px; height:18px; margin-top:2px; border-radius:50%; font-size:12px; color:var(--bg);
        display:flex; align-items:center; justify-content:center; }
  .ins .mk { background:var(--ins); } .out .mk { background:var(--out); }
  .vs { align-self:center; flex:none; width:38px; height:38px; border-radius:50%; border:2px solid var(--line);
        display:flex; align-items:center; justify-content:center; font-size:13px; font-weight:800; color:var(--sub); background:var(--bg); }
  @media (max-width: 640px) {
    .pc { display:none; }
    .cmp { flex-direction:column; gap:8px; padding:10px; }
    .vs { width:32px; height:32px; font-size:12px; }
    .col { padding:10px 12px; }
    .hd { padding-bottom:8px; }
    .chips { margin:8px 0 6px; }
    .ic { width:30px; height:30px; }
  }
</style>
```

:::details 表で見る
| 種類 | 機能 | 確実さ |
|---|---|---|
| 指示として LLM に読ませる機能 | CLAUDE.md、`.claude/rules/`、Skills | LLM が読んで従う必要があるため、① 空間と ④ 時間の制約を受けます。守られない場合があります |
| LLM の外側で動く機能 | Hooks、権限ルール、サンドボックス | LLM の判断に頼らずに動くため、会話が長くなっても効き方が変わりません |
:::

:::message alert
**必ず守らせたいことは、LLM の外側で動く機能（Hooks・権限ルール・サンドボックス）に置くのが基本です。**
:::

以下の表も、この区別を前提に読んでください。

### ハーネスの 4 要素ごとの機能

Claude Code の主な機能を、ハーネスの 4 要素に分けます。2 つの要素にまたがる機能は、主な役割のほうに置き、もう一方の役割を説明に書いています。

:::message
この節で扱うのは、Claude Code のセッションの中で LLM の周りに置く仕組みです。CI/CD（GitHub Actions など）での lint やテスト、ブランチ保護、プルリクエストの承認は、セッションの外で動く仕組みなので、この表には含めていません。役割としては同じ Guardrails にあたりますが、エージェントが作業を終えた後に、エージェントが書き換えられない場所で判定します。Claude Code の Hooks・権限ルール・サンドボックスと組み合わせて使うものです。両者の違いは「[セッションの中の仕組みと開発の工程の仕組み](#セッションの中の仕組みと開発の工程の仕組み)」で補足しています。
:::

#### Action（ツール連携）

| 機能 | 何をするか |
|---|---|
| MCP | 外部のツールやサービスにつなぐ。ライブラリーの最新の仕様など、学習データにない情報を取りに行くときにも使う |
| Bash・WebFetch などの組み込みツール | コマンドの実行や Web ページの取得を行う。文字数や計算のように LLM が誤りやすい処理を、コードの実行に任せられる |

#### Context（記憶）

| 機能 | 何をするか |
|---|---|
| CLAUDE.md | 毎回のセッションの開始時に読み込まれる指示ファイル。公式ドキュメントは 200 行以内を目安としています。プロジェクト直下の CLAUDE.md は、コンパクション後もディスクから読み直されます |
| `.claude/rules/` | 話題ごとに分けた指示ファイル。`paths` を指定すると、一致するファイルを扱うときだけ読み込まれます |
| Skills | 起動時には説明文だけが読み込まれ、必要になったときに本文が読み込まれる手順書。繰り返す作業の手順を固定する点では、Orchestration の役割も持ちます |
| `/compact`・`/clear`・`/autocompact`・`/context` | 会話の要約、会話の消去、自動コンパクションを始める量の設定、コンテキストの使用量の確認 |

#### Guardrails（安全制御）

| 機能 | 何をするか |
|---|---|
| Hooks | `PreToolUse`（ツール実行前、止められる）、`PostToolUse`（ツール実行後）、`Stop`（応答の終了時、終了を止めて作業を続けさせられる）などのタイミングで、コマンドを実行する。Hooks のコマンドはコンテキストの外で実行され、LLM に返す出力だけがコンテキストに入ります |
| 権限ルール・権限モード | ツールの呼び出しを allow / ask / deny で制御する。plan モードでは、計画を承認するまで編集を止める |
| サンドボックス | Bash などのシェルコマンドについて、OS のレベルでファイルとネットワークへのアクセスを制限する |

#### Orchestration（ループ制御）

| 機能 | 何をするか |
|---|---|
| サブエージェント | 独立したコンテキストウィンドウで動き、要約だけを返す。使えるツールや使うモデルを個別に指定できる。作業したセッションとは別のコンテキストでレビューさせる点では、Guardrails の役割も持ちます |
| `/rewind`・Git のコミット | 作業を前の状態に戻す。作業を小さく分けて、ステップごとに確定させる |

### 17 の制約 × Claude Code の機能

| 制約 | Claude Code での対策 |
|---|---|
| [1. Context Rot][c1] | 関係のない作業に移るときは `/clear` を実行する。長い作業の途中で `/compact` を実行し、要約に残す内容を指示する（例: `/compact focus on the auth bug fix`）。多くのファイルを読む調査はサブエージェントに任せ、要約だけを受け取る |
| [2. Lost in the Middle][c2] | 毎回守らせたい指示は CLAUDE.md に書く（コンパクション後も読み直される）。Skills の本文は、コンパクション後に上限を超えると末尾が切り詰められ、先頭が残るため、重要な指示を SKILL.md の先頭に書く |
| [3. Priority Saturation][c3] | CLAUDE.md を 200 行以内に保ち、特定のファイルにだけ関係する規則は `paths` 付きの rules に、ときどき必要な手順は Skills に移す。必ず実行させたい処理は、指示ではなく Hooks にする |
| [4. Context Window Limit][c4] | 自動コンパクションに任せるか、`/autocompact` で早めに要約させる。必要なら 100 万トークン対応のモデルを選ぶ。それでも Context Rot は残るため、1 の対策と組み合わせる |
| [5. Hallucination][c5] | `PostToolUse` や `Stop` の Hooks で、テスト・型検査・lint を実行し、失敗したら作業を続けさせる。ライブラリーの仕様は MCP や Web の公式ドキュメントで確かめさせる |
| [6. Sycophancy][c6] | レビュー専用のサブエージェントを作り、作業したセッションとは別のコンテキストでレビューさせる。CLAUDE.md に「根拠がなければ同意しない」と書くことは補助的な対策にとどまる |
| [7. Knowledge Boundary][c7] | 使っているライブラリーのバージョンを CLAUDE.md に明記する。最新の仕様は MCP や WebFetch で取得させる |
| [8. Miscalibration][c8] | Claude の「完了しました」という報告ではなく、テストの結果やコマンドの出力で完了を判断する。`Stop` の Hooks で、テストが通るまで終了させない |
| [9. CoT Unfaithfulness][c9] | 説明ではなく、差分とテスト結果で判断する。思考過程は、問題の手がかりとしては使えるが、正しさの証明にはならない |
| [10. Self-Evaluation Bias][c10] | レビューは別のサブエージェントで行う。サブエージェントは親の会話履歴を受け取らないため、作業中のやり取りを前提にせずにレビューできる。サブエージェントの `model` フィールドで、作業したモデルとは別のモデルも指定できる |
| [11. Prompt Sensitivity][c11] | 繰り返す作業は Skills やカスタムコマンドにし、毎回同じ文面で指示する。Skills の `description` の書き方で、読み込まれるかどうかが変わる点にも注意する |
| [12. Non-determinism][c12] | 同じ出力が返る前提で手順を組まない。受け入れの判定はテストで行う |
| [13. Tokenization Limits][c13] | 文字数・計算・並べ替えは、Bash でコードを実行させて求める |
| [14. Instruction Decay][c14] | 作業の区切りごとに `/clear` を実行し、新しいセッションで始める。守らせたい規則は会話ではなく CLAUDE.md に書く（会話で伝えた指示はコンパクションで要約される）。Hooks は会話の長さに影響されない |
| [15. Compounding Errors][c15] | 作業を小さく分け、ステップごとにテストを実行してコミットする。誤りに気づいたら、誤りを含む会話を続けず、`/rewind` で戻すか新しいセッションでやり直す |
| [16. Prompt Injection][c16] | 権限ルールの deny と ask で、危険なコマンドや外部への送信を止める。サンドボックスで、ネットワークの接続先を許可したドメインだけに限る。信頼できないリポジトリーでは、その `.mcp.json` や Hooks がどう扱われるかを確認してから開く |
| [17. Reward Hacking][c17] | 権限ルールの deny や ask、`PreToolUse` の Hooks で、判定に関わるファイルの書き換えを止める。対象の例は、テストのディレクトリー、テストや lint の設定ファイル、`package.json` のスクリプト、CI の設定、Hooks のスクリプト、`.claude/settings.json`。Hooks のスクリプトや設定を書き換えられると、Hooks による検証そのものが効かなくなる。完了の判定は Claude の報告ではなくテストの実際の出力で行い、これらのファイルを変更した差分は人が確認する |

### 機能の組み合わせ方

```mermaid
flowchart LR
  U["利用者の指示"] --> M["メインのセッション"]
  M -->|"毎回読み込む"| C["CLAUDE.md<br/>(200 行以内)"]
  M -->|"該当ファイルのときだけ"| R[".claude/rules/"]
  M -->|"必要なときだけ"| S["Skills"]
  M -->|"調査・レビューを任せる"| A["サブエージェント<br/>(別コンテキスト・別モデル)"]
  M -->|"ツール呼び出し"| T["Bash・Edit・MCP"]
  T --> P["権限ルール・サンドボックス"]
  T --> H["Hooks<br/>(テスト・型検査・書き換え禁止)"]
  H -->|"失敗を返す"| M
```

この図で、LLM の外側で動くのは権限ルール、サンドボックス、Hooks です。② 信頼性と ⑤ 行為の制約には、この 3 つが最も確実に効きます。

### セッションの中の仕組みと開発の工程の仕組み

ここまでの表と図は、Claude Code のセッションの中の仕組みだけを扱っています。実際の開発では、エージェントが作業を終えてプッシュした後にも、CI/CD やレビューといった開発の工程の仕組みが判定を行います。どちらも LLM の出力を LLM の外側で確かめる点で Guardrails の役割を持ちますが、動く場所が違います。

```mermaid
flowchart LR
  subgraph IN["エージェントのセッションの中（この記事の表の範囲）"]
    L["LLM"] <--> H["Claude Code の仕組み<br/>Hooks・権限ルール・サンドボックス<br/>サブエージェント・/rewind・コミット"]
  end
  IN -->|"プッシュ・プルリクエスト"| OUT
  subgraph OUT["開発の工程（セッションの外）"]
    C["CI/CD の lint・テスト"] --> B["ブランチ保護・レビューと承認"] --> M["マージ・デプロイ"]
  end
```

| 観点 | セッションの中の仕組み | 開発の工程の仕組み（セッションの外） |
|---|---|---|
| 例 | Hooks、権限ルール、Claude Code のサンドボックス | GitHub Actions などの CI/CD、ブランチ保護、レビューと承認、CI 環境の隔離 |
| 動くタイミング | エージェントが作業している間 | エージェントが作業を終えて手放した後 |
| 設定する人と場所 | エージェントを使う人が、Claude Code の設定で | リポジトリーの管理者が、GitHub などの側で |
| エージェントが書き換えられるか | 設定ファイルを守らないと書き換えられる（17. Reward Hacking の対策を参照） | ブランチ保護をかけていれば、エージェントからは変えられない |

両方を組み合わせる理由は、最後の行にあります。セッションの中の仕組みは、エージェントが設定やテストを書き換えると効かなくなる可能性があります。開発の工程の仕組みは、エージェントが変更できない場所で判定するため、Reward Hacking や Miscalibration（「完了しました」という報告）に対する最後の確認になります。

なお、Git のコミットと `/rewind` を Orchestration の表に入れているのは、エージェント自身がセッションの中で実行し、作業を区切ったり戻したりするために使うからです。同じ Git に関係する仕組みでも、GitHub 側のブランチ保護やプルリクエストの承認は、開発の工程の仕組みにあたります。

機能名や既定の挙動は変わることがあるため、使う前に [Claude Code の公式ドキュメント](https://code.claude.com/docs) で確認してください。

## どこから手をつけるか

17 の制約すべてに一度に対処する必要はありません。導入の手間と効き方の確実さから、次の順で進めるのが現実的です。この順序は筆者の判断で、研究で被害の大きさの順位が決まっているわけではありません。

| 順序 | 内容 | 主に効く制約 |
|---|---|---|
| 1. 設定で確実に止める | 権限ルールで危険なコマンドとテストファイルの書き換えを止め、サンドボックスで接続先を限る。`Stop` の Hooks でテストを実行する | 5、8、16、17 |
| 2. 使い方の習慣で抑える | 作業ごとに `/clear` を実行する。CLAUDE.md を短く保つ。作業を小さく分けてコミットする | 1、3、14、15 |
| 3. 構成で補う | レビュー専用のサブエージェントを作る。繰り返す作業を Skills にする。仕様は MCP や公式ドキュメントで確かめさせる | 6、7、10、11 |
| 4. 人が確認する | 1〜3 を組んでも残る制約について、差分とテスト結果を人が確認する | 5、6、11、12、16、17 |

## まとめ

- LLM の制約は、利用者から見た失敗の単位で数えると 17 個あり、「空間」「信頼性」「入力」「時間」「行為」の 5 つに分けられます。
- 17 の制約の多くは、自己注意の計算、位置の扱い、次のトークンの予測、人の評価による学習、トークン化といった、今の LLM の作り方から来ています。モデルが新しくなっても、程度が小さくなることはあっても、なくなりはしません。
- ハーネスの 4 要素（Action・Context・Guardrails・Orchestration）のうち、最も多くの分類に効くのは Guardrails です。中心にある原則は「LLM の出力を LLM 自身に検証させない」ことです。
- Claude Code では、指示として読ませる機能（CLAUDE.md、rules、Skills）と、LLM の外側で動く機能（Hooks、権限ルール、サンドボックス）を分けて考えます。必ず守らせたいことは後者に置きます。
- 手をつける順序は、設定で確実に止められるもの、使い方の習慣で抑えられるもの、構成で補うもの、人が確認するもの、の順です。
- Prompt Sensitivity、Non-determinism、Hallucination、Sycophancy、Prompt Injection、Reward Hacking は、ハーネスを組んでも残ります。この部分は、人による確認を前提に作業を設計します。

:::message
ハーネスや設定は、LLM の制約があるから存在します。どの制約がどこで現れるかを知っておけば、LLM がどこで失敗するかを事前に予測し、確実に効く場所に対策を置けます。防げない失敗がどこに残るかを把握して、そこを人が確認するところまで含めて、LLM を使いこなすということだと考えています。
:::

:::message alert
LLM に読ませる指示（CLAUDE.md、rules、Skills）は、守られないことがあります。必ず守らせたいことは、Hooks・権限ルール・サンドボックスに置いてください。
:::

## 参考文献

### ① 空間

- Hong, K., Troynikov, A., & Huber, J. (2025). *Context Rot: How Increasing Input Tokens Impacts LLM Performance*. Chroma Technical Report. https://research.trychroma.com/context-rot
- Liu, N. F., Lin, K., Hewitt, J., Paranjape, A., Bevilacqua, M., Petroni, F., & Liang, P. (2024). Lost in the Middle: How Language Models Use Long Contexts. *Transactions of the Association for Computational Linguistics*, 12, 157–173. https://arxiv.org/abs/2307.03172
- Jaroslawicz, D., Whiting, B., Shah, P., & Maamari, K. (2025). How Many Instructions Can LLMs Follow at Once? https://arxiv.org/abs/2507.11538

### ② 信頼性

- Kalai, A. T., Nachum, O., Vempala, S. S., & Zhang, E. (2025). Why Language Models Hallucinate. https://arxiv.org/abs/2509.04664
- Xu, Z., Jain, S., & Kankanhalli, M. (2024). Hallucination is Inevitable: An Innate Limitation of Large Language Models. https://arxiv.org/abs/2401.11817
- Sharma, M., Tong, M., Korbak, T., et al. (2023). Towards Understanding Sycophancy in Language Models. https://arxiv.org/abs/2310.13548
- Pan et al. (2025). Can LLMs Refuse Questions They Do Not Know? Measuring Knowledge-Aware Refusal in Factual Tasks. https://arxiv.org/abs/2510.01782
- Cheng, J., Marone, M., Weller, O., Lawrie, D., Khashabi, D., & Van Durme, B. (2024). Dated Data: Tracing Knowledge Cutoffs in Large Language Models. COLM 2024. https://arxiv.org/abs/2403.12958
- Kadavath, S., Conerly, T., Askell, A., et al. (2022). Language Models (Mostly) Know What They Know. https://arxiv.org/abs/2207.05221
- OpenAI. (2023). GPT-4 Technical Report. https://arxiv.org/abs/2303.08774
- Chen, Y., Benton, J., Radhakrishnan, A., et al. (2025). Reasoning Models Don't Always Say What They Think. Anthropic. https://arxiv.org/abs/2505.05410
- Huang, J., Chen, X., Mishra, S., Zheng, H. S., Yu, A. W., Song, X., & Zhou, D. (2024). Large Language Models Cannot Self-Correct Reasoning Yet. ICLR 2024. https://arxiv.org/abs/2310.01798
- Panickssery, A., Bowman, S. R., & Feng, S. (2024). LLM Evaluators Recognize and Favor Their Own Generations. NeurIPS 2024. https://arxiv.org/abs/2404.13076

### ③ 入力

- Sclar, M., Choi, Y., Tsvetkov, Y., & Suhr, A. (2023). Quantifying Language Models' Sensitivity to Spurious Features in Prompt Design. https://arxiv.org/abs/2310.11324
- Hua, A., Tang, K., Gu, C., Gu, J., Wong, E., & Qin, Y. (2025). Flaw or Artifact? Rethinking Prompt Sensitivity in Evaluating LLMs. EMNLP 2025. https://arxiv.org/abs/2509.01790
- He, H., & Thinking Machines Lab. (2025). Defeating Nondeterminism in LLM Inference. *Connectionism*. https://thinkingmachines.ai/blog/defeating-nondeterminism-in-llm-inference/
- Uzan, O., & Pinter, Y. (2025). CharBench: Evaluating the Role of Tokenization in Character-Level Tasks. https://arxiv.org/abs/2508.02591

### ④ 時間

- Laban, P., Hayashi, H., Zhou, Y., & Neville, J. (2025). LLMs Get Lost In Multi-Turn Conversation. https://arxiv.org/abs/2505.06120
- Sinha, A., Arun, A., Goel, S., Staab, S., & Geiping, J. (2025). The Illusion of Diminishing Returns: Measuring Long Horizon Execution in LLMs. https://arxiv.org/abs/2509.09677

### ⑤ 行為

- Greshake, K., Abdelnabi, S., Mishra, S., Endres, C., Holz, T., & Fritz, M. (2023). Not what you've signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection. AISec 2023. https://arxiv.org/abs/2302.12173
- Wallace, E., Xiao, K., Leike, R., Weng, L., Heidecke, J., & Beutel, A. (2024). The Instruction Hierarchy: Training LLMs to Prioritize Privileged Instructions. https://arxiv.org/abs/2404.13208
- Von Arx, S., Chan, L., & Barnes, E. (2025). Recent Frontier Models Are Reward Hacking. METR. https://metr.org/blog/2025-06-05-recent-reward-hacking/
- MacDiarmid, M., Wright, B., Uesato, J., et al. (2025). Natural Emergent Misalignment from Reward Hacking in Production RL. https://arxiv.org/abs/2511.18397

### Claude Code（2026-11-11 参照）

- How Claude remembers your project. https://code.claude.com/docs/en/memory
- Extend Claude Code. https://code.claude.com/docs/en/features-overview
- Explore the context window. https://code.claude.com/docs/en/context-window
- Create custom subagents. https://code.claude.com/docs/en/sub-agents
- Hooks reference. https://code.claude.com/docs/en/hooks
- Configure permissions. https://code.claude.com/docs/en/permissions
- Choose a permission mode. https://code.claude.com/docs/en/permission-modes
- Sandboxing. https://code.claude.com/docs/en/sandboxing

### 関連する筆者の記事

- CLAUDE.mdがなくても戦える — LLMの構造的制約を踏まえたプロンプト駆動開発（Zenn）. 制約を踏まえて、作業をステップに分け、成果物をファイルに残しながら進める方法を、Claude Code に限らない形で説明しています。この記事の ④ 時間の対策（作業を短く分ける、成果物をファイルに残す）を実践する手順にあたります。 https://zenn.dev/shuji_bonji/articles/c9d325f1fd7646
- Understanding LLM through Claude Code（以前の 8 つの制約の整理と、Claude Code の機能との対応）. https://shuji-bonji.github.io/understanding-llm-through-claude-code/ja/

[c1]: #1.-context-rot（入力が長いほど品質が下がる）
[c2]: #2.-lost-in-the-middle（中間の情報が使われにくい）
[c3]: #3.-priority-saturation（指示が多いほど守られにくい）
[c4]: #4.-context-window-limit（一度に受け取れる長さの上限）
[c5]: #5.-hallucination（事実と異なる内容を生成する）
[c6]: #6.-sycophancy（利用者に同調して正確さを損なう）
[c7]: #7.-knowledge-boundary（知らないことを知らないと言えない）
[c8]: #8.-miscalibration（確信度と正しさが一致しない）
[c9]: #9.-cot-unfaithfulness（思考過程が実際の判断と一致しない）
[c10]: #10.-self-evaluation-bias（自分の誤りを自分で見つけにくい）
[c11]: #11.-prompt-sensitivity（書き方で結果が変わる）
[c12]: #12.-non-determinism（同じ入力でも出力が変わる）
[c13]: #13.-tokenization-limits（文字単位の処理を誤る）
[c14]: #14.-instruction-decay（長い会話で最初の指示が守られなくなる）
[c15]: #15.-compounding-errors（手順が多いほど誤りが積み重なる）
[c16]: #16.-prompt-injection（読み込んだデータの命令文を実行してしまう）
[c17]: #17.-reward-hacking（目的ではなく評価基準を満たそうとする）