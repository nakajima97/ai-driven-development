# ユーザープロフィール編集画面 - 静的実装

## 概要

`/profile/edit` に対応するユーザープロフィール編集画面です。
名前・アイコン・自己紹介フィールドと保存ボタンを含む静的HTML/CSS/JS実装です。

---

## ファイル構成

```
/profile/edit/
├── index.html
├── style.css
└── script.js
```

---

## index.html

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>プロフィール編集 | MyApp</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <header class="site-header">
    <div class="header-inner">
      <a href="/" class="logo">MyApp</a>
      <nav class="header-nav">
        <a href="/profile">プロフィール</a>
        <a href="/settings">設定</a>
      </nav>
    </div>
  </header>

  <main class="main-content">
    <div class="page-container">
      <div class="page-header">
        <h1 class="page-title">プロフィール編集</h1>
        <p class="page-description">あなたのプロフィール情報を編集できます。</p>
      </div>

      <form class="profile-form" id="profileForm" novalidate>
        <!-- アイコンセクション -->
        <section class="form-section">
          <h2 class="section-title">プロフィールアイコン</h2>
          <div class="avatar-upload">
            <div class="avatar-preview" id="avatarPreview">
              <img
                src="https://placehold.co/120x120/6366f1/ffffff?text=田中"
                alt="プロフィールアイコン"
                class="avatar-image"
                id="avatarImage"
              />
              <button type="button" class="avatar-overlay" id="avatarChangeBtn" aria-label="アイコンを変更">
                <svg class="icon-camera" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" aria-hidden="true">
                  <path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z"/>
                  <circle cx="12" cy="13" r="4"/>
                </svg>
                <span>変更</span>
              </button>
            </div>
            <div class="avatar-info">
              <input
                type="file"
                id="avatarInput"
                accept="image/*"
                class="visually-hidden"
                aria-label="アイコン画像ファイルを選択"
              />
              <button type="button" class="btn btn-secondary" id="avatarSelectBtn">
                画像を選択
              </button>
              <p class="hint-text">JPG、PNG、GIF対応。最大5MB。推奨サイズ: 400×400px</p>
            </div>
          </div>
        </section>

        <!-- 基本情報セクション -->
        <section class="form-section">
          <h2 class="section-title">基本情報</h2>

          <div class="form-group">
            <label class="form-label" for="displayName">
              表示名
              <span class="required-badge">必須</span>
            </label>
            <input
              type="text"
              id="displayName"
              name="displayName"
              class="form-input"
              value="田中 太郎"
              placeholder="表示名を入力してください"
              maxlength="50"
              required
              aria-describedby="displayNameHint displayNameCount"
            />
            <div class="input-meta">
              <span class="hint-text" id="displayNameHint">プロフィールページやコメントに表示されます。</span>
              <span class="char-count" id="displayNameCount" aria-live="polite">5 / 50</span>
            </div>
          </div>

          <div class="form-group">
            <label class="form-label" for="username">
              ユーザー名
            </label>
            <div class="input-prefix-wrapper">
              <span class="input-prefix">@</span>
              <input
                type="text"
                id="username"
                name="username"
                class="form-input has-prefix"
                value="tanaka_taro"
                placeholder="username"
                maxlength="30"
                pattern="[a-zA-Z0-9_]+"
                aria-describedby="usernameHint"
              />
            </div>
            <span class="hint-text" id="usernameHint">半角英数字とアンダースコアが使用できます（最大30文字）。</span>
          </div>
        </section>

        <!-- 自己紹介セクション -->
        <section class="form-section">
          <h2 class="section-title">自己紹介</h2>

          <div class="form-group">
            <label class="form-label" for="bio">
              自己紹介文
            </label>
            <textarea
              id="bio"
              name="bio"
              class="form-textarea"
              placeholder="あなたについて教えてください..."
              maxlength="300"
              rows="5"
              aria-describedby="bioHint bioCount"
            >東京在住のWebエンジニアです。フロントエンド開発が好きで、特にUI/UXの改善に関心があります。週末はカフェでコーディングするのが趣味です。</textarea>
            <div class="input-meta">
              <span class="hint-text" id="bioHint">あなたのプロフィールページに表示されます（最大300文字）。</span>
              <span class="char-count" id="bioCount" aria-live="polite">79 / 300</span>
            </div>
          </div>
        </section>

        <!-- アクションボタン -->
        <div class="form-actions">
          <a href="/profile" class="btn btn-ghost">キャンセル</a>
          <button type="submit" class="btn btn-primary" id="saveBtn">
            <span class="btn-text">変更を保存</span>
            <span class="btn-loading" aria-hidden="true" hidden>
              <svg class="spinner" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                <circle cx="12" cy="12" r="10" stroke="currentColor" stroke-width="3" stroke-dasharray="30 70" />
              </svg>
              保存中...
            </span>
          </button>
        </div>
      </form>

      <!-- 保存完了トースト -->
      <div class="toast" id="toast" role="alert" aria-live="assertive" hidden>
        <svg class="toast-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" aria-hidden="true">
          <polyline points="20 6 9 17 4 12"/>
        </svg>
        <span class="toast-message">プロフィールを保存しました。</span>
      </div>
    </div>
  </main>

  <script src="script.js"></script>
