# TARP Form — WordPress Plugin Specification (v1.0)
**File:** WP-Plugins-tarp-form.md  
**Initial Publication:** 2025-12-05
**Project:** TARP Form  
**License:** TARP Tools License  
**Author:** TARP developer (tarp.tokyo)  
**Status:** Specification v1.0  

---

# 1. 概要（Overview）

**TARP Form** は、WordPress サイトに柔軟で安全かつ拡張性の高いフォーム機能を提供する次世代フォームプラグインである。  
以下の課題を解決することを目的とする：

- 既存フォームプラグインの HTML 構造固定化問題  
- 複雑なバリデーション設定、コード依存構造の解消  
- 管理画面とフロントのデザインの乖離  
- ディレクター・マーケターでも扱える「ノーコード編集」実装  
- 同一ページ内の複数フォームの衝突問題  
- DB 保存の標準化と WP らしい拡張性不足  
- カスタマイズ性と保守性の両立  

TARP Form は、**超汎用／超軽量／超安全設計**をコンセプトとする。

---

# 2. 主な特徴（Key Features）

## 2.1 メイン機能
- バックエンド完全自動生成  
- DB保存（entries / entries_meta 方式）  
- バリデーション自由設定  
- HTML構造の統一（div ラッパ方式）  
- 確認画面・完了画面の自動生成  
- ショートコードベースの出力  
- 同一ページ内複数フォーム対応  
- 多言語環境対応  
- すべてのフロント HTML をテーマ側で自由装飾可能  

---

# 3. フォーム構築方式（Build Architecture）

## 3.1 エンティティ構造
### フォーム（form）
- form_id
- form_name
- settings（JSON）
- mail_settings（JSON）
- validation_settings（JSON）

### エントリ（entries）
- entry_id
- form_id
- created_at
- user_agent
- ip_address

### メタ（entries_meta）
- meta_id
- entry_id
- key
- value

WP 標準の postmeta に近い拡張性を持つ。

---

# 4. HTML 仕様（UI Structure）

TARP Form のフロント HTML は、以下の **統一構造** で出力される。

```
<div class="tarp-form">
  <div class="tarp-field" data-type="text" data-name="your_name">
    <label class="tarp-label">
      お名前
      <span class="tarp-required">必須</span>
    </label>

    <div class="tarp-inputwrap">
      <input type="text" name="your_name" required>
    </div>

    <div class="tarp-notes">（例）山田太郎</div>

    <div class="tarp-error"></div>
  </div>
</div>
```

## 4.1 ラッパ（tarp-field）
- すべてのフィールドは共通の `.tarp-field` で包む
- data-type 属性（例：text / email / zip / tel / agree）
- data-name 属性に name を格納
- テーマ側で装飾しやすい安全構造

## 4.2 必須表示
- `.tarp-required`  
- 文言は GUI 設定で変更可（例：※、Required）

## 4.3 備考（補足説明）
- `.tarp-notes`  
- HTML可／改行可／長文可

## 4.4 エラー表示
- `.tarp-error` に表示  
- 全エラー文言を GUI で編集可能  

---

# 5. バリデーション（Validation）

## 5.1 標準バリデーション
| 種類 | 内容 |
|------|------|
| required | 必須 |
| email | メール形式 |
| tel | 電話番号 |
| zip | 郵便番号 |
| number | 数値のみ |
| kana | カタカナ |
| hira | ひらがな |
| ascii | 半角 |
| max | 最大文字数 |
| min | 最小文字数 |
| url_ban | URL禁止 |

## 5.2 郵便番号補完（ZIP Auto）
- 郵便番号入力 → 住所自動入力  
- HTML編集不要  
- GUI設定で ON/OFF 可能

## 5.3 エラーメッセージ編集
- WordPress管理画面から変更可能  
- 言語別設定も対応予定

---

# 6. 確認画面 & 完了画面

## 6.1 画面遷移仕様
- 同ショートコードで *フォーム → 確認 → 完了* を自動出力
- 直接アクセスはフォーム画面へリダイレクト

## 6.2 HTML例

### 確認画面
```
<div class="tarp-confirm">
  <div class="tarp-confirm-row">
    <div class="tarp-confirm-label">お名前</div>
    <div class="tarp-confirm-value">山田太郎</div>
  </div>
</div>
```

### 完了画面
```
<div class="tarp-thanks">
  <p>送信が完了しました。</p>
</div>
```

---

# 7. メール送信（Mail System）

## 7.1 管理者宛メール
- 複数宛先対応
- 件名テンプレート
- 差出人名設定可能
- フィールド出力順を GUI で制御

