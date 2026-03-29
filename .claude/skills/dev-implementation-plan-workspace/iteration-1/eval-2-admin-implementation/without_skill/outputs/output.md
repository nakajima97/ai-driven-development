# 管理者ユーザー管理機能 実装計画

## 概要

管理者が一般ユーザーを管理するための機能を実装する。対象機能は以下の3つ。

- ユーザー一覧取得API
- ユーザー停止API
- ユーザー検索機能

技術スタック: Next.js (App Router) + TypeScript + PostgreSQL + Prisma

---

## DBスキーマ設計

### Userテーブル（既存テーブルへの追加を想定）

```prisma
model User {
  id          String      @id @default(cuid())
  email       String      @unique
  name        String
  role        UserRole    @default(USER)
  status      UserStatus  @default(ACTIVE)
  suspendedAt DateTime?
  suspendedBy String?
  createdAt   DateTime    @default(now())
  updatedAt   DateTime    @updatedAt

  suspendedByAdmin Admin? @relation(fields: [suspendedBy], references: [id])
}

model Admin {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  suspendedUsers User[]
}

enum UserRole {
  USER
  ADMIN
}

enum UserStatus {
  ACTIVE
  SUSPENDED
}
```

### マイグレーション対応

- `status` カラム追加（デフォルト: `ACTIVE`）
- `suspendedAt` カラム追加（停止日時記録用）
- `suspendedBy` カラム追加（操作した管理者ID記録用、監査ログ目的）

---

## API設計

### 1. ユーザー一覧取得API

**エンドポイント**: `GET /api/admin/users`

**クエリパラメータ**:

| パラメータ | 型 | 必須 | 説明 |
|---|---|---|---|
| `page` | number | No | ページ番号（デフォルト: 1） |
| `limit` | number | No | 1ページあたりの件数（デフォルト: 20、最大: 100） |
| `status` | `ACTIVE` \| `SUSPENDED` | No | ステータスでフィルタ |
| `q` | string | No | 検索クエリ（email・name部分一致） |
| `sortBy` | `createdAt` \| `name` \| `email` | No | ソート対象（デフォルト: `createdAt`） |
| `order` | `asc` \| `desc` | No | ソート順（デフォルト: `desc`） |

**レスポンス**:

```json
{
  "users": [
    {
      "id": "string",
      "email": "string",
      "name": "string",
      "status": "ACTIVE | SUSPENDED",
      "suspendedAt": "ISO8601 | null",
      "createdAt": "ISO8601"
    }
  ],
  "pagination": {
    "total": 100,
    "page": 1,
    "limit": 20,
    "totalPages": 5
  }
}
```

**ファイルパス**: `app/api/admin/users/route.ts`

---

### 2. ユーザー停止API

**エンドポイント**: `PATCH /api/admin/users/[id]/status`

**リクエストボディ**:

```json
{
  "status": "SUSPENDED | ACTIVE",
  "reason": "string (optional)"
}
```

**レスポンス**:

```json
{
  "id": "string",
  "status": "SUSPENDED | ACTIVE",
  "suspendedAt": "ISO8601 | null",
  "updatedAt": "ISO8601"
}
```

**エラーケース**:
- `404`: 対象ユーザーが存在しない
- `400`: 既に同じステータスのユーザーへの操作
- `403`: 管理者以外がアクセス

**ファイルパス**: `app/api/admin/users/[id]/status/route.ts`

---

### 3. 検索機能

一覧取得APIのクエリパラメータ `q` で実現する。別エンドポイントは不要。

**検索対象フィールド**: `name`（前方・部分一致）、`email`（前方・部分一致）

**Prisma実装例**:

```typescript
where: q
  ? {
      OR: [
        { name: { contains: q, mode: 'insensitive' } },
        { email: { contains: q, mode: 'insensitive' } },
      ],
    }
  : undefined,
```

---

## ディレクトリ構成