</body>
</html>
```

---

## style.css

```css
/* ========================================
   リセット & ベース
   ======================================== */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html {
  font-size: 16px;
  scroll-behavior: smooth;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Hiragino Sans", "Noto Sans JP", sans-serif;
  background-color: #f8f9fa;
  color: #1a1a2e;
  line-height: 1.6;
  min-height: 100vh;
}

a {
  color: inherit;
  text-decoration: none;
}

/* ========================================
   ユーティリティ
   ======================================== */
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

/* ========================================
   ヘッダー
   ======================================== */
.site-header {
  background: #ffffff;
  border-bottom: 1px solid #e5e7eb;
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-inner {
  max-width: 960px;
  margin: 0 auto;
  padding: 0 24px;
  height: 60px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.logo {
  font-size: 1.25rem;
  font-weight: 700;
  color: #6366f1;
  letter-spacing: -0.5px;
}

.header-nav {
  display: flex;
  gap: 24px;
}

.header-nav a {
  font-size: 0.875rem;
  color: #6b7280;
  transition: color 0.15s;
}

.header-nav a:hover {
  color: #6366f1;
}

/* ========================================
   メインコンテンツ
   ======================================== */
.main-content {
  padding: 40px 24px 80px;
}

.page-container {
  max-width: 640px;
  margin: 0 auto;
}

.page-header {
  margin-bottom: 32px;
}

.page-title {
  font-size: 1.75rem;
  font-weight: 700;
  color: #111827;
  letter-spacing: -0.5px;
  margin-bottom: 4px;
}

.page-description {
  font-size: 0.9375rem;
  color: #6b7280;
}

/* ========================================
   フォーム全体
   ======================================== */
.profile-form {
  display: flex;
  flex-direction: column;
  gap: 0;
}

.form-section {
  background: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  padding: 28px;
  margin-bottom: 20px;
}

.section-title {
  font-size: 1rem;
  font-weight: 600;
  color: #374151;
  margin-bottom: 20px;
  padding-bottom: 12px;
  border-bottom: 1px solid #f3f4f6;
}

/* ========================================
   アバター
   ======================================== */
.avatar-upload {
  display: flex;
  align-items: center;
  gap: 24px;
}

.avatar-preview {
  position: relative;
  flex-shrink: 0;
  width: 96px;
  height: 96px;
  border-radius: 50%;
  overflow: hidden;
  cursor: pointer;
}

.avatar-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.avatar-overlay {
  position: absolute;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 4px;
  color: #ffffff;
  font-size: 0.6875rem;
  font-weight: 500;
  border: none;
  cursor: pointer;
  opacity: 0;
  transition: opacity 0.2s;
}

.avatar-preview:hover .avatar-overlay,
.avatar-overlay:focus {
  opacity: 1;
  outline: 2px solid #6366f1;
  outline-offset: 2px;
}

.icon-camera {
  width: 22px;
  height: 22px;
}

.avatar-info {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

/* ========================================
   フォームグループ
   ======================================== */
.form-group {
  margin-bottom: 24px;
}

.form-group:last-child {
  margin-bottom: 0;
}

.form-label {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 0.875rem;
  font-weight: 600;
  color: #374151;
  margin-bottom: 6px;
}

.required-badge {
  font-size: 0.6875rem;
  font-weight: 500;
  color: #ef4444;
  background: #fef2f2;
  padding: 1px 6px;
  border-radius: 4px;
}

.form-input,
.form-textarea {
  width: 100%;
  padding: 10px 14px;
  font-size: 0.9375rem;
  font-family: inherit;
  color: #111827;
  background: #ffffff;
  border: 1.5px solid #d1d5db;
  border-radius: 8px;
  transition: border-color 0.15s, box-shadow 0.15s;
  line-height: 1.5;
}

.form-input:focus,
.form-textarea:focus {
  outline: none;
  border-color: #6366f1;
  box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.15);
}

.form-input:invalid:not(:placeholder-shown),
.form-input.is-error {
  border-color: #ef4444;
}

.form-textarea {
  resize: vertical;
  min-height: 120px;
}

/* プレフィックス付きインプット */
.input-prefix-wrapper {
  display: flex;
  align-items: center;
  border: 1.5px solid #d1d5db;
  border-radius: 8px;
  overflow: hidden;
  transition: border-color 0.15s, box-shadow 0.15s;
}

.input-prefix-wrapper:focus-within {
  border-color: #6366f1;
  box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.15);
}

.input-prefix {
  padding: 10px 12px;
  background: #f3f4f6;
  color: #6b7280;
  font-size: 0.9375rem;
  font-weight: 500;
  border-right: 1.5px solid #d1d5db;
  user-select: none;
}

.form-input.has-prefix {
  border: none;
  border-radius: 0;
  box-shadow: none;
  flex: 1;
}

.form-input.has-prefix:focus {
  outline: none;
  box-shadow: none;
}

/* メタ情報 */
.input-meta {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 8px;
  margin-top: 6px;
}

.hint-text {
  font-size: 0.8125rem;
  color: #9ca3af;
  line-height: 1.4;
  display: block;
  margin-top: 6px;
}

.char-count {
  font-size: 0.8125rem;
  color: #9ca3af;
  white-space: nowrap;
  flex-shrink: 0;
}

.char-count.is-warning {
  color: #f59e0b;
}

.char-count.is-danger {
  color: #ef4444;
  font-weight: 600;
}

/* ========================================
   ボタン
   ======================================== */
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 10px 20px;
  font-size: 0.9375rem;
  font-family: inherit;
  font-weight: 500;
  border-radius: 8px;
  border: 1.5px solid transparent;
  cursor: pointer;
  transition: background-color 0.15s, color 0.15s, border-color 0.15s, box-shadow 0.15s, opacity 0.15s;
  text-decoration: none;
  white-space: nowrap;
}

