
# Nextcloud 構築手順（LAMPサーバー）

このドキュメントでは、LAMPサーバー上に自前クラウドストレージサービス「Nextcloud」を構築する手順をまとめます。

---

## ✅ 構築の目的

- WordPressと並ぶWebアプリケーションとして、NextcloudをLAMP上に導入
- 自社ファイル管理、クラウドストレージ環境の構築
- 将来的にPostfixを連携してユーザー通知を可能にする（3.3で実施予定）

---

## ✅ 1. 必要なPHPモジュールのインストール

Nextcloudで必要なPHP拡張をインストールします。

```bash
sudo apt install php-curl php-gd php-xml php-mbstring php-zip php-bz2 php-intl php-imagick php-bcmath php-gmp -y

2. Nextcloud のダウンロードと配置
cd /var/www/　　　　ApacheのWebルートディレクトリ /var/www/ に移動します。
sudo curl -LO https://download.nextcloud.com/server/releases/nextcloud-28.0.4.zip　　Nextcloudの公式サイトから ZIP アーカイブ（バージョン28.0.4）をダウンロードします。
-L：リダイレクトを自動的にたどる（httpsリダイレクト対応）-O：出力ファイル名をURLと同じ名前にする（保存名を自動決定）　nextcloud-28.0.4.zip というファイルが /var/www/ に保存されます。

sudo unzip nextcloud-28.0.4.zip　　ダウンロードした ZIP ファイルを展開します。/var/www/nextcloud/ というディレクトリが作成され、中にNextcloud本体ファイルが展開されます。
※※※unzipがないのでsudo apt install unzip -y

sudo chown -R www-data:www-data nextcloud　　nextcloud ディレクトリ以下すべてのファイルとフォルダの所有者を www-data に変更します。-R：再帰的にすべてのサブディレクトリ・ファイルに適用
sudo chmod -R 755 nextcloud　nextcloud 以下のファイル/ディレクトリに実行・読み込み権限を設定します。セキュアかつ動作に必要なアクセス権限を適用。Apacheが読み込めるようにします。

3. Apache仮想ホストの設定
Nextcloud専用の仮想ホストを作成します。
sudo nano /etc/apache2/sites-available/nextcloud.conf

以下の内容を貼り付けて保存：
<VirtualHost *:80>
    ServerName yourdomain.example.com　　　yourdomain.example.comではなく
    DocumentRoot /var/www/nextcloud

    <Directory /var/www/nextcloud>
        Require all granted
        AllowOverride All
        Options FollowSymLinks MultiViews
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/nextcloud_error.log
    CustomLog ${APACHE_LOG_DIR}/nextcloud_access.log combined
</VirtualHost>

仮想ホストを有効化：
sudo a2ensite nextcloud.conf
sudo a2enmod rewrite headers env dir mime
sudo systemctl reload apache2

 4. MariaDB にNextcloud用DBとユーザーを作成
sudo mysql -u root -p

ログイン後、以下のコマンドを実行：
CREATE DATABASE nextcloud;
CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY 'securepassword';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

5. ブラウザから初期セットアップ
以下のURLにアクセス：
http://yourdomain.example.com　　windows画面のメモ帳からWindowsでの名前解決設定（cloud.local）例サーバーのIP192.168.56.10 cloud.local
セットアップ画面で以下を設定：

管理者アカウントの作成

ユーザー名 admin パスワード　password
FQDN（ドメイン）が未設定の場合は /etc/hosts を使って一時的に解決できます。

Postfixによるメール通知は後続の 3.3 Nextcloudメール連携 にて設定します。

項目	入力値	補足
データベースのユーザー名	nextclouduser	CREATE USER で作成済み
データベースのパスワード	securepassword	上記ユーザーのパスワード
データベース名	nextcloud	CREATE DATABASE で作成済み
データベースのホスト名	localhost（そのまま）	MariaDBが同一サーバー内ならOK


