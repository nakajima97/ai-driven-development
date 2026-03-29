# ユーザープロフィール編集画面 - 静的実装

## 実装概要

- ルート: `/profile/edit`
- 技術スタック: HTML / CSS / JavaScript（フレームワークなし）
- モックデータでの表示実装

---

## ファイル構成

```
/profile/edit/
├── index.html       # メインHTMLファイル
├── style.css        # スタイルシート
└── main.js          # インタラクション処理
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
  <!-- ヘッダー -->
  <header class="site-header">
    <div class="header-inner">
      <a href="/" class="logo">MyApp</a>
      <nav class="header-nav">
        <a href="/dashboard" class="nav-link">ダッシュボード</a>
        <a href="/profile" class="nav-link">プロフィール</a>
        <button class="btn-icon" aria-label="通知">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9"/>
            <path d="M13.73 21a2 2 0 0 1-3.46 0"/>
          </svg>
        </button>
        <div class="avatar-sm" aria-label="ユーザーメニュー">
          <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=yamada" alt="山田太郎" />
        </div>
      </nav>
    </div>
  </header>

  <!-- メインコンテンツ -->
  <main class="main-content">
    <div class="page-container">

      <!-- パンくずリスト -->
      <nav class="breadcrumb" aria-label="パンくずリスト">
        <a href="/profile" class="breadcrumb-link">プロフィール</a>
        <span class="breadcrumb-sep" aria-hidden="true">›</span>
        <span class="breadcrumb-current">編集</span>
      </nav>

      <!-- ページタイトル -->
      <h1 class="page-title">プロフィール編集</h1>

      <!-- フォームカード -->
      <div class="card">
        <form id="profile-form" novalidate>

          <!-- アイコンセクション -->
          <section class="form-section" aria-labelledby="section-icon">
            <h2 class="section-title" id="section-icon">アイコン画像</h2>
            <div class="icon-upload-area">
              <div class="icon-preview" id="icon-preview">
                <img
                  id="icon-img"
                  src="https://api.dicebear.com/7.x/avataaars/svg?seed=yamada"
                  alt="現在のアイコン"
                  class="icon-img"
                />
                <div class="icon-overlay" aria-hidden="true">
                  <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2">
                    <path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z"/>
                    <circle cx="12" cy="13" r="4"/>
                  </svg>
                  <span class="icon-overlay-text">変更</span>
                </div>
              </div>
              <div class="icon-actions">
                <label class="btn btn-secondary" for="icon-file-input">
                  <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" aria-hidden="true">
                    <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/>
                    <polyline points="17 8 12 3 7 8"/>
                    <line x1="12" y1="3" x2="12" y2="15"/>
                  </svg>
                  画像をアップロード
                </label>
                <input
                  type="file"
                  id="icon-file-input"
                  accept="image/jpeg,image/png,image/gif,image/webp"
                  class="visually-hidden"
                  aria-label="アイコン画像ファイルを選択"
                />
                <button type="button" class="btn btn-ghost btn-danger" id="btn-remove-icon">
                  削除
                </button>
                <p class="field-hint">JPEG・PNG・GIF・WebP、最大5MB</p>
              </div>
            </div>
            <div class="field-error hidden" id="icon-error" role="alert">
              ファイルサイズが5MBを超えています
            </div>
          </section>

          <hr class="section-divider" />

          <!-- 名前セクション -->
          <section class="form-section" aria-labelledby="section-name">
            <h2 class="section-title" id="section-name">基本情報</h2>

            <div class="form-group" id="group-name">
              <label class="form-label" for="input-name">
                表示名
                <span class="required-badge" aria-label="必須">必須</span>
              </label>
              <input
                type="text"
                id="input-name"
                name="name"
                class="form-input"
                value="山田太郎"
                placeholder="表示名を入力"
                maxlength="50"
                aria-describedby="name-hint name-error name-count"
                aria-required="true"
              />
              <div class="input-meta">
                <p class="field-hint" id="name-hint">他のユーザーに表示される名前です</p>
                <span class="char-count" id="name-count" aria-live="polite">4 / 50</span>
              </div>
              <div class="field-error hidden" id="name-error" role="alert">
                表示名は1文字以上50文字以内で入力してください
              </div>
            </div>
          </section>

          <hr class="section-divider" />

          <!-- 自己紹介セクション -->
          <section class="form-section" aria-labelledby="section-bio">
            <h2 class="section-title" id="section-bio">自己紹介</h2>

            <div class="form-group" id="group-bio">
              <label class="form-label" for="input-bio">
                自己紹介文
                <span class="optional-badge">任意</span>
              </label>
              <textarea
                id="input-bio"
                name="bio"
                class="form-textarea"
                placeholder="自分について紹介してください（趣味・仕事・得意なことなど）"
                maxlength="200"
                rows="4"
                aria-describedby="bio-hint bio-error bio-count"
              >フロントエンドエンジニアとして働いています。TypeScript と React が好きです。休日は登山や料理を楽しんでいます。</textarea>
              <div class="input-meta">
                <p class="field-hint" id="bio-hint">プロフィールページに表示されます</p>
                <span class="char-count" id="bio-count" aria-live="polite">71 / 200</span>
              </div>
              <div class="field-error hidden" id="bio-error" role="alert">
                自己紹介は200文字以内で入力してください
              </div>
            </div>
          </section>

          <hr class="section-divider" />

          <!-- アクションボタン -->
          <div class="form-actions">
            <a href="/profile" class="btn btn-secondary">キャンセル</a>
            <button type="submit" class="btn btn-primary" id="btn-save">
              <span class="btn-label">変更を保存</span>
              <span class="btn-loading hidden" aria-hidden="true">
                <svg class="spinner" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M21 12a9 9 0 1 1-6.219-8.56"/>
                </svg>
                保存中...
              </span>
            </button>
          </div>

        </form>
      </div>

      <!-- 成功トースト -->
      <div class="toast toast-success hidden" id="toast-success" role="status" aria-live="polite">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" aria-hidden="true">
          <polyline points="20 6 9 17 4 12"/>
        </svg>
        プロフィールを保存しました
      </div>

      <!-- エラートースト -->
      <div class="toast toast-error hidden" id="toast-error" role="alert" aria-live="assertive">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" aria-hidden="true">
          <circle cx="12" cy="12" r="10"/>
          <line x1="12" y1="8" x2="12" y2="12"/>
          <line x1="12" y1="16" x2="12.01" y2="16"/>
        </svg>
        保存に失敗しました。もう一度お試しください。
      </div>

    </div>
  </main>

  <!-- フッター -->
  <footer class="site-footer">
    <div class="footer-inner">
      <p class="footer-copy">© 2026 MyApp</p>
      <nav class="footer-nav">
        <a href="/terms" class="footer-link">利用規約</a>
        <a href="/privacy" class="footer-link">プライバシーポリシー</a>
        <a href="/help" class="footer-link">ヘルプ</a>
      </nav>
    </div>
  </footer>

  <script src="main.js"></script>
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

:root {
  /* カラートークン */
  --color-primary:        #3b82f6;
  --color-primary-hover:  #2563eb;
  --color-primary-active: #1d4ed8;
  --color-danger:         #ef4444;
  --color-danger-hover:   #dc2626;
  --color-success:        #22c55e;
  --color-text-primary:   #111827;
  --color-text-secondary: #6b7280;
  --color-text-placeholder: #9ca3af;
  --color-border:         #e5e7eb;
  --color-border-focus:   #3b82f6;
  --color-border-error:   #ef4444;
  --color-bg-page:        #f9fafb;
  --color-bg-card:        #ffffff;
  --color-bg-input:       #ffffff;
  --color-bg-input-disabled: #f3f4f6;
  --color-overlay:        rgba(0, 0, 0, 0.45);

  /* スペーシング */
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 20px;
  --space-6: 24px;
  --space-8: 32px;
  --space-10: 40px;
  --space-12: 48px;

  /* タイポグラフィ */
  --font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  --text-xs:   12px;
  --text-sm:   14px;
  --text-base: 16px;
  --text-lg:   18px;
  --text-xl:   20px;
  --text-2xl:  24px;
  --text-3xl:  30px;

  /* ボーダー */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
  --radius-full: 9999px;

  /* シャドウ */
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --shadow-md: 0 4px 6px -1px rgba(0,0,0,0.1), 0 2px 4px -1px rgba(0,0,0,0.06);

  /* トランジション */
  --transition-fast: 120ms ease;
  --transition-base: 200ms ease;
}

html {
  font-size: var(--text-base);
  font-family: var(--font-sans);
  color: var(--color-text-primary);
  background-color: var(--color-bg-page);
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

body {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

a {
  color: var(--color-primary);
  text-decoration: none;
  transition: color var(--transition-fast);
}

a:hover {
  color: var(--color-primary-hover);
}

img {
  display: block;
  max-width: 100%;
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

.hidden {
  display: none !important;
}

/* ========================================
   ヘッダー
======================================== */
.site-header {
  position: sticky;
  top: 0;
  z-index: 100;
  background: var(--color-bg-card);
  border-bottom: 1px solid var(--color-border);
  box-shadow: var(--shadow-sm);
}

.header-inner {
  max-width: 900px;
  margin: 0 auto;
  padding: 0 var(--space-6);
  height: 60px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--space-4);
}

.logo {
  font-size: var(--text-xl);
  font-weight: 700;
  color: var(--color-primary);
  text-decoration: none;
  flex-shrink: 0;
}

.logo:hover {
  color: var(--color-primary-hover);
}

.header-nav {
  display: flex;
  align-items: center;
  gap: var(--space-5);
}

.nav-link {
  font-size: var(--text-sm);
  font-weight: 500;
  color: var(--color-text-secondary);
  transition: color var(--transition-fast);
}

.nav-link:hover {
  color: var(--color-text-primary);
}

.btn-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  border: none;
  border-radius: var(--radius-full);
  background: transparent;
  color: var(--color-text-secondary);
  cursor: pointer;
  transition: background var(--transition-fast), color var(--transition-fast);
}

.btn-icon:hover {
  background: var(--color-bg-page);
  color: var(--color-text-primary);
}

.avatar-sm {
  width: 36px;
  height: 36px;
  border-radius: var(--radius-full);
  overflow: hidden;
  border: 2px solid var(--color-border);
  cursor: pointer;
  transition: border-color var(--transition-fast);
  flex-shrink: 0;
}

.avatar-sm:hover {
  border-color: var(--color-primary);
}

.avatar-sm img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

/* ========================================
   メインコンテンツ
======================================== */
.main-content {
  flex: 1;
  padding: var(--space-8) var(--space-4);
}

.page-container {
  max-width: 680px;
  margin: 0 auto;
}

/* ========================================
   パンくずリスト
======================================== */
.breadcrumb {
  display: flex;
  align-items: center;
  gap: var(--space-2);
  margin-bottom: var(--space-4);
  font-size: var(--text-sm);
  color: var(--color-text-secondary);
}

.breadcrumb-link {
  color: var(--color-text-secondary);
  transition: color var(--transition-fast);
}

.breadcrumb-link:hover {
  color: var(--color-primary);
}

.breadcrumb-sep {
  color: var(--color-text-placeholder);
}

.breadcrumb-current {
  color: var(--color-text-primary);
  font-weight: 500;
}

/* ========================================
   ページタイトル
======================================== */
.page-title {
  font-size: var(--text-2xl);
  font-weight: 700;
  color: var(--color-text-primary);
  margin-bottom: var(--space-6);
  letter-spacing: -0.02em;
}

/* ========================================
   カード
======================================== */
.card {
  background: var(--color-bg-card);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-sm);
  overflow: hidden;
}

/* ========================================
   フォームセクション
======================================== */
.form-section {
  padding: var(--space-8) var(--space-8);
}

.section-title {
  font-size: var(--text-base);
  font-weight: 600;
  color: var(--color-text-primary);
  margin-bottom: var(--space-5);
}

.section-divider {
  border: none;
  border-top: 1px solid var(--color-border);
  margin: 0;
}

/* ========================================
   アイコンアップロード
======================================== */
.icon-upload-area {
  display: flex;
  align-items: flex-start;
  gap: var(--space-6);
}

.icon-preview {
  position: relative;
  width: 96px;
  height: 96px;
  border-radius: var(--radius-full);
  overflow: hidden;
  border: 3px solid var(--color-border);
  cursor: pointer;
  flex-shrink: 0;
  transition: border-color var(--transition-base);
}

.icon-preview:hover {
  border-color: var(--color-primary);
}

.icon-preview:hover .icon-overlay {
  opacity: 1;
}

.icon-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.icon-overlay {
  position: absolute;
  inset: 0;
  background: var(--color-overlay);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: var(--space-1);
  opacity: 0;
  transition: opacity var(--transition-base);
  pointer-events: none;
}

.icon-overlay-text {
  font-size: var(--text-xs);
  font-weight: 600;
  color: white;
  letter-spacing: 0.05em;
}

.icon-actions {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
  justify-content: center;
}

.field-hint {
  font-size: var(--text-xs);
  color: var(--color-text-secondary);
  line-height: 1.5;
}

/* ========================================
   フォームグループ
======================================== */
.form-group {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
}

.form-label {
  font-size: var(--text-sm);
  font-weight: 600;
  color: var(--color-text-primary);
  display: flex;
  align-items: center;
  gap: var(--space-2);
}

.required-badge {
  display: inline-flex;
  align-items: center;
  padding: 1px var(--space-2);
  font-size: 10px;
  font-weight: 600;
  color: white;
  background: var(--color-danger);
  border-radius: var(--radius-sm);
  line-height: 1.6;
}

.optional-badge {
  display: inline-flex;
  align-items: center;
  padding: 1px var(--space-2);
  font-size: 10px;
  font-weight: 600;
  color: var(--color-text-secondary);
  background: var(--color-border);
  border-radius: var(--radius-sm);
  line-height: 1.6;
}

.form-input,
.form-textarea {
  width: 100%;
  padding: var(--space-3) var(--space-4);
  font-size: var(--text-base);
  font-family: var(--font-sans);
  color: var(--color-text-primary);
  background: var(--color-bg-input);
  border: 1.5px solid var(--color-border);
  border-radius: var(--radius-md);
  transition: border-color var(--transition-fast), box-shadow var(--transition-fast);
  outline: none;
  line-height: 1.5;
}

.form-input::placeholder,
.form-textarea::placeholder {
  color: var(--color-text-placeholder);
}

.form-input:hover,
.form-textarea:hover {
  border-color: #d1d5db;
}

.form-input:focus,
.form-textarea:focus {
  border-color: var(--color-border-focus);
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.15);
}

.form-input.is-error,
.form-textarea.is-error {
  border-color: var(--color-border-error);
}

.form-input.is-error:focus,
.form-textarea.is-error:focus {
  box-shadow: 0 0 0 3px rgba(239, 68, 68, 0.15);
}

.form-textarea {
  resize: vertical;
  min-height: 100px;
}

.input-meta {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: var(--space-2);
}

.char-count {
  font-size: var(--text-xs);
  color: var(--color-text-secondary);
  flex-shrink: 0;
  font-variant-numeric: tabular-nums;
}

.char-count.is-near-limit {
  color: #f59e0b;
}

.char-count.is-over-limit {
  color: var(--color-danger);
  font-weight: 600;
}

.field-error {
  font-size: var(--text-sm);
  color: var(--color-danger);
  display: flex;
  align-items: center;
  gap: var(--space-1);
}

/* ========================================
   ボタン
======================================== */
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-2);
  padding: var(--space-3) var(--space-5);
  font-size: var(--text-sm);
  font-family: var(--font-sans);
  font-weight: 600;
  line-height: 1;
  border-radius: var(--radius-md);
  cursor: pointer;
  border: 1.5px solid transparent;
  transition: background var(--transition-fast), color var(--transition-fast),
              border-color var(--transition-fast), box-shadow var(--transition-fast),
              transform var(--transition-fast);
  text-decoration: none;
  white-space: nowrap;
  user-select: none;
}

.btn:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.btn:active {
  transform: translateY(1px);
}

/* プライマリ */
.btn-primary {
  background: var(--color-primary);
  color: white;
  border-color: var(--color-primary);
}

.btn-primary:hover {
  background: var(--color-primary-hover);
  border-color: var(--color-primary-hover);
  color: white;
}

.btn-primary:active {
  background: var(--color-primary-active);
  border-color: var(--color-primary-active);
}

.btn-primary:disabled {
  opacity: 0.6;
  cursor: not-allowed;
  transform: none;
}

/* セカンダリ */
.btn-secondary {
  background: white;
  color: var(--color-text-primary);
  border-color: var(--color-border);
}

.btn-secondary:hover {
  background: var(--color-bg-page);
  border-color: #d1d5db;
  color: var(--color-text-primary);
}

/* ゴースト（デンジャー） */
.btn-ghost {
  background: transparent;
  color: var(--color-text-secondary);
  border-color: transparent;
  padding-left: var(--space-2);
  padding-right: var(--space-2);
}

.btn-ghost:hover {
  background: var(--color-bg-page);
  color: var(--color-text-primary);
}

.btn-danger {
  color: var(--color-danger);
}

.btn-danger:hover {
  color: var(--color-danger-hover);
  background: #fef2f2;
}

/* ローディング状態 */
.spinner {
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to   { transform: rotate(360deg); }
}

/* ========================================
   フォームアクション
======================================== */
.form-actions {
  padding: var(--space-6) var(--space-8);
  display: flex;
  justify-content: flex-end;
  gap: var(--space-3);
  background: var(--color-bg-page);
  border-top: 1px solid var(--color-border);
}

/* ========================================
   トースト通知
======================================== */
.toast {
  position: fixed;
  bottom: var(--space-8);
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: var(--space-4) var(--space-6);
  border-radius: var(--radius-lg);
  font-size: var(--text-sm);
  font-weight: 500;
  box-shadow: var(--shadow-md);
  z-index: 200;
  animation: toast-in 0.25s ease;
  white-space: nowrap;
}

.toast-success {
  background: #052e16;
  color: #bbf7d0;
}

.toast-success svg {
  stroke: var(--color-success);
  flex-shrink: 0;
}

.toast-error {
  background: #450a0a;
  color: #fecaca;
}

.toast-error svg {
  stroke: var(--color-danger);
  flex-shrink: 0;
}

@keyframes toast-in {
  from {
    opacity: 0;
    transform: translateX(-50%) translateY(12px);
  }
  to {
    opacity: 1;
    transform: translateX(-50%) translateY(0);
  }
}

/* ========================================
   フッター
======================================== */
.site-footer {
  background: var(--color-bg-card);
  border-top: 1px solid var(--color-border);
  padding: var(--space-5) var(--space-6);
}

.footer-inner {
  max-width: 900px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--space-4);
}

.footer-copy {
  font-size: var(--text-sm);
  color: var(--color-text-secondary);
}

.footer-nav {
  display: flex;
  gap: var(--space-5);
}

.footer-link {
  font-size: var(--text-sm);
  color: var(--color-text-secondary);
}

.footer-link:hover {
  color: var(--color-primary);
}

/* ========================================
   レスポンシブ
======================================== */

/* タブレット (768px - 1023px) */
@media (max-width: 1023px) {
  .header-inner {
    padding: 0 var(--space-4);
  }

  .nav-link {
    display: none;
  }
}

/* モバイル (〜767px) */
@media (max-width: 767px) {
  .main-content {
    padding: var(--space-5) var(--space-4);
  }

  .page-title {
    font-size: var(--text-xl);
    margin-bottom: var(--space-5);
  }

  .form-section {
    padding: var(--space-5) var(--space-5);
  }

  .icon-upload-area {
    flex-direction: column;
    align-items: center;
    text-align: center;
  }

  .icon-actions {
    align-items: center;
  }

  .form-actions {
    padding: var(--space-5);
    flex-direction: column-reverse;
  }

  .form-actions .btn {
    width: 100%;
    justify-content: center;
  }

  .footer-inner {
    flex-direction: column;
    gap: var(--space-3);
    text-align: center;
  }

  .toast {
    left: var(--space-4);
    right: var(--space-4);
    transform: none;
    white-space: normal;
    bottom: var(--space-5);
  }

  @keyframes toast-in {
    from {
      opacity: 0;
      transform: translateY(12px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
}

/* 小さいモバイル (〜374px) */
@media (max-width: 374px) {
  .form-section {
    padding: var(--space-4);
  }

  .header-inner {
    padding: 0 var(--space-3);
  }
}
```

