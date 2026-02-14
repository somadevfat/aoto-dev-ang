# 自動開発プロジェクト初期化ワークフロー（Master Init Process）

このワークフローは、プロジェクトの「魂（憲法）」と「骨格（構造）」を注入し、自動開発が正常に機能する状態を機械的に作り出すものである。

## 前提条件

- `bun` がインストールされていること。（なければインストールする）
- ユーザーがプロジェクトのルートディレクトリにいること。

## 手順

1. **.agent ディレクトリの完全構築**
   - 既存の `.agent` があればバックアップを取り、新たに作成する。
     // turbo

   ```bash
   [ -d .agent ] && mv .agent .agent_backup_$(date +%s)
   mkdir -p .agent/rules .agent/skills/coding_standards .agent/workflows .agent/templates/design
   ```

2. **憲法とルールの配置**
   - `auto-dev-v2-draft/rules/00_憲法.md` を `.agent/rules/` にコピーする。
     // turbo

   ```bash
   cp auto-dev-v2-draft/rules/00_憲法.md .agent/rules/
   ```

3. **スキル（Golden Stack & Standards）の配置**
   - 技術スタック定義とコーディング規約をコピーする。
     // turbo

   ```bash
   cp auto-dev-v2-draft/skills/tech_stack.md .agent/skills/
   cp auto-dev-v2-draft/skills/coding_standards/be_standard.md .agent/skills/coding_standards/
   cp auto-dev-v2-draft/skills/coding_standards/fe_standard.md .agent/skills/coding_standards/
   ```

4. **設計テンプレートの配置**
   - `be_design_template.md` と `fe_design_template.md` をコピーする。
     // turbo

   ```bash
   cp auto-dev-v2-draft/templates/design/* .agent/templates/design/
   ```

5. **実行計画書の初期化**
   - `EXECUTION_PLAN_TEMPLATE.md` を `tasks/TODO.md` としてコピーし、ユーザーがすぐ編集できる状態にする。
     // turbo

   ```bash
   mkdir -p tasks
   cp auto-dev-v2-draft/tasks/EXECUTION_PLAN_TEMPLATE.md tasks/TODO.md
   ```

6. **Bunプロジェクトの初期化（Golden Stack Base）**
   - `package.json` がなければ生成し、必須パッケージをインストールする。
     // turbo

   ```bash
   [ ! -f package.json ] && bun init -y
   # Backend & Tooling
   bun add elysia @elysiajs/swagger @sinclair/typebox drizzle-orm postgres
   bun add -d drizzle-kit @types/bun biome
   # Frontend (Later install via create-next-app, but types are needed)
   ```

7. **Git初期化とブランチ運用開始**
   - `.gitignore` を作成し、初期コミットを行う。
     // turbo

   ```bash
   echo "node_modules/\n.env\n.DS_Store\ndist/\n.next/\n" > .gitignore
   git init
   git add .
   git commit -m "chore: initial commit with auto-dev-v2 structure"
   git branch -M main
   git checkout -b dev
   ```

8. **ユーザーへの引き渡し**
   - 「初期化が完了しました。`tasks/TODO.md` を開き、【Phase 1】の要件定義を開始してください。」と伝える。
