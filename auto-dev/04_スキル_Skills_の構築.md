## 4. スキル (Skills) の構築

スキルは **特定のタスクに対する実行手順** をパッケージ化したものです。
`<root>/.agent/skills/<スキル名>/SKILL.md` に配置します。

### 4.1 `requirements-analysis` - 要件分析スキル

````markdown
---
name: requirements-analysis
description: 要件定義書を分析し、DDDリスト・関数リスト・共通化まで自然言語レベルで完了する。要件定義書が渡されたときに使用。
---

# 要件分析スキル

## 実行タイミング

- 要件定義書（Markdown）が渡されたとき

## 核心思想

> **コードを1行も書く前に、自然言語ですべてを設計する。**
> 作業者（エージェント）がやることを100%明確にしてから、コードに起こす。

## 手順

### ステップ1: 要件の構造化

1. 要件定義書を読み込む
2. 以下を抽出する:
   - 機能要件（ユーザーストーリー形式に変換）
   - 非機能要件（性能、セキュリティ、スケーラビリティ）
   - ドメインの特定

### ステップ2: DDD リストの作成

要件定義の時点で DDDリスト を作成する:

1. Context（境界づけられたコンテキスト）の一覧
2. 各 Context 内のエンティティ・VO・ドメインサービス
3. Context 間の関係性（コンテキストマップ）

```text
## DDDリスト例

### Context: User（ユーザー管理）
- Entity: User（id, name, email, role）
- VO: Email（バリデーション付き）
- VO: UserId
- DomainService: UserAuthenticator
- Repository: UserRepository

### Context: Order（注文管理）
- Entity: Order（id, userId, items, status）
- VO: OrderId, OrderStatus
- Entity: OrderItem（productId, quantity, price）
- DomainService: OrderProcessor
- Repository: OrderRepository
```

### ステップ3: 関数単位への細分化 & 共通化

DDDリストの各要素をさらに **関数単位** にリストアップする。
この段階で **共通化できる関数** を自然言語の時点で特定する:

```text
## 関数リスト例

### shared/（共通）
- validateEmail(email: string): boolean — メール形式の検証
- generateId(): string — UUID生成
- formatDate(date: Date): string — 日付フォーマット

### Context: User
- createUser(name, email, role): User — ユーザー作成
- findUserById(id): User | null — ID検索
- updateUserRole(id, role): User — ロール変更
- authenticateUser(email, password): AuthToken — 認証

### Context: Order
- createOrder(userId, items): Order — 注文作成
- calculateTotal(items): number — 合計金額計算
- updateOrderStatus(id, status): Order — ステータス変更
```

### ステップ4: フロントエンド設計（自然言語）

フロントエンドも同様に自然言語で設計:

```text
## コンポーネントリスト

### shared/（共通コンポーネント ← 先に作成）
- Button — 汎用ボタン（variant: primary/secondary/danger）
- Input — 入力フィールド（バリデーション対応）
- Modal — モーダルダイアログ
- Table — テーブル表示
- Loading — ローディングスピナー

### Context: User（ページ: /users）
- UserList — ユーザー一覧表示
- UserDetail — ユーザー詳細
- UserForm — ユーザー作成/編集フォーム
- useUsers() — ユーザーデータ取得フック

### Context: Order（ページ: /orders）
- OrderList — 注文一覧
- OrderDetail — 注文詳細
- useOrders() — 注文データ取得フック
```

### ステップ5: 出力

分析結果を以下のフォーマットで出力:

```text
## 分析結果
- 設計手法: [クリーンアーキテクチャ / クリーンアーキテクチャ+DDD]
- Context 一覧: [リスト]
- DDDリスト: [上記の詳細]
- 関数リスト（共通化済み）: [上記の詳細]
- コンポーネントリスト（共通化済み）: [上記の詳細]
- parts ブランチ計画: [クラス/関数/コンポーネント単位のブランチ名リスト]
```
````

```

```

### 4.2 `tdd-implementation` - TDD実装スキル

```markdown
---
name: tdd-implementation
description: TDD（テスト駆動開発）のRed-Green-Refactorサイクルに従ってコードを実装する。コード実装時に使用。
---

# TDD 実装スキル

## 実行タイミング

- コードの実装フェーズ

## 手順

### ステップ1: Red（失敗するテストを書く）

1. 実装するクラス/関数のインターフェースを定義
2. そのインターフェースに対するテストを書く
3. テストが **失敗する** ことを確認（`bun test` で実行）

### ステップ2: Green（テストを通す最小限のコードを書く）

1. テストを通すために必要な最小限の実装コードを書く
2. テストが **成功する** ことを確認
3. この段階では美しさは求めない

### ステップ3: Refactor（リファクタリング）

1. コードの重複を排除
2. 命名の改善
3. 設計パターンの適用
4. テストが引き続き **成功する** ことを確認

## 重要な制約

- 実装コードを書く前に必ずテストを先に書く
- 一度に1つのテストケースだけに集中する
- テストは日本語で命名する（例: `it('ユーザー名が空の場合エラーを返す')`)
```

