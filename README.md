# lamp-server-portfolio
LAMP + Zabbix + Postfix構成で作ったインフラ構築ポートフォリオ

## 📷 スクリーンショット

- WordPressの初期画面表示
- Zabbixで異常を検知して通知が送られる画面
- Postfixでメールが届いている様子

## ✍️ 構築の工夫ポイント

- fail2banとUFWでSSHなどへの不正アクセス対策
- logrotateでログ肥大化を防止
- Zabbix → Postfix → メール通知の流れを確認
- WordPressからMariaDBへの書き込み確認済み

## 📌 今後の展望

- AWSでの再構築予定（EC2 + RDS + CloudWatch）
- Ansibleによる構築自動化も検討中
