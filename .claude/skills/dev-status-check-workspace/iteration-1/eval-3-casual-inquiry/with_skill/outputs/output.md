## 現状確認レポート

### 技術スタック

このプロジェクトは、AI駆動開発のワークフローを定義・管理するためのドキュメントリポジトリです。アプリケーションコードは含まれておらず、現時点では以下の構成となっています。

- **フロントエンド**: なし（アプリケーションコードなし）
- **バックエンド**: なし（アプリケーションコードなし）
- **DB**: なし
- **テスト**: なし（アプリケーションコードなし）
- **ビルド/パッケージ**: なし（package.json / Gemfile / pyproject.toml 等は未存在）
- **CI/CD**: なし（.github/workflows/ は未存在）

#### 使用しているツール・技術（ワークフロー・Skill管理層）

- **Claude Code**: AI駆動開発のエージェント実行環境
- **Skill機構**: `.claude/skills/` 配下にSkillを定義し、Claude Codeから呼び出す仕組み
- **設定管理**: `.claude/settings.json` でパーミッション制御（`Write`, `Read`, `Edit`, `Bash` の許可/拒否ルール）
- **Markdown**: ドキュメント・ワークフロー定義のフォーマット

### ディレクトリ構造

```
/home/user/source/ai-driven-development/
├── README.md                        # プロジェクト概要（現在は空）
├── docs/
│   ├── ai-dev-workflow.md           # AI駆動開発ワークフロー全体の定義
│   ├── ai-dev-workflow-skills.md    # 各Skillの設計・役割・成果物の仕様
│   └── ai-dev-workflow-skills-review.md  # Skill仕様の疑問点と回答メモ
└── .claude/
    ├── settings.json                # Claude Codeのパーミッション設定
    ├── settings.local.json          # ローカル設定
    ├── agents/                      # エージェント設定
    └── skills/
        ├── dev-status-check/        # Skill 1: 現状確認
        │   ├── SKILL.md
        │   └── evals/evals.json
        ├── dev-requirements/        # Skill 2: 要件定義
        ├── dev-static-screen/       # Skill 3: 静的画面作成
        ├── dev-implementation-plan/ # Skill 4: 実装計画立案
        ├── dev-test-creation/       # Skill 5: テストコード作成
        ├── dev-code-implementation/ # Skill 6: コード実装
        ├── dev-refactoring/         # Skill 7: リファクタリング
        ├── dev-pr-review/           # Skill 8: PRレビュー
        └── *-workspace/             # 各Skillの作業ディレクトリ（eval結果等）
```

### コーディング規約

このリポジトリはドキュメント・ワークフロー定義が主体のため、コードの命名規則よりもドキュメント規約が中心です。

- **命名規則**:
  - Skillディレクトリ名: kebab-case（例: `dev-status-check`, `dev-requirements`）
  - ファイル名: kebab-case（例: `ai-dev-workflow-skills.md`）
- **ファイル構成**:
  - 各Skillは `SKILL.md` を必ず持つ
  - evalは `evals/evals.json` に定義
  - ワークスペースは `*-workspace/` ディレクトリで管理
- **成果物の記録方針**:
  - 一時的な調査結果・実装時のみ有効な情報: GitHub Issueのコメントに記録
  - 長期保有すべき設計・規約: `docs/` 配下のMarkdownに記録
  - 実装成果（コード・差分）: リポジトリのコードとして管理
  - レビュー・PR関連: PRに記録
- **Issue運用**:
  - Issue本文: 決定・要件・結果・最終アウトプット
  - Issueコメント: 進捗・追加調査・議論・フォローアップ

### ライブラリ一覧

アプリケーション依存ライブラリは現時点では定義されていません。
プロジェクトの性質上、ライブラリ管理ファイル（package.json等）は存在しません。

| カテゴリ | 使用ツール・ライブラリ |
|----------|----------------------|
| AI実行環境 | Claude Code (claude-sonnet-4-6) |
| ドキュメント | Markdown |
| ワークフロー管理 | Skillメカニズム（.claude/skills/） |
| 設定管理 | JSON (settings.json) |

### 確認が必要な項目

このリポジトリは現在「AI駆動開発ワークフロー自体の定義・管理」を目的としており、アプリケーション開発のための具体的な技術スタックは未定義です。新しい機能開発を進めるにあたって、以下の確認が必要です。

1. **開発対象の明確化**: 実装したい機能はこのリポジトリ自体（ワークフローSkillの追加・改善）ですか？それとも別のプロジェクトですか？
2. **フロントエンド技術スタックの決定**: Webアプリを開発する場合、React/Vue/Svelte等どれを使いますか？
3. **バックエンド技術スタックの決定**: バックエンドが必要な場合、Node.js/Rails/FastAPI等どれを使いますか？
4. **テスト戦略の決定**: テストフレームワーク（Jest/Vitest/Playwright等）は何を使いますか？

---

## 次のステップ

現状確認が完了しました。次は **Skill 2: 要件定義 (`dev-requirements`)** に進むことをお勧めします。

要件定義では「何を作るか」を明確にし、画面が必要かどうかの判断も行います。
開発したい機能の概要を教えていただければ、要件定義のサポートを開始します。
