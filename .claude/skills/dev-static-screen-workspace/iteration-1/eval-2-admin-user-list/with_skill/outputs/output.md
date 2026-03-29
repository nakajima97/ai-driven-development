# 管理者ユーザー一覧画面 - 静的ページ実装

## ルート

`/admin/users`

## コンポーネント構成

```
app/
  admin/
    users/
      page.tsx              # ページコンポーネント（メインエントリ）
      _components/
        UserSearchForm.tsx  # 検索フォーム
        UserTable.tsx       # ユーザー一覧テーブル
        SuspendButton.tsx   # 停止ボタン（確認ダイアログ付き）
        StatusBadge.tsx     # ステータスバッジ
      _mocks/
        users.ts            # モックデータ
```

---

## ファイル内容

### `app/admin/users/_mocks/users.ts`

```typescript
export type UserStatus = "active" | "suspended" | "pending";

export type User = {
  id: string;
  name: string;
  email: string;
  role: string;
  status: UserStatus;
  createdAt: string;
  lastLoginAt: string | null;
};

export const mockUsers: User[] = [
  {
    id: "usr_001",
    name: "山田 太郎",
    email: "yamada.taro@example.com",
    role: "一般ユーザー",
    status: "active",
    createdAt: "2024-01-15",
    lastLoginAt: "2026-03-28",
  },
  {
    id: "usr_002",
    name: "鈴木 花子",
    email: "suzuki.hanako@example.com",
    role: "マネージャー",
    status: "active",
    createdAt: "2024-02-20",
    lastLoginAt: "2026-03-27",
  },
  {
    id: "usr_003",
    name: "田中 次郎",
    email: "tanaka.jiro@example.com",
    role: "一般ユーザー",
    status: "suspended",
    createdAt: "2024-03-01",
    lastLoginAt: "2026-02-10",
  },
  {
    id: "usr_004",
    name: "佐藤 美咲",
    email: "sato.misaki@example.com",
    role: "管理者",
    status: "active",
    createdAt: "2023-11-10",
    lastLoginAt: "2026-03-29",
  },
  {
    id: "usr_005",
    name: "伊藤 健一",
    email: "ito.kenichi@example.com",
    role: "一般ユーザー",
    status: "pending",
    createdAt: "2026-03-25",
    lastLoginAt: null,
  },
  {
    id: "usr_006",
    name: "渡辺 由美",
    email: "watanabe.yumi@example.com",
    role: "マネージャー",
    status: "suspended",
    createdAt: "2023-08-05",
    lastLoginAt: "2025-12-01",
  },
  {
    id: "usr_007",
    name: "小林 亮",
    email: "kobayashi.ryo@example.com",
    role: "一般ユーザー",
    status: "active",
    createdAt: "2024-06-18",
    lastLoginAt: "2026-03-20",
  },
  {
    id: "usr_008",
    name: "加藤 奈々",
    email: "kato.nana@example.com",
    role: "一般ユーザー",
    status: "active",
    createdAt: "2024-09-30",
    lastLoginAt: "2026-03-15",
  },
];
```

---

### `app/admin/users/_components/StatusBadge.tsx`

```tsx
import { UserStatus } from "../_mocks/users";

type Props = {
  status: UserStatus;
};

const statusConfig: Record<UserStatus, { label: string; className: string }> = {
  active: {
    label: "有効",
    className: "bg-green-100 text-green-800",
  },
  suspended: {
    label: "停止中",
    className: "bg-red-100 text-red-800",
  },
  pending: {
    label: "承認待ち",
    className: "bg-yellow-100 text-yellow-800",
  },
};

export function StatusBadge({ status }: Props) {
  const config = statusConfig[status];
  return (
    <span
      className={`inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium ${config.className}`}
    >
      {config.label}
    </span>
  );
}
```

---

### `app/admin/users/_components/SuspendButton.tsx`

