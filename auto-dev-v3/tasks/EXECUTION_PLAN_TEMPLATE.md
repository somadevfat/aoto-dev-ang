# 自動開発実行計画書（Master Execution Plan）

**【重要】** このTODOリストは、プロジェクト開始時に `docs/requirements_detail.md` 等の要件定義に基づき、具体的なクラス名・機能名を埋めてから `tasks/TODO.md` として運用すること。

---

## 🔹 Phase 1: 要件定義・設計（Requirements & Design）

_すべて `main` ブランチで作業。ドキュメントのみコミット。_

### 1-1. 要件定義とDDDモデリング

- [ ] `docs/requirements_detail.md` 作成・ヒアリング内容の記入
- [ ] `docs/ddd_core.md` (DDD定義) 作成
  - [ ] コンテキスト境界の定義（Bounded Context）
  - [ ] ✨ **[要件依存]** エンティティ一覧の定義（例: `User`, `Order`, `Product`）
  - [ ] ✨ **[要件依存]** 値オブジェクト一覧の定義（例: `Email`, `Price`）
  - [ ] ドメインサービスの特定（エンティティを跨ぐロジック）
- [ ] **【承認】** ドメインモデルのレビュー（ユーザー確認）

### 1-2. API仕様策定（Schema First）

- [ ] `docs/api_spec.yaml` (OpenAPI 3.1) 作成
  - [ ] 共通エラーレスポンス定義（400, 401, 404, 500）
  - [ ] ✨ **[要件依存]** エンドポイント定義（例: `POST /orders`, `GET /users/:id`）
  - [ ] リクエスト/レスポンスのスキーマ型定義
- [ ] **【承認】** API仕様のレビュー

### 1-3. バックエンド詳細設計

- [ ] `docs/be_design.md` 作成
  - [ ] **DBスキーマ設計**: テーブル名, カラム, 型, 制約, インデックス
  - [ ] **アーキテクチャ設計**: ディレクトリ構造の確定（Standard準拠）
  - [ ] ✨ **[要件依存]** クラスリスト作成（Repository, Service, UseCase）
  - [ ] ✨ **[要件依存]** 関数リスト作成（メソッド名, 引数, 戻り値, 処理概要）
- [ ] **【承認】** BE設計のレビュー

---

## 🔹 Phase 2: バックエンド環境構築 & 実装（Backend Dev）

### 2-1. 環境構築 (Setup)

_branch: `feature/be-init`_

- [ ] `git checkout -b feature/be-init`
- [ ] `bun init -y` 実行
- [ ] パッケージインストール (Elysia, Drizzle, etc.)
- [ ] `src/` ディレクトリ構造作成（`src/context`, `src/shared`）
- [ ] `docker-compose.yml` 作成（PostgreSQL）
- [ ] `drizzle.config.ts` & `src/shared/infrastructure/db/schema.ts` (初期空ファイル) 作成
- [ ] DB接続確認（Docker起動 -> 接続テスト）
- [ ] **【AIセルフレビュー】**
  - [ ] 不要なファイルがないか
  - [ ] 設定ファイル (`biome.json`, `drizzle.config.ts`) が正しいか
- [ ] `dev` へマージ

### 2-2. データベース実装 (Database)

_branch: `feature/be-db-schema`_

- [ ] `git checkout -b feature/be-db-schema`
- [ ] `src/shared/infrastructure/db/schema.ts` に共通テーブル定義実装
- [ ] 各コンテキスト用テーブル定義（詳細設計準拠）
- [ ] マイグレーション生成 & 適用
- [ ] **【AIセルフレビュー】**
  - [ ] テーブル定義が設計書と一致しているか
  - [ ] インデックスが適切に設定されているか
- [ ] `dev` へマージ

### 2-3. ドメイン層実装 (Domain Layer - RGRG Cycle)

_branch: `feature/be-domain`_

- [ ] `git checkout -b feature/be-domain`
- [ ] ✨ **[要件依存]** コンテキストごとのエンティティ実装 (RGRG)
  - [ ] **RED**: テスト作成（`src/context/[Context]/domain/tests/xxx.test.ts`） -> 失敗を確認
  - [ ] **GREEN**: テストを通すための最小限の実装
  - [ ] **REFACTOR**: コード整理・重複排除
  - [ ] **GREEN CHECK**: テストが依然として通ることを再確認
- [ ] **【AIセルフレビュー】**
  - [ ] ドメインロジックが流出していないか
  - [ ] 型定義は厳密か (`any` 禁止)
- [ ] `dev` へマージ

### 2-4. インフラ層 & リポジトリ実装 (Infra Layer - RGRG Cycle)

_branch: `feature/be-infra`_

- [ ] `git checkout -b feature/be-infra`
- [ ] リポジトリインターフェース定義（`src/context/[Context]/domain/repository/IxRepository.ts`）
- [ ] リポジトリ実装 (RGRG)
  - [ ] **RED**: テスト作成（`src/context/[Context]/infrastructure/tests/xRepository.test.ts`） -> 失敗を確認
  - [ ] **GREEN**: Drizzleを使ったクエリ実装
  - [ ] **REFACTOR**: クエリの最適化・コード整理
  - [ ] **GREEN CHECK**: テスト再実行でパス確認
- [ ] **【AIセルフレビュー】**
  - [ ] SQLインジェクションの懸念はないか (ORM使用で回避)
  - [ ] トランザクション制御は適切か
- [ ] `dev` へマージ

