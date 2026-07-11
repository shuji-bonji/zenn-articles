---
title: '環境構築 — 前提ツールをまとめて入れる'
---

この章では、必要なものを先に全部入れてしまい、後から順に設定していけるようにしておきます。ここではツールの導入と導入確認までにとどめます。

:::message
**この章でできるようになること**
以降の章で使うツール（Ghostty / tmux / Neovim 0.12 / Nerd Font / 補助コマンド）を Homebrew で一括導入し、nvim --version などで土台が整ったことを確認できるようになります。
:::

:::message
**前提**: macOS（Apple Silicon）と [Homebrew](https://brew.sh) を導入済みであること。Homebrew が未導入なら先に入れてください。（下記）
:::

## 1. Homebrew（未導入なら）

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew --version    # バージョンが出れば OK
```

導入済みなら次へ進みます。

## 2. 一括インストール

標準のターミナルで実行します。

```bash
# ターミナルエミュレータ（GUI アプリなので --cask）
brew install --cask ghostty

# CLI ツール一式
#   tmux     : セッション管理（消えない作業机）
#   neovim   : エディタ本体（0.12 系が入る）
#   ripgrep  : 高速 grep（telescope の全文検索 live_grep が使う）
#   fd       : 高速 find（telescope のファイル検索が使う）
#   fzf      : ファジーファインダ（プロジェクト切替の sessionizer で使う）
#   node     : LSP サーバ等の導入に必要（Mason が裏で使う / npm 同梱）
brew install tmux neovim ripgrep fd fzf node
```

:::message
**node を nvm / fnm / volta / asdf などで管理している人へ**

その場合は、上の `brew install` から `node` を外してください。

Homebrew 版とバージョンマネージャ版が二重に入ると、PATH でどちらが優先されるか分かりにくくなり、トラブルのもとになります。

本書で node を使うのは LSP やフォーマッタの導入（Mason）まわりだけで、**必要なのは PATH の通った `node` / `npm` がひとつあること** だけです。

バージョンマネージャ経由でもまったく問題ありません。`which node` と `node -v`（v20 以降が目安）で、使いたい方が効いているか確認しておきましょう。
:::

:::message
同じく **`ripgrep` / `fd` / `fzf` / `neovim` などを、すでに別の方法（既存の Homebrew・MacPorts・各種バージョンマネージャ等）で入れている場合**も、重複を避けて該当分を `brew install` の行から外して構いません。

本書が前提にするのは「コマンドが PATH に通っていること」だけなので、導入経路は問いません（`neovim` だけは **0.12 以上** が必要です。§3 で確認します）。
:::

### Nerd Font（アイコン表示用）

ファイラ（neo-tree）や各種 UI のアイコンは [**Nerd Font**](https://www.nerdfonts.com) で表示されます。お好みのフォントで構いませんが、本書の設定例に合わせるなら BlexMono（IBM Plex Mono ベース）を入れておきましょう。

```bash
brew install --cask font-blex-mono-nerd-font
```

:::message
Homebrew は 2024 年に cask-fonts を本体へ統合したため、`brew tap homebrew/cask-fonts` は不要です。別の Nerd Font が好みなら `font-jetbrains-mono-nerd-font` などに読み替えてください。
フォントは Ghostty の `font-family` で指定します。（ghostty 章の設定例参照）
:::

## 3. 導入されたことを確認する

```bash
ghostty --version    # 1.2 以上（quick-terminal-size 等に必要）
tmux -V              # 例: tmux 3.5a
nvim --version | head -1   # NVIM v0.12.x であること（本書の前提）
rg --version | head -1     # ripgrep
fd --version               # fd
fzf --version              # fzf
node -v                    # v20 以降が目安
```

:::message alert
**`nvim --version` が 0.11 以下だと本書の作法は通りません**。
本書は `vim.pack` / `vim.lsp.config()` / コア補完など **0.12 で標準化された機能**を前提にしています（理由は「はじめに」）。
古い場合は `brew upgrade neovim` で 0.12 系へ上げてください。
:::

## 4. 入れたツールは、それぞれ何をするもの？

ここで入れたものが、それぞれ何をするツール／ライブラリなのかを先に押さえておきましょう。
詳しい設定や使い方は、各章でひとつずつ扱っていきます。

| ツール              | どんなもの（役割）                                                                                                               | 主に使う章                                   |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- |
| **Ghostty**         | GPU で高速描画するターミナルエミュレータ                                                                                         | ghostty（描画・設定・操作・常駐化）          |
| **tmux**            | ターミナル多重化ツール<br>1 枚の画面を複数の作業スペースに分割し、かつ裏で動いている作業を消さないようにセッションを管理します。 | tmux / neovim-tmux / remote-dev / agent-pair |
| **Neovim 0.12**     | Vim 系のモーダルエディタ<br>本書で IDE に育てる主役の本体です。                                                                  | neovim-ide 以降すべて                        |
| **ripgrep**（`rg`） | 非常に高速な grep<br>**全文検索**（telescope の `live_grep`）の裏側で動きます。                                                  | neovim-ide（全文検索）                       |
| **fd**              | 高速な `find` の代替<br>**ファイル名検索**（telescope の `find_files`）の裏側で動きます。                                        | neovim-ide（ファイル検索）                   |
| **fzf**             | ファジーファインダ<br>候補をあいまい検索で素早く絞り込みます。<br>プロジェクト切替の sessionizer で使います。                    | neovim-tmux（sessionizer）                   |
| **node**（Node.js） | JavaScript ランタイム（`npm` 同梱）<br>LSP サーバやフォーマッタの導入・実行に必要です。                                          | neovim-ide（LSP 導入）/ neovim-llm（補助）   |
| **Nerd Font**       | アイコン用グリフを含むフォント<br>ファイラなどの UI アイコンを正しく表示するために使います。                                     | neovim-ide（ファイラのアイコン）             |

設定や使い方は各章で扱っていきます。ここでは **道具が揃った** ところまでで十分です。

:::message
**`rg` / `fd` / `fzf` は Neovim 専用ではありません。**
これらは **独立した汎用コマンドラインツール** で、単体でも普段使いに便利です。
本書では telescope や sessionizer の「裏側」として使いますが、それはあくまで一利用例で、ターミナルから直接使う価値も十分にあります。

- **`rg`（ripgrep）** — `grep` の高速版。`rg "検索語"` でカレントディレクトリ配下を再帰検索できます。 → [コマンド紹介シリーズ：rg](https://zenn.dev/akasan/articles/25f1eca029854b)
- **`fd`** — `find` の使いやすい代替。`fd 名前` でファイルを高速に探せます。 → [fd（`find` の高速な代替）](https://zenn.dev/21f/articles/fd-find-alternative)
- **`fzf`** — 任意のリストを対話的に絞り込むファジーファインダ。`ls | fzf` や履歴検索（`Ctrl-r`）など、パイプで何でも絞れます。 → [あいまい検索 fzf のすゝめ](https://zenn.dev/nowa0402/articles/5eb780280f2523)

各ツールを単体で使い込みたい人は、上のリンク記事が分かりやすいです。
:::

## まとめて消したいとき

各章末にも個別のアンインストール手順がありますが、この章で入れたものをまとめて消したいときは、次のように実行します。

```bash
brew uninstall --cask ghostty font-blex-mono-nerd-font
brew uninstall tmux neovim ripgrep fd fzf node
```
