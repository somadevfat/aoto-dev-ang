## 3. ルール (Rules) の構築

ルールは **常にエージェントが従うべき制約** です。  
`<root>/.agent/rules/` に配置します。

### 3.1 `coding.md` - コーディング規約

````markdown
---
description: コーディング規約と品質基準
globs: ["*.ts", "*.tsx", "*.js", "*.jsx"]
alwaysApply: true
---

# コーディング規約

## 技術スタック（固定）

- **ランタイム**: Bun
- **バックエンド**: ElysiaJS（型共有を最大限活用）
- **フロントエンド**: Next.js（App Router）
- **言語**: TypeScript（strict モード必須）
- DB は Repository パターンで抽象化し、後から変更可能にする

## JSDoc（必須）

すべての public 関数・クラス・メソッドに日本語 JSDoc を記述する。
以下の3項目を必ず含める:

1. **機能説明**: その関数/クラスが何をするか
2. **@param**: 各引数の型と説明
3. **@returns**: 戻り値の型と説明

```typescript
/**
 * ユーザーをIDで検索して返す
 * @param id - ユーザーの一意識別子
 * @returns 見つかったユーザー。存在しない場合は null
 */
async findById(id: string): Promise<User | null> { ... }
```
````

## コードスタイル

- 関数名・変数名は英語で記述
- コメントは日本語で記述
- 1ファイル200行以内を目安とする

## エラーハンドリング

- try-catch は必ず具体的なエラー型でキャッチ
- エラーメッセージは日本語で記述

````

### 3.2 `architecture.md` - アーキテクチャ制約

```markdown
---
description: アーキテクチャの制約と設計方針
alwaysApply: true
---

# アーキテクチャ制約

## 基本方針

- クリーンアーキテクチャを基本とする
- ドメインがある場合はDDDを適用する
- DDD適用時は **Context（機能）ベース** でディレクトリを分割する
- レイヤー間の依存は内側→外側のみ

## Context ベースの構造（DDD適用時）

機能（境界づけられたコンテキスト）ごとにディレクトリを分け、
各コンテキスト内にレイヤーを持たせる:

```text
backend/src/
├── contexts/
│   ├── user/                 # ユーザー管理コンテキスト
│   │   ├── domain/           # エンティティ, VO, リポジトリIF
│   │   ├── application/      # ユースケース, DTO
│   │   ├── infrastructure/   # リポジトリ実装, 外部API
│   │   └── presentation/     # コントローラー, ルート
│   ├── order/                # 注文コンテキスト
│   │   ├── domain/
│   │   ├── application/
│   │   ├── infrastructure/
│   │   └── presentation/
│   └── shared/               # 共有カーネル（共通型・ユーティリティ）
│       ├── domain/
│       └── infrastructure/
└── main.ts                   # ElysiaJS エントリポイント
````

フロントエンド（Next.js App Router）も同様に context ベース:

```text
frontend/src/
├── app/                      # App Router
│   ├── (auth)/               # 認証関連ルート
│   ├── (dashboard)/          # ダッシュボード関連ルート
│   └── layout.tsx
├── contexts/                 # 機能ベースモジュール
│   ├── user/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── types/
│   │   └── api/
│   └── order/
│       ├── components/
│       ├── hooks/
│       ├── types/
│       └── api/
└── shared/                   # 共通コンポーネント・ユーティリティ
```

## レイヤー間の制約

- Domain → 外部依存なし
- Application → Domain のみ依存
- Infrastructure → Domain, Application に依存
- Presentation → Application のみ依存
- Context 間の直接依存は禁止（shared 経由のみ）

## DB抽象化

- Repository パターンを使用
- インターフェースを各 Context の Domain 層に定義
- 実装を各 Context の Infrastructure 層に配置

````

### 3.3 `testing.md` - テスト方針

```markdown
---
description: テスト方針とTDDルール
alwaysApply: true
---

# テスト方針

## TDD ルール

- 実装コードを書く前に必ずテストを先に書く
- Red → Green → Refactor のサイクルを厳守
- テストタイトルは日本語で記述

## テストレベル

- **ユニットテスト** (主軸): 全ての関数・クラスに対して作成
- **E2Eテスト**: Playwright を使用し、主要なユーザーフローを検証

## カバレッジ

- ユニットテストカバレッジ 80% 以上を目標
- ドメインロジックは 100% カバー

## テストツール

- ユニットテスト: `bun test`（Bun 組み込みテストランナー）
- E2Eテスト: Playwright
````

### 3.4 `security.md` - セキュリティ規約

```markdown
---
description: セキュリティに関するコーディングルールとレビュー観点
alwaysApply: true
---

# セキュリティ規約

## 入力バリデーション

- 全てのユーザー入力を検証する（ElysiaJS の型バリデーション活用）
- SQLインジェクション・XSS対策を必ず施す
- パラメータはホワイトリスト方式でバリデーション

## 認証・認可

- 認証トークンはHTTP Only Cookie で管理
- パスワードは bcrypt 等でハッシュ化
- 全てのAPIエンドポイントに認可チェックを実装

## 機密情報

- シークレット情報はハードコードしない（.env 使用）
- .env ファイルは .gitignore に含める
- ログに機密情報を出力しない

## レビュー観点

実装時に以下のセキュリティチェックを自己レビューとして実施:

- [ ] 入力バリデーションは十分か
- [ ] 認可チェックは全APIに適用されているか
- [ ] 機密情報がコードに含まれていないか
- [ ] エラーメッセージに内部情報が漏洩していないか
- [ ] 依存パッケージに既知の脆弱性がないか
```

### 3.5 `lint.md` - Lint & コミットフック

````markdown
---
description: Lint設定とコミット時の自動チェック
alwaysApply: true
---

# Lint & コミットフック

## ESLint ルール

- `@typescript-eslint/no-explicit-any` を **error** に設定（any 禁止）
- `@typescript-eslint/strict` プリセットを使用
- import の自動ソートを有効化

## Husky + lint-staged

コミット時に以下を自動実行する:

1. **ESLint**: `*.ts`, `*.tsx` ファイルの lint チェック
2. **型チェック**: `tsc --noEmit` で型エラー検出
3. **スペルチェック**: `cspell` でスペルミス検出
4. **Prettier**: フォーマット統一

## セットアップ

プロジェクト初期化時に以下を設定:

```bash
bun add -d husky lint-staged eslint prettier cspell \
  @typescript-eslint/eslint-plugin @typescript-eslint/parser
bunx husky init
```
````

## .lintstagedrc.js 設定例

```javascript
module.exports = {
  "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
  "*.{ts,tsx,md}": ["cspell --no-must-find-files"],
};
```

```

```