```tsx
"use client";

import { useState } from "react";
import { UserStatus } from "../_mocks/users";

type Props = {
  userId: string;
  userName: string;
  currentStatus: UserStatus;
};

export function SuspendButton({ userId, userName, currentStatus }: Props) {
  const [showConfirm, setShowConfirm] = useState(false);

  const isSuspended = currentStatus === "suspended";

  const handleClick = () => {
    setShowConfirm(true);
  };

  const handleConfirm = () => {
    // TODO: API連携時に実装
    console.log(
      `${isSuspended ? "再有効化" : "停止"}: userId=${userId}`
    );
    setShowConfirm(false);
  };

  const handleCancel = () => {
    setShowConfirm(false);
  };

  return (
    <>
      <button
        onClick={handleClick}
        className={`inline-flex items-center px-3 py-1.5 text-sm font-medium rounded-md transition-colors focus:outline-none focus:ring-2 focus:ring-offset-1 ${
          isSuspended
            ? "bg-green-50 text-green-700 hover:bg-green-100 focus:ring-green-500 border border-green-300"
            : "bg-red-50 text-red-700 hover:bg-red-100 focus:ring-red-500 border border-red-300"
        }`}
        aria-label={`${userName}を${isSuspended ? "再有効化" : "停止"}`}
      >
        {isSuspended ? "再有効化" : "停止"}
      </button>

      {/* 確認ダイアログ */}
      {showConfirm && (
        <div
          className="fixed inset-0 z-50 flex items-center justify-center bg-black/50"
          role="dialog"
          aria-modal="true"
          aria-labelledby="confirm-dialog-title"
        >
          <div className="bg-white rounded-lg shadow-xl p-6 max-w-sm w-full mx-4">
            <h3
              id="confirm-dialog-title"
              className="text-lg font-semibold text-gray-900 mb-2"
            >
              {isSuspended ? "アカウントを再有効化" : "アカウントを停止"}しますか？
            </h3>
            <p className="text-sm text-gray-600 mb-6">
              <span className="font-medium text-gray-900">{userName}</span>{" "}
              のアカウントを{isSuspended ? "再有効化" : "停止"}します。
              {!isSuspended && (
                <span className="block mt-1 text-red-600">
                  停止中はログインできなくなります。
                </span>
              )}
            </p>
            <div className="flex justify-end gap-3">
              <button
                onClick={handleCancel}
                className="px-4 py-2 text-sm font-medium text-gray-700 bg-white border border-gray-300 rounded-md hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-1 focus:ring-gray-400 transition-colors"
              >
                キャンセル
              </button>
              <button
                onClick={handleConfirm}
                className={`px-4 py-2 text-sm font-medium text-white rounded-md focus:outline-none focus:ring-2 focus:ring-offset-1 transition-colors ${
                  isSuspended
                    ? "bg-green-600 hover:bg-green-700 focus:ring-green-500"
                    : "bg-red-600 hover:bg-red-700 focus:ring-red-500"
                }`}
              >
                {isSuspended ? "再有効化する" : "停止する"}
              </button>
            </div>
          </div>
        </div>
      )}
    </>
  );
}
```

---

### `app/admin/users/_components/UserSearchForm.tsx`

```tsx
"use client";

import { useState } from "react";
import { UserStatus } from "../_mocks/users";

type Props = {
  onSearch: (query: string, statusFilter: UserStatus | "all") => void;
  totalCount: number;
  filteredCount: number;
};

export function UserSearchForm({ onSearch, totalCount, filteredCount }: Props) {
  const [query, setQuery] = useState("");
  const [statusFilter, setStatusFilter] = useState<UserStatus | "all">("all");

  const handleQueryChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const newQuery = e.target.value;
    setQuery(newQuery);
    onSearch(newQuery, statusFilter);
  };

  const handleStatusChange = (e: React.ChangeEvent<HTMLSelectElement>) => {
    const newStatus = e.target.value as UserStatus | "all";
    setStatusFilter(newStatus);
    onSearch(query, newStatus);
  };

  const handleReset = () => {
    setQuery("");
    setStatusFilter("all");
    onSearch("", "all");
  };

  const isFiltered = query !== "" || statusFilter !== "all";

  return (
    <div className="bg-white border border-gray-200 rounded-lg p-4 mb-6">
      <div className="flex flex-col sm:flex-row gap-3">
        {/* キーワード検索 */}
        <div className="flex-1">
          <label
            htmlFor="user-search"
            className="block text-sm font-medium text-gray-700 mb-1"
          >
            キーワード検索
          </label>
          <div className="relative">
            <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
              <svg
                className="h-4 w-4 text-gray-400"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
                aria-hidden="true"
              >
                <path
                  strokeLinecap="round"
                  strokeLinejoin="round"
                  strokeWidth={2}
                  d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
                />
              </svg>
            </div>
            <input
              id="user-search"
              type="text"
              value={query}
              onChange={handleQueryChange}
              placeholder="名前・メールアドレスで検索"
              className="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-md text-sm placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition-colors"
            />
          </div>
        </div>

        {/* ステータスフィルター */}
        <div className="sm:w-48">
          <label
            htmlFor="status-filter"
            className="block text-sm font-medium text-gray-700 mb-1"
          >
            ステータス
          </label>
          <select
            id="status-filter"
            value={statusFilter}
            onChange={handleStatusChange}
            className="block w-full py-2 px-3 border border-gray-300 rounded-md text-sm focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition-colors bg-white"
          >
            <option value="all">すべて</option>
            <option value="active">有効</option>
            <option value="suspended">停止中</option>
            <option value="pending">承認待ち</option>
          </select>
        </div>

        {/* リセットボタン */}
        {isFiltered && (
          <div className="sm:self-end">
            <button
              onClick={handleReset}
              className="w-full sm:w-auto px-4 py-2 text-sm font-medium text-gray-600 bg-gray-100 border border-gray-300 rounded-md hover:bg-gray-200 focus:outline-none focus:ring-2 focus:ring-offset-1 focus:ring-gray-400 transition-colors"
            >
              クリア
            </button>
          </div>
        )}
      </div>

      {/* 検索結果件数 */}
      <div className="mt-3 text-sm text-gray-500">
        {isFiltered ? (
          <span>
            <span className="font-medium text-gray-900">{filteredCount}</span> 件表示
            （全 {totalCount} 件中）
          </span>
        ) : (
          <span>全 <span className="font-medium text-gray-900">{totalCount}</span> 件</span>
        )}
      </div>
    </div>
  );
}
```

