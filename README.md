# kurukurugames.com

[kurukurugames.com](https://kurukurugames.com/) の静的サイト。GitHub Pages で配信する。

WordPress.com から移行したもの。配色・書体・レイアウトは移行前のサイトから採取している。

## 構成

```
index.html                  トップ（配信中のゲーム / 紹介 / 連絡先）
privacy-policy/index.html   プライバシーポリシー（日本語 / English / 한국어 / 繁體中文）
contact/index.html          お問い合わせ（メール / X）
assets/css/site.css         全ページ共通のスタイル
assets/img/                 ロゴ・アイコン・ストアバッジ
.nojekyll                   GitHub Pages の Jekyll 処理を無効化
CNAME.pending               独自ドメイン適用時に CNAME へリネームする（下記「独自ドメインの適用」）
```

ビルド工程は無い。HTML と CSS をそのまま配信する。

`/contact/` は **App Store Connect / Google Play Console の「サポート URL」から
参照される想定**なので、URL を変えたり削除したりしない。フォームは置いていない
（静的サイトには送信を受けるサーバーが無く、ストアの要件もフォームではなく
「連絡手段が書かれた到達可能なページ」であるため）。

- ページ間のリンクは**すべて相対パス**で書く。`/assets/...` のようなルート相対パスは
  `https://<org>.github.io/<repo>/` 配下では壊れるため使わない
- 外部から読むのは Google Fonts のみ（DM Sans / IBM Plex Sans / IBM Plex Mono / Noto Sans JP）

## ローカルでの確認

```bash
python -m http.server 8000
```

`http://localhost:8000/` を開く。`file://` で直接開くとパス解決が変わるので、
必ず HTTP サーバー経由で確認する。

## 独自ドメインの適用【順序を守る】

**`CNAME` ファイルを先に置いてはいけない。** 置いた時点で GitHub Pages は
`https://<org>.github.io/<repo>/` へのアクセスを `kurukurugames.com` へリダイレクトする。
DNS 切り替え前だとリダイレクト先は旧 WordPress サイトのままなので、
**新サイトの表示確認ができなくなる。**

1. リポジトリを push し、Settings → Pages で Source をブランチに設定する
2. `https://<org>.github.io/<repo>/` で**表示を確認する**（この時点で CNAME は置かない）
3. WordPress.com の DNS で apex の A レコードを GitHub Pages の 4 IP へ差し替える

   ```
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
   ```

4. `CNAME.pending` を `CNAME` にリネームして push する
   （または Settings → Pages の Custom domain に `kurukurugames.com` を入力する。
   どちらでも同じで、後者は GitHub が CNAME ファイルを自動コミットする）
5. 証明書が発行されたら Settings → Pages の **Enforce HTTPS** を有効にする

### 🚨 DNS を触るときの絶対条件

このドメインでは **Titan Email が稼働している**。以下の 4 種を消すとメールが止まる。

| 種別 | ホスト | 値 |
|---|---|---|
| MX | `@` | `mx1.titan.email` (10) |
| MX | `@` | `mx2.titan.email` (20) |
| TXT | `@` | `v=spf1 include:spf.titan.email ~all` |
| TXT | `titan3._domainkey` | DKIM 公開鍵 |
| TXT | `_dmarc` | `v=DMARC1;p=none;sp=none;adkim=r;aspf=r;pct=100` |

**変更するのは A レコードだけ。** 作業前に DNS 画面の全レコードを控えておくこと。

## 更新のしかた

HTML を直接編集して commit / push すれば、GitHub Pages が数十秒で反映する。

- 文言の変更 → 該当 HTML を編集
- 見た目の変更 → `assets/css/site.css` を編集
- 画像の差し替え → `assets/img/` に置いて HTML の `src` を変更

ストアバッジ（`badge-appstore-ja.png` / `badge-googleplay-ja.png`）は
2024-05 に取得した Apple / Google の公式バッジ。各社のガイドライン改定に追従する場合は
公式配布物から取り直す。
