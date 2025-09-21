# Docker Web Development Kit

PHP, Nginx, MySQL, phpMyAdmin, Node.js を含んだWeb開発環境です。

## 構成

- **PHP:** 8.3
- **Web Server:** Nginx
- **Database:** MySQL 9.4
- **Database Tool:** phpMyAdmin
- **JavaScript:** Node.js (npmを含む)
- **PHP Tools:** Composer, PHPUnit

## 使い方

```bash
# 1. リポジトリをクローン
git clone https://github.com/makohawk/docker-web-devkit.git
cd docker-web-devkit

# 2. 環境変数を設定
cp .env.example .env

# 3. コンテナをビルドして起動
docker compose build
docker compose up -d
```

## コンテナの管理

```bash
# コンテナを停止
docker compose stop

# コンテナを削除
docker compose down

# データボリュームも含めて完全に削除
docker compose down -v
```

## アクセス

- **Web:** [http://localhost:8080](http://localhost:8080)
- **phpMyAdmin:** [http://localhost:81](http://localhost:81)
  - **サーバー:** `mysql`
  - **ユーザー名:** (`.env`ファイルで設定した `MYSQL_USER`)
  - **パスワード:** (`.env`ファイルで設定した `MYSQL_PASSWORD`)

## 開発

### コンテナへのアクセス

開発作業は主にPHPコンテナ内で行います。

```bash
# PHPコンテナに入る
docker compose exec php bash
```

### PHP (Composer)

```bash
# (PHPコンテナ内で)

# 依存パッケージをインストール
composer install

# パッケージを追加
composer require vendor/package-name
```

### Node.js

```bash
# (PHPコンテナ内で)

# package.json がまだない場合
npm init -y

# package.json に基づいて依存パッケージをインストール
npm install

# パッケージを追加
npm install <package-name>
```

## PHPUnitのインストール

PHPUnitをプロジェクトに導入し、`phpunit`コマンドのエイリアスを設定する手順です。

```bash
# PHPコンテナ内で作業します
docker compose exec php bash

# プロジェクトルートに移動
cd /var/www/html

# composer.json がまだない場合、以下のコマンドで作成
cat > composer.json << 'EOF'
{
    "require": {
        "php": ">=8.3"
    },
    "require-dev": {
        "phpunit/phpunit": "^10.0"
    },
    "autoload": {
        "psr-4": {
            "App\\": "src/",
            "Tests\\": "src/tests/"
        }
    }
}
EOF

# Composerを使ってPHPUnitをインストール
composer install

# `phpunit`コマンドのエイリアスを永続化設定
echo "alias phpunit='vendor/bin/phpunit'" >> ~/.bashrc

# 設定を現在のシェルに再読み込み
source ~/.bashrc

# バージョンを確認してインストール完了
phpunit --version
```

## テスト (PHPUnit)

**アプリケーションコード**は `src/` ディレクトリに配置します。
<br/>
**テストコード**は `src/tests/` ディレクトリに配置します。

```bash
# (PHPコンテナ内で、エイリアス設定後)
phpunit tests/Unit/SampleTest.php
```

## ディレクトリ構造

アプリケーションコードとテストコードの配置場所です。

```
docker-web-devkit/
├── docker/
│   ├── nginx/
│   │   ├── Dockerfile
│   │   └── default.conf
│   ├── php/
│   │   └── Dockerfile
│   └── mysql/
│       ├── Dockerfile
│       └── my.cnf
├── src/                          # PHPアプリケーションコード
│   ├── index.php                # (例: 初期アプリケーションのエントリーポイント)
│   └── tests/                   # テストファイル (srcディレクトリ内)
│       └── unit/                # ユニットテスト用のディレクトリ
│           └── SampleTest.php   # サンプルテストファイル
├── .env.example                  # 環境変数テンプレート
├── .gitignore
├── docker-compose.yml
└── README.md
```