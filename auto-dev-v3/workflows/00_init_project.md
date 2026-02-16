# Auto-Dev V3: 超自律型プロジェクト初期化ワークフロー

このワークフローを実行することで、リポジトリに「開発の自律性（魂）」と「最強のスキル群（肉体）」を注入します。

## 🚀 1. 初期化コマンド（AIエージェントが実行）

// turbo

```bash
# .agentディレクトリの構築と全アセットの配備
mkdir -p .agent/rules .agent/skills .agent/workflows .agent/templates/design
cp -r auto-dev-v3/rules/* .agent/rules/
cp -r auto-dev-v3/skills/* .agent/skills/
cp -r auto-dev-v3/workflows/* .agent/workflows/
cp -r auto-dev-v3/templates/* .agent/templates/

# 実行計画書とドキュメント構造の初期化
mkdir -p tasks docs/design docs/requirements
cp auto-dev-v3/tasks/EXECUTION_PLAN_TEMPLATE.md tasks/TODO.md
```

## 🛠 2. 環境の自動構築 (Zero-Config Setup)

// turbo

```bash
# Golden Stack (Bun + Next.js + Elysia) の展開
if [ ! -f package.json ]; then
  bun init -y
  # 本番依存
  bun add elysia @elysiajs/swagger @sinclair/typebox drizzle-orm postgres next react react-dom zustand @tanstack/react-query
  # 開発依存
  bun add -d drizzle-kit @types/bun biome playwright vitest
fi

# Gitの初期化とフローの適用
if [ ! -d .git ]; then
  git init
  echo "node_modules/\n.env\n.DS_Store\ndist/\n.next/\n" > .gitignore
  git add .
  git commit -m "chore: initial commit with auto-dev-v3 world"
  git branch -M main
  git checkout -b dev
fi
```

## 🧠 3. システム・オーケストレーターの起動

1. **コンテキストの自動同期**: `tasks/TODO.md` を読み込み、現在のフェーズが `Phase 1: 要件定義` であることを確認する。
2. **スキルセットのロード**: `auto-dev-orchestrator` スキルをアクティブにし、以降の全操作をその支配下に置く。
3. **ユーザーへの最初の問いかけ**:
   - AI: 「Auto-Dev V3 システムを起動しました。すべての専門スキル（20種以上）と憲法がロードされました。
     現在、`Phase 1: 要件定義` に滞在しています。
     あなたの『作りたいもの』の概要を今ここで話していただくか、リポジトリ内の要件ドキュメントを読み込ませることで、自律開発プロセスを開始します。
     どう進めますか？」

## ⚠️ 注意事項

- 初期化完了後は、個別のルールファイルを直接編集せず、必要に応じて `auto-dev-orchestrator` を通じてシステムのチューニングを行ってください。
- 実装は常に RGRG サイクルを尊守し、設計ドキュメントとコードの同期を保ってください。
