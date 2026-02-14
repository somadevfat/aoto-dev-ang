# バックエンド コーディング規約 & ベストプラクティス（Bun / ElysiaJS / Drizzle）

このファイルはバックエンド実装時に **必ず参照** すること。
これに違反するコードは憲法違反として扱われる。

## 1. 原則（Principles）

- **Strict TypeScript**: `any` 型の使用を禁止。`unknown` を使用し、バリデーションを経て型安全にする。
- **Immutable**: 変数は可能な限り `const` を使用。データの変更は新しいオブジェクトの生成で行う。
- **Fail Fast**: エラーは早期に検出し、例外を投げるか Result 型を返す。

## 2. ディレクトリ構造（DDD / Bounded Context）

```
src/
├── context/
│   ├── Order/             # 境界づけられたコンテキスト（Bounded Context）
│   │   ├── domain/        # ドメイン層
│   │   │   ├── model/     # Entity, VO
│   │   │   ├── service/   # Domain Service
│   │   │   └── tests/     # Domain Tests (Unit)
│   │   ├── usecase/       # アプリケーション層
│   │   │   ├── service/   # Application Service
│   │   │   └── tests/     # UseCase Tests (Unit with Mock)
│   │   ├── infrastructure/# インフラ層
│   │   │   ├── repository/# Repository Impl
│   │   │   └── tests/     # Infra Tests (Integration with DB)
│   │   └── presentation/  # UI層（Controller）
│   │       ├── controller/# Elysia Handlers
│   │       └── tests/     # E2E API Tests
│   └── User/              # 別コンテキスト...
├── shared/                # 全コンテキスト共通（Kernel）
│   ├── domain/
│   └── infrastructure/    # DB Connection, Environment
└── index.ts               # App Entrypoint
```

## 2.1 テスト配置ルール（Colocation）

- 原則として、テスト対象のコードがあるディレクトリと同じ階層に `tests/` ディレクトリを作成し、そこに格納する。
- ファイル名は `[対象ファイル名].test.ts` とする。

## 3. 実装ルール

### 3.1 Drizzle ORM (Database)

- **Schema Definition**: すべてのテーブル定義は `src/infrastructure/db/schema.ts` に集約しない。ドメインごとに分割する。
- **Type Safety**: `InferSelectModel`, `InferInsertModel` を使用して型を自動生成する。
- **Repository Pattern**: Controllerから直接Drizzleを呼ばない。必ず `Repository Interface` を介して呼ぶ。

### 3.2 ElysiaJS (Controller)

- **TypeBox Validation**: すべての `body`, `query`, `params` は TypeBox (`t`) でスキーマ定義する。
- **Response**: 成功時は `{ success: true, data: ... }`、失敗時は `{ success: false, error: ... }` の形式を統一する。
- **Dependency Injection**: コンストラクタインジェクションを使用し、テスト容易性を高める。

## 4. エラーハンドリング

- **Custom Errors**: アプリケーション固有のエラークラス（`DomainError`, `ResourceNotFoundError`）を定義し、Elysiaの `onError` フックで一元管理してHTTPステータスコードに変換する。
- **Logging**: エラー発生時は必ずスタックトレースを含めてログ出力する。

## 5. テスト（Bun Test）

- **Unit Test**: `usecase` と `domain` は、インフラ層をモックして単体テストを書く。
- **Integration Test**: `infrastructure` と `presentation` は、テスト用DB（またはインメモリSQLite）を使って結合テストを書く。