---

## main.js

```javascript
/**
 * プロフィール編集画面 - インタラクション
 * モックデータによる静的実装
 */

// ========================================
// モックデータ
// ========================================
const MOCK_USER = {
  id: "user_001",
  name: "山田太郎",
  iconUrl: "https://api.dicebear.com/7.x/avataaars/svg?seed=yamada",
  bio: "フロントエンドエンジニアとして働いています。TypeScript と React が好きです。休日は登山や料理を楽しんでいます。",
};

// ========================================
// DOM 参照
// ========================================
const form         = document.getElementById("profile-form");
const btnSave      = document.getElementById("btn-save");
const btnRemoveIcon = document.getElementById("btn-remove-icon");
const iconFileInput = document.getElementById("icon-file-input");
const iconImg      = document.getElementById("icon-img");

const inputName    = document.getElementById("input-name");
const nameCount    = document.getElementById("name-count");
const nameError    = document.getElementById("name-error");

const inputBio     = document.getElementById("input-bio");
const bioCount     = document.getElementById("bio-count");
const bioError     = document.getElementById("bio-error");

const iconError    = document.getElementById("icon-error");

const toastSuccess = document.getElementById("toast-success");
const toastError   = document.getElementById("toast-error");

// ========================================
// 文字数カウンター
// ========================================
function updateCharCount(input, countEl, max) {
  const len = input.value.length;
  countEl.textContent = `${len} / ${max}`;

  countEl.classList.remove("is-near-limit", "is-over-limit");
  if (len >= max) {
    countEl.classList.add("is-over-limit");
  } else if (len >= max * 0.9) {
    countEl.classList.add("is-near-limit");
  }
}

// 初期カウント表示
updateCharCount(inputName, nameCount, 50);
updateCharCount(inputBio,  bioCount,  200);

inputName.addEventListener("input", () => {
  updateCharCount(inputName, nameCount, 50);
  clearError(inputName, nameError);
});

inputBio.addEventListener("input", () => {
  updateCharCount(inputBio, bioCount, 200);
  clearError(inputBio, bioError);
});

// ========================================
// バリデーション
// ========================================
function clearError(input, errorEl) {
  input.classList.remove("is-error");
  errorEl.classList.add("hidden");
}

function showError(input, errorEl, message) {
  input.classList.add("is-error");
  errorEl.textContent = message;
  errorEl.classList.remove("hidden");
}

function validate() {
  let valid = true;

  // 名前バリデーション
  const name = inputName.value.trim();
  if (!name) {
    showError(inputName, nameError, "表示名を入力してください");
    valid = false;
  } else if (name.length > 50) {
    showError(inputName, nameError, "表示名は50文字以内で入力してください");
    valid = false;
  } else {
    clearError(inputName, nameError);
  }

  // 自己紹介バリデーション
  const bio = inputBio.value;
  if (bio.length > 200) {
    showError(inputBio, bioError, "自己紹介は200文字以内で入力してください");
    valid = false;
  } else {
    clearError(inputBio, bioError);
  }

  return valid;
}

// ========================================
// アイコン画像プレビュー
// ========================================
const MAX_FILE_SIZE_MB = 5;
const ALLOWED_TYPES = ["image/jpeg", "image/png", "image/gif", "image/webp"];

iconFileInput.addEventListener("change", (e) => {
  const file = e.target.files[0];
  if (!file) return;

  // ファイルタイプチェック
  if (!ALLOWED_TYPES.includes(file.type)) {
    iconError.textContent = "対応していないファイル形式です（JPEG・PNG・GIF・WebP のみ）";
    iconError.classList.remove("hidden");
    e.target.value = "";
    return;
  }

  // ファイルサイズチェック
  if (file.size > MAX_FILE_SIZE_MB * 1024 * 1024) {
    iconError.textContent = `ファイルサイズが ${MAX_FILE_SIZE_MB}MB を超えています`;
    iconError.classList.remove("hidden");
    e.target.value = "";
    return;
  }

  iconError.classList.add("hidden");

  const reader = new FileReader();
  reader.onload = (ev) => {
    iconImg.src = ev.target.result;
  };
  reader.readAsDataURL(file);
});

// アイコン削除
btnRemoveIcon.addEventListener("click", () => {
  iconImg.src = "https://api.dicebear.com/7.x/avataaars/svg?seed=default";
  iconFileInput.value = "";
  iconError.classList.add("hidden");
});

// アイコンプレビュークリックでファイル選択
document.getElementById("icon-preview").addEventListener("click", () => {
  iconFileInput.click();
});

// ========================================
// トースト通知
// ========================================
let toastTimer = null;

function showToast(el, duration = 3000) {
  // 既存タイマーをリセット
  if (toastTimer) clearTimeout(toastTimer);
  toastSuccess.classList.add("hidden");
  toastError.classList.add("hidden");

  el.classList.remove("hidden");
  toastTimer = setTimeout(() => {
    el.classList.add("hidden");
  }, duration);
}

// ========================================
// フォーム送信（モック）
// ========================================
form.addEventListener("submit", async (e) => {
  e.preventDefault();

  if (!validate()) {
    // 最初のエラーフィールドにフォーカス
    const firstError = form.querySelector(".is-error");
    if (firstError) firstError.focus();
    return;
  }

  // ローディング状態
  btnSave.disabled = true;
  btnSave.querySelector(".btn-label").classList.add("hidden");
  btnSave.querySelector(".btn-loading").classList.remove("hidden");

  // モック：API 呼び出しのシミュレーション（1.2秒待機）
  await new Promise((resolve) => setTimeout(resolve, 1200));

  // モック：90% の確率で成功、10% でエラー（デモ用）
  const isSuccess = Math.random() < 0.9;

  // ローディング解除
  btnSave.disabled = false;
  btnSave.querySelector(".btn-label").classList.remove("hidden");
  btnSave.querySelector(".btn-loading").classList.add("hidden");

  if (isSuccess) {
    showToast(toastSuccess);
    // モック：ページタイトルのアバター同期
    const headerAvatar = document.querySelector(".avatar-sm img");
    if (headerAvatar && iconImg.src) {
      headerAvatar.src = iconImg.src;
    }
  } else {
    showToast(toastError);
  }
});

// ========================================
// フォーム離脱ガード（変更ありの場合に警告）
// ========================================
let isDirty = false;

function markDirty() {
  isDirty = true;
}

inputName.addEventListener("input", markDirty);
inputBio.addEventListener("input", markDirty);
iconFileInput.addEventListener("change", markDirty);
btnRemoveIcon.addEventListener("click", markDirty);

window.addEventListener("beforeunload", (e) => {
  if (isDirty) {
    e.preventDefault();
    e.returnValue = "";
  }
});

form.addEventListener("submit", () => {
  // 送信成功時はガードを解除
  isDirty = false;
});
```

