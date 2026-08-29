# CLAUDE.md — pomeloworks-site 運用ノート

このリポジトリは組織サイト **https://pomeloworks.dev/** の正本である。GitHub Pages（プロジェクトサイト）で配信し、カスタムドメイン `pomeloworks.dev` を紐付けている。アプリ本体（`workout-tracker`）・リモートメッセージ配信（`workout-tracker-messages`）とは独立したリポジトリであり、それらの規約・CIとは無関係。

## サイトの3役

1. **Play Console組織登録の「組織ウェブサイト」欄**（非公開項目）および、将来のストア掲載情報「デベロッパーのウェブサイト」（**公開**）
2. **app-ads.txt の設置場所**（設置済み。IAB仕様によりドメインルート直下必須）
3. **プライバシーポリシーの置き場**（設置済み：`https://pomeloworks.dev/wakutora/privacy/`）

## 構成

**`docs/` の中だけがWebに配信される。その外にあるものは配信されない**（下記「配信範囲」参照）。

| ファイル | 役割 |
|---|---|
| `docs/index.html` | ページ本体。静的HTML1枚。**ビルドツールなし・今後も導入しない** |
| `docs/CNAME` | カスタムドメイン紐付け。**削除・変更・配信フォルダ外への移動を禁止**（配信フォルダ直下にある必要がある） |
| `docs/app-ads.txt` | AdMob広告在庫の正規販売者宣言（IAB仕様）。公開URLは `https://pomeloworks.dev/app-ads.txt`。**ドメインルート直下必須・パス変更禁止**。下記「app-ads.txt」参照 |
| `docs/wakutora/privacy/index.html` | ワクトラのプライバシーポリシー。公開URLは `https://pomeloworks.dev/wakutora/privacy/`。**本文は開発者承認済みのverbatim・改変禁止**。下記「プライバシーポリシー」参照 |
| `docs/.nojekyll` | GitHub PagesのJekyll処理を無効化する空ファイル |
| `CLAUDE.md` | 本ファイル。**配信対象外** |
| `handoff/` | 作業ハンドオフ文書の控え。**配信対象外** |
| `work_logs/` | 作業ログ。`YYYY-MM-DD_slug.md` で1セッション1ファイル。**配信対象外** |
| `.gitignore` | WSLが生成する `*:Zone.Identifier` を除外するだけのファイル |

## 配信範囲

GitHub Pagesのソースは **`main` ブランチの `/docs`**（2026-08-29に `/` から変更）。`docs/` が配信上のドメインルートとして扱われる。

- **`docs/` に置いたファイルは、パスがそのまま公開URLになる**。`docs/foo/bar.html` → `https://pomeloworks.dev/foo/bar.html`。非公開にしたいファイルを `docs/` に入れない。
- **`docs/` の外（`CLAUDE.md`・`handoff/` 等）は配信されない**。HTMLかどうかは無関係で、判定基準は「`docs/` 配下にcommitされているか」の一点のみ。Pagesに除外リストのような設定項目は存在しない。
- **`.nojekyll` があるためJekyllの暗黙のフィルタは働かない**。`_` や `.` で始まるファイルも `docs/` にあれば配信される。
- **配信されないことと、秘密であることは別**。このリポジトリはGitHub上でPUBLICなので、`CLAUDE.md` も `handoff/` も `https://github.com/pomelopun/pomeloworks-site` からは誰でも読める。**外部に見せられない値をこのリポジトリに書かない**（git履歴からも消えない）。
- ソース設定を変更しても再ビルドは自動で走らない。変更時は `gh api -X POST repos/pomelopun/pomeloworks-site/pages/builds` でビルドを要求すること。

## 更新手順

1. `docs/` 配下のファイルを編集し、main へ commit・push する。**デプロイ作業は存在しない**（pushでGitHub Pagesが自動再デプロイ）。ブランチはmainのみ。
2. 反映確認：`curl -sI https://pomeloworks.dev/ | head -1` が `200` を返すこと。反映遅延＝Pagesデプロイ1〜2分＋CDNキャッシュ最大10分（`Cache-Control: max-age=600`）。即時反映されない前提で慌てない。
3. 見た目の確認はブラウザで https://pomeloworks.dev/ を開く（キャッシュ残りに注意）。

## 規律

- **公開済みパスは追加のみ・転用禁止**（配信リポジトリと同じ規律）。ストア掲載・審査書類・AdMobにURLが記載されるため、一度公開したパスのリネーム・削除・意味変更をしない。
- **個人情報を載せない**：本名・自宅住所・電話番号は掲載禁止。住所はVO住所であっても掲載しない方針（掲載義務なし。公開経路を増やさない）。
- **アクセス解析・トラッキングを入れない**。サイト側の情報収集ゼロを維持し、アプリのプライバシー方針と整合させる。
- 配色はアプリのカラートークン表（design_color_tokens v0.3 ライトモード）に整合させる。`docs/index.html` の `:root` CSS変数が対応表。
- **`pomelopun.github.io`（ユーザーサイトリポジトリ）を作成してカスタムドメインを付けることは恒久的に禁止**。ユーザーサイトへのカスタムドメイン設定はアカウント配下の全プロジェクトサイトの基底URLに波及し、リモートメッセージ配信URL（配信設計書D-40：`https://pomelopun.github.io/workout-tracker-messages/v1/messages.ja.json`）がリダイレクト経由に変質するため。

