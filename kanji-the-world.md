# 漢字世界（Kanji the World）— 仕様書（防御公開版）
**バージョン：1.0（MVP）**  
**初回公開日：2025-11-18**  
**著者：tarp.tokyo**

---

## 1. 概要
「漢字世界」は、漢字や言語を学習しながら冒険するブラウザベースの学習RPGです。  
プレイヤーは“書き取り”によって文字を習得・育成し、漢字を味方につけて世界を旅します。  

ゲームは Webブラウザ上で動作し、モバイル・PC 両対応。PWA対応でオフライン学習にも対応予定です。

---

## 2. 基本機能（MVP）
- Canvas 上での漢字手書き入力（マウス・指・ペン対応）
- 書き取りによる「LP（ラーニングポイント）」獲得
- 辞書への文字登録／育成ステータスの上昇（習熟度・熟練度）
- 出現する漢字の捕獲・育成（進化は Ver.2以降に対応）
- 辞書検索における手書き入力による補完
- スマホ・PC対応の簡易バトルシステム（Ver.1は限定形式）

---

## 3. 書き取り入力の仕様
- 技術：HTML5 Canvas 上で stroke 情報を収集
- デバイス：スマートフォン（指）、PC（マウス・タッチペン）
- 文字認識処理：クライアントサイドでの stroke matching（後に精度向上予定）
- 書き取り結果に応じて LP・習熟度・熟練度が上昇
- 書き順・美しさ・スピードなどによる評価は Ver.2 以降

---

## 4. データ構造（簡易）
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
- 各漢字にはステータス（熟練度・習熟度・LP）が存在
- 特定条件を満たすと進化（例：「一」→「十」→「百」などの特別系統）
- ストーリー上重要な「進化条件」は GitHub仕様書では非公開とする

---

## 6. 世界観・構成
- 世界には「白」「赤」「青」「緑」「黒」などのエリアがあり、それぞれに対応する漢字が出現
- 東西南北＋色名で構成される各村や図書館などで、学習・回復・収集を行う
- ストーリー詳細・エンディング演出は伏せるが、教育×冒険が融合した世界観

---

## 7. 将来のバージョン構想
- Ver.2：キャラクター進化、辞書分岐進化、ストーリー拡張、辞書UI強化
- Ver.3：オンライン対戦／協力（マルチプレイ）、レイド戦、ランキングシステム
- 教育アプリとしての展開（読み上げ／筆順強調／他言語対応）

---

## 8. 他社製品との関係について
- 任天堂DSソフト『漢検DS』や『美文字トレーニング』等に見られる手書き認識ゲームは存在するが、本プロジェクトは HTML5 Canvas による完全Web実装であり、既存の操作体系・UI・認識エンジンを流用していない。
- ゲームタイトルの記載は歴史的背景・比較目的であり、商標使用意図は一切ありません。

---

## 9. 知的財産・ライセンス
- 本仕様書および『漢字世界』に関する設計・アイデア・実装方針は **TARP Tools License** に基づき提供されます。
- 商用利用・複製・類似ゲーム制作・再配布・逆コンパイルなどは禁止されます。
- https://tools.tarp.tokyo/terms/ にてライセンス詳細を確認できます。

---

# Kanji the World — Specification (English Version)
**Version: 1.0 (MVP)**  
**Initial Publication Date: 2025-11-18**  
**Author: tarp.tokyo**

---

## 1. Overview
Kanji the World is a browser-based educational RPG that blends handwriting practice with exploration and character growth.  
Players write characters on-screen to learn, master, and evolve them, journeying through a world powered by language.

---

## 2. Core Features (MVP)
- Handwriting recognition on HTML5 Canvas (mouse, finger, stylus)
- Earn LP (Learning Points) through writing
- Register kanji to a dictionary and increase mastery/familiarity
- Basic turn-based battles featuring kanji characters
- Handwriting-based dictionary lookup
- Available on mobile and desktop

---

## 3. Handwriting System
- Canvas-based stroke collection
- Smart recognition logic (browser-side)
- Input devices: finger, mouse, stylus
- LP/familiarity increase based on recognition results
- Evaluation by order/speed/accuracy: planned for Ver.2+

---

## 4. Data Model
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
- Each kanji has mastery/familiarity/LP
- Evolution is possible based on criteria (e.g. 「一」→「十」→「百」…)
- Exact evolution conditions are intentionally omitted to avoid spoilers

---

## 6. World Setting
- The world is divided into color-coded regions (White, Red, Blue, Green, Black)
- Each area features a library, village, or elemental theme
- Story and ending are intentionally withheld (narrative surprises planned)

---

## 7. Future Versions
- Ver.2: Kanji evolution branches, story expansion, advanced UI
- Ver.3: Multiplayer modes, raids, ranking, educational test mode
- Educational expansion (stroke guides, multilingual training, audio)

---

## 8. Related Games
- Games like “漢検DS” and “美文字トレーニング” (Nintendo DS) previously used handwriting for learning
- Kanji the World is a **fully browser-native system using HTML5 Canvas**, not based on those interfaces or engines.
- Titles are mentioned for historical comparison purposes only.

---

## 9. License / Intellectual Property
This document and the project “Kanji the World” are protected under the **TARP Tools License**.  
No redistribution, reverse engineering, commercial replication, or derivative publishing is allowed.  
See: https://tools.tarp.tokyo/terms/ for license details.
