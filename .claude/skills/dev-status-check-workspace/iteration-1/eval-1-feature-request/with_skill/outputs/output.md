## 現状確認レポート

### 技術スタック

- フロントエンド: 未定義（ソースコードなし）
- バックエンド: 未定義（ソースコードなし）
- DB: 未定義
- テスト: 未定義
- ビルド/パッケージ: 未定義
- ドキュメント管理: Markdown（`docs/` 配下）
- 設定管理: JSON（`.claude/settings.json`, `.vscode/settings.json`）
- バージョン管理: Git

> **注意**: このリポジトリは現時点でアプリケーションコードを持たない「AI駆動開発ワークフロー定義リポジトリ」です。
> `package.json`・`Gemfile`・`pyproject.toml` 等の依存関係ファイルは存在しません。

---

### ディレクトリ構造

```
/home/user/source/ai-driven-development/
├── README.md                    # プロジェクト概要（現在は空）
├── docs/                        # 設計ドキュメント
│   ├── ai-dev-workflow.md       # AI駆動開発ワークフロー全体定義（Mermaid図付き）
│   ├── ai-dev-workflow-skills.md      # 各Skillの仕様定義リスト
│   └── ai-dev-workflow-skills-review.md  # Skill仕様に関するQ&A記録
├── .claude/
│   ├── settings.json            # Claude Code パーミッション設定
│   ├── settings.local.json      # ローカル追加パーミッション設定
│   └── skills/                  # Claude Code Skills（AI駆動開発ワークフロー実装）
│       ├── dev-status-check/    # Step 1: 現状確認 Skill
│       │   ├── SKILL.md
│       │   └── evals/evals.json
│       ├── dev-requirements/    # Step 2: 要件定義 Skill
│       │   ├── SKILL.md
│       │   └── evals/evals.json
│       ├── dev-static-screen/   # Step 3: 静的画面作成 Skill
│       │   ├── SKILL.md
│       │   └── evals/evals.json
│       ├── dev-implementation-plan/  # Step 4: 実装計画 Skill
│       │   ├── SKILL.md
│       │   └── evals/evals.json
│       ├── dev-test-creation/   # Step 5: テストコード作成 Skill
│       │   └── SKILL.md
│       ├── dev-code-implementation/  # Step 6: コード実装 Skill
│       │   └── SKILL.md
│       ├── dev-refactoring/     # Step 7: リファクタリング Skill
│       │   └── SKILL.md
│       ├── dev-pr-review/       # Step 8: PRレビュー Skill
│       │   └── SKILL.md
│       ├── dev-status-check-workspace/   # Evalワークスペース
│       ├── dev-requirements-workspace/
│       ├── dev-static-screen-workspace/
│       └── dev-implementation-plan-workspace/
└── .vscode/
    └── settings.json            # VS Code 設定（git操作の自動承認）
```

---

### コーディング規約

- **命名規則**:
  - Skillディレクトリ: `dev-[ステップ名]` のケバブケース
  - ワークスペースディレクトリ: `dev-[ステップ名]-workspace`
  - ファイル名: スネークケース（`evals.json`, `SKILL.md` など）
- **ファイル構成パターン**:
  - 各 Skill は `SKILL.md`（仕様）と `evals/evals.json`（評価シナリオ）を持つ
  - ドキュメントは `docs/` 配下の Markdown に記録
  - 一時的・実装固有の情報は GitHub Issue に記録（コードには残さない）
- **成果物の保存方針**:
  - 今回の実装のみ参照: GitHub Issue コメントに記録
  - 長期保有が望ましい内容: `docs/` 配下の Markdown に記録
  - 実装成果（コード・差分）: リポジトリのコードとして保存
  - レビュー・イベント: PR で保存・共有
- **ワークフロー規約**:
  - 開発は 8ステップの順序型ワークフロー（画面駆動開発）
  - 各 Skill は前ステップの完了を前提とする（未完了はエラー）
  - ステップ間の引き継ぎ情報は Issue で管理

---

### 依存関係・設定ファイル

| ファイル | 内容 |
|---------|------|
| `.claude/settings.json` | Claude Code の許可・拒否コマンド設定（Write, Bash, Read, Edit の制限） |
| `.claude/settings.local.json` | ローカル追加設定（dev-implementation-plan への Edit 権限） |
| `.vscode/settings.json` | VS Code の git 操作自動承認設定 |
| `.mcp.json` | MCP サーバー設定（空ファイル） |

> **依存ライブラリ**: `package.json` 等が存在しないため、外部ライブラリへの依存はありません。

---

### プロジェクト特性

このリポジトリは **AI駆動開発ワークフロー自体を定義・管理するメタリポジトリ** です。

- **目的**: Claude Code Skills を使ったAI駆動開発の標準ワークフローを定義・評価する
- **開発手法**: 画面駆動開発（画面を最初に確定させ、それを中心に機能を開発）
- **ワークフロー**: 8ステップ順序型（現状確認 → 要件定義 → 静的画面 → 実装計画 → テスト → 実装 → リファクタリング → PRレビュー）
- **Skill評価**: 各 Skill に `evals.json` が定義されており、入出力評価シナリオが管理されている

---

### 確認が必要な項目

1. **技術スタックの未定義**: リポジトリにアプリケーションコードが存在しない。「ユーザー通知機能」はどのプロジェクト向けに実装するのか確認が必要。
   - このリポジトリ自体に通知機能を追加するのか？
   - それとも別の対象プロジェクトの現状確認が必要か？

2. **フロントエンド技術の選定**: 通知機能に UI が伴う場合、使用するフレームワーク（React/Vue/Svelte など）が未定義。

3. **バックエンド/DB の選定**: 通知の永続化・配信方法（WebSocket/Push通知/メール等）が未定義。

4. **テスト環境の選定**: テストフレームワーク・実行環境が未定義。

> これらが定義されていない場合、次のステップ（要件定義）に進む前にアーキテクチャの基本方針を決定する必要があります。

---

### 次のステップ

現状確認が完了しました。

**確認依頼**: 上記「確認が必要な項目」について、特に以下を教えてください：
- ユーザー通知機能はどのプロジェクト（リポジトリ）に実装しますか？
- このリポジトリ自体が対象であれば、使用する技術スタックを教えてください。

技術スタックが確認できたら、「2. 要件定義 (`dev-requirements`)」に移行して、ユーザー通知機能の画面要件を整理します。
