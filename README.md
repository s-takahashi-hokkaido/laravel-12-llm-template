# Laravel 12 LLM Template

Laravel 12 + Docker環境で構築されたLLMアプリケーション開発用テンプレートプロジェクトです。

## 概要

このプロジェクトは、Laravel 12を使用したWebアプリケーション開発のためのテンプレートです。Docker環境で動作し、開発環境の統一と素早いセットアップを実現します。

## 技術スタック

- **フレームワーク**: Laravel 12
- **PHP**: 8.3
- **Webサーバー**: Nginx 1.20
- **データベース**: MySQL 8.0
- **コンテナ**: Docker / Docker Compose
- **フロントエンド**: Blade + Alpine.js
- **言語**: 日本語（ja）
- **タイムゾーン**: Asia/Tokyo

## プロジェクト構成

```
.
├── infra/              # Docker関連設定
│   ├── mysql/         # MySQL設定
│   ├── nginx/         # Nginx設定
│   └── php/           # PHP設定
├── src/               # Laravelアプリケーション
├── docker-compose.yml # Docker Compose設定
└── README.md          # このファイル
```

## 環境構築

### 前提条件

- Docker Desktop がインストールされていること
- Docker Compose が利用可能であること

### セットアップ手順

1. リポジトリをクローン

```bash
git clone <repository-url>
cd laravel-12-llm-template
```

2. Docker環境を起動

```bash
docker-compose up -d
```

3. 依存パッケージをインストール（初回のみ）

```bash
docker-compose exec app composer install
```

4. 環境変数ファイルを設定

```bash
cd src
cp .env.example .env
cd ..
```

5. アプリケーションキーを生成

```bash
docker-compose exec app php artisan key:generate
```

6. データベースマイグレーション実行

```bash
docker-compose exec app php artisan migrate
```

7. storageディレクトリのパーミッション設定（初回のみ）

```bash
docker-compose exec app chmod -R 777 storage bootstrap/cache
```

8. ブラウザでアクセス

```
http://localhost:8080
```

### Windows環境での注意事項

Windows + WSL + Docker環境で開発する場合、以下の点に注意してください：

**ブラウザアクセス時の問題**

`localhost:8080` でアクセスできない場合は、以下を試してください：

1. **IPv4アドレスを明示的に使用**
   ```
   http://127.0.0.1:8080
   ```

2. **WSLのIPアドレスを確認してアクセス**
   ```bash
   wsl hostname -I
   ```
   表示されたIPアドレス + `:8080` でアクセス

3. **Docker Desktopの設定確認**
   - 設定 → General → "Use WSL 2 based engine" が有効
   - 設定 → Resources → WSL Integration で適切なディストリビューションが選択されている

**パーミッションエラーが発生する場合**

WSL環境では、storageディレクトリのパーミッション問題が発生することがあります。以下のコマンドで修正してください：

```bash
docker-compose exec app chmod -R 777 storage bootstrap/cache
```

## よく使うコマンド

### Docker操作

```bash
# コンテナ起動
docker-compose up -d

# コンテナ停止
docker-compose down

# ログ確認
docker-compose logs -f app

# コンテナ再起動
docker-compose restart
```

### Laravel操作

```bash
# Artisanコマンド実行
docker-compose exec app php artisan <command>

# マイグレーション実行
docker-compose exec app php artisan migrate

# キャッシュクリア
docker-compose exec app php artisan cache:clear

# テスト実行
docker-compose exec app php artisan test
```

### Composer操作

```bash
# パッケージインストール
docker-compose exec app composer install

# パッケージ追加
docker-compose exec app composer require <package-name>

# パッケージ更新
docker-compose exec app composer update
```

## データベース接続

開発環境では以下の設定でデータベースに接続できます：

- ホスト: `localhost` (Windowsホストから) / `db` (コンテナ内から)
- ポート: `3306`
- データベース名: `laravel` (デフォルト)
- ユーザー名: `phper` (デフォルト)
- パスワード: `secret` (デフォルト)

詳細は `src/.env` ファイルで設定変更できます。

## 開発について

### Laravelアプリケーションの開発

Laravelアプリケーション固有の情報は [`src/README.md`](src/README.md) を参照してください。

### 開発ルールとコーディング規約

開発ルールやコーディング規約は [`src/CLAUDE.md`](src/CLAUDE.md) に記載されています。

主な開発方針：
- コントローラーは薄く保ち、ビジネスロジックはサービス層に委譲
- データアクセスはサービスから直接Eloquentを使用
- バリデーションはFormRequestクラスで管理
- フロントエンドはBlade + Alpine.jsで構築

### Git運用ルール

- 機能追加・修正時はfeatureブランチを作成
- develop/mainへの直接pushは禁止
- PRを作成してコードレビューを実施
- マイグレーション含む変更は慎重に扱う

詳細は [`src/CLAUDE.md`](src/CLAUDE.md) の「Git操作ルール」を参照してください。

## トラブルシューティング

### コンテナが起動しない

```bash
# コンテナの状態確認
docker-compose ps

# ログ確認
docker-compose logs

# コンテナを完全に削除して再構築
docker-compose down -v
docker-compose up -d --build
```

### データベース接続エラー

1. `.env` ファイルのDB設定を確認
2. DBコンテナが起動しているか確認: `docker-compose ps`
3. DBコンテナのログを確認: `docker-compose logs db`

### パーミッションエラー

```bash
docker-compose exec app chmod -R 777 storage bootstrap/cache
```

## ライセンス

このテンプレートプロジェクトはMITライセンスの下で公開されています。

---

**Note**: このプロジェクトはLaravel 12フレームワークをベースにしています。Laravelの詳細は[公式ドキュメント](https://laravel.com/docs)を参照してください。