## app-ads.txt

2026-08-23 設置済み。内容は次の1行（末尾に改行あり）：

```
google.com, pub-2874004131127276, DIRECT, f08c47fec0942fa0
```

- `pub-2874004131127276` はAdMobのサイト運営者ID。`f08c47fec0942fa0` はGoogleの認証機関IDで全パブリッシャー共通の固定値。
- 実体は `docs/app-ads.txt`。**公開URL（`https://pomeloworks.dev/app-ads.txt`）をドメインルート直下から動かさない**。IAB仕様でパスが固定されており、移動＝宣言の消失として扱われる。
- 内容を書き換えるのは、AdMobのサイト運営者IDが変わったときと、他のアドネットワークを追加したとき（1行1販売者で追記）のみ。
- 確認：`curl -s https://pomeloworks.dev/app-ads.txt` が上記1行を返すこと（配信確認済み）。
- 残作業：AdMob管理画面（アプリ > app-ads.txt）でクロール状態を確認する。反映まで最大24時間程度。前提として、ストア掲載情報の「デベロッパーのウェブサイト」に `https://pomeloworks.dev` が設定されていること（AdMobはストア掲載のURLからドメインを引いてクロールする）。

## DNS台帳（Cloudflare／pomeloworks.dev ゾーン）

以下すべて **DNS only（グレー雲）**。Proxied（オレンジ雲）にするとGitHub側の証明書発行・更新が失敗する。

| 種別 | 名前 | 値 |
|---|---|---|
| A | `@` | `185.199.108.153` / `185.199.109.153` / `185.199.110.153` / `185.199.111.153`（4本） |
| AAAA | `@` | `2606:50c0:8000::153` / `8001::153` / `8002::153` / `8003::153`（4本） |
| CNAME | `www` | `pomelopun.github.io` |
| TXT | `_github-pages-challenge-pomelopun` | f502b7043e33efccdf032530edf5b2 |
| TXT | `@`（SPF） | `v=spf1 include:_spf.mx.cloudflare.net include:_spf.google.com ~all` |
| TXT | `_dmarc` | `v=DMARC1; p=none; rua=mailto:contact@pomeloworks.dev` |
| TXT | `cf2024-1._domainkey` | Email RoutingのDKIM。`v=DKIM1; h=sha256; k=rsa; p=MIIBIjANBg…`（公開鍵はCloudflareが自動設定。全文はCloudflare管理画面で確認）。**触らない・削除しない** |

このほか **Email Routing のMXレコードが同居している。MXには絶対に触らない**（`contact@pomeloworks.dev` の受信が止まる）。SPF・DMARCは下記の運用注記に従うこと。

**送信経路とDMARCポリシーの運用注記**：`contact@` の送信はAccount BのGmail send-as（`smtp.gmail.com` 経由）。この経路はドメイン認証が整合しないため、**DMARCの `p=none` を維持すること**。`p=quarantine` / `p=reject` への強化は、ドメイン名義でDKIM署名する送信経路（外部SMTPリレー等）へ移行してから。SPFの `include:_spf.google.com` はこのGmail SMTP経由送信を許可するために追記したもの。なお台帳のDKIM（`cf2024-1._domainkey`）はEmail Routing（受信・転送）用であり、Gmail send-asの送信には適用されない。**このDKIMの存在をもってDMARCを強化してはならない**。

障害時はこの台帳とCloudflare側の実レコードを突き合わせる。`dig pomeloworks.dev +noall +answer -t A` の結果が上表の4 IPと一致するのが正常状態。

## プライバシーポリシー

2026-08-29 設置済み。実体は `docs/wakutora/privacy/index.html`、公開URLは `https://pomeloworks.dev/wakutora/privacy/`（**末尾スラッシュ付きのこのURLが正**。スラッシュなしは301で転送されるが、登録には正規URLを使う）。

- **本文は開発者承認済みのverbatim。文言の変更・誤字修正を独断で行わない**。改定が必要な場合は承認を取ってから差し替える（本文§6が改定手続を定めている）。
- 参照元が複数ある公開パスのため**リネーム・削除禁止**：アプリ設定画面（S-08）の`PRIVACY_POLICY_URL`、Play Consoleのストア掲載「プライバシーポリシー」欄、データセーフティ申告が同URLを指す。
- 内容は`app-ads.txt`・DNS・トップページとは独立。設置作業でそれらに触れない。
- 確認：`curl -sI https://pomeloworks.dev/wakutora/privacy/ | head -1` が `200` を返すこと。
- `docs/index.html` のフッターからリンク済み（2026-08-30）。ポリシー側フッターからもトップへ戻れる。
- 残作業：Play Consoleのストア掲載「プライバシーポリシー」欄に同URLを登録する。

## 将来タスク

### ストア公開時

- `docs/index.html` のワクトラ欄を「開発中」バッジからGoogle Playリンクへ更新する。
