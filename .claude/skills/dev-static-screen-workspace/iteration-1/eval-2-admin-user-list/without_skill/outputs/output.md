# 管理者ユーザー一覧画面 静的ページ実装

## ファイルパス
`app/admin/users/page.tsx`

## 実装コード

```tsx
// app/admin/users/page.tsx

import { useState } from "react";

// 型定義
type UserStatus = "active" | "suspended";

interface User {
  id: number;
  name: string;
  email: string;
  role: string;
  status: UserStatus;
  createdAt: string;
  lastLogin: string;
}

// ダミーデータ
const DUMMY_USERS: User[] = [
  {
    id: 1,
    name: "山田 太郎",
    email: "yamada@example.com",
    role: "管理者",
    status: "active",
    createdAt: "2024-01-15",
    lastLogin: "2026-03-28",
  },
  {
    id: 2,
    name: "佐藤 花子",
    email: "sato@example.com",
    role: "一般ユーザー",
    status: "active",
    createdAt: "2024-03-22",
    lastLogin: "2026-03-27",
  },
  {
    id: 3,
    name: "鈴木 一郎",
    email: "suzuki@example.com",
    role: "一般ユーザー",
    status: "suspended",
    createdAt: "2023-11-05",
    lastLogin: "2025-12-10",
  },
  {
    id: 4,
    name: "田中 美咲",
    email: "tanaka@example.com",
    role: "モデレーター",
    status: "active",
    createdAt: "2024-06-01",
    lastLogin: "2026-03-29",
  },
  {
    id: 5,
    name: "伊藤 健太",
    email: "ito@example.com",
    role: "一般ユーザー",
    status: "active",
    createdAt: "2025-01-20",
    lastLogin: "2026-03-25",
  },
  {
    id: 6,
    name: "渡辺 由美",
    email: "watanabe@example.com",
    role: "一般ユーザー",
    status: "suspended",
    createdAt: "2023-08-14",
    lastLogin: "2025-09-03",
  },
  {
    id: 7,
    name: "小林 正樹",
    email: "kobayashi@example.com",
    role: "モデレーター",
    status: "active",
    createdAt: "2024-09-10",
    lastLogin: "2026-03-28",
  },
  {
    id: 8,
    name: "加藤 さくら",
    email: "kato@example.com",
    role: "一般ユーザー",
    status: "active",
    createdAt: "2025-02-28",
    lastLogin: "2026-03-20",
  },
];

export default function AdminUsersPage() {
  const [searchQuery, setSearchQuery] = useState("");
  const [roleFilter, setRoleFilter] = useState("all");
  const [statusFilter, setStatusFilter] = useState("all");
  const [users, setUsers] = useState<User[]>(DUMMY_USERS);
  const [suspendingUserId, setSuspendingUserId] = useState<number | null>(null);

  // フィルタリング
  const filteredUsers = users.filter((user) => {
    const matchesSearch =
      user.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
      user.email.toLowerCase().includes(searchQuery.toLowerCase());
    const matchesRole = roleFilter === "all" || user.role === roleFilter;
    const matchesStatus =
      statusFilter === "all" || user.status === statusFilter;
    return matchesSearch && matchesRole && matchesStatus;
  });

  // 停止ボタンのハンドラ
  const handleSuspend = (userId: number) => {
    setSuspendingUserId(userId);
  };

  const confirmSuspend = () => {
    if (suspendingUserId === null) return;
    setUsers((prev) =>
      prev.map((u) =>
        u.id === suspendingUserId ? { ...u, status: "suspended" as UserStatus } : u
      )
    );
    setSuspendingUserId(null);
  };

  const cancelSuspend = () => {
    setSuspendingUserId(null);
  };

  const activeCount = users.filter((u) => u.status === "active").length;
  const suspendedCount = users.filter((u) => u.status === "suspended").length;

  return (
    <div className="min-h-screen bg-gray-50">
      {/* ヘッダー */}
      <header className="bg-white border-b border-gray-200 shadow-sm">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="w-8 h-8 bg-indigo-600 rounded-lg flex items-center justify-center">
              <svg
                className="w-5 h-5 text-white"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  strokeLinecap="round"
                  strokeLinejoin="round"
                  strokeWidth={2}
                  d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z"
                />
              </svg>
            </div>
            <div>
              <h1 className="text-xl font-semibold text-gray-900">
                ユーザー管理
              </h1>
              <p className="text-xs text-gray-500">管理者ダッシュボード</p>
            </div>
          </div>
          <button className="inline-flex items-center gap-2 px-4 py-2 bg-indigo-600 text-white text-sm font-medium rounded-lg hover:bg-indigo-700 transition-colors">
            <svg
              className="w-4 h-4"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                strokeLinecap="round"
                strokeLinejoin="round"
                strokeWidth={2}
                d="M12 4v16m8-8H4"
              />
            </svg>
            新規ユーザー追加
          </button>
        </div>
      </header>

      <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        {/* 統計カード */}
        <div className="grid grid-cols-1 sm:grid-cols-3 gap-4 mb-8">
          <div className="bg-white rounded-xl border border-gray-200 p-5 shadow-sm">
            <p className="text-sm text-gray-500 mb-1">総ユーザー数</p>
            <p className="text-3xl font-bold text-gray-900">{users.length}</p>
          </div>
          <div className="bg-white rounded-xl border border-gray-200 p-5 shadow-sm">
            <p className="text-sm text-gray-500 mb-1">アクティブ</p>
            <p className="text-3xl font-bold text-green-600">{activeCount}</p>
          </div>
          <div className="bg-white rounded-xl border border-gray-200 p-5 shadow-sm">
            <p className="text-sm text-gray-500 mb-1">停止中</p>
            <p className="text-3xl font-bold text-red-500">{suspendedCount}</p>
          </div>
        </div>

        {/* 検索・フィルタフォーム */}
        <div className="bg-white rounded-xl border border-gray-200 shadow-sm p-5 mb-6">
          <h2 className="text-sm font-medium text-gray-700 mb-4">
            検索・フィルター
          </h2>
          <div className="flex flex-col sm:flex-row gap-3">
            {/* キーワード検索 */}
            <div className="flex-1 relative">
              <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                <svg
                  className="w-4 h-4 text-gray-400"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
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
                type="text"
                placeholder="名前・メールアドレスで検索..."
                value={searchQuery}
                onChange={(e) => setSearchQuery(e.target.value)}
                className="w-full pl-10 pr-4 py-2.5 text-sm border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent"
              />
            </div>

            {/* ロールフィルタ */}
            <select
              value={roleFilter}
              onChange={(e) => setRoleFilter(e.target.value)}
              className="px-3 py-2.5 text-sm border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent bg-white"
            >
              <option value="all">すべてのロール</option>
              <option value="管理者">管理者</option>
              <option value="モデレーター">モデレーター</option>
              <option value="一般ユーザー">一般ユーザー</option>
            </select>

            {/* ステータスフィルタ */}
            <select
              value={statusFilter}
              onChange={(e) => setStatusFilter(e.target.value)}
              className="px-3 py-2.5 text-sm border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent bg-white"
            >
              <option value="all">すべてのステータス</option>
              <option value="active">アクティブ</option>
              <option value="suspended">停止中</option>
            </select>

            {/* クリアボタン */}
            <button
              onClick={() => {
                setSearchQuery("");
                setRoleFilter("all");
                setStatusFilter("all");
              }}
              className="px-4 py-2.5 text-sm text-gray-600 border border-gray-300 rounded-lg hover:bg-gray-50 transition-colors whitespace-nowrap"
            >
              クリア
            </button>
          </div>
        </div>

        {/* ユーザーテーブル */}
        <div className="bg-white rounded-xl border border-gray-200 shadow-sm overflow-hidden">
          <div className="px-5 py-4 border-b border-gray-200 flex items-center justify-between">
            <h2 className="text-sm font-medium text-gray-700">
              ユーザー一覧
              <span className="ml-2 text-gray-400 font-normal">
                ({filteredUsers.length}件)
              </span>
            </h2>
          </div>

          {filteredUsers.length === 0 ? (
            <div className="px-5 py-16 text-center">
              <svg
                className="w-12 h-12 text-gray-300 mx-auto mb-3"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  strokeLinecap="round"
                  strokeLinejoin="round"
                  strokeWidth={1.5}
                  d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0z"
                />
              </svg>
              <p className="text-gray-500 text-sm">
                条件に一致するユーザーが見つかりません
              </p>
            </div>
          ) : (
            <div className="overflow-x-auto">
              <table className="w-full text-sm">
                <thead>
                  <tr className="bg-gray-50 border-b border-gray-200">
                    <th className="px-5 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                      ユーザー
                    </th>
                    <th className="px-5 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                      ロール
                    </th>
                    <th className="px-5 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                      ステータス
                    </th>
                    <th className="px-5 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                      登録日
                    </th>
                    <th className="px-5 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                      最終ログイン
                    </th>
                    <th className="px-5 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">
                      操作
                    </th>
                  </tr>
                </thead>
                <tbody className="divide-y divide-gray-100">
                  {filteredUsers.map((user) => (
                    <tr
                      key={user.id}
                      className={`hover:bg-gray-50 transition-colors ${
                        user.status === "suspended" ? "opacity-60" : ""
                      }`}
                    >
                      {/* ユーザー情報 */}
                      <td className="px-5 py-4">
                        <div className="flex items-center gap-3">
                          <div className="w-9 h-9 rounded-full bg-indigo-100 flex items-center justify-center flex-shrink-0">
                            <span className="text-indigo-700 font-semibold text-sm">
                              {user.name.charAt(0)}
                            </span>
                          </div>
                          <div>
                            <p className="font-medium text-gray-900">
                              {user.name}
                            </p>
                            <p className="text-gray-500 text-xs">
                              {user.email}
                            </p>
                          </div>
                        </div>
                      </td>

                      {/* ロール */}
                      <td className="px-5 py-4">
                        <span
                          className={`inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium ${
                            user.role === "管理者"
                              ? "bg-purple-100 text-purple-700"
                              : user.role === "モデレーター"
                              ? "bg-blue-100 text-blue-700"
                              : "bg-gray-100 text-gray-600"
                          }`}
                        >
                          {user.role}
                        </span>
                      </td>

                      {/* ステータス */}
                      <td className="px-5 py-4">
                        <span
                          className={`inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-full text-xs font-medium ${
                            user.status === "active"
                              ? "bg-green-100 text-green-700"
                              : "bg-red-100 text-red-600"
                          }`}
                        >
                          <span
                            className={`w-1.5 h-1.5 rounded-full ${
                              user.status === "active"
                                ? "bg-green-500"
                                : "bg-red-400"
                            }`}
                          />
                          {user.status === "active" ? "アクティブ" : "停止中"}
                        </span>
                      </td>

                      {/* 登録日 */}
                      <td className="px-5 py-4 text-gray-600">
                        {user.createdAt}
                      </td>

                      {/* 最終ログイン */}
                      <td className="px-5 py-4 text-gray-600">
                        {user.lastLogin}
                      </td>

                      {/* 操作ボタン */}
                      <td className="px-5 py-4">
                        <div className="flex items-center justify-end gap-2">
                          <button className="px-3 py-1.5 text-xs font-medium text-gray-600 border border-gray-300 rounded-lg hover:bg-gray-50 transition-colors">
                            詳細
                          </button>
                          <button className="px-3 py-1.5 text-xs font-medium text-indigo-600 border border-indigo-300 rounded-lg hover:bg-indigo-50 transition-colors">
                            編集
                          </button>
                          {user.status === "active" && (
                            <button
                              onClick={() => handleSuspend(user.id)}
                              className="px-3 py-1.5 text-xs font-medium text-red-600 border border-red-300 rounded-lg hover:bg-red-50 transition-colors"
                            >
                              停止
                            </button>
                          )}
                          {user.status === "suspended" && (
                            <button
                              onClick={() =>
                                setUsers((prev) =>
                                  prev.map((u) =>
                                    u.id === user.id
                                      ? { ...u, status: "active" as UserStatus }
                                      : u
                                  )
                                )
                              }
                              className="px-3 py-1.5 text-xs font-medium text-green-600 border border-green-300 rounded-lg hover:bg-green-50 transition-colors"
                            >
                              有効化
                            </button>
                          )}
                        </div>
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          )}

          {/* ページネーション（静的） */}
          <div className="px-5 py-4 border-t border-gray-200 flex items-center justify-between">
            <p className="text-sm text-gray-500">
              {filteredUsers.length}件中 1〜{Math.min(filteredUsers.length, 10)}件表示
            </p>
            <div className="flex items-center gap-1">
              <button
                disabled
                className="px-3 py-1.5 text-sm text-gray-400 border border-gray-200 rounded-lg disabled:cursor-not-allowed"
              >
                前へ
              </button>
              <button className="px-3 py-1.5 text-sm bg-indigo-600 text-white rounded-lg">
                1
              </button>
              <button className="px-3 py-1.5 text-sm text-gray-600 border border-gray-200 rounded-lg hover:bg-gray-50">
                2
              </button>
              <button className="px-3 py-1.5 text-sm text-gray-600 border border-gray-200 rounded-lg hover:bg-gray-50">
                次へ
              </button>
            </div>
          </div>
        </div>
      </main>

      {/* 停止確認モーダル */}
      {suspendingUserId !== null && (
        <div className="fixed inset-0 bg-black/50 flex items-center justify-center z-50 p-4">
          <div className="bg-white rounded-2xl shadow-xl max-w-md w-full p-6">
            <div className="flex items-center gap-3 mb-4">
              <div className="w-10 h-10 bg-red-100 rounded-full flex items-center justify-center flex-shrink-0">
                <svg
                  className="w-5 h-5 text-red-600"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    strokeLinecap="round"
                    strokeLinejoin="round"
                    strokeWidth={2}
                    d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"
                  />
                </svg>
              </div>
              <div>
                <h3 className="text-base font-semibold text-gray-900">
                  ユーザーを停止しますか？
                </h3>
                <p className="text-sm text-gray-500">
                  {users.find((u) => u.id === suspendingUserId)?.name}
                </p>
              </div>
            </div>
            <p className="text-sm text-gray-600 mb-6">
              このユーザーのアカウントを停止すると、ログインできなくなります。後から有効化することも可能です。
            </p>
            <div className="flex gap-3 justify-end">
              <button
                onClick={cancelSuspend}
                className="px-4 py-2 text-sm font-medium text-gray-700 border border-gray-300 rounded-lg hover:bg-gray-50 transition-colors"
              >
                キャンセル
              </button>
              <button
                onClick={confirmSuspend}
                className="px-4 py-2 text-sm font-medium text-white bg-red-600 rounded-lg hover:bg-red-700 transition-colors"
              >
                停止する
              </button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}
```

