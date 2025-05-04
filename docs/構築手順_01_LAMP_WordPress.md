# 構築手順書（LAMP + Zabbix + Postfix）

## 1. サーバー構成

| サーバー名     | 主な構成                                             |
|----------------|------------------------------------------------------|
| LAMPサーバー   | Apache / PHP / MariaDB / WordPress / ufw / fail2ban / logrotate |
| Zabbixサーバー | Zabbix / Postfix                                     |

---

## 2. LAMPサーバー構築手順

### 2.1 Apacheインストール

```bash
sudo apt update
sudo apt install apache2 -y

### 2.2 PHPインストール

```bash
sudo apt install php libapache2-mod-php php-mysql -y
php -v

### 2.3 MariaDBインストール・初期設定

```bash
sudo apt install mariadb-server -y
sudo systemctl start mariadb
sudo mysql_secure_installation

### 2.4 WordPress配置　（ダウンロード＆展開）
# 配置ディレクトリに移動
cd /var/www/html

# デフォルトの index.html を削除
sudo rm index.html

# 日本語版 WordPress をダウンロード
sudo wget https://ja.wordpress.org/latest-ja.tar.gz

# ダウンロードしたアーカイブを展開
sudo tar -xvzf latest-ja.tar.gz

# 解凍された wordpress ディレクトリ内のファイルを移動
sudo mv wordpress/* .

# 空の wordpress ディレクトリを削除
sudo rmdir wordpress

# 使用済みのアーカイブファイルを削除
sudo rm latest-ja.tar.gz

# 所有権を Apache 実行ユーザーに変更
sudo chown -R www-data:www-data /var/www/html

# ディレクトリとファイルのパーミッションを一括設定
sudo chmod -R 755 /var/www/html

ブラウザ確認

http://<LAMPサーバーのIPアドレス>/


### 2.5 WordPress用DB・ユーザー作成（MariaDB）

#### ① MariaDBでWordPress用のDBとユーザーを作成

```bash
sudo mysql -u root -p



CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

### 2.6 WordPress接続設定ファイル（wp-config.php）の編集手順

# WordPressのインストールディレクトリに移動
cd /var/www/html

# 設定ファイルのサンプルをコピーして wp-config.php を作成
sudo cp wp-config-sample.php wp-config.php

# 設定ファイルをエディタで開く
sudo nano wp-config.php

// ↓データベース名
define( 'DB_NAME', 'wordpress' );

// ↓データベースユーザー
define( 'DB_USER', 'wpuser' );

// ↓そのユーザーのパスワード
define( 'DB_PASSWORD', 'password' );

1. 編集後に Ctrl + O を押す
   → 「ファイル名を確認して Enter」と表示されるので、そのまま Enter を押す

2. 保存が完了したら Ctrl + X を押して nano を終了する


### 2.7 WordPressディレクトリのパーミッション設定

```bash
cd /var/www/html
sudo chown -R www-data:www-data .
sudo find . -type d -exec chmod 755 {} \;
sudo find . -type f -exec chmod 644 {} \;

### 2.8 WordPress初期セットアップ（ブラウザ操作）
1. Webブラウザを開く

2. 以下のURLにアクセス
   http://<LAMPサーバーのIPアドレス>/

3. 初期セットアップ画面が表示されることを確認

4. 以下の項目を入力してインストールを進める：

   ・サイトのタイトル  
   ・ユーザー名（WordPress管理者アカウント）  
   ・パスワード（強力なものを推奨）  
   ・メールアドレス  
   ・検索エンジンによるインデックス許可の有無（任意でチェック）

5. 「WordPressをインストール」ボタンをクリック

6. 「成功しました！」と表示されればセットアップ完了

7. ログイン画面（http://<LAMPサーバーのIPアドレス>/wp-login.php）にアクセスし、設定したアカウントでログインできることを確認

### 2.9 セキュリティ設定（UFW・fail2ban・logrotate）

#### UFW（ファイアウォール）設定

```bash
sudo apt install ufw -y
sudo ufw allow OpenSSH
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
sudo ufw status verbose


#### fail2ban（不正ログイン防止）設定
sudo apt install fail2ban -y
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local

[sshd]
enabled = true
port    = ssh
logpath = %(sshd_log)s
backend = systemd

sudo systemctl restart fail2ban
sudo fail2ban-client status sshd

#### logrotate（ログローテーション）設定
sudo apt install logrotate -y
sudo nano /etc/logrotate.d/wordpress

/var/log/wordpress.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    create 644 www-data www-data
    sharedscripts
    postrotate
        systemctl reload apache2 > /dev/null 2>/dev/null || true
    endscript
}







