# Laravel Application

このディレクトリには、Laravel 12アプリケーションのソースコードが含まれています。

## アプリケーション概要

- **Laravel バージョン**: 12.x
- **PHP バージョン**: 8.3
- **言語**: 日本語（ja）
- **タイムゾーン**: Asia/Tokyo
- **データベース**: MySQL 8.0

## ディレクトリ構成

```
app/
├── Console/           # Artisanコマンド
│   ├── Commands/     # カスタムコマンド
│   └── Kernel.php    # スケジューラー設定
├── Http/
│   ├── Controllers/  # コントローラー（薄く保つ）
│   ├── Requests/     # FormRequest（バリデーション）
│   └── Middleware/   # ミドルウェア
├── Models/           # Eloquent Model
├── Services/         # ビジネスロジック層
├── Exceptions/       # カスタム例外
└── DTOs/            # Data Transfer Objects

resources/
├── views/           # Bladeテンプレート
│   └── layouts/    # レイアウトファイル
└── js/             # JavaScriptファイル

database/
├── migrations/      # マイグレーション
└── seeders/        # シーダー

config/             # 設定ファイル
routes/             # ルート定義
tests/              # テスト
```

## アーキテクチャ

### 基本方針

このプロジェクトは、以下のアーキテクチャパターンを採用しています：

```
Controller (薄い)
    ↓ FormRequest でバリデーション
Service (ビジネスロジック)
    ↓
Model + Scope (データアクセス)
    ↓
Database
```

### 各層の責務

- **Controller**: HTTPリクエストの受付とレスポンス返却のみ（薄く保つ）
- **FormRequest**: バリデーションルール定義
- **Service**: ビジネスロジック、トランザクション制御
- **Model**: データベーステーブルとの対応、リレーション定義、Scopeメソッド
- **Repository**: 不採用（サービスから直接Eloquentを使用）

## 開発ルール

詳細な開発ルールは [CLAUDE.md](CLAUDE.md) を参照してください。

### コーディング規約

- すべてのpublicメソッドにPHPDocを記述
- 複雑なビジネスロジックには日本語コメントを記述
- N+1問題を防ぐため、リレーションは必ず `with()` で事前ロード
- 検索結果は必ずページネーション（`paginate()`）を使用

### 命名規則

- **Service**: `{Domain}Service` (例: `UserService`, `OrderService`)
- **FormRequest**: `{Domain}{Action}Request` (例: `UserStoreRequest`)
- **Command**: `{Action}Command` (例: `SendNotificationCommand`)
- **Model Scope**: `scope{Condition}` (例: `scopeActive`, `scopeRecent`)

## フロントエンド

- **テンプレートエンジン**: Blade
- **JavaScriptフレームワーク**: Alpine.js
- **理由**: 軽量でBladeと相性が良く、学習コストが低い

## テスト

### テストディレクトリ構成

```
tests/
├── Feature/         # 機能テスト
│   ├── Http/
│   │   └── Controllers/
│   └── Console/
│       └── Commands/
└── Unit/           # ユニットテスト
    ├── Services/
    └── Requests/
```

### 実装推奨テスト

- サービス層のユニットテスト
- FormRequestのバリデーションテスト
- 重要なコマンドの統合テスト
- コントローラーの機能テスト

### テスト実行

```bash
# すべてのテスト実行
php artisan test

# 特定のテスト実行
php artisan test --filter UserServiceTest

# カバレッジ付きで実行
php artisan test --coverage
```

## データベース

### マイグレーション

```bash
# マイグレーション実行
php artisan migrate

# ロールバック
php artisan migrate:rollback

# リフレッシュ
php artisan migrate:refresh

# マイグレーション作成
php artisan make:migration create_users_table
```

### シーダー

```bash
# シーダー実行
php artisan db:seed

# 特定のシーダー実行
php artisan db:seed --class=UserSeeder

# シーダー作成
php artisan make:seeder UserSeeder
```

## よく使うArtisanコマンド

```bash
# コントローラー作成
php artisan make:controller UserController

# モデル作成（マイグレーション付き）
php artisan make:model User -m

# FormRequest作成
php artisan make:request UserStoreRequest

# サービス作成（手動）
# app/Services/{Domain}Service.php を作成

# コマンド作成
php artisan make:command SendNotificationCommand

# キャッシュクリア
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear

# 設定キャッシュ
php artisan config:cache
php artisan route:cache
```

## 環境変数

主要な環境変数（`.env`ファイル）：

```env
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_TIMEZONE=Asia/Tokyo

APP_LOCALE=ja
APP_FALLBACK_LOCALE=en
APP_FAKER_LOCALE=ja_JP

DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=phper
DB_PASSWORD=secret
```

## パフォーマンス対策

- **Eager Loading**: N+1問題を防ぐため、リレーションは必ず `with()` で事前ロード
- **インデックス**: 検索条件として使われるカラムにはインデックスを設定
- **ページネーション**: 検索結果は必ず `paginate()` を使用
- **キャッシュ**: 頻繁にアクセスされる静的データはキャッシュを活用

## エラーハンドリング

### 標準例外の使用

- `ValidationException`: バリデーションエラー
- `ModelNotFoundException`: モデルが見つからない
- `QueryException`: データベースエラー

### カスタム例外

特定のドメインロジックで専用例外が必要な場合のみ追加：

```php
// app/Exceptions/PaymentException.php
namespace App\Exceptions;

class PaymentException extends \Exception
{
    // ...
}
```

## 定期実行タスク

Laravelスケジューラーを使用：

```php
// app/Console/Kernel.php
protected function schedule(Schedule $schedule)
{
    $schedule->command('send:notifications')
             ->dailyAt('09:00');
}
```

システムのcronで以下を登録：

```
* * * * * cd /path-to-project && php artisan schedule:run >> /dev/null 2>&1
```

## 関連ドキュメント

- [開発ルールとコーディング規約](CLAUDE.md)
- [プロジェクト全体のREADME](../README.md)
- [Laravel公式ドキュメント](https://laravel.com/docs)