```
app/
  api/
    admin/
      users/
        route.ts                   # GET /api/admin/users（一覧・検索）
        [id]/
          status/
            route.ts               # PATCH /api/admin/users/[id]/status
lib/
  admin/
    users.ts                       # ビジネスロジック（Prismaクエリ）
    validation.ts                  # Zodスキーマ
  auth/
    admin-guard.ts                 # 管理者認証ミドルウェア
prisma/
  schema.prisma                    # 上記スキーマ追加
  migrations/
    YYYYMMDD_add_user_status/      # マイグレーションファイル
```

---

## 認証・認可

- 全管理者APIは認証済み管理者ロールのみアクセス可
- Next.js middleware または各ルートハンドラ内でセッション検証
- `admin-guard.ts` として共通処理を切り出す

```typescript
// lib/auth/admin-guard.ts
export async function requireAdmin(request: Request) {
  const session = await getServerSession();
  if (!session || session.user.role !== 'ADMIN') {
    throw new Error('Forbidden');
  }
  return session;
}
```

---

## タスク分解

### フェーズ1: DB・基盤整備（優先度: 高）

| # | タスク | 見積 |
|---|---|---|
| 1-1 | Prismaスキーマに `status`, `suspendedAt`, `suspendedBy` カラム追加 | 0.5h |
| 1-2 | マイグレーション作成・適用 | 0.5h |
| 1-3 | 管理者認証ガード (`admin-guard.ts`) の実装 | 1h |
| 1-4 | Zodバリデーションスキーマ作成 (`validation.ts`) | 0.5h |

### フェーズ2: API実装（優先度: 高）

| # | タスク | 見積 |
|---|---|---|
| 2-1 | ユーザー一覧取得ビジネスロジック実装 (`lib/admin/users.ts`) | 1.5h |
| 2-2 | 一覧取得APIルートハンドラ実装 (`app/api/admin/users/route.ts`) | 1h |
| 2-3 | 検索クエリパラメータ対応 | 0.5h |
| 2-4 | ユーザー停止ビジネスロジック実装 | 1h |
| 2-5 | ユーザー停止APIルートハンドラ実装 | 0.5h |

### フェーズ3: テスト（優先度: 中）

| # | タスク | 見積 |
|---|---|---|
| 3-1 | 一覧取得APIのユニットテスト | 1.5h |
| 3-2 | ユーザー停止APIのユニットテスト | 1h |
| 3-3 | 認証ガードのテスト | 0.5h |

### フェーズ4: 管理画面UI（優先度: 中）

| # | タスク | 見積 |
|---|---|---|
| 4-1 | ユーザー一覧ページ作成 (`app/admin/users/page.tsx`) | 2h |
| 4-2 | 検索フォームコンポーネント実装 | 1h |
| 4-3 | ユーザー停止・有効化ボタン・確認ダイアログ | 1.5h |
| 4-4 | ページネーションコンポーネント | 1h |

**合計見積**: 約15.5時間

---

## 実装上の注意事項

### パフォーマンス
- `email` カラムにインデックスが存在しない場合は追加（Prismaスキーマの `@unique` により自動付与される）
- 検索クエリが多い場合は全文検索インデックス（`pg_trgm`）の導入を検討

### セキュリティ
- 管理者自身を停止できないよう制御する
- 操作ログ（誰がいつ誰を停止したか）は `suspendedBy` / `suspendedAt` で記録
- レート制限を検討（停止APIへの連打防止）

### エラーハンドリング
- 全APIは一貫したエラーレスポンス形式を使用する
```json
{
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "指定されたユーザーが見つかりません"
  }
}
```

---

## 実装順序（推奨）

1. DBスキーマ変更・マイグレーション（フェーズ1）
2. 認証ガード・バリデーション実装（フェーズ1）
3. 一覧取得API実装（フェーズ2）
4. ユーザー停止API実装（フェーズ2）
5. テスト作成（フェーズ3）
6. 管理画面UI実装（フェーズ4）
