# Postfix構築手順（送信専用メールサーバー）

本ドキュメントでは、LAMPサーバー上にPostfixを送信専用SMTPとして構築し、WordPressやNextcloudなどのアプリケーションからメール通知が送れるようにする手順をまとめます。

---

## ✅ 1. Postfixのインストール

```bash
sudo apt update
sudo apt install postfix -y

インストール中のプロンプトで以下を選択してください：

「インターネットサイト」 を選択
システムメール名は example.com や localhost など適切なFQDNを入力

2. mailutilsのインストール（テスト送信用）

sudo apt install mailutils -y

3.テストメール送信
次のコマンドでテストメールを送信して、メール送信が成功するかを確認します。

echo "これはPostfixのテストメールです" | mail -s "Postfixテスト" your_email@example.com

※ your_email@example.com の部分は、自身の受信確認可能なメールアドレスに置き換えてください。

4. セキュリティ設定（送信元制限）
送信元をLAMPサーバー内部に限定して、外部からのスパム利用を防ぎます。

sudo postconf -e 'smtpd_sender_restrictions=permit_mynetworks, reject'
sudo systemctl restart postfix

5. ログ確認（必要に応じて）

sudo tail -f /var/log/mail.log

構成の目的
LAMP構成内の WordPress / Nextcloud / fail2ban / Zabbix からの通知メールをPostfix経由で送信

Send-Only構成のため、外部からの受信は行いません

Gmailなどにメールが届かない場合は、SPF/DKIM/逆引きDNSの設定を検討してください（今回の構成では省略）