## 7.2 自動返信メール
- From メールアドレス設定
- From 名設定
- 本文テンプレートはショートコード埋め込み対応

---

# 8. Bot 対策（Security）

## 8.1 Smart Token（独自）
- フォーム描画時に乱数トークン生成  
- POST時に照合  
- Bot の直 POST を抑制

## 8.2 reCAPTCHA 互換
- 既存テーマの reCAPTCHA を尊重  
- プラグイン単独読み込みも可  
- 完全 OFF も可  

---

# 9. 複数フォーム対応

## 9.1 同一ページ複数フォーム
- 全フォームに固有 form_id を付与  
- POST 判定は form_id ベース  
- 確認／完了画面も form_id ごとに表示

## 9.2 フォーム複製
- ボタン1つで複製  
- バリデーション、メール設定含め完全コピー  
- 新しいフォーム量産が容易

---

# 10. Gutenberg（未来拡張）

v1：ショートコード中心  
v2：ドラッグ＆ドロップ GUI（D&D フォームビルダー）

- Snow Monkey Forms を超える操作性  
- Gutenberg ブロック化  
- ノーコード構築可能  
- Preview と同期するリアルタイムUI

---

# 11. テーマ開発者向け仕様

## 11.1 CSS
- Tailwind 不使用（衝突防止）  
- `.tarp-*` 名前空間  
- 装飾はテーマ側で上書き可能  

## 11.2 HTML Override
WooCommerce と同じ仕組み：

```
/wp-content/themes/your-theme/tarp-form/
```

にテンプレートを置くと上書き可能。

---

# 12. 保守・将来性

## 12.1 安全性
- 依存ライブラリほぼゼロ  
- jQuery 不使用  
- PHP側で完全バリデーション  
- WPアップデートで壊れにくい構造  

## 12.2 将来的な追加予定
- CRM送信  
- 外部API（Webhook）  
- ステップフォーム  
- 予約フォーム  
- 分岐ロジック  

---

# 13. ライセンス（License）

```
本仕様書および TARP Form は TARP Tools License の下で保護されます。
無断複製・改変・再配布・逆コンパイルは禁止されます。
https://tools.tarp.tokyo/terms/
```

---

# 14. 結論（Summary）

TARP Form は、  
**「現場で本当に必要とされる要件」をすべて満たし、  
既存フォームプラグインの弱点を根こそぎ改善した、  
WordPress フォームの決定版となる設計である。**

---

# TARP Form — WordPress Plugin Specification (v1.0)  
**File:** WP-Plugins-tarp-form.md  
**Initial Publication:** 2025-12-05
**Project:** TARP Form  
**License:** TARP Tools License  
**Author:** TARP developer (tarp.tokyo)  
**Status:** Specification v1.0  

---

# 1. Overview

**TARP Form** is a next‑generation WordPress form plugin designed to provide extreme flexibility, safety, and extensibility.  
It addresses long‑standing issues found in existing WordPress form plugins:

- Rigid HTML structure that prevents theme designers from customizing layouts  
- Complex validation systems requiring PHP code  
- Inconsistency between admin UI vs. front‑end HTML  
- Lack of tools that allow directors/marketers to build forms without coding  
- Multi-form conflicts on the same page  
- Poor extensibility and DB structure limitations  
- Difficult maintenance with existing plugins  

TARP Form is built with the concept of being **ultra‑flexible, ultra‑lightweight, and ultra‑safe**.

---

# 2. Key Features

## 2.1 Major Features
- Backend auto‑generation for all forms  
- Database saving (entries & entries_meta model)  
- Fully configurable validation  
- Unified front‑end HTML structure  
- Auto‑generated confirmation and completion screens  
- Shortcode‑based rendering  
- Full multi‑form compatibility (same page OK)  
- Multi‑language capable  
- Front‑end HTML fully theme‑customizable  

---

# 3. Form Architecture

## 3.1 Entities

### Form
- form_id  
- form_name  
- settings (JSON)  
- mail_settings (JSON)  
- validation_settings (JSON)  

### Entries
- entry_id  
- form_id  
- created_at  
- user_agent  
- ip_address  

### Entries Meta
- meta_id  
- entry_id  
- key  
- value  

Similar to WordPress postmeta — ensuring maximum extensibility.

---

# 4. Front-end HTML Structure

All TARP Form fields follow the **unified structure** below:

```
<div class="tarp-form">
  <div class="tarp-field" data-type="text" data-name="your_name">
    <label class="tarp-label">
      Your Name
      <span class="tarp-required">Required</span>
    </label>

    <div class="tarp-inputwrap">
      <input type="text" name="your_name" required>
    </div>

    <div class="tarp-notes">e.g. John Doe</div>

    <div class="tarp-error"></div>
  </div>
</div>
```

