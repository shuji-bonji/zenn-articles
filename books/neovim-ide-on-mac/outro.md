---
title: 'おわりに — 自分のマシンで動く AI と組む'
---

お疲れさまでした。ここまでで、Ghostty + tmux + Neovim 0.12 の開発環境を組み、**SSH 越しにローカル LLM サーバへ繋ぎ、Neovim から Ollama を叩き、tmux でエージェントと協働する**ところまでを通してきました。ここまで来れば、自分の手で配線した開発環境が、もう手元にあるはずです。

「AI に手伝ってもらう」だけなら、クラウドの Copilot で十分です。本書がこだわったのは、**自分の機械で動く AI を、配線レベルから自分の開発環境に組み込む**こと。コードを外に出さず、サーバを再起動しても自動で立ち上がり、SSH の先で自走させる——この「土俵」を持っていることが、AI 駆動開発時代の足腰になります。

## 次の一歩

- **サーバ側を組む** — 本書の接続先（localllm / Ollama / LiteLLM）の構築は姉妹本『[Ollama で、Mac をローカル LLM にする](https://github.com/shuji-bonji/local-llm-on-mac)』へ。Open WebUI・SearXNG・モデル選定・エージェント化（OpenCode / A2A）・Routing/Cascade まで扱います。
- **設定を育てる** — kickstart.nvim を起点に、自分のワークフローに合わせて Lua 設定を伸ばしていきましょう。さらに深掘りしたいときは、[Neovimをはじめよう feat. mini.nvim](https://zenn.dev/kawarimidoll/books/6064bf6f193b51) や『実践Vim』へ進んでみてください。
