# CLAUDE.md — pomeloworks-site 運用ノート

このリポジトリは組織サイト **https://pomeloworks.dev/** の正本である。GitHub Pages（プロジェクトサイト）で配信し、カスタムドメイン `pomeloworks.dev` を紐付けている。アプリ本体（`workout-tracker`）・リモートメッセージ配信（`workout-tracker-messages`）とは独立したリポジトリであり、それらの規約・CIとは無関係。

## サイトの3役

1. **Play Console組織登録の「組織ウェブサイト」欄**（非公開項目）および、将来のストア掲載情報「デベロッパーのウェブサイト」（**公開**）
2. **app-ads.txt の設置場所**（AdMob承認後。IAB仕様によりドメインルート直下必須）
3. **プライバシーポリシーの置き場**（公開準備時に作成）

## 構成

| ファイル | 役割 |
|---|---|
| `index.html` | ページ本体。静的HTML1枚。**ビルドツールなし・今後も導入しない** |
| `CNAME` | カスタムドメイン紐付け（GitHubがPages設定時に自動生成。**削除・変更禁止**） |
| `.nojekyll` | GitHub PagesのJekyll処理を無効化する空ファイル |
| `CLAUDE.md` | 本ファイル |

## 更新手順

1. ファイルを編集し、main へ commit・push する。**デプロイ作業は存在しない**（pushでGitHub Pagesが自動再デプロイ）。ブランチはmainのみ。
2. 反映確認：`curl -sI https://pomeloworks.dev/ | head -1` が `200` を返すこと。反映遅延＝Pagesデプロイ1〜2分＋CDNキャッシュ最大10分（`Cache-Control: max-age=600`）。即時反映されない前提で慌てない。
3. 見た目の確認はブラウザで https://pomeloworks.dev/ を開く（キャッシュ残りに注意）。

## 規律

- **公開済みパスは追加のみ・転用禁止**（配信リポジトリと同じ規律）。ストア掲載・審査書類・AdMobにURLが記載されるため、一度公開したパスのリネーム・削除・意味変更をしない。
- **個人情報を載せない**：本名・自宅住所・電話番号は掲載禁止。住所はVO住所であっても掲載しない方針（掲載義務なし。公開経路を増やさない）。
- **アクセス解析・トラッキングを入れない**。サイト側の情報収集ゼロを維持し、アプリのプライバシー方針と整合させる。
- 配色はアプリのカラートークン表（design_color_tokens v0.3 ライトモード）に整合させる。`index.html` の `:root` CSS変数が対応表。
- **`pomelopun.github.io`（ユーザーサイトリポジトリ）を作成してカスタムドメインを付けることは恒久的に禁止**。ユーザーサイトへのカスタムドメイン設定はアカウント配下の全プロジェクトサイトの基底URLに波及し、リモートメッセージ配信URL（配信設計書D-40：`https://pomelopun.github.io/workout-tracker-messages/v1/messages.ja.json`）がリダイレクト経由に変質するため。

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

## 将来タスク

### app-ads.txt（AdMob承認後）

- ルート直下に `app-ads.txt` を追加。内容は次の1行（`pub-` 以降はAdMobのサイト運営者ID）：

  ```
  google.com, pub-XXXXXXXXXXXXXXXX, DIRECT, f08c47fec0942fa0
  ```

  末尾の `f08c47fec0942fa0` はGoogleの認証機関IDで全パブリッシャー共通の固定値。
- 前提：ストア掲載情報の「デベロッパーのウェブサイト」に `https://pomeloworks.dev` が設定されていること（AdMobはストア掲載のURLからドメインを引いてクロールする）。
- 設置後：AdMob管理画面（アプリ > app-ads.txt）でクロール状態を確認。反映まで最大24時間程度。

### プライバシーポリシー（公開準備時）

- パス方針：`/wakutora/privacy.html`（アプリ名スラッグの最終確定は商標確認後・起草時。確定までパスを作らない）。
- 作成後、`index.html` フッターのコメント位置にリンクを追加する。
- Play Consoleのストア掲載「プライバシーポリシー」欄に同URLを登録する。

### ストア公開時

- `index.html` のワクトラ欄を「開発中」バッジからGoogle Playリンクへ更新する。
