# 現状確認レポート

対象リポジトリ: `/home/user/source/ai-driven-development`

---

## 技術スタック

### 言語・フレームワーク
- **言語**: 特定のプログラミング言語のソースコードは存在しない（ドキュメント・設定のみのリポジトリ）
- **ツール**: Claude Code（AI駆動開発ワークフロー管理）

### フロントエンド
- 該当なし（コードベース未実装）

### バックエンド
- 該当なし（コードベース未実装）

### データベース
- 該当なし

### テストツール
- 該当なし（テストコードは存在しないが、Skill評価（evals）の仕組みが `.claude/skills/*/evals/evals.json` に定義されている）

### ビルドツール・パッケージマネージャー
- 該当なし（`package.json`, `Gemfile`, `pyproject.toml`, `go.mod` などは存在しない）

---

## ディレクトリ構造

```
/home/user/source/ai-driven-development/
├── README.md                  # プロジェクト概要（現状はほぼ空）
├── docs/                      # ドキュメント群
│   ├── ai-dev-workflow.md     # AI駆動開発ワークフロー全体の設計書
│   ├── ai-dev-workflow-skills.md  # 各Skillの設計リストと成果物定義
│   └── ai-dev-workflow-skills-review.md  # Skill仕様の疑問点と回答メモ
└── .claude/                   # Claude Code設定ディレクトリ
    ├── settings.json          # Claude Code権限設定
    ├── settings.local.json    # ローカル設定
    ├── skills/                # AI駆動開発ワークフローのSkill群
    │   ├── dev-status-check/          # Step 1: 現状確認
    │   ├── dev-requirements/          # Step 2: 要件定義
    │   ├── dev-static-screen/         # Step 3: 静的画面作成
    │   ├── dev-implementation-plan/   # Step 4: 実装計画
    │   ├── dev-test-creation/         # Step 5: テスト作成
    │   ├── dev-code-implementation/   # Step 6: コード実装
    │   ├── dev-refactoring/           # Step 7: リファクタリング
    │   ├── dev-pr-review/             # Step 8: PRレビュー
    │   └── *-workspace/               # 各Skillの評価作業ディレクトリ
    └── agents/                # （空）
```

各 Skill ディレクトリの構成:
```
dev-[skill-name]/
├── SKILL.md          # Skillの定義・手順・成果物仕様
└── evals/            # 評価用データ（一部Skillのみ）
    └── evals.json    # テストプロンプト・アサーション
```

---

## コーディング規約・パターン

### ドキュメント規約
- Markdown形式（`.md`）でドキュメントを管理
- `docs/` 配下に長期保有ドキュメントを格納
- GitHub Issue にはコメントとして一時的な調査結果を記録
- PR本文・コメントにはレビュー関連情報を記録

### Skill設計パターン
- 各 Skill は `SKILL.md` で定義され、以下の構造を持つ:
  - フロントマター（`name`, `description`）
  - 概要・前提条件・手順・成果物・次のステップ
- 成果物の保存先は3種類:
  - Issue コメント: 一時的な調査結果、進捗メモ
  - Markdown（`docs/`）: 長期保有の設計・規約ドキュメント
  - コード/PR: 実装差分・レビューログ

### 命名規則
- Skill名: ケバブケース（`dev-status-check`, `dev-requirements` など）
- ワークスペース: `[skill-name]-workspace` という命名
- ドキュメント: `ai-dev-workflow*.md` のようなドット区切り命名

### ワークフロー設計パターン
- **順次実行**: 各Stepは前のStepの完了を前提とする（未完了の場合はエラー）
- **スタンドアロン実行**: 単独のStepを任意に起動することも可能
- **画面駆動開発**: 画面を最初に確定させ、それを中心に実装を進める

---

## 開発ワークフロー概要

このリポジトリはAI駆動開発のワークフロー自体を定義・管理するためのリポジトリ。実際のアプリケーションコードは存在せず、ワークフロー設計ドキュメントとClaudeのSkill定義が主なコンテンツ。

ワークフローの8ステップ:
1. **現状確認** (`dev-status-check`) - 技術スタック・コード規約の収集
2. **要件定義** (`dev-requirements`) - 画面要件の整理・構造化
3. **静的画面作成** (`dev-static-screen`) - UIモック・プロトタイプ生成
4. **実装計画** (`dev-implementation-plan`) - API/DB/アーキテクチャ設計
5. **テスト作成** (`dev-test-creation`) - ユニット/統合/E2Eテスト生成
6. **コード実装** (`dev-code-implementation`) - テストグリーンを目指す実装
7. **リファクタリング** (`dev-refactoring`) - 品質改善・最適化
8. **PRレビュー** (`dev-pr-review`) - PR作成・レビュー依頼

---

## CI/CD設定

`.github/workflows/` は存在しない（CI/CD未設定）。

---

## Claude Code権限設定（`.claude/settings.json`）

許可された操作:
- `git status`, `git add *`, `git commit *`, `git log *`, `git push`
- `Write(*.md)`, `Read(*.md)`, `Edit(/.claude/skills/**)`, `Write(.claude/skills/**)`
- `mkdir -p *`

禁止された操作:
- `curl *`
- `.env` や `secrets/` の読み取り

---

## 確認が必要な項目

- 実際のアプリケーションコード（フロントエンド・バックエンド）は存在しない。このリポジトリはワークフロー管理に特化しており、特定の技術スタックは持っていない。
- アーキテクチャは「ドキュメント/Skill定義リポジトリ」として機能しており、コードを開発する場合は別リポジトリを対象にする想定。

---

## 次のステップ

このリポジトリ自体に実装するコードがある場合は、「2. 要件定義 Skill (`dev-requirements`)」へ移行することを推奨します。

ただし、このリポジトリがワークフロー設計リポジトリであることを踏まえると、**実際の開発対象リポジトリを別途指定**する必要があります。開発したい機能やリポジトリを教えてください。