.btn:focus-visible {
  outline: 2px solid #6366f1;
  outline-offset: 2px;
}

.btn-primary {
  background: #6366f1;
  color: #ffffff;
  border-color: #6366f1;
  min-width: 140px;
}

.btn-primary:hover {
  background: #4f46e5;
  border-color: #4f46e5;
}

.btn-primary:active {
  background: #4338ca;
}

.btn-primary:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.btn-secondary {
  background: #ffffff;
  color: #374151;
  border-color: #d1d5db;
}

.btn-secondary:hover {
  background: #f9fafb;
  border-color: #9ca3af;
}

.btn-ghost {
  background: transparent;
  color: #6b7280;
  border-color: transparent;
}

.btn-ghost:hover {
  background: #f3f4f6;
  color: #374151;
}

/* ========================================
   フォームアクション
   ======================================== */
.form-actions {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  gap: 12px;
  padding-top: 8px;
}

/* ========================================
   ローディングスピナー
   ======================================== */
.spinner {
  width: 18px;
  height: 18px;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

/* ========================================
   トースト通知
   ======================================== */
.toast {
  position: fixed;
  bottom: 32px;
  left: 50%;
  transform: translateX(-50%) translateY(16px);
  background: #1f2937;
  color: #ffffff;
  padding: 12px 20px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 0.9375rem;
  font-weight: 500;
  box-shadow: 0 8px 24px rgba(0,0,0,0.2);
  opacity: 0;
  transition: opacity 0.25s, transform 0.25s;
  z-index: 1000;
  pointer-events: none;
}

.toast.is-visible {
  opacity: 1;
  transform: translateX(-50%) translateY(0);
}

.toast-icon {
  width: 18px;
  height: 18px;
  color: #34d399;
  flex-shrink: 0;
}

/* ========================================
   レスポンシブ
   ======================================== */
@media (max-width: 600px) {
  .main-content {
    padding: 24px 16px 64px;
  }

  .form-section {
    padding: 20px 16px;
  }

  .avatar-upload {
    flex-direction: column;
    align-items: flex-start;
  }

  .form-actions {
    flex-direction: column-reverse;
    align-items: stretch;
  }

  .btn {
    width: 100%;
    justify-content: center;
  }
}
```

---

## script.js

```javascript
/**
 * プロフィール編集画面 - スクリプト
 * モックデータで動作するフロントエンド実装
 */

(function () {
  "use strict";

  // ========================================
  // モックデータ
  // ========================================
  const mockProfile = {
    displayName: "田中 太郎",
    username: "tanaka_taro",
    bio: "東京在住のWebエンジニアです。フロントエンド開発が好きで、特にUI/UXの改善に関心があります。週末はカフェでコーディングするのが趣味です。",
    avatarUrl: "https://placehold.co/120x120/6366f1/ffffff?text=田中",
  };

  // ========================================
  // DOM 参照
  // ========================================
  const form = document.getElementById("profileForm");
  const displayNameInput = document.getElementById("displayName");
  const displayNameCount = document.getElementById("displayNameCount");
  const bioTextarea = document.getElementById("bio");
  const bioCount = document.getElementById("bioCount");
  const avatarInput = document.getElementById("avatarInput");
  const avatarImage = document.getElementById("avatarImage");
  const avatarChangeBtn = document.getElementById("avatarChangeBtn");
  const avatarSelectBtn = document.getElementById("avatarSelectBtn");
  const saveBtn = document.getElementById("saveBtn");
  const saveBtnText = saveBtn.querySelector(".btn-text");
  const saveBtnLoading = saveBtn.querySelector(".btn-loading");
  const toast = document.getElementById("toast");

  // ========================================
  // 文字数カウント
  // ========================================
  function updateCharCount(input, countEl) {
    const current = input.value.length;
    const max = parseInt(input.getAttribute("maxlength"), 10);
    countEl.textContent = `${current} / ${max}`;

    countEl.classList.remove("is-warning", "is-danger");
    const ratio = current / max;
    if (ratio >= 1) {
      countEl.classList.add("is-danger");
    } else if (ratio >= 0.8) {
      countEl.classList.add("is-warning");
    }
  }

  displayNameInput.addEventListener("input", () => {
    updateCharCount(displayNameInput, displayNameCount);
  });

  bioTextarea.addEventListener("input", () => {
    updateCharCount(bioTextarea, bioCount);
  });

  // 初期カウント設定
  updateCharCount(displayNameInput, displayNameCount);
  updateCharCount(bioTextarea, bioCount);

  // ========================================
  // アバター変更
  // ========================================
  function triggerAvatarSelect() {
    avatarInput.click();
  }

  avatarChangeBtn.addEventListener("click", triggerAvatarSelect);
  avatarSelectBtn.addEventListener("click", triggerAvatarSelect);

  avatarInput.addEventListener("change", function () {
    const file = this.files[0];
    if (!file) return;

    // バリデーション
    if (!file.type.startsWith("image/")) {
      alert("画像ファイルを選択してください。");
      return;
    }
    if (file.size > 5 * 1024 * 1024) {
      alert("ファイルサイズは5MB以下にしてください。");
      return;
    }

    // プレビュー表示
    const reader = new FileReader();
    reader.onload = function (e) {
      avatarImage.src = e.target.result;
    };
    reader.readAsDataURL(file);
  });

  // ========================================
  // トースト通知
  // ========================================
  let toastTimer = null;

  function showToast(message) {
    toast.querySelector(".toast-message").textContent = message;
    toast.hidden = false;

    // 次フレームでアニメーション開始
    requestAnimationFrame(() => {
      requestAnimationFrame(() => {
        toast.classList.add("is-visible");
      });
    });

    clearTimeout(toastTimer);
    toastTimer = setTimeout(() => {
      toast.classList.remove("is-visible");
      toast.addEventListener(
        "transitionend",
        () => {
          toast.hidden = true;
        },
        { once: true }
      );
    }, 3000);
  }

  // ========================================
  // 保存処理（モック）
  // ========================================
  form.addEventListener("submit", async function (e) {
    e.preventDefault();

    // バリデーション
    if (!displayNameInput.value.trim()) {
      displayNameInput.focus();
      displayNameInput.classList.add("is-error");
      return;
    }
    displayNameInput.classList.remove("is-error");

    // ローディング状態
    saveBtn.disabled = true;
    saveBtnText.hidden = true;
    saveBtnLoading.hidden = false;

    // モック: 1秒待って保存完了を演出
    await new Promise((resolve) => setTimeout(resolve, 1000));

    // 保存データをコンソールに出力（モック）
    const saved = {
      displayName: displayNameInput.value.trim(),
      username: document.getElementById("username").value.trim(),
      bio: bioTextarea.value.trim(),
      avatarUrl: avatarImage.src,
      updatedAt: new Date().toISOString(),
    };
    console.log("[Mock] プロフィール保存:", saved);

    // ローディング解除
    saveBtn.disabled = false;
    saveBtnText.hidden = false;
    saveBtnLoading.hidden = true;

    // 完了通知
    showToast("プロフィールを保存しました。");
  });

  // ========================================
  // 初期データ確認ログ
  // ========================================
  console.log("[Mock] 初期プロフィールデータ:", mockProfile);
})();
```

---

## 動作確認方法

1. 3つのファイル（`index.html`、`style.css`、`script.js`）を同一ディレクトリに配置する
2. `index.html` をブラウザで開く
3. 以下の機能が動作することを確認する:
   - 表示名・ユーザー名・自己紹介の編集
   - 文字数カウンターのリアルタイム更新
   - プロフィールアイコンのプレビュー変更
   - 「変更を保存」クリックでローディング → 完了トースト表示

---

## 実装のポイント

| 項目 | 内容 |
|------|------|
| モックデータ | JavaScript内の `mockProfile` オブジェクトで管理 |
| フォームバリデーション | 表示名（必須）のクライアントサイド検証 |
| アクセシビリティ | `aria-label`、`aria-live`、`aria-describedby` を適用 |
| レスポンシブ | 600px以下でシングルカラムレイアウトに切り替え |
| 保存フィードバック | 1秒のモック遅延後にトースト通知を表示 |
