# 01 · Cisco AnyConnect：各プラットフォームでのインストール、接続、アップデート

> ウェブサイト版（より詳しい内容）：https://www.leotun.com/ja/guides/anyconnect-china?utm_source=github&utm_content=client-01

AnyConnect は Cisco の企業向け VPN クライアントで、現在の正式名称は **Cisco Secure Client** です。使い方は変わりません。証明書ファイルもプロファイルの読み込みも不要で、アドレス、アカウント、パスワードを入力すれば接続できます。中国で使えるかどうかや、つながらないときに何に切り替えるかは、「[中国から海外のサービスへ ガイド 02](https://github.com/vipinus/guides-zh-CN/blob/main/overseas-access/02-anyconnect-in-china.md)」（中国語）をご覧ください。

## インストール

| プラットフォーム | インストールするもの | 入手先 |
|---|---|---|
| Windows 10 以降 | Cisco Secure Client 5.x | [ウェブサイトの Cisco ページ](https://www.leotun.com/ja/anyconnect?utm_source=github&utm_content=client-01) |
| Windows 7 / 8 | AnyConnect 4.9（対応する最後のバージョン。更新は終了しています） | 同上 |
| macOS | Cisco Secure Client 5.x | 同上 |
| iOS | Cisco Secure Client | App Store。ストアにない場合は、同じプロトコルを使うオープンソースの OpenConnect をインストールしてください |
| Android | Cisco Secure Client | Google Play、またはウェブサイトのページの APK |
| Linux | ウェブサイトのページのインストーラー、またはディストリビューションのリポジトリの openconnect | 同上 |

Windows にはオープンソースの OpenConnect-GUI もあり、同じアカウントで使えます。

## 4 つのステップで接続

1. クライアントを開き、アドレス欄にサーバーのアドレスを入力します。LeoTun をお使いの方は、ウェブサイトで**地域の国旗をクリック**すると、その地域のアドレスをコピーできます。
2. アカウント（登録したメールアドレス）とパスワードを入力します。
3. 「Connected」（接続済み）と表示されたら、IP を表示するウェブページを開き、出口が選んだ地域になっていることを確認します。
4. 地域を変えるときは、別のアドレスを使います。クライアントは使ったアドレスを覚えているので、次回からはドロップダウンリストから選べます。

## アップデート

先にアンインストールする必要はありません。古いバージョンの上に新しいバージョンを上書きインストールすれば、保存したアドレスはそのまま残ります。スマホはアプリストアから更新します。OS の大型アップデートのあとは、まずクライアントを更新し、それから接続の問題を調べてください。

## よく出るメッセージ

| メッセージ | 意味 |
|---|---|
| Login failed（ログイン失敗） | アカウントかパスワードが違う、またはアカウントの期限が切れている |
| Untrusted server certificate（信頼されていないサーバー証明書） | システムの時計がずれているか、今のネットワークが暗号化通信に割り込んでいる可能性が高いです。別のネットワークで試してください |
| Connection attempt has failed（接続に失敗しました） | このアドレスに一時的に到達できません。地域を切り替えてください |

---
[LeoTun](https://www.leotun.com/ja?utm_source=github&utm_content=client-01) チーム編集 · ご質問は[お問い合わせページ](https://www.leotun.com/ja/contact?utm_source=github&utm_content=client-01)へ（グループ、メール、サポートの連絡先をまとめています）· 登録すると 4 時間の無料トライアル。招待した友だちが支払うと、お互いに 15 ポイント（「個人」プランで約 15 日分）を進呈。常時実施中のキャンペーンです