## 4.1 Field Wrapper (`tarp-field`)
- Every field uses the `.tarp-field` wrapper  
- `data-type` attribute identifies field type  
- `data-name` holds the field’s name attribute  
- Designed for easy theme styling  

## 4.2 Required Label
- Displayed with `.tarp-required`  
- Text is fully configurable (e.g., Required / Mandatory / ※)

## 4.3 Notes (Description)
- `.tarp-notes` supports multi-line text  
- Can contain simple HTML and long descriptions  

## 4.4 Error Display
- `.tarp-error` used for validation messages  
- All error messages are editable from admin panel  

---

# 5. Validation System

## 5.1 Built-in Validation Rules
| Rule | Description |
|------|-------------|
| required | Mandatory field |
| email | Valid email |
| tel | Phone number |
| zip | Postal code |
| number | Numeric only |
| kana | Katakana only |
| hira | Hiragana only |
| ascii | Half‑width ASCII |
| max | Max length |
| min | Min length |
| url_ban | Reject URLs |

## 5.2 ZIP Auto-fill
- Postal code → auto-fill address fields  
- No HTML editing required  
- Can be toggled ON/OFF via GUI  

## 5.3 Customizable Error Messages
- Fully editable from admin panel  
- Multi-language message sets supported  

---

# 6. Confirmation & Completion Screens

## 6.1 Auto-generated flow
The same shortcode automatically switches between:

1. **Input screen**  
2. **Confirmation screen**  
3. **Completion screen**  

Direct access forces redirect back to input screen.

### Confirmation Screen Example
```
<div class="tarp-confirm">
  <div class="tarp-confirm-row">
    <div class="tarp-confirm-label">Your Name</div>
    <div class="tarp-confirm-value">John Doe</div>
  </div>
</div>
```

### Completion Screen Example
```
<div class="tarp-thanks">
  <p>Your submission has been completed.</p>
</div>
```

---

# 7. Mail System

## 7.1 Admin Notification Mail
- Multiple recipients  
- Subject templates  
- Customizable field display order  

## 7.2 Auto-reply Mail (User)
- Configurable From address  
- Configurable sender name  
- Template supports field shortcode insertion  

---

# 8. Security & Bot Protection

## 8.1 Smart Token (Custom Logic)
- Random token generated upon form rendering  
- Verified on POST  
- Prevents direct bot submissions  

## 8.2 reCAPTCHA Compatibility
- Inherits site-wide reCAPTCHA setting  
- Can disable inherited setting and load plugin-side version  
- Fully optional  

---

# 9. Multi-form Support

## 9.1 Multiple Forms on Same Page
- Each form has a unique `form_id`  
- POST handling is ID-based  
- Confirmation/Thanks screens also ID-based  

## 9.2 Form Duplication
- One-click duplication  
- Copies:
  - Fields  
  - Validation  
  - Mail settings  
  - Notes / Required labels  
- Allows fast production of similar forms  

---

# 10. Gutenberg Support (Future Roadmap)

### v1.0：Shortcode-first  
### v2.0：Drag & Drop Form Builder (GUI)

- More advanced than existing WP form builders  
- Seamlessly integrates with Gutenberg  
- Real-time preview  
- No coding required  

---

# 11. Theme Developer Specifications

## 11.1 CSS Policy
- Tailwind CSS is **not** bundled (to avoid conflicts)  
- Uses `.tarp-*` namespace  
- Front-end looks minimal by default for full theme freedom  

## 11.2 Template Override
Themes can override plugin templates by adding:

```
/wp-content/themes/your-theme/tarp-form/
```

Same concept as WooCommerce template inheritance.

---

# 12. Maintenance & Extensibility

## 12.1 Reliability
- No external JS dependencies  
- No jQuery required  
- Validation fully guaranteed by PHP  
- Resistant to future WP updates  

## 12.2 Future Extensions
- CRM integration  
- Webhook / API posting  
- Multi-step forms  
- Reservation/booking features  
- Conditional branching  

---

# 13. License

```
This specification and TARP Form are protected under the TARP Tools License.
Unauthorized reproduction, modification, redistribution, or reverse engineering is prohibited.
https://tools.tarp.tokyo/terms/
```

---

# 14. Summary

TARP Form is engineered to become:  

**“the definitive WordPress form plugin”**  

By solving every major pain point of existing solutions and offering  
professional-grade extensibility and total theme freedom.

---

(END)

