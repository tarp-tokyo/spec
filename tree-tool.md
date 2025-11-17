# 🌲 Tree Tool — 構造化階層図生成ツール（TARP Tools）
**バージョン：2.0（防御公開版）**  
**初回公開日：2025-11-17**  
**著者：tarp.tokyo**

---

## 1. 概要
Tree Tool は、フォルダ構造・サイトマップ・ロジックツリーなどの階層構造をブラウザ上で視覚的に生成・編集できる GUI ツールである。

React（Vite）＋ Tailwind CSS をベースとし、完全クライアントサイドで動作する。

本仕様書は、Tree Tool の内部構造・アルゴリズム・UI設計・データ形式などを第三者が再現可能な精度で記録し、防御公開（Defensive Publication）を目的とする。

---

## 2. コア機能一覧
- テキスト入力 → 自動階層構造生成
- D&D による階層移動
- 階層折り畳み（Collapse / Expand）
- JSON 出力（クリーン JSON）
- URL 共有（LZ圧縮）
- レスポンシブ対応
- Logic View（React Flow）表示
- ZIP / フォルダドラッグ＆ドロップ解析（v2.5）
- 大規模ツリー（3000〜5000ファイル）対応

---

## 3. 内部データ構造
```json
{
  "id": "uuid",
  "name": "string",
  "children": [
    { "id": "...", "name": "...", "children": [...] }
  ]
}
```

特徴：
- 再帰構造  
- すべてのノードが共通構造  
- children は常に配列  
- ノード移動は「元の children から削除 → 新しい親の children に挿入」

---

## 4. 折りたたみアルゴリズム
collapsed フラグを保持せず、描画時に判定する遅延展開方式を採用。

---

## 5. D&D 方式
HTML5 Drag and Drop API で構築。

処理フロー：
1. dragstart：node.id を転送データに格納  
2. drop：  
   - 移動元 children からノード削除  
   - 移動先 children にノード追加  
   - 状態再レンダリング

---

## 6. Logic View（React Flow）
- ノード位置は自動レイアウト  
- edges = 親 → 子  
- ズーム・パン  
- nodeTypes でカスタムノード対応

---

## 7. URL共有（LZ圧縮）
JSON → LZString 圧縮 → Base64 → `?data=` パラメータ化。

---

## 8. フォルダ / ZIP 解析（v2.5）
- FileSystem API：フォルダ展開  
- JSZip：ZIP 展開  
- パスを split('/') し再帰構造を自動生成

---

## 9. 大規模ツリー最適化
- Virtual DOM 最適化  
- ノード遅延展開  
- 最小レンダリング方式  

---

## 10. 想定ユースケース
- Webサイト構造設計  
- React/Next.js プロジェクト構成管理  
- ロジックツリー・MECE分解  
- 大規模リポジトリ解析  
- 情報整理ツールとしての利用  

---

## 11. ライセンス／権利
本仕様書および Tree Tool に関する技術は **TARP Tools License** に基づく。  
内容の無断再利用・商用利用・再配布を禁止する。

詳細：https://tools.tarp.tokyo/terms/

