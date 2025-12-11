# TARP Universal Progressive Attention UI Specification

Version: 1.0  
Status: Under Development
Author: tarp.tokyo  
License: TARP Tools License (see /terms/)

### 1. 概要

「注意喚起 UI が、ユーザーのアクティブ使用時間に応じて徐々に静まる（視覚強度を下げる）」TARP Tools独自仕様。

### 2. 目的

* 初回ユーザーには強く認知される
* 使うほど邪魔にならない
* 本来の作業体験を損なわない
* 優しい“学習UI”を実現する
* 過度なモーダル表示を避け、ブランド哲学を守る

### 3. 設計思想

* UIはユーザーとともに成長する
* 注意喚起は“押し付けない”
* 時間ベースで自然消滅する
* UXは教育的に、侵襲性は最小に

### 4. 動作仕様

1. **初回訪問**

   * 強めのコントラスト
   * アニメーションまたは強調色
   * 「押して読んでね」誘導を強調

2. **クリックされた瞬間**

   * アクティブ時間計測開始（localStorage）

3. **10分経過**

   * 彩度 20% 減少
   * ボーダー色を落とす

4. **30分経過**

   * 通常 UI と同化
   * アイコンのみ表示

5. **1時間以降**

   * 完全に“控えめ”状態へ
   * （ただしクリックで全文は読める）

### 5. 実装方式

* localStorage に「アクティブ使用秒数」を記録
* Visibility API で非アクティブ時間は計測しない
* CSS フィルタ or HSL 調整で段階的に視覚減衰

### 6. 対象コンポーネント

* 注意喚起ボタン
* フォームの案内
* チュートリアル
* 印刷前の警告
* 設定ガイド

### 7. 導入例

* Liner Tool の印刷注意喚起
* Tailwind Builder の初回ガイド
* Times Table Tool の音声案内
* WordPress プラグインの設定ガイドにも転用可能

### 8. ライセンス & 権利

This specification and all associated TARP implementation patterns are protected under the **TARP Tools License**.
Unauthorized reproduction, reverse engineering, modification, or re-distribution is prohibited.

See:
`https://tools.tarp.tokyo/terms/`

### Revision History

| Version   | Date       | Changes               |
| --------- | ---------- | --------------------- |
| 1.0 | 2025-12-11 | Initial version |