---

### `app/admin/users/_components/UserTable.tsx`

```tsx
import { User } from "../_mocks/users";
import { StatusBadge } from "./StatusBadge";
import { SuspendButton } from "./SuspendButton";

type Props = {
  users: User[];
};

export function UserTable({ users }: Props) {
  if (users.length === 0) {
    return (
      <div className="bg-white border border-gray-200 rounded-lg">
        <div className="flex flex-col items-center justify-center py-16 px-4">
          <svg
            className="h-12 w-12 text-gray-300 mb-4"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
            aria-hidden="true"
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth={1.5}
              d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0z"
            />
          </svg>
          <p className="text-gray-500 text-sm font-medium">
            条件に一致するユーザーが見つかりません
          </p>
          <p className="text-gray-400 text-xs mt-1">
            検索条件を変更してお試しください
          </p>
        </div>
      </div>
    );
  }

  return (
    <div className="bg-white border border-gray-200 rounded-lg overflow-hidden">
      {/* デスクトップ・タブレット表示 */}
      <div className="hidden sm:block overflow-x-auto">
        <table className="min-w-full divide-y divide-gray-200">
          <thead className="bg-gray-50">
            <tr>
              <th
                scope="col"
                className="px-6 py-3 text-left text-xs font-semibold text-gray-500 uppercase tracking-wider"
              >
                ユーザー
              </th>
              <th
                scope="col"
                className="px-6 py-3 text-left text-xs font-semibold text-gray-500 uppercase tracking-wider"
              >
                ロール
              </th>
              <th
                scope="col"
                className="px-6 py-3 text-left text-xs font-semibold text-gray-500 uppercase tracking-wider"
              >
                ステータス
              </th>
              <th
                scope="col"
                className="px-6 py-3 text-left text-xs font-semibold text-gray-500 uppercase tracking-wider hidden lg:table-cell"
              >
                登録日
              </th>
              <th
                scope="col"
                className="px-6 py-3 text-left text-xs font-semibold text-gray-500 uppercase tracking-wider hidden lg:table-cell"
              >
                最終ログイン
              </th>
              <th scope="col" className="px-6 py-3 text-right">
                <span className="sr-only">操作</span>
              </th>
            </tr>
          </thead>
          <tbody className="bg-white divide-y divide-gray-200">
            {users.map((user) => (
              <tr
                key={user.id}
                className="hover:bg-gray-50 transition-colors"
              >
                <td className="px-6 py-4 whitespace-nowrap">
                  <div className="flex items-center">
                    <div className="h-9 w-9 flex-shrink-0 rounded-full bg-blue-100 flex items-center justify-center">
                      <span className="text-sm font-semibold text-blue-700">
                        {user.name.charAt(0)}
                      </span>
                    </div>
                    <div className="ml-3">
                      <div className="text-sm font-medium text-gray-900">
                        {user.name}
                      </div>
                      <div className="text-xs text-gray-500">{user.email}</div>
                    </div>
                  </div>
                </td>
                <td className="px-6 py-4 whitespace-nowrap">
                  <span className="text-sm text-gray-700">{user.role}</span>
                </td>
                <td className="px-6 py-4 whitespace-nowrap">
                  <StatusBadge status={user.status} />
                </td>
                <td className="px-6 py-4 whitespace-nowrap hidden lg:table-cell">
                  <span className="text-sm text-gray-500">{user.createdAt}</span>
                </td>
                <td className="px-6 py-4 whitespace-nowrap hidden lg:table-cell">
                  <span className="text-sm text-gray-500">
                    {user.lastLoginAt ?? "未ログイン"}
                  </span>
                </td>
                <td className="px-6 py-4 whitespace-nowrap text-right">
                  <div className="flex items-center justify-end gap-2">
                    <a
                      href={`/admin/users/${user.id}`}
                      className="inline-flex items-center px-3 py-1.5 text-sm font-medium text-blue-700 bg-blue-50 border border-blue-200 rounded-md hover:bg-blue-100 focus:outline-none focus:ring-2 focus:ring-offset-1 focus:ring-blue-500 transition-colors"
                    >
                      詳細
                    </a>
                    {user.status !== "pending" && (
                      <SuspendButton
                        userId={user.id}
                        userName={user.name}
                        currentStatus={user.status}
                      />
                    )}
                  </div>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>

      {/* モバイル表示 */}
      <div className="sm:hidden divide-y divide-gray-200">
        {users.map((user) => (
          <div key={user.id} className="p-4">
            <div className="flex items-center justify-between mb-2">
              <div className="flex items-center gap-3">
                <div className="h-9 w-9 flex-shrink-0 rounded-full bg-blue-100 flex items-center justify-center">
                  <span className="text-sm font-semibold text-blue-700">
                    {user.name.charAt(0)}
                  </span>
                </div>
                <div>
                  <div className="text-sm font-medium text-gray-900">
                    {user.name}
                  </div>
                  <div className="text-xs text-gray-500">{user.email}</div>
                </div>
              </div>
              <StatusBadge status={user.status} />
            </div>
            <div className="flex items-center justify-between mt-3">
              <div className="text-xs text-gray-500">
                <span className="font-medium">{user.role}</span>
                <span className="mx-1">·</span>
                最終ログイン: {user.lastLoginAt ?? "未ログイン"}
              </div>
              <div className="flex items-center gap-2">
                <a
                  href={`/admin/users/${user.id}`}
                  className="inline-flex items-center px-3 py-1.5 text-xs font-medium text-blue-700 bg-blue-50 border border-blue-200 rounded-md hover:bg-blue-100 focus:outline-none focus:ring-2 focus:ring-offset-1 focus:ring-blue-500 transition-colors"
                >
                  詳細
                </a>
                {user.status !== "pending" && (
                  <SuspendButton
                    userId={user.id}
                    userName={user.name}
                    currentStatus={user.status}
                  />
                )}
              </div>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

### `app/admin/users/page.tsx`

```tsx
"use client";

