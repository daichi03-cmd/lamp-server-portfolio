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