---

## レスポンシブ対応チェックリスト

- [x] モバイル (320px-767px)
  - アイコンエリアを縦積みレイアウト
  - アクションボタンを全幅・縦並び
  - セクションパディングを縮小
  - トーストを左右マージン付きで表示
- [x] タブレット (768px-1023px)
  - ナビリンクを非表示（ハンバーガーメニューへの移行を想定）
  - カード・フォームのレイアウトはデスクトップと同等
- [x] デスクトップ (1024px+)
  - 最大幅 680px のカードを中央寄せ
  - フルナビ表示
- [x] 大画面 (1440px+)
  - ページコンテナの最大幅制限により適切な余白を確保

---

## 実装のポイント

### アクセシビリティ
- 全フォーム要素に `aria-describedby` でエラーメッセージ・ヒント・文字数カウンターを関連付け
- エラーメッセージに `role="alert"`、成功メッセージに `role="status"` を付与
- フォーカス状態の視覚的フィードバック（`focus-visible` 対応）
- 画像には適切な `alt` テキストを設定

### インタラクション
- 文字数リアルタイムカウント（90%超で黄色警告、上限で赤色）
- アイコンプレビュー（ファイル選択前にプレビュー表示）
- 保存ボタンのローディング状態（スピナーアニメーション）
- フォーム離脱ガード（未保存変更がある場合にブラウザ警告）
- トースト通知（成功・失敗）

### バリデーション
- 送信時のクライアントサイドバリデーション
- エラーがある場合は最初のエラーフィールドにフォーカス
- フォーカスが外れた後の再入力でエラーをクリア

---

## フィードバックのお願い

静的画面が完成しました。以下の点についてご確認ください：

1. **レイアウト・構成** - アイコン・名前・自己紹介のフィールド配置はこのイメージでよいですか？
2. **不足要素** - ユーザー名（@handle）やSNSリンクなど追加フィールドはありますか？
3. **インタラクション** - アイコン変更の方法（クリック/ドラッグ&ドロップなど）について要件はありますか？
4. **デザイントーン** - カラースキームや全体的なデザインの方向性はこれでよいですか？

フィードバックを確認後、次のステップ「**4. 実装計画の立案 (`dev-implementation-plan`)**」に進むことをご提案します。
