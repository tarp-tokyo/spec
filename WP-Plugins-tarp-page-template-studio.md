# TARP Page Template Studio — 仕様書（日本語版）

バージョン：1.0（MVP）  
初回公開日：2025-12-05  
著者：TARP developer (tarp.tokyo)

---

## 概要
TARP Page Template Studio は、WordPress 固定ページに対し  
**テーマを編集せずに専用 PHP / CSS を生成・編集・適用できる** 開発者向けプラグインです。

ローカル環境でテーマファイルを触れない場合や、  
他社管理サイトでも直接テンプレートを編集したい場合に最適です。

---

## 機能一覧

### 1. ページ固有テンプレートの自動生成
- 固定ページ編集画面から「テンプレートを生成する」をクリックすると  
  `/tarp-pages/{slug}.php` と `/tarp-pages/{slug}.css` が生成される。
- 生成されたテンプレートがそのページ専用テンプレートとして適用される。

### 2. 既存 `page-スラッグ.php` の読み込み
ページに以下のテンプレートがテーマ内に存在する場合、自動で読み込む：

`/wp-content/themes/{theme}/page-{slug}.php`

- このとき CSS は `/tarp-pages/{slug}.css` を生成し読み込む（テーマ側を汚さない）。

### 3. テンプレート選択がある場合（親子テンプレート対応）
固定ページ編集画面で「テンプレート」が選択されている場合：

例）`page-contact-confirm.php` をテンプレートとして選択した場合  
→ プラグインはそのテンプレートを **優先して読み込む**

CSS は以下を読む：

`/tarp-pages/contact-confirm.css`


---

## テンプレート読み込みの優先順位

1. **固定ページでテンプレートが選択されている場合**
2. **テーマ内の `page-スラッグ.php` が存在する場合**
3. **プラグインが生成した `/tarp-pages/スラッグ.php` がある場合**
4. **フォールバック：テーマの page.php**

---

## 「テンプレート生成」ボタンの仕様
### A. 既にファイルが存在する場合はボタンを無効化（安全仕様）
- `/tarp-pages/slug.php` が存在する → 生成ボタンはクリック不可（グレーアウト）
- 過剰上書きを防止

---

## CSS 読み込み仕様
- PHP の位置に関わらず CSS は必ず以下に生成：

`/tarp-pages/{slug}.css`


- 読み込みは `wp_enqueue_style()` により実行
- テーマ内 CSS を汚さない構造

---

## プラグイン無効化時フォールバック

プラグインが無効になってもサイトが白画面にならないように以下を実行：

1. テンプレート読み込みフィルタをすべて停止  
2. WordPress の標準テンプレート階層にフォールバック  
3. プラグイン生成ファイルを読み込もうとしても失敗しない安全構造

---

## 管理画面 UI 仕様

### CodeMirror6 エディタ搭載（完全ローカル）
- VS Code 風 UI
- シンタックスハイライト（PHP / CSS）
- 行番号
- タブインデント
- ダークテーマ（One Dark）

### タイトル表示例
`PHP Template (page-about.php)`
`CSS File (/tarp-pages/about.css)`


---

## 保存仕様
- WordPress の「更新」では保存されない（本文とは別管理）
- プラグイン UI の「PHP / CSS を保存する」ボタンでのみ保存可能
- 保存先は `/tarp-pages/{slug}.php` と `/tarp-pages/{slug}.css`

---

## セキュリティ
- nonce による CSRF 保護
- `current_user_can()` による権限検証
- 直接アクセス禁止 (`if (!defined('ABSPATH')) exit;`)

---

## 今後追加予定（拡張案）
- 忍者モード（検索から隠す）
- 高度なロールアクセス権設定
- 外部 IDE 連携（GitHub Codespaces / VSCode Web など）
- 自動バックアップ機能
- プレビュー付きテンプレート比較モード

---

## ライセンス
非公開ツール（Public Release 予定なし）。  
開発者本人および特定案件向けの内部利用を想定。  

本仕様書および Kanji the World は TARP Tools License の下で保護されます。  
無断複製・改変・再配布・逆コンパイルは禁止されます。  
https://tools.tarp.tokyo/terms/

---

# TARP Page Template Studio — Specification (English)

Version: 1.0 (MVP)  
Initial Publication: 2025-12-05  
Author: TARP developer (tarp.tokyo)

---

## Overview
TARP Page Template Studio is a WordPress developer tool that allows you to:
- Generate page-specific PHP and CSS templates  
- Edit them directly inside the WP admin with a VS-Code–style editor  
- Load these templates automatically without modifying the active theme

Ideal for:
- Client projects where you cannot touch the theme
- Emergency, on-server editing
- Sites inherited from other agencies
- Secure template customization without FTP access

---

## Features

### 1. Automatic template generation
Click “Generate Template” on a page edit screen:

Creates:

`/tarp-pages/{slug}.php`
`/tarp-pages/{slug}.css`


Both become the page-specific template.

---

### 2. Support for existing theme templates
If the theme already contains:

`page-{slug}.php`

→ This is used as the **PHP template**,  
but CSS is **always loaded from**:


`/tarp-pages/{slug}.css`


Ensures theme cleanliness and maintainability.

---

### 3. Support for page template selection (“Template” dropdown)
If the editor selects a template such as:  

`page-contact-confirm.php`


The plugin **prioritizes this template**.

CSS is loaded from:

`/tarp-pages/contact-confirm.css`


---

## Template Loading Priority

1. **Page Template selected in editor**  
2. **Theme’s `page-{slug}.php` exists**  
3. **Plugin-generated `/tarp-pages/{slug}.php` exists**  
4. **Fallback to theme’s page.php**

Fully compatible with complex slugs & contact-confirm style templates.

---

## Safety: Disable “Generate Template” when file exists
To prevent accidental overwrites:

- If `/tarp-pages/{slug}.php` already exists →  
  The button becomes disabled (grayed-out)

---

## CSS Loading Rules
Always:

`/tarp-pages/{slug}.css`


Even when using a theme’s PHP template.

Loaded via `wp_enqueue_style()`.

---

## Plugin Deactivation Fallback
If the plugin is deactivated:

- The site continues running normally  
- Standard WP template hierarchy applies  
- No fatal error even if plugin-generated templates exist

Zero-risk fail-safe design.

---

## Admin UI — Code Editor
### CodeMirror 6 (local)
- No CDN required
- PHP & CSS syntax highlighting
- One Dark theme
- Line numbers
- Indentation
- Smooth scrolling

### File name display
Examples:

`PHP Template (page-about.php)`
`CSS File (/tarp-pages/about.css)`


---

## Save Behavior
Important:

- WordPress “Update” does **NOT** save template changes  
- Only the button **“Save PHP / CSS”** writes to disk  
- Saves to `/tarp-pages/{slug}.php` and `.css`

---

## Security
- Nonce protection
- Capability checks (`edit_post`)
- Direct access blocked

---

## Future Roadmap
- Stealth Ninja Mode (hide from plugin list)
- Access-role restrictions
- Versioning and backups
- Side-by-side diff view
- Template preview renderer

---

## License
Private, internal-use plugin.  
Not intended for public distribution.  

This spec and the Kanji the World project are protected by the TARP Tools License.  
No unauthorized copying, republishing, tampering or derivation allowed.  
https://tools.tarp.tokyo/terms/
