# Docker Web Development Kit

PHP + Nginx + MySQL + phpMyAdmin + Node.js の開発環境

## 使い方

```bash
git clone https://github.com/makohawk/docker-web-devkit.git
cd docker-web-devkit
cp .env.example .env
docker compose build
docker compose up -d
```

## アクセス

- Web: http://localhost:8080
- phpMyAdmin: http://localhost:81

## 開発

```bash
# PHPコンテナに入る
docker compose exec php bash

# Composerを使用
composer install
composer require package/name

# テスト実行
phpunit

# 停止
docker compose stop
docker compose down

# データも含めて削除
docker compose down -v
```

## PHPUnitのインストール
```bash
# PHPコンテナ内で
cd /var/www/html

# composer.jsonを作成（まだない場合）
cat > composer.json << 'EOF'
{
    "require-dev": {
        "phpunit/phpunit": "^10.0"
    },
    "require": {
        "php": ">=8.3"
    },
    "autoload": {
        "psr-4": {
            "Tests\\": "tests/"
        }
    }
}
EOF

# PHPUnitをプロジェクトにインストール
composer install

# .bashrcに追加（コンテナ内で永続化）
echo "alias phpunit='vendor/bin/phpunit'" >> ~/.bashrc

# 設定を再読込
source ~/.bashrc

# PHPUnitの確認
phpunit --version
```

## ディレクトリ構造

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
├── src/                         # PHPアプリケーションコード
│   ├── index.php               # (例: 初期アプリケーションのエントリーポイント)
│   └── tests/                  # テストファイル (srcディレクトリ内)
│       └── unit/               # ユニットテスト用のディレクトリ
│           └── SampleTest.php  # サンプルテストファイル
├── .env.example                 # 環境変数テンプレート
├── .gitignore
├── docker-compose.yml
└── README.md
```

## 構成

- PHP 8.3 + Composer + PHPUnit
- Node.js + npm
- MySQL 9.4
- Nginx

コードは `src/` に書く。テストは `src/tests/` に書く。

## ライセンス
MIT License