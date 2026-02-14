# フロントエンド コーディング規約 & ベストプラクティス（Next.js / App Router）

このファイルはフロントエンド実装時に **必ず参照** すること。
これに違反するコードは憲法違反として扱われる。

## 1. 原則（Principles）

- **Strict TypeScript**: `Props`, `State`, `Effect` の型を厳密に定義する。`any` 使用禁止。
- **Component Design**: 1ファイルの責任は1つ（Single Responsibility）。
- **Atomic Design-ish**: 複雑さを防ぐため、`features/` と `components/ui/` の2層構造を基本とする。

## 2. ディレクトリ構造（Features Sliced）

```
src/
├── app/          # Next.js App Router (Routing Only)
│   ├── layout.tsx
│   ├── page.tsx
│   └── (routes)/ # Grouped Routing
├── features/     # 機能ごとの凝集度を高めたモジュール
│   ├── auth/     # 認証機能
│   │   ├── components/ # 認証専用UI
│   │   ├── hooks/      # 認証ロジック
│   │   ├── types/      # 認証関連の型
│   │   └── api/        # 認証APIクライアント
│   └── posts/    # 投稿機能
├── components/   # 全体で再利用するUI
│   ├── ui/       # Button, Input, Modal (Shadcn/Radix)
│   └── layout/   # Header, Footer
├── hooks/        # 汎用Hooks
├── lib/          # API Client設定, Utility Functions
└── styles/       # Tailwind Global Config
```

## 3. 実装ルール

### 3.1 App Router & Server Component 原則（Strict Rules）

- **Defalut Server**: すべてのコンポーネントはデフォルトで `Server Component` として作成する。
- **'use client' の最小化**:
  - `'use client'` ディレクティブは、`useState`, `useEffect`, イベントリスナー (`onClick` 等) が**真に必要な末端のコンポーネント**にのみ付与する。
  - ページ全体 (`page.tsx`) やレイアウト (`layout.tsx`) に `'use client'` を付けることを**固く禁ずる**（Context Provider等は別ファイルに切り出してimportする）。
- **Data Fetching Responsibility**:
  - **Page File**: 原則として `page.tsx` でデータ取得を行う（Server Side Fetching）。
  - **Pass as Props**: 取得したデータは、型安全な Props として Client Component に渡す。
  - **Forbidden**: クライアントコンポーネント内での `useEffect` を使ったデータ取得は原則禁止とする。必要な場合は `TanStack Query` の Hydration パターンを使用する。

### 3.2 コンポーネント設計（React Server Component Pattern）

- **Props Definition**: `interface Props { ... }` を定義し、FCの引数型とする。
- **Container/Presenter**: ロジック（Client）と表示（Server/Client）を分離する意識を持つ。

### 3.2 状態管理（Zustand & React Query）

- **Server State**: 非同期データの管理には `TanStack Query (React Query)` を使用する。
  - ローディング、エラー、キャッシュの管理を一元化する。
- **Client Global State**: アプリ設定やUIの状態（モーダル開閉など）には `Zustand` を使用する。
- **Context API**: Prop Drilling回避のためだけに使用する（頻繁な更新には不向き）。

### 3.3 スタイリング（Tailwind CSS）

- **Class Sorting**: `prettier-plugin-tailwindcss` によりクラス順を自動整列する。
- **Custom Utility**: よく使うスタイル（`btn-primary` 等）は `tailwind.config.js` ではなく `UnoCSS` 的なショートカットクラスを作成するか、コンポーネント化してカプセル化する。

## 4. エラーハンドリング（Error Boundary）

- **Route Error**: `error.tsx` ファイルを配置し、予期せぬエラー時にフォールバックUIを表示する。
- **API Error**: API呼び出し失敗時は `toast` 通知を表示し、ユーザーに状況を伝える。

## 5. テスト（Vitest / Testing Library）

- **Component Test**: UIの描画、イベント発火時の挙動をテストする。
- **Hook Test**: カスタムフックのロジック（状態遷移）をテストする。
