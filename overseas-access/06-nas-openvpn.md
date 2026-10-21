# 06 · Synology や QNAP の NAS を経路に通す方法

NAS は家の中でいちばん経路を必要としているのに、いちばん放っておかれがちな機器です。ダウンローダーは海外のリソースに届く必要があり、Docker はイメージを pull し、Cloud Sync は海外のクラウドストレージと同期し、Plex はメタデータを取得します。NAS では Hiddify は動きませんが、Synology にも QNAP にも OpenVPN クライアントが標準で入っています。当サイトの OpenVPN ページから .ovpn をダウンロードして読み込めばつながり、起動時には自動で再接続します。この記事では、Synology と QNAP それぞれの読み込み場所、重要なオプション「デフォルトゲートウェイを使用」、パスワードを変えたあとの対応、そして「外から NAS にアクセスする」のは別の話でプライベートネットワークを使う理由を説明します。

## まず 2 つのことを区別する

NAS 自体を経路に通す（ダウンロード、イメージの pull、海外のクラウドストレージとの同期）：OpenVPN を使います。この記事で扱う内容です。

外から NAS にアクセスする（出張先から共有フォルダーを開く、カメラを見る）：プライベートネットワーク（Tailscale）を使い、NAS とスマホを同じプライベートネットワークに入れます。「海外から中国の自宅のカメラや NAS を見る」をご覧ください。この役目はプライベートネットワークに任せましょう。OpenVPN は NAS を当サービスの経路につなぐもの、プライベートネットワークはあなたを自宅につなぐものです。

## Synology

1. 当サイトにログインし、OpenVPN ページで地域の国旗をクリックして、その地域の .ovpn ファイルをダウンロードします。
2. 「コントロールパネル」→「ネットワーク」→「ネットワークインターフェース」→「作成」→「VPN プロファイルの作成」→「OpenVPN（.ovpn ファイルのインポート経由）」と進み、当サイトのアカウントのメールアドレスとパスワードを入力します。
3. 「デフォルトゲートウェイを使用」（Use default gateway）：チェックを入れると NAS の外向きの通信がすべて経路を通ります（ダウンロード、Docker、Cloud Sync のどれにも効きます）。外すとトンネルを張るだけで、外向きの通信は手元の回線から出ていきます。ほとんどの場合はチェックを入れてください。
4. 「VPN 接続が切断された時に再接続」（Reconnect when the VPN connection is lost）にチェックを入れておけば、NAS の再起動や経路の揺らぎのあとも自動で復帰します。接続したら、パッケージで一度 pull を試してみてください。

## QNAP

1. 同じ要領で .ovpn をダウンロードします。
2. 「QVPN Service」→「VPN クライアント」→「追加」→「OpenVPN」と進み、ファイルを読み込んで、アカウントとパスワードを入力します。
3. 接続設定で「VPN を NAS のデフォルトゲートウェイとして使用」（Use VPN as the default gateway of the NAS）と自動再接続にチェックを入れて、接続します。

## 経路を通る通信、通らない通信

- 通る：Download Station / Download Station の BT や HTTP のタスク、Docker イメージの pull、Cloud Sync による Google Drive / Dropbox / OneDrive との同期、Plex や Emby のメタデータ取得、パッケージセンターの更新。
- 通らない：LAN 内であなたが NAS にアクセスする通信。もともと LAN の中なので影響しません。
- 中国にいて、中国のサイトには直接つなぎたい：NAS には振り分けのスクリプトがなく、OpenVPN をつなぐと NAS の外向きの通信はすべて経路を通ります。中国のダウンロード元が遅いなら、ダウンロードのタスクで中国のミラーアドレスを使うか、必要なときだけ経路につないでください。

## 知っておきたいこと

- 設定ファイルにはアカウント情報が含まれていて、パスワードを変えると NAS に入れたパスワードは使えなくなります。.ovpn をダウンロードし直して差し替えるか、VPN プロファイルのパスワードだけ変更してください。
- アカウントの期限が切れると NAS は切断されます。更新すれば自動で再接続し、設定を差し替える必要はありません。
- NAS は 1 台と数え、ほかの端末と同時接続の枠を共有します（個人 2 台、ファミリー 4 台、ビジネス 8 台）。
- 経路は UDP で動きます。家庭用ブロードバンドにつないだ NAS が UDP の制限にぶつかることは、まずありません。

## よくある質問

**「デフォルトゲートウェイを使用」にチェックを入れても、LAN から NAS にアクセスできますか？** できます。LAN の通信はゲートウェイを通らないので、共有フォルダー、Plex の再生、管理画面のどれにも影響しません。

**NAS を経路に通したら、Plex のリモート再生が遅くなりました。** Plex のリモート再生は NAS の外向きの通信を使うので、デフォルトゲートウェイにチェックを入れると経路を遠回りします。リモート再生なら、プライベートネットワークで NAS にアクセスするほうが直接的です。または、ダウンロードが必要なときだけ OpenVPN をつないでください。

**Download Station だけを経路に通せますか？** Synology と QNAP にはアプリ単位の振り分けがありません。細かく制御したいなら、Docker で OpenVPN 付きのダウンロード用コンテナ（qbittorrent + gluetun など）を動かし、それだけを経路に通してください。

**Linux ベースの NAS（TrueNAS、Unraid）はどうすればいいですか？** Linux のやり方でできます。openvpn パッケージを入れ、設定ファイルを /etc/openvpn/client/ に置きます。「Linux サーバーやコマンドラインツールを経路に通す方法」をご覧ください。

## 関連記事

- [OpenVPN ページ：クライアントのダウンロードと設定ファイル](https://www.leotun.com/ja/openvpn?utm_source=github&utm_content=overseas-access-06)
- [OpenVPN の使い方と選ぶべき場面](https://www.leotun.com/ja/guides/openvpn-setup?utm_source=github&utm_content=overseas-access-06)
- [海外から中国の自宅のカメラや NAS を見る](https://www.leotun.com/ja/guides/home-camera?utm_source=github&utm_content=overseas-access-06)
- [Linux サーバーやコマンドラインツールを経路に通す方法](https://www.leotun.com/ja/guides/linux-server?utm_source=github&utm_content=overseas-access-06)

この記事のウェブサイト版（ほかの言語版もあります）：https://www.leotun.com/ja/guides/nas-openvpn?utm_source=github&utm_content=overseas-access-06

---
[LeoTun](https://www.leotun.com/ja?utm_source=github&utm_content=overseas-access-06) チーム編集 · ご質問は[お問い合わせページ](https://www.leotun.com/ja/contact?utm_source=github&utm_content=overseas-access-06)へ（グループ、メール、サポートの連絡先をまとめています）· 登録すると 4 時間の無料トライアル。招待した友だちが支払うと、お互いに「個人」プラン 20 日分を進呈。常時実施中のキャンペーンです
