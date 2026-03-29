## 現状確認レポート

### 技術スタック

- フロントエンド: なし（現時点でフロントエンドコードは存在しない）
- バックエンド: なし（現時点でバックエンドコードは存在しない）
- DB: 未定義
- テスト: 未定義
- ビルド/パッケージ: 未定義

> **注意**: このリポジトリはアプリケーションコードを持たないドキュメント・ワークフロー設計リポジトリです。`package.json`、`Gemfile`、`pyproject.toml`、`go.mod` などの依存関係ファイルは存在しません。

---

### ディレクトリ構造

```
/home/user/source/ai-driven-development/
├── README.md                        # プロジェクト概要（現在は空に近い）
├── docs/                            # ワークフロー設計ドキュメント
│   ├── ai-dev-workflow.md           # AI駆動開発ワークフローの全体設計・手順書
│   ├── ai-dev-workflow-skills.md    # 各Skillの役割・入出力・成果物の定義一覧
│   └── ai-dev-workflow-skills-review.md  # Skill仕様に対するQ&A・疑問点整理
└── .claude/                         # Claude Code設定・Skillファイル群
    ├── settings.json                # Claude Code権限設定
    ├── settings.local.json          # ローカル設定（追加ディレクトリ等）
    └── skills/                      # AI駆動開発ワークフロー Skill群
        ├── dev-status-check/        # Skill 1: 現状確認
        │   ├── SKILL.md
        │   └── evals/
        │       ├── evals.json       # 評価シナリオ定義
        │       └── outputs/         # Skill実行時の出力保存先
        ├── dev-requirements/        # Skill 2: 要件定義
        ├── dev-static-screen/       # Skill 3: 静的画面作成
        ├── dev-implementation-plan/ # Skill 4: 実装計画
        ├── dev-test-creation/       # Skill 5: テストコード作成
        ├── dev-code-implementation/ # Skill 6: コード実装
        ├── dev-refactoring/         # Skill 7: リファクタリング
        ├── dev-pr-review/           # Skill 8: PRレビュー依頼
        ├── dev-status-check-workspace/     # Skillの評価ワークスペース
        ├── dev-requirements-workspace/
        ├── dev-static-screen-workspace/
        └── dev-implementation-plan-workspace/
```

---

### コーディング規約

- **命名規則**: ケバブケース（`dev-status-check`、`ai-dev-workflow-skills.md` など）でディレクトリ・ファイルを命名
- **ファイル構成**:
  - 各 Skill は `SKILL.md`（手順・成果物定義）と `evals/`（評価シナリオ）で構成
  - ドキュメントはすべて `docs/` 配下に配置
  - 設定は `.claude/settings.json`（共有設定）と `.claude/settings.local.json`（ローカル設定）に分離
- **成果物の記録方針**（`docs/ai-dev-workflow-skills.md` より）:
  - 一時的な調査結果・スポット対応 → GitHub Issue にコメントとして記録
  - 長期保有が望ましい内容（アーキテクチャ指針・運用ルール等） → `docs/` 配下の Markdown に記録
  - 実装成果（コード・差分） → コードとして保存・管理
  - レビュー・イベント → PR で保存・共有
- **その他**:
  - Issueの本文: 決定・要件・結果・最終アウトプット
  - Issueのコメント: 進捗・追加調査・議論・フォローアップ

---

### 既存コードパターン

このリポジトリにはアプリケーションコードは存在しないが、Skill設計から以下のパターンが読み取れる:

- **Skill構成パターン**: 各Skillは `SKILL.md`（フロントマター付き）+ `evals/evals.json` の構造
- **フロントマター形式**: `name`・`description` フィールドを持つ YAML フロントマター
- **評価（eval）パターン**: `id`・`prompt`・`expected_output`・`files`・`assertions` を持つ JSON 形式
- **ワークフロー設計原則**:
  - 画面駆動開発（画面を最初に確定させてから機能開発）
  - 前のSkillの完了が次のSkillの前提条件（未完了はエラー）
  - シーケンシャル実行とスタンドアロン実行の両方をサポート

---

### テストツール

- 専用のテストフレームワーク: 未定義（package.jsonなし）
- Skill評価の仕組み: `evals/evals.json` による評価シナリオ定義（`assertions` 形式）
- 評価ワークスペース: `*-workspace/iteration-*/eval-*/with_skill/outputs/` 構造で出力を管理

---

### 確認が必要な項目

1. **アプリケーション開発の対象**: このリポジトリ自体はワークフロー設計・Skillのみのリポジトリであり、実際に開発するアプリケーションが別リポジトリに存在するかどうか不明。開発対象のプロジェクトのパスを確認する必要がある。
2. **テスト実行環境**: Skill評価を自動実行するためのCI/CDや実行スクリプトが未整備。
3. **GitHub連携**: Issue番号やリポジトリの設定が未確認。現状確認レポートをIssueコメントとして記録するためにはGitHub Issueへのアクセスが必要。

---

## 次のステップ

現状確認が完了しました。このプロジェクトはAI駆動開発ワークフローの **Skill設計・評価リポジトリ** です。

アプリケーション機能の開発を進める場合は、**「2. 要件定義 Skill (`dev-requirements`)」** への移行を提案します。

要件定義では以下を整理します:
- 開発する機能の目的とユーザーストーリー
- 画面が必要か否かの判断
- 画面構成案・機能リスト・優先度