import { useState, useMemo } from "react";
import { mockUsers, UserStatus } from "./_mocks/users";
import { UserSearchForm } from "./_components/UserSearchForm";
import { UserTable } from "./_components/UserTable";

export default function AdminUsersPage() {
  const [searchQuery, setSearchQuery] = useState("");
  const [statusFilter, setStatusFilter] = useState<UserStatus | "all">("all");

  const filteredUsers = useMemo(() => {
    return mockUsers.filter((user) => {
      const matchesQuery =
        searchQuery === "" ||
        user.name.includes(searchQuery) ||
        user.email.toLowerCase().includes(searchQuery.toLowerCase());

      const matchesStatus =
        statusFilter === "all" || user.status === statusFilter;

      return matchesQuery && matchesStatus;
    });
  }, [searchQuery, statusFilter]);

  const handleSearch = (query: string, status: UserStatus | "all") => {
    setSearchQuery(query);
    setStatusFilter(status);
  };

  return (
    <div className="min-h-screen bg-gray-50">
      {/* 管理画面ヘッダー */}
      <header className="bg-white border-b border-gray-200 sticky top-0 z-10">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex items-center justify-between h-16">
            <div className="flex items-center gap-3">
              <a
                href="/admin"
                className="text-gray-400 hover:text-gray-600 transition-colors"
                aria-label="管理トップへ戻る"
              >
                <svg
                  className="h-5 w-5"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                  aria-hidden="true"
                >
                  <path
                    strokeLinecap="round"
                    strokeLinejoin="round"
                    strokeWidth={2}
                    d="M15 19l-7-7 7-7"
                  />
                </svg>
              </a>
              <div>
                <h1 className="text-lg font-semibold text-gray-900">
                  ユーザー管理
                </h1>
              </div>
            </div>
            <div className="flex items-center gap-3">
              <button className="inline-flex items-center gap-2 px-4 py-2 text-sm font-medium text-white bg-blue-600 rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-1 focus:ring-blue-500 transition-colors">
                <svg
                  className="h-4 w-4"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                  aria-hidden="true"
                >
                  <path
                    strokeLinecap="round"
                    strokeLinejoin="round"
                    strokeWidth={2}
                    d="M12 4v16m8-8H4"
                  />
                </svg>
                <span className="hidden sm:inline">ユーザー追加</span>
                <span className="sm:hidden">追加</span>
              </button>
            </div>
          </div>
        </div>
      </header>

      {/* メインコンテンツ */}
      <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        {/* ページタイトルエリア */}
        <div className="mb-6">
          <h2 className="text-xl font-semibold text-gray-900">
            ユーザー一覧
          </h2>
          <p className="mt-1 text-sm text-gray-500">
            登録済みユーザーの管理・停止・詳細確認ができます。
          </p>
        </div>

        {/* ステータス概要カード */}
        <div className="grid grid-cols-2 sm:grid-cols-4 gap-4 mb-6">
          {[
            {
              label: "全ユーザー",
              count: mockUsers.length,
              colorClass: "text-gray-900",
              bgClass: "bg-white",
            },
            {
              label: "有効",
              count: mockUsers.filter((u) => u.status === "active").length,
              colorClass: "text-green-700",
              bgClass: "bg-green-50",
            },
            {
              label: "停止中",
              count: mockUsers.filter((u) => u.status === "suspended").length,
              colorClass: "text-red-700",
              bgClass: "bg-red-50",
            },
            {
              label: "承認待ち",
              count: mockUsers.filter((u) => u.status === "pending").length,
              colorClass: "text-yellow-700",
              bgClass: "bg-yellow-50",
            },
          ].map((card) => (
            <div
              key={card.label}
              className={`${card.bgClass} border border-gray-200 rounded-lg px-4 py-3`}
            >
              <div className={`text-2xl font-bold ${card.colorClass}`}>
                {card.count}
              </div>
              <div className="text-xs text-gray-500 mt-0.5">{card.label}</div>
            </div>
          ))}
        </div>

        {/* 検索フォーム */}
        <UserSearchForm
          onSearch={handleSearch}
          totalCount={mockUsers.length}
          filteredCount={filteredUsers.length}
        />

        {/* ユーザーテーブル */}
        <UserTable users={filteredUsers} />
      </main>
    </div>
  );
}
```

---

## レスポンシブ対応チェックリスト

- [x] モバイル (320px-767px) — カード形式での表示、各行に名前・メール・ステータス・操作ボタンを縦積み
- [x] タブレット (768px-1023px) — テーブル表示（登録日・最終ログイン列は非表示）
- [x] デスクトップ (1024px+) — 全カラム表示
- [x] 大画面 (1440px+) — max-w-7xl でセンタリング

## 実装した機能

- テーブル形式のユーザー一覧（名前・メール・ロール・ステータス・登録日・最終ログイン・操作）
- キーワード検索（名前・メールアドレスによるリアルタイムフィルタリング）
- ステータスフィルター（すべて・有効・停止中・承認待ち）
- 検索結果件数の表示
- 停止ボタン（確認ダイアログ付き）
- 停止中ユーザーの再有効化ボタン
- 承認待ちユーザーには停止ボタンを非表示
- ステータスバッジ（色分け）
- ユーザーアバター（名前の頭文字）
- ステータス概要カード（全・有効・停止中・承認待ち件数）
- 空状態（検索結果が0件のとき）
- ユーザー追加ボタン（ヘッダー内）
- 詳細リンク（各行）

## フィードバック確認ポイント

1. テーブルのカラム構成は適切ですか？（追加・削除したい項目はありますか？）
2. 停止ボタンの確認ダイアログのメッセージ文言は適切ですか？
3. 検索フォームの検索対象（名前・メールのみ）で十分ですか？ロールでの絞り込みも必要ですか？
4. ステータス概要カードは必要ですか？
5. ページネーションは必要ですか？（現在はモックデータ全件表示）
6. ソート機能（列ヘッダークリックで並び替え）は必要ですか？

---

次のステップ: 静的画面を承認したら「実装計画の立案 (`dev-implementation-plan`)」に進みます。
