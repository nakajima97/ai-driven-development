# プロフィール編集機能 バックエンド実装計画

## 概要

プロフィール編集画面（名前・アイコンURL・自己紹介）に対応するバックエンド実装の計画書です。

技術スタック: Next.js (App Router) + TypeScript + PostgreSQL + Prisma

---

## 1. DBスキーマ設計

### Prismaスキーマ (`prisma/schema.prisma`)

```prisma
model User {
  id          String   @id @default(cuid())
  email       String   @unique
  name        String?
  iconUrl     String?
  bio         String?
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  @@map("users")
}
```

### マイグレーション

既存の `User` モデルに以下のフィールドを追加する想定:

| フィールド | 型       | 制約        | 説明               |
|-----------|---------|------------|-------------------|
| `name`    | String  | nullable   | 表示名（最大100文字）|
| `iconUrl` | String  | nullable   | アイコン画像のURL   |
| `bio`     | String  | nullable   | 自己紹介（最大500文字）|

---

## 2. APIエンドポイント設計

### GET `/api/profile`

現在ログイン中のユーザーのプロフィールを取得する。

**Request**
- Headers: `Authorization: Bearer <token>` （セッション認証）

**Response 200**
```json
{
  "id": "clxxxx",
  "name": "山田太郎",
  "iconUrl": "https://example.com/icon.png",
  "bio": "よろしくお願いします。"
}
```

**Response 401**
```json
{ "error": "Unauthorized" }
```

---

### PUT `/api/profile`

現在ログイン中のユーザーのプロフィールを更新する。

**Request Body**
```json
{
  "name": "山田太郎",
  "iconUrl": "https://example.com/icon.png",
  "bio": "よろしくお願いします。"
}
```

**バリデーションルール**
| フィールド | 必須 | 最大長 | 備考                         |
|-----------|------|-------|------------------------------|
| `name`    | 任意 | 100文字 | 空文字はnullとして扱う        |
| `iconUrl` | 任意 | 2083文字 | URL形式チェック               |
| `bio`     | 任意 | 500文字 | 改行を許容                    |

**Response 200**
```json
{
  "id": "clxxxx",
  "name": "山田太郎",
  "iconUrl": "https://example.com/icon.png",
  "bio": "よろしくお願いします。"
}
```

**Response 400**
```json
{
  "error": "Validation failed",
  "details": {
    "name": "最大100文字です",
    "iconUrl": "有効なURLを入力してください"
  }
}
```

**Response 401**
```json
{ "error": "Unauthorized" }
```

---

## 3. ファイル構成

```
app/
  api/
    profile/
      route.ts          # GET / PUT ハンドラ
lib/
  validations/
    profile.ts          # Zodスキーマ定義
  db/
    profile.ts          # Prismaクエリ関数
```

---

## 4. 実装詳細

### バリデーションスキーマ (`lib/validations/profile.ts`)

```typescript
import { z } from "zod";

export const updateProfileSchema = z.object({
  name: z.string().max(100).optional().nullable(),
  iconUrl: z.string().url().max(2083).optional().nullable(),
  bio: z.string().max(500).optional().nullable(),
});

export type UpdateProfileInput = z.infer<typeof updateProfileSchema>;
```

### DBクエリ関数 (`lib/db/profile.ts`)

```typescript
import { prisma } from "@/lib/prisma";
import type { UpdateProfileInput } from "@/lib/validations/profile";

export async function getProfile(userId: string) {
  return prisma.user.findUnique({
    where: { id: userId },
    select: { id: true, name: true, iconUrl: true, bio: true },
  });
}

export async function updateProfile(userId: string, data: UpdateProfileInput) {
  return prisma.user.update({
    where: { id: userId },
    data,
    select: { id: true, name: true, iconUrl: true, bio: true },
  });
}
```

### APIルート (`app/api/profile/route.ts`)

```typescript
import { NextResponse } from "next/server";
import { auth } from "@/lib/auth"; // 認証ライブラリに合わせて変更
import { updateProfileSchema } from "@/lib/validations/profile";
import { getProfile, updateProfile } from "@/lib/db/profile";

export async function GET() {
  const session = await auth();
  if (!session?.user?.id) {
    return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
  }

  const profile = await getProfile(session.user.id);
  return NextResponse.json(profile);
}

export async function PUT(request: Request) {
  const session = await auth();
  if (!session?.user?.id) {
    return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
  }

  const body = await request.json();
  const result = updateProfileSchema.safeParse(body);

  if (!result.success) {
    return NextResponse.json(
      { error: "Validation failed", details: result.error.flatten().fieldErrors },
      { status: 400 }
    );
  }

  const updated = await updateProfile(session.user.id, result.data);
  return NextResponse.json(updated);
}
```

---

## 5. タスク分解

| # | タスク | 優先度 | 見積もり |
|---|-------|--------|---------|
| 1 | Prismaスキーマに `name` / `iconUrl` / `bio` フィールドを追加 | 高 | 0.5h |
| 2 | DBマイグレーション実行 | 高 | 0.5h |
| 3 | Zodバリデーションスキーマ作成 (`lib/validations/profile.ts`) | 高 | 0.5h |
| 4 | Prismaクエリ関数実装 (`lib/db/profile.ts`) | 高 | 0.5h |
| 5 | GET `/api/profile` ルート実装 | 高 | 0.5h |
| 6 | PUT `/api/profile` ルート実装 | 高 | 1h |
| 7 | 静的画面とAPIを繋ぎ込み（フロントエンド側の `fetch` 実装） | 中 | 1h |
| 8 | エラーハンドリング・ローディング状態の実装 | 中 | 0.5h |
| 9 | ユニットテスト（バリデーション・DBクエリ） | 中 | 1h |
| 10 | E2Eテスト（プロフィール更新フロー） | 低 | 1h |

**合計見積もり**: 約 7h

---

## 6. 考慮事項・注意点

- **認証**: `auth()` の実装は既存の認証ライブラリ（NextAuth.js / Clerk 等）に合わせてください。
- **iconUrl のセキュリティ**: URLの形式チェックのみ行い、ドメイン制限が必要な場合はアプリ要件に応じて追加してください。
- **既存ユーザーデータ**: マイグレーション時、既存レコードの新規フィールドは `NULL` になります。フロントエンド側でnullを空文字として扱う処理が必要です。
- **画像アップロード**: 今回はURL入力のみの対応です。将来的にファイルアップロードが必要な場合は別途設計が必要です。
