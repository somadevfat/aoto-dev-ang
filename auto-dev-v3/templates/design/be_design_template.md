# バックエンド詳細設計テンプレート（Template: BE Design）

## 1. DDD（Domain-Driven Design）リスト

_「ビジネスの中核」を定義し、境界線を明確にする。_

### ドメインモデル

- [ ] **Entity**: 一意な識別子（ID）を持ち、ライフサイクルを持つ概念。
  - **EntityName**: ID, props..., methods...
- [ ] **ValueObject**: 属性のみで定義され、不変（Immutable）な概念。
  - **ValueName**: props...
- [ ] **Service**: 複数のエンティティを跨ぐ操作。

---

## 2. DB設計（Database Schema）

_DDDモデルを物理データに落とし込む。_

### テーブル定義

- [ ] **Table**: `table_name`
  - `columns...`
- [ ] **Index**: `indexing...`

---

## 3. クラス設計・構成（Architecture & Classes）

- [ ] **ClassName**: `Role`
  - **Layer**: Domain / UseCase / Infrastructure
  - **Dependencies**: ...

---

## 4. 関数リスト・詳細振る舞い（Functions & Behaviors）

- [ ] **Method**: `name(args)`
  - **Process**: (自然言語でのステップバイステップ)
