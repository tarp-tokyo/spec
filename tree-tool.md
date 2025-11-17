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
- Logic View（React Flow）表示（v2）
- ZIP / フォルダドラッグ＆ドロップ解析（v2.5）
- 大規模ツリー（3000〜5000ファイル）対応（v2）

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

## 6. Logic View（React Flow）（v2）
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

## 9. 大規模ツリー最適化（v2）
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



# 🌲 Tree Tool — Structured Hierarchy Generator (TARP Tools)
**Version: 2.0 (Defensive Publication Edition)**  
**Initial Publication Date: 2025-11-17**  
**Author: tarp.tokyo**

---

## 1. Overview
Tree Tool is a browser-based GUI application that generates, visualizes, and edits hierarchical structures such as folder trees, site maps, and logic trees.

It is built with React (Vite) and Tailwind CSS, running entirely on the client side without server dependencies.

This document provides a complete technical specification of the Tree Tool, including its data structures, UI behavior, internal logic, and algorithms.  
Its purpose is to serve as a **Defensive Publication**, establishing prior art and preventing unauthorized patent claims by third parties.

---

## 2. Core Features
- Text input → automatic hierarchy generation  
- Drag & Drop node rearrangement  
- Collapse / Expand toggles  
- Clean JSON export  
- URL sharing using LZ compression  
- Responsive layout  
- Logic View powered by React Flow（v2）  
- Folder / ZIP drag‑and‑drop parsing (v2.5)  
- Scales up to 3000–5000 nodes for large structures（v2）  

---

## 3. Internal Data Structure
```json
{
  "id": "uuid",
  "name": "string",
  "children": [
    { "id": "...", "name": "...", "children": [...] }
  ]
}
```

### Characteristics
- Recursive, uniform node structure  
- All nodes share the same schema (ROOT included)  
- `children` is always an array  
- Moving a node = remove from old parent → insert into new parent  

This structure is simple yet powerful, enabling fast rendering and reliable manipulation.

---

## 4. Collapse Logic
Tree Tool does **not** store a `collapsed` flag in each node.  
Instead, visibility is determined during rendering.

### Reason
Avoids state bloat and improves performance in large trees by using **lazy evaluation**.

---

## 5. Drag & Drop System
Based on the native HTML5 Drag & Drop API.

### Event Flow
1. `dragstart`: embed `node.id` into the transfer data  
2. `drop`:  
   - Remove node from its original parent’s `children`  
   - Insert node into new parent’s `children`  
   - Trigger rerender  

This ensures minimal state mutation and predictable behavior.

---

## 6. Logic View (React Flow)（v2）
Logic View represents the tree in a top‑down MECE‑style logic map.

### Features
- Automatic layout  
- Parent → child edges  
- Zoom and pan support  
- Custom node types (via `nodeTypes`)  

This view transforms folder trees into structured logical diagrams suitable for planning and presentations.

---

## 7. URL Sharing (LZ Compression)
JSON data is transformed via:

1. Serialize JSON  
2. LZString compression  
3. Base64 encoding  
4. Embedded into `?data=` parameter  

This allows long hierarchical data to be shared in a single URL.

---

## 8. Folder / ZIP Parsing (v2.5)
### Folder D&D
- Uses FileSystem API to read directories recursively  

### ZIP D&D
- Uses JSZip to extract paths  

### Path Processing
Each file path is split by `/` and inserted into the recursive data structure, reproducing the folder tree automatically.

---

## 9. Large‑Scale Performance Optimizations（v2）
Tree Tool supports several thousand nodes through:

- Virtualized rendering strategies  
- Lazy rendering for collapsed branches  
- Efficient keyed diffing  
- Minimal DOM updates  

This enables smooth usage even with massive codebases or website structures.

---

## 10. Use Cases
- Website architecture planning  
- React/Next.js project folder design  
- Logic tree / MECE breakdowns  
- Repository analysis  
- Structured planning for documents and presentations  

---

## 11. License & Rights
The Tree Tool and its associated specifications are protected under the **TARP Tools License**.  
Unauthorized redistribution, commercial usage, publication of modified derivatives, and reverse engineering are prohibited.

Full policy: https://tools.tarp.tokyo/terms/

---

## 12. Purpose of This Document (Defensive Publication)
This English document ensures the Tree Tool’s technical concepts qualify as **prior art**,  
preventing third parties from claiming patents on identical or derivative mechanisms.

By publishing this at a verifiable timestamp on GitHub, tarp.tokyo legally establishes:

- Authorship  
- Public availability  
- Originality  
- Prior existence  

This protects Tree Tool globally under USPTO, JPO, and EPO requirements for prior art.

---

End of Document.
