### 2.4 WordPress配置（ダウンロード＆展開）

#### 配置ディレクトリに移動
```bash
cd /var/www/html
```
#### デフォルトの index.html を削除
```bash　　
sudo rm index.html
```
#### 日本語版 WordPress をダウンロードしそのアーカイブを展開
```bash
sudo wget https://ja.wordpress.org/latest-ja.tar.gz　　
sudo tar -xvzf latest-ja.tar.gz
```
#### 解凍された WordPress のファイルを現在のディレクトリに移動
```bash
sudo mv wordpress/* .
```
#### 空のwordpressディレクトリと使用済みのアーカイブファイルを削除
```bash
sudo rmdir wordpress　　　WordPressの空のディレクトリを削除します。
sudo rm latest-ja.tar.gz　　使用済みのアーカイブファイルを削除
```
#### 所有権を Apache 実行ユーザーに変更しディレクトリとファイルのパーミッションを一括設定
```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html　
```
### 2.5 WordPress用DB・ユーザー作成（MariaDB）

 MariaDBでWordPress用のDBとユーザーを作成

```bash
sudo mysql -u root -p
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

```
### 2.6 WordPress接続設定ファイル（wp-config.php）の編集手順

#### WordPressのインストールディレクトリに移動

```bash
cd /var/www/html
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wpuser' );
define( 'DB_PASSWORD', 'password' );
編集後に Ctrl + O を押す
→ 「ファイル名を確認して Enter」と表示されるので、そのまま Enter を押す

保存が完了したら Ctrl + X を押して nano を終了する
```
### 2.7 WordPressディレクトリのパーミッション設定

```bash
cd /var/www/html
sudo chown -R www-data:www-data .
sudo find . -type d -exec chmod 755 {} \;
sudo find . -type f -exec chmod 644 {} \;
```

### 2.8 WordPress初期セットアップ（ブラウザ操作）

1. 以下のURLにアクセス:
http://<LAMPサーバーのIPアドレス>/


2. 初期セットアップ画面が表示されることを確認。

3. 以下の項目を入力してインストールを進めます：

- サイトのタイトル
- ユーザー名（WordPress管理者アカウント）
- パスワード（強力なものを推奨）
- メールアドレス
- 検索エンジンによるインデックス許可の有無（任意でチェック）

4. 「WordPressをインストール」ボタンをクリック。

5. 「成功しました！」と表示されればセットアップ完了。

