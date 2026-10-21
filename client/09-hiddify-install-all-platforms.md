# 09 · Hiddify を全プラットフォームに入れる：Windows・macOS・Linux・Android・iOS

当サイトの Hiddify ページで使うクライアントは、オープンソースの Hiddify です。sing-box のコアを内蔵しているので、インストールすればほかに設定するものはありません。5 つのプラットフォームとも同じアプリですが、初回起動でつまずくポイントはそれぞれ違います。Windows ではウイルス対策ソフトにブロックや削除をされることがあり、Mac では「壊れている」と表示されるうえにネットワーク機能拡張の許可も必要で、Linux では許可コマンドを実行しないと VPN モードが動かず、iPhone では中国の App Store に見つかりません。この記事では、プラットフォームごとに入手先、インストール方法、初回起動時にやることを説明し、最後にサブスクリプションを読み込んで接続するまでの最短手順をまとめます。

## 入手先

| プラットフォーム | インストーラー | 入手先 |
|---|---|---|
| Windows | インストーラー（.exe）、x64 のみ | Hiddify ページのダウンロード欄 |
| macOS | .dmg、Apple シリコンと Intel 共通の 1 パッケージ | Hiddify ページのダウンロード欄 |
| Linux | .deb（Debian / Ubuntu 系）、x64 のみ | Hiddify ページのダウンロード欄 |
| Android | .apk、ARM64 と x86-64 の 2 種類 | Hiddify ページのダウンロード欄、または Google Play |
| iPhone / iPad | App Store の Hiddify Proxy & VPN | App Store。中国以外の地域の Apple ID が必要 |

Hiddify ページのダウンロード欄は、お使いのシステムに合ったパッケージを自動でおすすめし、その下にほかのシステム用のパッケージを並べています。各行の横には Hiddify 公式のリリースページを指す「公式サイト」アイコンもあるので、バージョンを見比べられます。デスクトップの 3 システム（Windows、macOS、Linux）には動画チュートリアルもあります。

デスクトップ用のインストーラーは Hiddify 公式リリースパッケージをそのままミラーしたもので、手を加えたり再パッケージしたりはしていません。許可する前にご自身で確かめることもできます。インストーラーを VirusTotal にアップロードして確認してください。方法は「[トラブルシューティング 06](../troubleshooting/06-antivirus-false-positive.md)」をご覧ください。

## Windows

1. Hiddify ページのダウンロード欄からインストーラーをダウンロードし、ダブルクリックでインストールします。（現時点の当社のテストでは Windows セキュリティには検出されません。インストール済みのウイルス対策ソフトに検出された場合は、「[トラブルシューティング 06](../troubleshooting/06-antivirus-false-positive.md)」末尾のよくある質問をご覧ください。）

## macOS

1. Hiddify ページのダウンロード欄から .dmg をダウンロードして開き、Hiddify を「アプリケーション」にドラッグします。
2. 「Finder」→「アプリケーション」で Control キーを押しながら Hiddify のアイコンをクリックし、「開く」を選び、ダイアログでもう一度「開く」をクリックします。
3. それでも開けない場合：「システム設定」→「プライバシーとセキュリティ」の「セキュリティ」欄で「このまま開く」をクリックし、パスワードを入力して確認します。
4. 「壊れているため開けません」と表示された場合：ファイルは壊れておらず、システムの Gatekeeper がブロックしています。「ターミナル」を開いて下のコマンドを実行し、return キーを押してログインパスワードを入力してから、Hiddify をもう一度開いてください。

```bash
sudo xattr -dr com.apple.quarantine /Applications/Hiddify.app
```

このコマンドは、ファイルに付いた「ダウンロードしたもの」という隔離フラグを外すだけで、対象はこのアプリだけです。`sudo spctl --master-disable` で Mac 全体の Gatekeeper をオフにするのは**やめてください**。

インストール後に初めて接続をクリックすると、「ネットワーク機能拡張」の許可も求められます。この手順を飛ばすと、アイコンが回り続けていつまでも接続できません。

