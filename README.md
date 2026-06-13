# えんメモ ランディングページ

保活アプリ「えんメモ」（`../hokatsu`）の公式サイト。GitHub Pages でホスティングする。

ビルド不要の素の HTML + CSS 構成。ファイルを編集して push するだけで反映される。

## ページ構成

| ファイル | 内容 |
|---|---|
| `index.html` | トップページ（概要・機能紹介・スクリーンショット） |
| `privacy.html` | プライバシーポリシー（ストア申請にも使用） |
| `releases.html` | リリースノート |
| `assets/css/style.css` | 全ページ共通のスタイル |
| `assets/img/` | 画像（アプリアイコン・スクリーンショット・ストアバッジ） |
| `assets/img/icons/` | UIアイコン（Material Symbols Rounded のSVG。アプリ内と同系統） |
| `assets/fonts/` | Webフォント（LINE Seed JP の woff2） |
| `.nojekyll` | GitHub Pages の Jekyll 処理を無効化（素のHTML配信） |

デザインはアプリに合わせている: 配色はアプリの `lib/theme/app_colors.dart` と同じ値を
`style.css` の `:root` の CSS 変数で定義。フォントはアプリのPDF出力と同じ
[LINE Seed JP](https://seed.line.me/index_jp.html)（SIL Open Font License 1.1）を自己ホスト。
`../hokatsu/assets/fonts/*.ttf` を `fonttools ttLib.woff2 compress` で woff2 化したもの
（Rg=400 / Bd=500〜700 / Eb=800〜900 にマッピング）。

## よくあるメンテナンス作業

### リリースノートを追加する

`releases.html` を開き、コメントで書いてあるテンプレートに従って
`<section class="release">` ブロックを**一番上**にコピペ追記する（新しいものが上）。

### スクリーンショットを差し替える

`assets/img/screenshot-home.png` を上書きすればトップページのヒーロー画像が変わる。
iPhone の縦スクショ（だいたい 9:19.5）ならそのまま収まる。

### ストアリンク

ストアバッジは公式画像を使用している（`assets/img/appstore-badge.svg` / `googleplay-badge.png`、
規約上バッジ画像そのものは改変しないこと）。

`index.html` 内に2箇所（ヒーローとページ下部CTA）あり、それぞれバッジ画像をリンクで囲んである:

```html
<a href="https://apps.apple.com/jp/app/id6776868012" target="_blank" rel="noopener">
  <img class="badge-appstore" src="assets/img/appstore-badge.svg" alt="App Storeでダウンロード">
</a>
<a href="https://play.google.com/store/apps/details?id=com.tsuka54lab.enmemo" target="_blank" rel="noopener">
  <img class="badge-googleplay" src="assets/img/googleplay-badge.png" alt="Google Playで手に入れよう">
</a>
```

バッジの再取得が必要になったら:
- Apple: <https://tools.applemediaservices.com/api/badges/download-on-the-app-store/black/ja-jp?size=250x83>
- Google: <https://play.google.com/intl/ja/badges/>（リンク生成ツールもここにある）

### アイコンを追加・変更する

アイコンはアプリ内と同じ Material Icons 系（Material Symbols **Rounded / Fill**）で統一している。
黒いSVGを CSS の mask で塗る仕組みなので、色は CSS の `color`（`.c-primary` 等のユーティリティクラス）で変えられる。

SVG は CSS に **data URI として埋め込んである**（外部SVGファイルを `mask-image` で参照すると、
`index.html` をダブルクリックで開くローカルプレビュー時にCORSでブロックされ表示されないため）。
元のSVGファイルは `assets/img/icons/` に保管してある。

新しいアイコンを使うには:

1. <https://fonts.google.com/icons> でアイコンを探す（スタイルは Rounded、Fill をON）
2. SVG をダウンロードして `assets/img/icons/〇〇.svg` に置く
   （URL直叩きなら `https://fonts.gstatic.com/s/i/short-term/release/materialsymbolsrounded/〇〇/fill1/24px.svg`）
3. SVG を data URI に変換する:
   ```bash
   python3 -c "import urllib.parse,sys;s=open(sys.argv[1]).read().strip().replace('\"',chr(39));print('url(\"data:image/svg+xml,'+urllib.parse.quote(s,safe=\"-_.~!*'()/:;,= \")+'\")')" assets/img/icons/〇〇.svg
   ```
4. `style.css` のアイコン定義に1行足す（出力された `url(...)` を2箇所に貼る）:
   `.ic-〇〇 { -webkit-mask-image: url("data:..."); mask-image: url("data:..."); }`
5. HTML で `<span class="micon ic-〇〇 c-primary" aria-hidden="true"></span>` のように使う

### プライバシーポリシーを改定する

`privacy.html` を編集し、冒頭の「制定日」の下に「最終改定日: YYYY年M月D日」を追記する。
外部送信を伴う機能（解析ツール等）をアプリに追加した場合は、必ず第3〜5条あたりを見直すこと。

### ナビゲーション・フッターを変える

共通ヘッダー/フッターは仕組み上 **3つの HTML すべてに同じものがコピーされている**。
変更するときは `index.html` / `privacy.html` / `releases.html` の3ファイルとも直すこと。

## ローカルでの確認

ブラウザで `index.html` を直接開けばOK（ビルド不要）。

```bash
open index.html
```

## GitHub Pages の公開手順（初回のみ）

1. このディレクトリを GitHub リポジトリとして push する
2. リポジトリの Settings → Pages → Source を「Deploy from a branch」にし、
   `main` ブランチ / `/ (root)` を選択
3. 数分後 `https://<ユーザー名>.github.io/<リポジトリ名>/` で公開される

公開URLが決まったら、ストア申請のプライバシーポリシーURL欄には
`https://<ユーザー名>.github.io/<リポジトリ名>/privacy.html` を指定する。
あわせて `index.html` 冒頭のコメントアウトされた OGP メタタグも有効化するとSNSシェアがきれいになる。