### 4.3 `ddd-modeling` - DDDモデリングスキル

```markdown
---
name: ddd-modeling
description: ドメイン駆動設計に基づいてドメインモデルを設計する。ドメインが複雑と判断された場合に使用。
---

# DDD モデリングスキル

## 実行タイミング

- 要件分析でドメインが複雑と判断された場合

## 手順

### ステップ1: ユビキタス言語の定義

1. 要件定義書からドメイン用語を抽出
2. 用語集を作成（日本語 ⇔ コード上の英語名）

### ステップ2: 境界づけられたコンテキストの特定

1. サブドメインを特定
2. コンテキストマップを作成

### ステップ3: ドメインモデル設計

1. エンティティの特定（識別子を持つオブジェクト）
2. バリューオブジェクトの特定（不変オブジェクト）
3. 集約ルートの決定
4. ドメインサービスの特定
5. リポジトリインターフェースの定義

### ステップ4: 出力

- ドメインモデル図（Mermaid形式）
- Context 分割図
- 各 Context 内のエンティティ/VOの型定義ファイル
- リポジトリインターフェース
```

### 4.4 `clean-architecture` - クリーンアーキテクチャスキル

````markdown
---
name: clean-architecture
description: Contextベースのクリーンアーキテクチャ構造を生成する。プロジェクト初期構築時に使用。
---

# クリーンアーキテクチャスキル

## 実行タイミング

- プロジェクトの初期構築時

## 手順

### ステップ1: プロジェクト初期化

1. Bun プロジェクトを初期化（backend / frontend）
2. ElysiaJS ・ Next.js のセットアップ
3. Husky / lint-staged / ESLint / Prettier / cspell のセットアップ

### ステップ2: Contextベースのディレクトリ構造生成

要件分析で特定した Context ごとに以下のディレクトリを生成:

```text
backend/src/contexts/[context名]/
├── domain/
├── application/
├── infrastructure/
└── presentation/

frontend/src/contexts/[context名]/
├── components/
├── hooks/
├── types/
└── api/
```

### ステップ3: レイヤー間の制約

- Domain → 外部依存なし
- Application → Domain のみ依存
- Infrastructure → Domain, Application に依存
- Presentation → Application のみ依存
- Context 間の直接依存は禁止（shared 経由のみ）
````

### 4.5 `e2e-testing` - E2Eテストスキル

```markdown
---
name: e2e-testing
description: Playwrightを使用したE2Eテストを作成・実行する。結合テストフェーズで使用。
---

# E2E テストスキル

## 実行タイミング

- 全機能の結合が完了した後

## 手順

### ステップ1: テストシナリオの作成

1. 要件定義書の機能要件からユーザーフローを特定
2. 各フローに対してテストケースを作成

### ステップ2: Playwright テストの実装

1. ブラウザサブエージェントを活用してUIの動作確認
2. テストコードを `e2e/` ディレクトリに配置
3. テストは日本語で命名

### ステップ3: 実行と検証

1. `npx playwright test` で実行
2. 失敗した場合は原因を特定して修正
3. スクリーンショットを保存して結果を報告
```

### 4.6 `self-growth` - 自己成長スキル

````markdown
---
name: self-growth
description: 失敗時にWebで調査し、原因と対処法をファイルに蓄積する。エラー発生時・テスト失敗時に自動適用。
---

# 自己成長スキル（失敗学習）

## 実行タイミング

- テストが失敗したとき
- ビルドエラーが発生したとき
- 実装アプローチが間違っていたとき

## 手順

### ステップ1: 失敗の記録

失敗が発生したら、`.learnings/raw/` に以下の形式でログを保存:

```markdown
# [YYYY-MM-DD] [short-title]

## エラー内容

[what happened]

## 原因

[why it happened]

## Web調査結果

[what was found from web search]

## 対処法

[how it was fixed]

## タグ

[e.g. TypeScript, ElysiaJS, DDD, Playwright]
```
````

### ステップ2: Web調査

1. エラーメッセージや失敗内容でWeb検索する
2. 公式ドキュメントやStack Overflowの情報を収集
3. 根本原因と正しい対処法を特定

### ステップ3: 辞書の更新

`.learnings/raw/` のログが **5件以上** 蓄積されたら、
`.learnings/dictionary.md` を自動生成・更新する:

```markdown
# 📖 対処辞書

## 索引

- [TypeScript](#typescript)
- [ElysiaJS](#elysiajs)
- [Next.js](#nextjs)
- [Playwright](#playwright)
- [DDD](#ddd)

---

## TypeScript

### any 型エラー

- **問題**: ...
- **対処**: ...
- **参照**: [raw/2025-01-15_any-type-error.md]

...
```

## 重要な原則

- ただの作業者ではなく、**失敗から学ぶ**ことを最優先
- 同じ失敗を二度と繰り返さないように辞書を参照する
- 辞書はタグ別に索引を付け、素早く対処法を見つけられるようにする

```

```