## 実装の概要

### ファイル構成
- `app/admin/users/page.tsx` — メインページコンポーネント（Client Component）

### 実装した機能

#### 1. テーブル形式のユーザー一覧
- 8件のダミーユーザーデータを表示
- カラム: ユーザー（アバター・名前・メール）、ロール、ステータス、登録日、最終ログイン、操作

#### 2. 検索フォーム
- キーワード検索（名前・メールアドレス）
- ロールフィルター（管理者 / モデレーター / 一般ユーザー）
- ステータスフィルター（アクティブ / 停止中）
- クリアボタン

#### 3. 停止ボタン
- アクティブユーザーに「停止」ボタンを表示
- クリック時に確認モーダルを表示
- 確認後にステータスを `suspended` に変更
- 停止中ユーザーには「有効化」ボタンを表示

### UI設計のポイント
- ヘッダー: ページタイトル + 新規追加ボタン
- 統計カード: 総ユーザー数・アクティブ数・停止中数
- テーブル行の停止ユーザーは `opacity-60` で視覚的に区別
- ロールバッジ: 管理者=紫、モデレーター=青、一般=グレー
- ステータスバッジ: アクティブ=緑、停止=赤（インジケータドット付き）
- ページネーション（静的、デザイン確認用）
- レスポンシブ対応（`sm:` breakpoint使用）
