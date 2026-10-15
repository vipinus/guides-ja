# 05 · Linux サーバーやコマンドラインツールを経路に通す方法

Linux で経路を使う方法は 2 つあり、目的で選びます。マシン全体を通したい場合（Docker イメージの取得、海外のネットワークが必要なサービスの運用、無人運転）は OpenVPN を使います。設定ファイルを 1 つ /etc/openvpn/client/ に置き、起動時に自動で立ち上げます。いくつかのコマンド（git、pip、npm、curl）だけを通したい場合は Webプロキシを使います。プロキシのアドレスを環境変数 http_proxy に入れるだけで、ほかの通信には影響しません。デスクトップ版の Linux なら NetworkManager に読み込むこともできます。この記事では 3 つの方法すべてについて、パスワードファイルの書き方と確認用のコマンドも含めて説明します。

## 方法 1：マシン全体を経路に通す（OpenVPN）

1. クライアントをインストールします。Debian / Ubuntu は sudo apt install openvpn、Fedora は sudo dnf install openvpn、Arch は sudo pacman -S openvpn です。
2. 当サイトにログインし、OpenVPN ページで地域の国旗をクリックして .ovpn をダウンロードします。Linux 向けの設定にはアカウントとパスワードが入っていません（NetworkManager は認証情報入りのファイルを受け付けないため）。そこで、パスワードファイルを別に作ります。2 行で、1 行目に当サイトのメールアドレス、2 行目にパスワードを書き、chmod 600 にします。
3. .ovpn を /etc/openvpn/client/<地域>.conf にコピーし、ファイル内の auth-user-pass のあとにパスワードファイルのパスを書き足します。
4. sudo systemctl enable --now openvpn-client@<地域> を実行します。状態は systemctl status openvpn-client@<地域> で確認し、curl -s https://ipinfo.io/country で選んだ地域が表示されるか確かめます。
5. 地域を変えるときは、別の設定をダウンロードして別のユニットを有効にします。同時に動かすのは 1 つだけにしてください。

## 方法 2：コマンドラインツールだけを経路に通す（Webプロキシ）

当サイトのWebプロキシページに表示されるプロキシのアドレスは、アカウントとパスワードと組み合わせてそのまま環境変数に入れられます：export https_proxy=https://ユーザー名:パスワード@プロキシのアドレス export http_proxy=$https_proxy。パスワードに記号が含まれる場合は URL エンコードが必要です。これで、このターミナルの curl、wget、git、pip、npm はすべて経路を通り、ほかのプログラムには影響しません。

Docker デーモンはシェルの環境変数を読まないので、/etc/systemd/system/docker.service.d/proxy.conf の Environment= に書いて docker を再起動します。apt は /etc/apt/apt.conf.d/proxy.conf に Acquire::https::Proxy を書きます。

この方法にはそもそも切れる「接続」がなく、root 権限も不要で、ルーティングも変えません。会社のサーバーや、ダウンロードだけ速くしたい場合に向いています。対象になるのはプロキシ変数に従うプログラムだけで、ほかの通信はそのままです。

## 方法 3：デスクトップ版 Linux の NetworkManager

1. network-manager-openvpn-gnome をインストールします（当サイトの OpenVPN ページからワンクリックでソフトウェアセンターを開けます）。
2. 「設定」→「ネットワーク」→「VPN」→「+」→「ファイルからインポート」で .ovpn を選び、当サイトのメールアドレスとパスワードを入力して保存します。
3. VPN のオン・オフは上部バーかトレイから切り替えます。起動時に自動で接続したい場合は、有線・無線接続の設定で「VPN に自動接続」（Automatically connect to VPN）にチェックを入れます。

## 確認とトラブルシューティング

