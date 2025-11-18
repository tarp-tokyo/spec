# 漢字世界（Kanji the World）— 仕様書（防御公開版）
**バージョン：1.0（MVP）**  
**初回公開日：2025-11-17**  
**著者：tarp.tokyo**

---

## 1. 概要
「漢字世界」は、漢字や言語を学習しながら冒険するブラウザベースの学習RPGです。  
プレイヤーは“書き取り”によって文字を習得・育成し、漢字を味方につけて世界を旅します。  
Webブラウザ（PWA）上で動作し、オフライン利用にも対応します。

---

## 2. 基本機能（MVP）
- Canvas 上での漢字手書き入力（マウス・指・ペン対応）
- 書き取りによる「LP（ラーニングポイント）」獲得
- 辞書への文字登録／育成ステータスの上昇（習熟度・熟練度）
- 漢字の捕獲・育成（進化は Ver.2以降）
- 辞書検索における手書き入力
- スマホ・PC対応の簡易バトル（Ver.1は限定仕様）

---

## 3. 書き取り入力仕様
- 技術：HTML5 Canvas 上で stroke 情報を取得
- 入力：指・マウス・タッチペン
- データ：座標・順序・時刻などを記録
- 認識：クライアントサイド処理
- 評価：書き順・速度・形状（Ver.2以降）

---

## 4. データ構造（例）
```json
{
  "kanji": "漢",
  "level": 2,
  "lp": 87,
  "熟練度": 0.52,
  "習熟度": 0.35,
  "辞書登録": true
}
```

---

## 5. ステータスと進化
- 各漢字は LP・熟練度・習熟度を保持
- 条件達成で進化（例：「一」→「十」→「百」など）
- 進化条件や演出は仕様書では非公開（Ver.2以降）

---

## 6. 世界観
- 「白」「赤」「青」「緑」「黒」の色エリア＋東西南北で構成
- 図書館での記録・回復・辞書登録など
- ストーリー・エンディングは未記載（ユーザー発見型構成）

---

## 7. 将来バージョン計画
- Ver.2：進化・分岐育成・図書館強化・手書きUI強化
- Ver.3：マルチプレイ・ランキング・協力イベント
- 教育展開：筆順／音声読み上げ／多言語UI

---

## 8. 筆跡署名と改ざん防止構造（Ver.1より導入）
本プロジェクトでは、書き取り動作で生成されるストロークデータ（座標・順序・時刻）に対し：

- プレイヤーの UUID を紐付け  
- ストローク配列に対して SHA-256 署名を付加  

することで、以下のような真正性を保証します：

- 書いた本人の特定（匿名UUID）  
- 改ざんの検出  
- 辞書登録・熟練度履歴の正当性確認  
- PWA環境でも、後のクラウド同期時に真正性を検証可能

この構造により、教育ログの信頼性・ランキングの公平性・チート対策が強化されます。

---

## 9. 他社製品との比較
- 任天堂DSの『漢検DS』『美文字トレーニング』等とは異なり、HTML5技術に基づいた完全Web構成。
- UI・操作系・認識ロジックはすべて独自実装。
- ゲームタイトルは比較目的の言及であり、商標使用意図はありません。

---

## 10. ライセンスと知的財産
本仕様書および Kanji the World は **TARP Tools License** の下で保護されます。  
無断複製・改変・再配布・逆コンパイルは禁止されます。  
https://tools.tarp.tokyo/terms/

---

# Kanji the World — Specification (English Version)
**Version: 1.0 (MVP)**  
**Initial Publication: 2025-11-17**  
**Author: tarp.tokyo**

---

## 1. Overview
Kanji the World is a browser-based learning RPG that combines handwriting practice and character growth.  
Players write kanji to learn and master them, journeying through themed regions. Offline-ready via PWA.

---

## 2. Core Features (MVP)
- HTML5 Canvas handwriting (mouse, finger, stylus)
- Earn LP (Learning Points) from strokes
- Register kanji to dictionary with stats (mastery/familiarity)
- Capture & train kanji characters (evolution in v2)
- Handwriting search
- Simple mobile-friendly battle system

---

## 3. Writing Input System
- Canvas collects coordinates, order, and timestamp
- Devices: touch, mouse, stylus
- Stroke recognition is local (browser-side)
- Future: order/speed/shape-based scoring

---

## 4. Data Model (example)
```json
{
  "kanji": "漢",
  "level": 2,
  "lp": 87,
  "mastery": 0.52,
  "familiarity": 0.35,
  "registered": true
}
```

---

## 5. Stats and Evolution
- Each kanji tracks LP, mastery, familiarity
- Evolves by rules (e.g. 一 → 十 → 百)
- Conditions hidden in spec (to avoid spoilers)

---

## 6. World Structure
- Five regions: White, Red, Blue, Green, Black × NESW directions
- Libraries offer save, recover, register
- Story intentionally excluded from spec

---

## 7. Future Plans
- v2: evolution trees, enhanced UI, library features
- v3: multiplayer, raid events, ranking
- Educational extensions: stroke order, audio, multilingual UI

---

## 8. Stroke Signature & Integrity Proof (from Ver.1)
Each stroke (position, time, order) is:

- Linked to the player’s UUID  
- Signed via SHA-256 hash  

This allows:

- Identity binding of who wrote it  
- Tamper detection  
- Validity of dictionary and stat registration  
- Offline-first, cloud-verifiable input tracking

This ensures fairness, anti-cheating, and trusted learning logs.

---

## 9. Game Comparison
- Mentioned DS titles like “漢検DS” are touch-input based but differ in tech/UI
- This is a pure HTML5 implementation
- Trademarks referenced for historical comparison only

---

## 10. License & IP
This spec and the Kanji the World project are protected by the **TARP Tools License**.  
No unauthorized copying, republishing, tampering or derivation allowed.  
https://tools.tarp.tokyo/terms/
