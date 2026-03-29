# プロジェクト現状確認レポート

対象リポジトリ: `/home/user/source/ai-driven-development`
調査日: 2026-03-29

---

## 技術スタック

このプロジェクトは**ドキュメント・ワークフロー定義リポジトリ**であり、アプリケーションコードを持つ Web プロジェクトとは異なります。以下の構成となっています。

| 項目 | 内容 |
|------|------|
| リポジトリ種別 | AI駆動開発ワークフロー定義・スキル管理リポジトリ |
| 主要言語 | Markdown（ドキュメント）、JSON（設定・eval定義） |
| ビルドツール | なし（アプリケーションビルド不要） |
| パッケージマネージャー | なし（package.json なし） |
| バージョン管理 | Git |
| CI/CD | 未確認（.github/workflows/ なし） |

### 使用ライブラリ・ツール

アプリケーション依存ライブラリは存在しません。ただし、以下のツール・プラットフォームが前提として利用されています。

- **Claude Code (Anthropic)**: AI開発アシスタント。`.claude/` 設定ディレクトリで管理
- **skill-creator プラグイン**: Claude Code の公式プラグイン（`settings.json` で有効化）
- **GitHub Issues**: 各開発ステップの成果物・進捗記録に使用
- **Git**: バージョン管理

---

## ディレクトリ構造

```
ai-driven-development/
├── README.md                        # プロジェクト概要（現在は空）
├── docs/                            # ワークフロー設計ドキュメント
│   ├── ai-dev-workflow.md           # AI駆動開発ワークフローの手順定義
│   ├── ai-dev-workflow-skills.md    # 各ステップのSkill設計リスト
│   └── ai-dev-workflow-skills-review.md  # Skill仕様のQ&Aメモ
└── .claude/                         # Claude Code 設定
    ├── settings.json                # 権限設定・プラグイン設定
    ├── settings.local.json          # ローカル設定
    └── skills/                      # 各Skillの定義と評価
        ├── dev-status-check/        # Step1: 現状確認Skill
        │   ├── SKILL.md
        │   └── evals/evals.json
        ├── dev-requirements/        # Step2: 要件定義Skill
        │   └── SKILL.md
        ├── dev-static-screen/       # Step3: 静的画面作成Skill
        │   ├── SKILL.md
        │   └── evals/
        ├── dev-implementation-plan/ # Step4: 実装計画Skill
        │   ├── SKILL.md
        │   └── evals/
        ├── dev-test-creation/       # Step5: テスト作成Skill
        │   └── SKILL.md
        ├── dev-code-implementation/ # Step6: コード実装Skill
        │   └── SKILL.md
        ├── dev-refactoring/         # Step7: リファクタリングSkill
        │   └── SKILL.md
        ├── dev-pr-review/           # Step8: PRレビューSkill
        │   └── SKILL.md
        └── *-workspace/             # 各Skillの評価実行ワークスペース
```

---

## コーディング規約・運用ルール

### ドキュメント規約

- **Markdown形式**: 全ドキュメントは `.md` ファイルで管理
- **docs/ 配下**: 長期保有する設計・アーキテクチャ方針を記録
- **GitHub Issues**: 一時的な調査結果・スポット対応・進捗はIssueに記録
  - Issue本文: 決定事項・要件・最終アウトプット
  - Issueコメント: 進捗・追加調査・議論・フォローアップ

### Git 規約

- コミットメッセージは `docs:` プレフィックスを使用している（例: `docs: add skill spec review with Q&A`）
- 日本語コミットメッセージも使用されている（例: `skill-creatorを追加`）

### Claude Code 権限設定（`.claude/settings.json`）

許可されている操作:
- `git status`, `git add *`, `git commit *`, `git log *`, `git push`
- `Write(*.md)`, `Read(*.md)`
- `Write(.claude/skills/**)`, `Edit(/.claude/skills/**)`
- `mkdir -p *`

禁止されている操作:
- `curl *`（外部通信禁止）
- `.env` および `secrets/` の読み込み禁止

---

## プロジェクトの目的・コンセプト

このリポジトリは **AI駆動開発ワークフロー**の定義・実装・評価を行うためのものです。

### ワークフローの8ステップ

1. **現状確認** (`dev-status-check`) - 技術スタック・既存コードの把握
2. **要件定義** (`dev-requirements`) - 画面要件の明確化
3. **静的画面の作成** (`dev-static-screen`) - UIモック生成
4. **実装計画の立案** (`dev-implementation-plan`) - API/DB設計
5. **テストコード作成** (`dev-test-creation`) - テスト生成
6. **コード実装** (`dev-code-implementation`) - テストグリーンを目指す実装
7. **リファクタリング** (`dev-refactoring`) - コード品質改善
8. **PR作成とレビュー依頼** (`dev-pr-review`) - 成果共有

### 開発コンセプト

- **画面駆動開発**: 画面をまず確定させ、それを中心に機能を開発する
- **コードを正**: ドキュメントは補助、実際のコードを信頼の源とする
- **Web開発前提**: フロントエンドを中心としたWeb開発フローを対象とする

---

## 確認が必要な項目

このプロジェクト自体はドキュメントリポジトリのため、新機能開発に際して以下を確認する必要があります。

1. **開発対象**: 新しいSkillを追加するのか、既存Skillを改善するのか？
2. **ターゲット言語/フレームワーク**: 新しいSkillを適用するアプリのスタックは何か？
3. **GitHub Issue**: 本開発タスクに対応するIssueが作成済みか？（成果物の記録先として必要）

---

## 次のステップ

現状確認が完了しました。次は **Step 2: 要件定義 (`dev-requirements`)** に進むことをお勧めします。

どんな機能を開発したいかを教えていただければ、要件定義のサポートができます。