- **macOS 15 Sequoia 以降**：「システム設定」→「一般」→「ログイン項目と機能拡張」を開き、いちばん下の「機能拡張」までスクロールして、表示を「カテゴリ別」に切り替えます。「ネットワーク機能拡張」の横の ⓘ をクリックし、Hiddify のスイッチをオンにして、求められたらパスワードか指紋で確認します。
- **macOS 13 Ventura / 14 Sonoma**：まず Hiddify を起動して一度接続をクリックすると、「システム機能拡張がブロックされました」と表示されます。続いて「システム設定」→「プライバシーとセキュリティ」を開き、下にスクロールして「開発元"…"のシステムソフトウェアの読み込みがブロックされました」の「許可」をクリックし、パスワードを入力します。

会社支給の Mac でこれらのボタンがグレーアウトしている場合は、デバイス管理のポリシーでロックされています。IT 部門に相談するか、Cisco、OpenVPN、プライベートネットワークのクライアントを代わりに使ってください。

## Linux

1. Hiddify ページのダウンロード欄から .deb をダウンロードし、ダブルクリックしてソフトウェアセンターでインストールします。または、ダウンロードしたディレクトリーでターミナルを開いて次を実行します（Debian、Ubuntu とその派生ディストリビューション）。

```bash
sudo apt install ./downloaded-file-name.deb
```

2. インストール直後に接続すると「operation not permitted」と表示され、VPN モードが起動しません。公式の deb はインストールしただけではこの権限を持っていないためです。ターミナルを開いて下のコマンドを実行し、Hiddify に権限を与えてから、Hiddify を開き直してください。

```bash
echo /usr/share/hiddify/lib | sudo tee /etc/ld.so.conf.d/hiddify.conf && sudo ldconfig && sudo setcap cap_net_admin,cap_net_raw+ep /usr/share/hiddify/hiddify
```

3. **Hiddify を更新するたびに再実行してください**。権限はプログラムのファイルに付いているので、更新でファイルが置き換わると権限も消えます。

deb パッケージは x64 版です。ARM の Linux 機器では OpenVPN を使ってください。アカウントは同じものが使えます。

## Android

1. Hiddify ページのダウンロード欄から .apk をダウンロードします。ページがお使いのスマホに合ったアーキテクチャを自動でおすすめします。ほとんどのスマホは ARM64 です。Google Play を使えるなら、ストアから直接インストールしてもかまいません。
2. インストール中に「提供元不明のアプリ」の確認やセキュリティソフトの警告が出たら、許可してください。
3. 初めて接続をタップすると、システムが VPN の許可を求めるので、許可をタップします。

## iPhone / iPad

1. App Store で「Hiddify」を検索してインストールします。無料です。
2. 見つからない、または「このAppは現在、お住まいの国または地域では入手できません」と表示される場合：iOS では、中国以外の地域の Apple ID で App Store からインストールします（Hiddify はアメリカ、香港、台湾、日本、シンガポールの App Store にあります）。新しく作るのがおすすめです。App Store で任意の無料アプリの「入手」をタップ →「Apple ID を新規作成」を選び、地域に香港やアメリカなどを、支払い方法に「なし」を選びます。アカウントを切り替えるのは App Store だけで、iCloud には影響しません。詳しい手順は「[06 · iOS でアプリをインストールできないとき](06-ios-app-store.md)」をご覧ください。
3. 初めて接続をタップすると、システムが VPN の許可を求めるので、許可をタップします。

他人から借りた Apple ID は**使わないでください**。端末を遠隔でロックされるおそれがあります。

## インストール後：サブスクリプションを読み込んで接続