- 出口の確認：curl -s https://ipinfo.io の国が、選んだ地域になっていること。
- DNS の確認：resolvectl status で、VPN インターフェースの DNS が経路から配布されたものになっていること。漏れている場合は、設定に dhcp-option DNS を追加するか、update-systemd-resolved スクリプトを使います。
- AUTH_FAILED：パスワードファイルが間違っているか、アカウントの期限が切れています。パスワードを変更した場合は、パスワードファイルも更新してください。
- TLS ハンドシェイクのタイムアウト：サーバーへの UDP が届いていません。地域かネットワークを変えてください。クラウドサーバーでは、セキュリティグループで送信方向の UDP を許可する必要があります。
- サーバーが中国にあり、中国国内のソースには直接つなぎたい：OpenVPN を接続すると、送信する通信はすべて経路を通ります。apt / pip / npm のソースを中国のミラーに変えるか、方法 2 に切り替えて特定のコマンドだけを通してください。

## 知っておきたいこと

- Linux マシン 1 台は 1 台と数え、ほかの端末と同時接続の枠を共有します（個人 2 台、ファミリー 4 台、ビジネス 8 台）。方法 2 の Webプロキシも接続ごとに数えられ、枠に含まれます。
- 設定ファイルとパスワードファイルはアカウントそのものと同じです。git リポジトリにコミットしたり、イメージに入れたりしないでください。
- パスワードを変更すると、古い設定はすぐに使えなくなります。これは意図した失効の仕組みです。パスワードファイルを更新すれば使えます。
- クラウドサーバーで経路を使う場合は、事業者の利用規約に従ってください。経路が記録するのは接続時間と通信量の合計だけです。

## よくある質問

**Linux 用の設定にアカウントとパスワードが入っていないのはなぜですか？** NetworkManager は設定を読み込むとき、認証情報入りのファイルを受け付けずに読み込みエラーを出します。そのため Linux 向けに発行する設定は、ご自身で入力する形にしています。systemd の方法では、パスワードファイルに書いてください。

**Docker だけを経路に通せますか？** できます。Docker デーモンにだけプロキシ変数を設定するか（方法 2）、OpenVPN 入りのコンテナ（gluetun）を動かして、特定のコンテナだけを経路に通します。

**Linux でプライベートネットワーク（Tailscale）を使うには？** 公式スクリプトで 1 行でインストールでき、tailscale up --login-server=<当サイトのコントロールサーバー> でログインします。外からこのマシンにアクセスしたい場合や、一度設定したら常時接続にしておきたい場合に向いています。ダウンロードを速くしたいだけなら、OpenVPN かプロキシのほうが簡単です。

**ルーターの OpenWrt でも同じですか？** OpenWrt では luci-app-openvpn をインストールして .ovpn をアップロードすれば、LAN 全体が経路を通ります。当サイトのファームウェアを入れたルーターならこの作業は不要で、アカウントでログインするだけです。

## 関連記事

- [OpenVPN ページ：クライアントのダウンロードと設定ファイル](https://www.leotun.com/ja/openvpn?utm_source=github&utm_content=overseas-access-05)
- [OpenVPN の使い方と選ぶべき場面](https://www.leotun.com/ja/guides/openvpn-setup?utm_source=github&utm_content=overseas-access-05)
- [Synology や QNAP の NAS を経路に通す方法](https://www.leotun.com/ja/guides/nas-openvpn?utm_source=github&utm_content=overseas-access-05)
- [Webプロキシとは何か、いつ使うか](https://www.leotun.com/ja/guides/web-proxy?utm_source=github&utm_content=overseas-access-05)

この記事のウェブサイト版（ほかの言語版もあります）：https://www.leotun.com/ja/guides/linux-server?utm_source=github&utm_content=overseas-access-05

---
[LeoTun](https://www.leotun.com/ja?utm_source=github&utm_content=overseas-access-05) チーム編集 · ご質問は[お問い合わせページ](https://www.leotun.com/ja/contact?utm_source=github&utm_content=overseas-access-05)へ（グループ、メール、サポートの連絡先をまとめています）· 登録すると 24 時間の無料トライアル。招待した友だちが支払うと、お互いに「個人」プラン 20 日分を進呈。長期実施中のキャンペーンです
