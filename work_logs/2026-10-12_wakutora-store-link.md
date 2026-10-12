# 作業ログ：ワクトラのストア公開に伴うトップページ更新

| 項目 | 内容 |
|---|---|
| 日付 | 2026-10-12 |
| 対象 | `pomelopun/pomeloworks-site` |
| 起点 | ワクトラのGoogle Play公開（CLAUDE.md「将来タスク：ストア公開時」） |
| 起点コミット | `e73f9a2` |
| 到達コミット | `850b69b` |
| 結果 | トップページのワクトラ欄を「配信中」に変更し、Google Playの掲載ページへリンクした |

---

## 1. やったこと

| コミット | 内容 |
|---|---|
| `850b69b` | `docs/index.html` のワクトラ欄を更新し、`CLAUDE.md` の将来タスクを完了済みの記載に置き換え |

### `docs/index.html` の変更点

- バッジ：「開発中」→「配信中」
- 説明文から「Google Playで公開予定です。」を削除
- 掲載ページへのテキストリンク「Google Playで見る」を追加
  - URL：`https://play.google.com/store/apps/details?id=dev.pomeloworks.wakutora&hl=ja`（HTML内では `&amp;`）
  - 既存の `.card p` と `a` のスタイルで表示されるため、CSSは追加していない

## 2. 確認

- 事前：掲載ページが `200` を返し、タイトルが「ワクトラ - 筋トレ記録メモ - Google Play のアプリ」であることを確認
- 事後：Pagesビルド `built 850b69b`、`curl -sI https://pomeloworks.dev/` が `200`、配信HTMLに新しいバッジとリンクが含まれることを確認