1. ウェブサイトにログインし、Hiddify ページを開きます。
2. 使いたい地域の国旗をクリックすると QR コードが表示されます。または、ページ上部の「全地域を一括インポート・自動選択」をクリックすると、中国以外の全地域を一度に読み込み、クライアントが自動で選ぶようになります。**中国の地域は別に読み込みます**。海外から中国の動画を見たりネットバンキングを使ったりするときは、中国の国旗をクリックして読み込んでください。
3. QR コードの下に、サブスクリプションの種類ごとに 3 つのボタンがあります。Hiddify で使うのは 1 つ目（Hiddify / sing-box / GUI.for.SingBox / Karing と表示され、サブタイトルは「sing-box サブスクリプション」）で、スマホでもパソコンでも同じボタンです。スマホ：タップしてから Hiddify に切り替え、案内に従って追加します。または別の端末の Hiddify でコードをスキャンします。パソコン：クリックすると https:// のアドレスがコピーされるので、Hiddify で「+」→「Add from clipboard」（クリップボードから追加）をクリックします。
4. 接続をクリックします。

3 つのボタンの違い、Clash などほかのクライアントへの読み込み方、サブスクリプションに期限があるかどうかは、「[02 · サブスクリプションリンクの読み込み方（Hiddify、Clash、Shadowrocket）](02-singbox-subscription-links.md)」をご覧ください。読み込んだのにつながらない場合は「[トラブルシューティング 04](../troubleshooting/04-singbox-import-not-connecting.md)」へ。

## よくある質問

**インストーラーに手を加えていますか？** いいえ。デスクトップ用のインストーラーは Hiddify 公式リリースパッケージをそのままミラーしたものです。Hiddify ページの各行に公式リリースページへのリンクがあるので、ご自身でバージョンを見比べられますし、ファイルを VirusTotal にアップロードして再確認することもできます。


**Mac で xattr コマンドを実行したのに、まだつながりません。** ほとんどの場合、「ネットワーク機能拡張」が許可されていません。上の macOS の節に従って、システム設定で Hiddify のスイッチをオンにしてください。

**Linux で Hiddify を更新したら、また VPN モードが動かなくなりました。** 想定どおりの動作です。更新後にもう一度、許可コマンドを実行してください。

**ARM の Windows や Linux のパソコンでも使えますか？** 使えます。OpenVPN など別の接続方法を使えば、同じアカウントで利用できます。Hiddify のデスクトップ版は現在 x64 のみです。

**許可の手順が面倒なら？** Cisco（Cisco Secure Client）、OpenVPN（OpenVPN Connect）、プライベートネットワーク（Tailscale）はどれもダブルクリックで開けます。ウイルス対策ソフトに検出されることも、Mac で「壊れている」と言われることもなく、アカウントも同じものです。

## 関連記事

- [Hiddify ページ：クライアントのダウンロードと各地域の QR コード](https://www.leotun.com/ja/singbox?utm_source=github&utm_content=client-09)
- [サブスクリプションリンクの読み込み方（Hiddify、Clash、Shadowrocket）](https://www.leotun.com/ja/guides/singbox-subscription?utm_source=github&utm_content=client-09)
- [クライアントがウイルス対策ソフトに検出される / Mac で壊れていると表示される：まず確認してから許可](https://www.leotun.com/ja/guides/antivirus-false-positive?utm_source=github&utm_content=client-09)
- [macOS の「ネットワーク機能拡張」の許可とは何か、どう許可するか](https://www.leotun.com/ja/guides/macos-network-extension?utm_source=github&utm_content=client-09)
- [iOS でアプリをインストールできないとき](https://www.leotun.com/ja/guides/ios-app-store?utm_source=github&utm_content=client-09)

---
[LeoTun](https://www.leotun.com/ja?utm_source=github&utm_content=client-09) チーム編集 · ご質問は[お問い合わせページ](https://www.leotun.com/ja/contact?utm_source=github&utm_content=client-09)へ（グループ、メール、サポートの連絡先をまとめています）· 登録すると 4 時間の無料トライアル。招待した友だちが支払うと、お互いに 15 ポイント（「個人」プランで約 15 日分）を進呈。常時実施中のキャンペーンです
