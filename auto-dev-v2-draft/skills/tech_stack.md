# 技術スタック（Golden Stack）

プロジェクトの複雑性とAIの暴走を防ぐため、以下の技術スタックを **唯一の正解** として採用する。
許可なくこれ以外のツール・ライブラリを使用してはならない。

## 1. ランタイム & 言語

- **Runtime**: `Bun v1.x` (高速性、TypeScriptネイティブ実行)
- **Language**: `TypeScript v5.x` (厳格モード `strict: true` 必須)

## 2. バックエンド開発

- **Framework**: `ElysiaJS` (Bun最適化、高速、Swagger標準搭載)
- **Validation**: `TypeBox` (Elysia標準、高速なスキーマ定義)
- **API Spec**: `OpenAPI 3.1` (Elysia Swaggerによる自動生成)
- **Type Generation**: `openapi-typescript` or `elysia-type` (OpenAPIスキーマからTS型を生成) -> **Frontend型共有の要**

## 3. データベース & ORM

- **Database**: `PostgreSQL 16` (堅牢性、普及率)
- **ORM**: `Drizzle ORM` (軽量、SQLに近いクエリビルダ、型安全性)
- **Migration**: `Drizzle Kit` (スキーマ管理)

## 4. フロントエンド開発

- **Framework**: `Next.js 14+ (App Router)`
- **Styling**: `Tailwind CSS 3.4` (ユーティリティファースト)
- **State Management**: `Zustand` (シンプル、軽量、Hooksベース)
- **Component**: `Radix UI` or `Shadcn UI` (アクセシビリティ標準準拠)
- **Data Fetching**: `TanStack Query` (キャッシュ管理、自動リフェッチ) or `Treaty (Elysia Client)`
- **Shared Utils**: `src/shared/` は純粋なユーティリティ関数（日付、計算）とインフラ基盤設定のみを許可する。ビジネスロジックは禁止。

## 5. テスト

- **Unit/Integration**: `Bun Test` (高速、Jest互換)
- **E2E**: `Playwright` (信頼性、ブラウザ操作の再現)
  - **Strategy**: E2Eテストは「Happy Path（正常系シナリオ）」の網羅に集中し、全ケース網羅はUnit/Integrationテストに委ねる。
- **Mocking**: `MSW (Mock Service Worker)` (APIモック)

## 6. 開発ツール & 品質管理

- **Linter/Formatter**: `Biome` (高速、Prettier/ESLint代替、ゼロコンフィグ)
- **CI/CD**: `GitHub Actions` (標準的)
