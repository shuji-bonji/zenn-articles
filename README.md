# zenn-articles

[Zenn](https://zenn.dev/shuji_bonji) の記事管理リポジトリ。Book は [zenn-books](https://github.com/shuji-bonji/zenn-books) で管理。

## 使い方

```sh
npm install
npx zenn new:article   # 新規記事
npx zenn preview       # プレビュー (http://localhost:8000)
```

公開は frontmatter の `published: true` にして main へ push。削除は Zenn ダッシュボード＋リポジトリ両方から。