### 2-5. ユースケース実装 (UseCase Layer - RGRG Cycle)

_branch: `feature/be-usecase`_

- [ ] `git checkout -b feature/be-usecase`
- [ ] ✨ **[要件依存]** Serviceクラス実装 (RGRG)
  - [ ] **RED**: テスト作成（`src/context/[Context]/usecase/tests/xxx.test.ts`） -> 失敗を確認
  - [ ] **GREEN**: ビジネスロジック実装
  - [ ] **REFACTOR**: コード整理
  - [ ] **GREEN CHECK**: テスト再実行でパス確認
- [ ] **【AIセルフレビュー】**
  - [ ] 依存性注入 (DI) は正しく行われているか
  - [ ] エラーハンドリングは漏れていないか
- [ ] `dev` へマージ

### 2-6. API実装 & 統合 (Presentation Layer - RGRG Cycle)

_branch: `feature/be-api`_

- [ ] `git checkout -b feature/be-api`
- [ ] Elysiaコントローラー実装（OpenAPI型準拠, TypeBoxバリデーション）
- [ ] エンドポイント実装 (RGRG)
  - [ ] **RED**: E2E APIテスト作成（`src/context/[Context]/presentation/tests/api.test.ts`） -> 失敗を確認
  - [ ] **GREEN**: コントローラー実装 & ルーティング
  - [ ] **REFACTOR**: バリデーション整理・共通化
  - [ ] **GREEN CHECK**: テスト再実行でパス確認
- [ ] Swagger UIで動作確認
- [ ] **【AIセルフレビュー】**
  - [ ] OpenAPI仕様とレスポンスが一致しているか
  - [ ] ステータスコードは適切か
- [ ] `dev` へマージ

---

## 🔹 Phase 3: フロントエンド詳細設計（Frontend Design）

_branch: `feature/fe-design`_

### 3-1. FE設計

- [ ] `docs/fe_design.md` 作成
  - [ ] **画面遷移設計**: ルーティングURLとページコンポーネントの対応
  - [ ] **コンポーネント分割**: Atoms, Molecules, Organisms, Templates
  - [ ] ✨ **[要件依存]** 状態管理設計（Zustand Store定義）
  - [ ] ✨ **[要件依存]** カスタムフック設計（API接続、ロジック分離）
- [ ] **【承認】** FE設計のレビュー
- [ ] `dev` へマージ

---

## 🔹 Phase 4: フロントエンド実装（Frontend Dev）

### 4-1. 環境構築 (Setup)

_branch: `feature/fe-init`_

- [ ] `git checkout -b feature/fe-init`
- [ ] `bun create next-app` 実行
- [ ] パッケージインストール（`shadcn-ui`, `lucide-react`, `zustand`, `tanstack-query`）
- [ ] ディレクトリ構造作成（Feature Sliced Design準拠）
- [ ] APIクライアント生成（OpenAPI -> TypeScript型生成）
- [ ] **【AIセルフレビュー】**
  - [ ] `tsconfig.json`, `tailwind.config.js` 設定確認
  - [ ] 型生成は成功しているか
- [ ] `dev` へマージ

### 4-2. UIコンポーネント実装 (Components - RGRG Cycle)

_branch: `feature/fe-components`_

- [ ] `git checkout -b feature/fe-components`
- [ ] ✨ **[要件依存]** 共通コンポーネント実装 (RGRG)
  - [ ] **RED**: テスト作成 (Rendering Test) -> 失敗を確認
  - [ ] **GREEN**: 実装 (Shadcn UI等利用)
  - [ ] **REFACTOR**: Props整理・スタイル調整
  - [ ] **GREEN CHECK**: テスト再実行でパス確認
- [ ] Storybook等のカタログ作成（任意）
- [ ] **【AIセルフレビュー】**
  - [ ] アクセシビリティ (a11y) は考慮されているか
  - [ ] スマホ表示 (Responsive) は崩れていないか
- [ ] `dev` へマージ

### 4-3. 機能実装 & ページ統合 (Features & Pages - RGRG Cycle)

_機能単位でブランチを切る_

- [ ] ✨ **[機能A]** 実装 `feature/fe-feature-a`
  - [ ] APIモック作成 (MSW)
  - [ ] カスタムフック実装 (RGRG)
    - [ ] **RED** -> **GREEN** -> **REFACTOR** -> **GREEN CHECK**
  - [ ] コンポーネント実装 (RGRG)
    - [ ] **RED** -> **GREEN** -> **REFACTOR** -> **GREEN CHECK**
  - [ ] ページ作成 (`app/feature-a/page.tsx`)
  - [ ] E2Eテストシナリオ実装 (Playwright)
  - [ ] **【AIセルフレビュー】**
    - [ ] ローディング・エラー状態のUIは実装されているか
    - [ ] 不要な再レンダリングはないか
  - [ ] `dev` へマージ

- [ ] ✨ **[機能B]** 実装 `feature/fe-feature-b`
  - [ ] ... (同上)

---

## 🔹 Phase 5: 品質保証 & リリース（QA & Release）

### 5-1. 全体検証

- [ ] 全ユニットテスト実行 & パス確認
- [ ] 全E2Eテスト実行 & パス確認
- [ ] ビルドテスト (`bun run build`)
- [ ] 未消化TODOの確認

### 5-2. デプロイ準備

- [ ] `README.md` 更新（起動手順、環境変数）
- [ ] 最終レビュー
- [ ] `dev` -> `main` へマージ
