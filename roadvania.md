# Roadvania — 技術開示書 / Technical Disclosure

**実地図の道路接続に基づく探索ゲームと地域・環境連動システム**  
**A Real-Map Exploration Game Based on Road Connectivity and Regional Environmental State**

| 項目 / Field | 内容 / Value |
|---|---|
| 文書ID / Document ID | ROADVANIA-TD-001 |
| 版 / Version | 2.0 — 防衛公開 改訂版 / Defensive publication, revised edition |
| 文書作成日 / Preparation date | 2026-10-16, Asia/Tokyo |
| 著者・公開者 / Author and publisher | tarp.tokyo |
| 文書ライセンス / Document license | TARP Tools License — https://tools.tarp.tokyo/terms/ |
| 前版の公開記録 / Prior release record | v1.0：2026-10-09 19:26:53 JST 初回掲載（commit e092574）、19:30:12 JST 現行版（commit 3d0177d）。第17節 / v1.0: first posted 2026-10-09 19:26:53 JST (commit e092574), current text 19:30:12 JST (commit 3d0177d). See Section 17 |
| 本版の実際の公開日時 / Actual public release time of this version | 2026-10-16 15:55:14 JST（commit d69356e）。第17節 / See Section 17 |
| 公開URL / Public URL | https://github.com/tarp-tokyo/spec/blob/main/roadvania.md |
| 実装状態 / Implementation status | 散歩MVP（実施形態A・E・F・J、およびB・C・Dの一部）を試作実装し、2026-10-16に実機確認版を公開。測定値は第15節の条件に限る。釣り・経済・マルチ・ZIP・天気などは提案段階 / A walking MVP (Embodiments A, E, F, J and parts of B, C, D) is implemented as a prototype and a device-test build was published on 2026-10-16. Measured values are limited to the conditions in Section 15. Fishing, economy, multiplayer, ZIP, and weather remain proposals |

## 1. 目的・範囲 / Purpose and scope

**日本語：** 本書はRoadvaniaの技術構想と具体的な実施形態を、防衛公開に使用できる形で記録する。v1.0で開示した道路接続に基づく移動、郵便番号から経路案内への処理、地域経済、時間・天気・テーマに加え、本版では次を開示する：実地図のベクタータイルをドット絵風に描く描画処理、絵文字のドット絵化と施設アイコン、ベクタータイルからの道路グラフ構築と立体交差の判定、橋の下の重なり表現、複数入力の統合とタップによる道なり自動移動、地域名の看板表示、多言語の名前選択、起動失敗の案内と安全な更新配信、ならびに提案段階の釣り・ひとり旅とマルチ・待ち合わせ・旅程と追体験。

**English:** This document records the Roadvania technical concept and concrete embodiments in a form intended for defensive publication. In addition to the road-connectivity movement, postal-code-to-navigation processing, regional economy, and time/weather/theme embodiments disclosed in v1.0, this edition discloses: pixel-art rendering of real-world vector map tiles; pixel-art conversion of emoji characters and facility icons; road-graph construction from vector tiles with grade-separation handling; depiction of passing beneath bridges; integration of multiple input sources and tap-to-walk route following; area-name signs; multilingual name selection; boot-failure notices and safe release deployment; and proposed embodiments for fishing, solo and multiplayer play, meeting points, and trip planning and replay.

**日本語：** 防衛公開は技術内容を公衆に利用可能とすることを目的とするが、先行技術としての評価は公開時期・内容・法域・対象請求項などに依存する。本書は新規性、特許性、非侵害、他者による特許取得の全面的阻止を保証しない。公開は自己の将来の特許出願にも影響し得る。公開と、著作権・ソフトウェア・素材の利用許諾は別であり、本書の利用条件は第18節のTARP Tools Licenseに従い、第三者素材への許諾や包括的な権利放棄を意味しない。[WIPO Patent FAQ](https://www.wipo.int/en/web/patents/faq_patents)、[JPO 新規性・進歩性](https://www.jpo.go.jp/system/laws/rule/guideline/patent/tukujitu_kijun/ht/03_0200.html)

**English:** Defensive publication aims to make technical information available to the public, but its treatment as prior art depends on timing, disclosure content, jurisdiction, and the claims being evaluated. This document does not guarantee novelty, patentability, non-infringement, or prevention of all later patents. Publication may also affect the originator's future patent applications. Public disclosure is distinct from copyright, software, and asset licensing; use of this document is governed by the TARP Tools License in Section 18 and grants no permission for third-party assets or blanket waiver of rights. [WIPO Patent FAQ](https://www.wipo.int/en/web/patents/faq_patents), [JPO novelty and inventive-step guidelines](https://www.jpo.go.jp/system/laws/rule/guideline/patent/tukujitu_kijun/ht/03_0200.html)

## 2. 由来と状態区分 / Provenance and status categories

**日本語：** 本版は、v1.0、tarp.tokyoが2026-10-09に統合した「Roadvania 仕様書 v2」（「Roadvania 全仕様書」と「Roadvania 統合仕様書」を統合）、および2026-10-09〜16の試作開発と検証の記録に基づく。仕様書v2の構想のうち、具体化したデータ構造・処理順序・例示値は提案実施形態であり、過去にすべて考案・承認・実装されていたとの主張ではない。

**English:** This edition is based on v1.0, the "Roadvania Specification v2" consolidated by tarp.tokyo on 2026-10-09 (merging the "Roadvania Full Specification" and the "Roadvania Integrated Specification"), and records of prototype development and verification from 2026-10-09 to 2026-10-16. Data structures, processing sequences, and example values developed from concepts in Specification v2 are proposed embodiments, not assertions that every detail had previously been conceived, approved, or implemented.

| 状態 / Status | 内容 / Content |
|---|---|
| 試作実装・検証済み / Implemented and verified in prototype | 実地図のドット絵描画と色の統一、絵文字のドット絵化（Twemoji）、施設アイコン、道路グラフと立体交差判定、道幅内の8方向連続移動、橋の下の重なり表現、キーボード・ゲームパッド・ドラッグ・タップ入力、タップによる道なり自動移動、地域の看板と道路名、ミニマップとコンパス、日本語・ひらがな・英語、端末への保存、起動失敗の案内、版の切替による配信 / Pixel-art map rendering with palette quantization, emoji sprites (Twemoji), facility icons, road graph with grade separation, eight-direction corridor movement, under-bridge occlusion, keyboard/gamepad/drag/tap input, tap-to-walk, area signs and road names, minimap and compass, Japanese/hiragana/English, local persistence, boot-failure notice, release switching |
| 選択済み方針 / Selected direction | 見た目はポケモン ダイヤモンド・パールの2D風、操作感はStardew Valley並みに滑らか（参照であり既存作品の素材は使わない）。実地図（OSM）を世界対応で使用。絵文字キャラ。完全無料・広告なし。オリジナル通貨RV / Visual target inspired by the 2D look of Pokémon Diamond/Pearl and the smoothness of Stardew Valley (references only; no assets from those works). Worldwide real map (OSM). Emoji characters. Free with no advertising. Original RV currency |
| 構想要件 / Concept requirements | 車・徒歩、釣り、図鑑、経済、ひとり旅とマルチ、待ち合わせ、知人限定の吹き出し会話、旅程づくり、旅の追体験、ZIP/NAVI、時間・天気、スキン、店舗コラボ通知、管理画面、公式Wiki / Driving and walking, fishing, field guide, economy, solo and multiplayer, meeting points, acquaintance-only speech bubbles, trip planning, trip replay, ZIP/NAVI, time and weather, skins, store collaboration notices, administration, official wiki |
| 提案実施形態 / Proposed embodiments | 以下に記載する未実装の処理、構造、計算、代替方式 / Unimplemented procedures, structures, calculations, and alternatives described below |
| 未決 / Open decisions | 第16節 / Section 16 |

## 3. 技術分野・課題・概要 / Technical field, problems, and abstract

**日本語：** 技術分野はブラウザ地図ゲーム、ベクタータイル描画、道路グラフ処理、入力制御、経路探索、地理状態に応じたゲーム処理、Web配信である。二次元地図をそのままゲーム化すると、v1.0で挙げた課題（高架と地上道路の誤接続、道路外の郵便番号代表点、ズームによる距離・燃料計算の変化、境界でのイベント連発、天気API障害への依存）に加え、試作で次の課題が確認された。

- 地図データの最大ズームを超えて拡大表示すると、表示用に切られた道路形状が返り、道路のつながりが途切れる。
- 歩道や私道の入口が車道と別の線で描かれ、交差点でしかつながっていないため、画面上は隣接していても遠回りになる。
- 駅のホームなど、地図上で他とつながらない歩行者道に入ると出られなくなる。
- 地図描画のなめらかな縁が中間色を生み、ドット絵として見えにくい。
- 端末ごとに絵文字の見た目が異なり、マルチプレイで相手の見え方が一致しない。
- 拡大表示では地名の位置が画面に入らず、現在地が分かりにくい。
- 更新の瞬間に開いていた画面や起動失敗で、白画面になる。

**English:** The technical field includes browser-based map games, vector-tile rendering, road-graph processing, input control, route search, geographically conditioned game processing, and web delivery. Beyond the v1.0 problems (false connections between elevated and ground roads, postal-code points outside roads, zoom-dependent distance or fuel, repeated boundary events, and dependence on weather APIs), the prototype revealed the following:

- When the display is zoomed beyond the data's maximum zoom, road geometry returned for display is clipped, breaking road connectivity.
- Sidewalks and private-road entrances are drawn as separate lines connected only at intersections, causing detours even when visually adjacent.
- Pedestrian ways that connect to nothing in the map data, such as station platforms, can trap a player.
- Anti-aliased map edges produce intermediate colors that weaken the pixel-art appearance.
- Emoji appearance differs by device, so multiplayer participants may not see the same character.
- At high zoom, place-name points rarely fall inside the view, making the current location unclear.
- Pages open during an update, or failed startups, may show a blank white screen.

**日本語：** 提案構成では、地理位置・道路接続・移動状態を表示から独立して保持し、移動と経路探索を同一の通行可能道路モデルに従わせる。表示は低解像度描画と整数倍拡大、パレットへの色寄せでドット絵化し、道路データは最大ズームちょうどの非表示地図から取り出す。同じ高さの道路は道幅が接していれば行き来でき、異なる高さの道路は接続点を介してのみ移れる。これらのうち実装済みのものは第15節の範囲で検証したが、一般的な性能・正確性の保証ではない。

**English:** The proposed arrangement stores geographic position, road connectivity, and movement state independently of presentation; manual movement and route search follow a common traversable-road model. Presentation achieves a pixel-art look through low-resolution rendering, integer upscaling, and palette quantization, while road data is extracted from a hidden map held exactly at the data's maximum zoom. Roads on the same level can be crossed between when their widths touch; roads on different levels are entered only through connection points. Implemented parts were verified within the scope of Section 15, which does not establish general performance or accuracy.

## 4. システム構成 / System architecture

**日本語：** クライアントのみで散歩MVPが動作する。表示用地図、文字用地図、道路データ用の非表示地図、ミニマップの4つの地図インスタンスと、施設・キャラ・デバッグ用の重ね描きキャンバスで構成する。サーバーは静的配信のみで、マルチ・天気・価格管理・経路処理のサーバーは提案段階である。

**English:** The walking MVP runs entirely on the client. It uses four map instances (display, labels, a hidden road-data map, and a minimap) plus overlay canvases for facilities, the player, and debugging. The server provides static delivery only; servers for multiplayer, weather, price administration, and routing remain proposals.

```text
Vector tiles (OpenMapTiles schema)
  -> display map: low-res render (pixelRatio = 1/P) -> palette quantization -> integer upscale
  -> label map: full-resolution text, shown only when zoomed out
  -> hidden data map at max data zoom (z14) -> roads, bridges, POIs, places, road names
       -> road graph (segments, levels, links, components)
Keyboard / gamepad / drag / tap -> input layer -> strongest motion vector or tap target
Motion vector + road graph -> corridor movement -> committed position (Web Mercator world units)
Tap target + road graph -> A* over segments -> waypoints -> straight-line smoothing -> follow
Committed position -> camera snapped to dot grid -> area sign / road name / facility names
Player sprite (Twemoji -> pixel sprite) -> bridge occlusion mask -> overlay
Game state -> versioned local save; inline boot guard; release switching on server
```

## 5. データモデル / Data model

**日本語：** 実装では以下をメモリ上に持つ。座標はWebメルカトルの「ズーム20のCSSピクセル」（以下world単位）で、距離は緯度に応じた換算でメートルにする。v1.0のRoadEdge・TurnRule・Vehicle・EconomyPolicy・WeatherCell・Eventなどは引き続き提案である。

**English:** The implementation holds the following in memory. Coordinates are Web Mercator CSS pixels at zoom 20 ("world units"), converted to meters with a latitude-dependent factor. The v1.0 RoadEdge, TurnRule, Vehicle, EconomyPolicy, WeatherCell, and Event records remain proposals.

| Record | Fields / フィールド | 状態 / Status |
|---|---|---|
| Segment | id, ax, ay, bx, by, cls, brunnel, level, halfWidth, adj (set of segment ids), comp | 実装 / Implemented |
| Bridge | ax, ay, bx, by, level, halfWorld or halfCss | 実装 / Implemented |
| Poi | x, y, emoji, names{ja, ja_hira, en}, rank | 実装 / Implemented |
| Place | x, y, key, names{ja, ja_hira, en}, cls | 実装 / Implemented |
| RoadNameSegment | ax, ay, bx, by, names{ja, ja_hira, en} | 実装 / Implemented |
| PlayerState | x, y, seg, facing, zoomIndex, pixelIndex, started, ready | 実装 / Implemented |
| SaveData (v1) | v, lng, lat, level, zoomIndex, pixelIndex, quantize, labels, minimap, lang, savedAt | 実装 / Implemented |
| FishCandidate, CatchRecord, Tackle | 第11節 / Section 11 | 提案 / Proposed |

**日本語：** `level`はOSMの`layer`を優先し、無ければ橋を1、トンネルを−1、それ以外を0とする。`layer`は相対的な上下関係であり高度メートルではない。道路の接続と高さが移動の根拠であり、描画上の上下順だけで道路を接続しない。

**English:** `level` takes OSM `layer` when present; otherwise bridges are 1, tunnels −1, and others 0. `layer` describes relative vertical ordering, not altitude in meters. Connectivity and level govern movement; visual stacking alone does not establish connections.

## 6. 実施形態A：道路グラフと道限定移動 / Embodiment A: road graph and road-constrained movement

### 6.1 ベクタータイルからの道路グラフ構築（実装） / Road-graph construction from vector tiles (implemented)

**日本語：** 表示ズームとは別に、地図データの最大ズーム（OpenMapTilesでz14）ちょうどで開いた非表示の地図インスタンスを置き、その読み込み済みタイルから道路線を取り出す。表示用地図（z16〜19）から取り出すと、拡大表示用に切られた形状が返り道路が途切れるためである。非表示地図はプレイヤーが一定距離（例：400m）離れたら中心を移し、タイルが読み込まれるたびにグラフを作り直す。

**English:** Separate from the display zoom, a hidden map instance is opened exactly at the data's maximum zoom (z14 for OpenMapTiles), and road lines are extracted from its loaded tiles. Extracting from the display map (z16–19) returns geometry clipped for overzoomed display, breaking roads. The hidden map is recentered when the player moves a set distance (for example 400 m), and the graph is rebuilt whenever tiles load.

**日本語：** 道路線を線分に分け、次の規則で結ぶ。許容距離は例として1.2mとする。

1. 同じ線の前後の線分は結ぶ。
2. 線の端どうしが重なる所は、高さが異なっても結ぶ（橋の両端・ランプ・トンネル出入口）。
3. 線の端が別の線の途中に重なる所（T字路、タイル境界の切れ目）は、同じ高さのときだけ結ぶ。
4. 線どうしが途中で交差する所は、同じ高さのときだけ交差点として結ぶ。
5. 同じ高さで、道幅どうしの隙間が一定以下（例：1.5m）の線分も結ぶ（別線で描かれた歩道・私道入口）。

徒歩の対象から、高速道路、自動車専用の幹線、徒歩禁止、駅のホームを除く。最後に、つながった線分の集まりごとに番号（comp）を付ける。

**English:** Road lines are split into segments and linked by the following rules, with a join tolerance of, for example, 1.2 m:

1. Consecutive segments of the same line are linked.
2. Where line endpoints coincide, segments are linked regardless of level (bridge ends, ramps, tunnel portals).
3. Where a line endpoint lies on the interior of another line (T-junctions, tile-boundary cuts), they are linked only on the same level.
4. Where lines cross in their interiors, they are linked as an intersection only on the same level.
5. Segments on the same level whose road widths are within a gap threshold (for example 1.5 m) are also linked (separately drawn sidewalks and private-road entrances).

Motorways, motor-vehicle-only trunk roads, foot-prohibited ways, and station platforms are excluded for walking. Finally, each connected set of segments receives a component number (comp).

```text
buildGraph(features):
  segs = split walkable lines into segments with level and halfWidth(class)
  link consecutive segments of each line
  for each pair of line ends within join: link (any level)
  for each line end on another segment within join: link if same level
  for each segment pair in the same spatial cell and same level:
    if segDistance(a, b) <= a.halfWidth + b.halfWidth + sideTolerance: link
  label connected components
```

### 6.2 道幅内の連続移動（実装） / Corridor movement (implemented)

**日本語：** v1.0で代替方式とした「道路幅を持つ通行領域内の連続移動」を採用した。プレイヤーは現在の線分を持つ。入力から次の位置を求め、現在の線分から2段までたどれる線分のうち、その位置を道幅（道路種別ごとの半幅＋足元の余裕、例：0.75m）内に含む最も近い線分があれば移動し、その線分を現在の線分とする。無ければ、向きを左右に20°ずつ（最大80°）振り、角度の余弦で速度を下げて再試行する（壁沿いスライドと角アシスト）。移動は8方向に正規化し、距離はメートルで計算してズームと切り離す（徒歩約1km/分を初期値とする）。

**English:** The corridor movement that v1.0 described as an alternative is adopted. The player holds a current segment. For each step, the next position is accepted if, among segments reachable within two links of the current segment, one contains the position within its corridor (class-dependent half width plus a foot tolerance, for example 0.75 m); that segment becomes current. Otherwise the direction is rotated by ±20° increments up to 80°, scaling speed by the cosine of the angle, and retried (wall sliding and corner assist). Movement is normalized to eight directions, and distance is computed in meters independently of zoom (initial walking speed about 1 km/min).

**日本語：** 開始地点は、車も通る地上の道路（例：400m以内）へ寄せ、孤立した歩行者道に降ろさない。保存位置から再開する場合は、保存した高さと同じ高さの道（例：30m以内）へ戻す。グラフを作り直した後は、同じ高さの近い線分へ付け直す。

**English:** The start position snaps to a ground-level road also used by vehicles (for example within 400 m), avoiding isolated pedestrian ways. When resuming, the player returns to a road on the saved level (for example within 30 m). After a rebuild, the player is reattached to a nearby segment on the same level.

### 6.3 橋の下の重なり表現（実装） / Under-bridge occlusion (implemented)

**日本語：** 道路・鉄道・歩道橋の橋の線分と幅、高さを保持する。キャラが橋より低い高さにいるとき、キャラの元画像を毎フレーム表示用キャンバスへ写し、プレイヤーより高い橋の形（幅を持つ線）をキャラのドット座標へ変換して消去合成（destination-out）で消す。これにより下の地図に描かれた橋がキャラの上に見え、橋の下をくぐる表現になる。左右反転の表示ではドット座標も反転させる。

**English:** Bridge segments for roads, railways, and footbridges are stored with width and level. When the character is on a lower level than a bridge, the source sprite is copied to the display canvas each frame, and the shapes of higher bridges (lines with width) are transformed into sprite-dot coordinates and erased with destination-out compositing. The bridge drawn in the underlying map then appears above the character, depicting passage beneath it. Coordinates are mirrored when the sprite is displayed flipped.

## 7. 入力・縮尺 / Input and scale

**日本語（実装）：** キーボード、ゲームパッド、ドラッグを共通の「向き・強さ・走る」に変換し、最も強い入力を採用する。ゲームパッドは標準配列を用い、左スティックに円形デッドゾーン（例：0.22）を設けて外側を0〜1に引き伸ばし、8方向にそろえる。ドラッグは押した位置に仮想スティックを表示し、一定距離（例：12px）以上動かすと移動を始め、離すと止まる。短く動かさずに離した操作はタップとして第8節の自動移動に渡す。フォーカス喪失時は押下状態を解除し、入力欄・設定画面の操作中は移動しない。

**English (implemented):** Keyboard, gamepad, and drag inputs are converted into a common direction, magnitude, and run flag, and the strongest input is used. Gamepads use the standard mapping; the left stick has a radial dead zone (for example 0.22), with the outer range rescaled to 0–1 and snapped to eight directions. Dragging shows a virtual stick at the press point, starts movement after a threshold (for example 12 px), and stops on release. A short press without movement is treated as a tap and passed to automatic movement in Section 8. Pressed states are cleared on focus loss, and movement is suspended while typing or while settings are open.

**日本語：** 位置はwebメルカトルのworld単位で保持し、表示倍率と物理距離を分離する。カメラ・ズーム・ドットの大きさの変更で距離や将来の燃料計算を変えない。v1.0に記載した32pxタイル換算の例は、本版ではベクタータイル描画（第9節）に置き換えた。

**English:** Position is held in Web Mercator world units, separating display scale from physical distance. Camera, zoom, or dot-size changes do not alter distance or future fuel calculations. The v1.0 example of 32-pixel tile scales is superseded by vector-tile rendering (Section 9).

## 8. 実施形態B：タップ移動・ZIP・NAVI / Embodiment B: tap-to-walk, ZIP, and NAVI

### 8.1 タップによる道なり自動移動（実装） / Tap-to-walk (implemented)

**日本語：** タップ位置から一定範囲（例：30m）の線分を目的地候補とし、(1) 現在の線分と同じ連結成分かつ同じ高さ、(2) 同じ連結成分、(3) いずれか、の順に最も近い線分を選ぶ。線分を節点とするA*で経路を求め、隣り合う線分の接点（端の重なり、交差点、または道幅の接触だけの場合は最も近い点）を通り道の点とする。最後に、経路上の線分の道幅内を一定間隔（例：1m）で確かめながら、現在地から最も遠い点までまっすぐ歩ける点を順に選んで途中の点を省く。追従中は第6.2節の移動を用い、一定時間（例：1秒）進めなければ理由を表示して止める。手動入力で中断する。

**English:** Segments within a radius of the tap (for example 30 m) are candidates, choosing the nearest in the order (1) same component and same level as the current segment, (2) same component, (3) any. A* over segments finds a route; waypoints are junction points between consecutive segments (coinciding ends, intersections, or the nearest point when widths merely touch). The route is then smoothed by repeatedly choosing the farthest waypoint reachable in a straight line whose samples (for example every 1 m) all stay within the corridors of the route segments. Following uses the movement of Section 6.2; if no progress is made for a period (for example 1 s), the reason is shown and movement stops. Manual input interrupts it.

### 8.2 ZIP・NAVI・ワープ（提案） / ZIP, NAVI, and warps (proposed)

**日本語：** v1.0の郵便番号処理（7桁文字列、住所候補、近似代表点、移動モードに合う道路への接続、距離上限と失敗表示）とNAVI（同一グラフでの経路探索、案内または自動移動、手動中断、燃料切れ等での停止・再探索）を維持する。郵便番号は日本の仕組みのため、海外では地名検索で代替する。ワープは、一度訪れた場所、招待を受けたフレンドの位置、旅の開始地点の選択に限る案とし、自由ワープによる旅・経済・行動範囲の拡張要素の弱体化を避ける。開始地点の選択（宇多津、浅草寺、京都駅、道後温泉、タイムズスクエア、エッフェル塔、タワーブリッジ）は実装済み。

**English:** The v1.0 postal-code processing (seven-digit strings, address candidates, approximate representative points, attachment to mode-appropriate roads, distance limits and failure reporting) and NAVI (route search on the same graph, guidance or automatic movement, manual interruption, stopping or replanning on fuel exhaustion) are retained. Because postal codes are a Japanese system, place-name search substitutes abroad. Warps are proposed to be limited to previously visited places, the location of an inviting friend, or the choice of a starting point, avoiding the weakening of travel, economy, and range-expansion elements by free warping. Choosing a starting point (Utazu, Senso-ji, Kyoto Station, Dogo Onsen, Times Square, Eiffel Tower, Tower Bridge) is implemented.

## 9. 実施形態E：実地図のドット絵描画（実装） / Embodiment E: pixel-art rendering of real maps (implemented)

**日本語：** ベクタータイルを独自の配色で、表示サイズの1/P（Pは1ドットのCSSピクセル数、例：3）の解像度で描き、拡大時に補間しない方式で整数倍に表示する。画面サイズはPの倍数にそろえ、カメラ中心をドットの格子にそろえてスクロール時のちらつきを抑える。

**English:** Vector tiles are drawn with an original palette at 1/P of the display resolution (P is CSS pixels per dot, for example 3) and shown at integer scale without interpolation. The viewport size is a multiple of P, and the camera center snaps to the dot grid to reduce shimmer while scrolling.

**日本語：** 描画のたびに地図の画素を読み出し、各画素をパレットの最も近い色（重み付きRGB距離）へ置き換えて別キャンバスに表示する（色の統一）。色ごとの結果をキャッシュする。縁の中間色が消え、輪郭がくっきりする。半透明は色が混ざるため使わず、トンネルは点線の縁と淡い色で表す。建物はIDから4色の屋根色を選ぶ（OpenMapTilesのIDは「OSMのID×10＋種類」のため10で割ってから分ける）。輪郭は1.5ドット幅とし、色の統一で消えないようにする。

**English:** After each render, map pixels are read back, replaced with the nearest palette color (weighted RGB distance), and shown on a separate canvas (palette quantization). Results are cached per color. Intermediate edge colors disappear and outlines become crisp. Transparency is avoided because it mixes colors; tunnels use dashed casings and pale fills. Buildings receive one of four roof colors from their ID (OpenMapTiles IDs are OSM ID × 10 plus type, so the ID is divided by 10 first). Outlines are 1.5 dots wide so quantization does not absorb them.

**日本語：** 水面は8×8ドットのさざ波模様を、地図の1ドット＝模様の1ドットになる画素比で登録し、一定間隔（例：0.7秒）で2つの模様を切り替える。水域の縁には帯を水面の上に描き、2色を交互に切り替えて波打ち際を表す。動きを減らす設定の端末では切り替えない。文字（地名・道路名・水域名）はドット絵化せず、通常解像度の文字専用地図を重ね、表示範囲に地名が入りやすい引いたズーム（例：z16以下）でだけ出す。

**English:** Water uses an 8×8-dot ripple pattern registered at a pixel ratio where one map dot equals one pattern dot, alternating between two patterns at an interval (for example 0.7 s). A band drawn over the water edge alternates between two colors to depict the shoreline. Devices requesting reduced motion receive no animation. Text (place, road, and water names) is not pixelated; a full-resolution text-only map is overlaid and shown only at wider zooms where names fall within view (for example z16 and below).

## 10. 実施形態F：絵文字キャラと施設アイコン（実装） / Embodiment F: emoji characters and facility icons (implemented)

**日本語：** どの端末でも同じ見た目にするため、使用する絵文字のSVG画像（Twemoji、CC BY 4.0）だけを同梱し、無い場合のみ端末の絵文字フォントで描く。ドット絵化は次の手順で行う：大きなキャンバス（例：192px）に描く → 透明でない範囲を測る → 輪郭1ドット分の余白を残してn×n（例：キャラ20、施設14）に縮めて足元を下端にそろえる → 不透明度のしきい値で半透明を切り捨てる → 隣接する透明ドットに濃い輪郭色を付ける。歩行中は1ドット上下させ、左右の向きは反転表示で表す。

**English:** To look identical on every device, only the SVG images (Twemoji, CC BY 4.0) of emoji actually used are bundled; the device emoji font is used only when an image is missing. Pixel conversion: draw on a large canvas (for example 192 px), measure the opaque bounding box, scale into n×n (for example 20 for the character, 14 for facilities) leaving a one-dot outline margin and aligning the feet to the bottom, discard semi-transparency by an alpha threshold, and color adjacent transparent dots with a dark outline. While walking, the sprite bobs by one dot; left/right facing uses mirroring.

**日本語：** 施設はOpenMapTilesのpoiのsubclass（無ければclass）から約40種の絵文字へ対応付け、タイル境界の重複を名前と位置でまとめる。アイコンはドットの格子にそろえて置き、プレイヤーが一定距離（例：45m）以内に近づくと名前を通常解像度の文字で出す。

**English:** Facilities are mapped from OpenMapTiles POI subclass (or class) to about 40 emoji, with tile-boundary duplicates merged by name and position. Icons are placed on the dot grid, and names are drawn in full-resolution text when the player comes within a distance (for example 45 m).

## 11. 実施形態C：地域・名前・経済 / Embodiment C: regions, names, and economy

### 11.1 地域の看板・道路名・多言語（実装） / Area signs, road names, and languages (implemented)

**日本語：** 現在地に最も近い細かい地域（丁目・町など）の点を選び、新しい地域が現在の地域より一定割合（例：20%）以上近いときだけ切り替えて、境界付近の往復による看板の連発を防ぐ。切り替え時に画面上部へ地域名の看板を出す。道路名は名前付き道路線から一定距離（例：10m）以内の最も近いものを情報欄に出す。名前は言語ごとに候補を順に探す：日本語はname:ja→name、ひらがなはname:ja-Hira→name:ja→name、英語はname:en→name_en→name:ja-Latn→name_int→name。UI文言は3言語を人が書いた辞書で持ち、漢字の読みを機械変換しない。

**English:** The nearest fine-grained area point (block, neighborhood) is selected, switching only when the new area is closer than the current one by a margin (for example 20%), preventing repeated signs at boundaries. A sign with the area name appears at the top of the screen when it changes. The nearest named road line within a distance (for example 10 m) is shown in the status panel. Names are chosen by language-specific fallback: Japanese name:ja→name; hiragana name:ja-Hira→name:ja→name; English name:en→name_en→name:ja-Latn→name_int→name. UI text is a human-written dictionary for three languages; kanji readings are not machine-converted.

### 11.2 経済（提案） / Economy (proposed)

**日本語：** v1.0の経済実施形態（RV、地域・店舗係数による価格、車種別の月額費用、燃料＝移動km÷燃費、二重徴収防止キー、価格版の固定）を維持する。加えて、魚の売値を「基準価格（魚種・サイズ・希少度）×鮮度係数×保冷係数（クーラーあり1.0、なし0.5）」で求める。釣りをしない旅でも、散歩距離・発見・イベントなどでRVを得られるようにする。数値は例示である。

**English:** The v1.0 economy embodiment (RV, prices from regional and store coefficients, vehicle-specific monthly costs, fuel = distance km ÷ efficiency, duplicate-billing keys, fixed price versions) is retained. Fish sale price is computed as base price (species, size, rarity) × freshness factor × cooling factor (1.0 with a cooler, 0.5 without). RV can also be earned without fishing, through walking distance, discoveries, and events. Values are illustrative.

## 12. 実施形態G：釣り（提案） / Embodiment G: fishing (proposed)

**日本語：** 釣りは当初伏せておき、地図上の実在の釣具屋（例：shop=fishing）へゲーム内で行って簡単な竿を買うと解禁する。一定数（例：10匹）釣ると、店に本格的な釣具（竿・リール・ライン・針・ルアー・エサ・オモリ・クーラー等）が並び、ランキングも解禁する。釣りの内容は所持装備の種類と組合せで決め、モード切替スイッチに依存しない。

**English:** Fishing is hidden at first and unlocked by visiting a real tackle shop on the map (for example shop=fishing) in-game and buying a simple rod. After a number of catches (for example 10), advanced tackle (rods, reels, lines, hooks, lures, bait, sinkers, coolers) appears in shops and rankings unlock. Fishing behavior is determined by the types and combination of equipment held, not by a mode switch.

**日本語：** 水辺の判定は、海（海岸線から作った面）、湖・池（natural=water）、川・水路（waterway）、港（harbour等）を種類付きで取得し、プレイヤーが水域に面しているか一定距離内かで行う。釣果表は、地域ごとにFishBaseの分類・分布とGBIFの観測記録を取得・検証・キャッシュした候補表から、水域の種類・時刻・季節で選ぶ。観測記録は「今その場所で釣れる確率」ではなく、記録が無い地域を「魚がいない」としない。捕獲が規制される生き物は「発見・観察」として扱う。鮮度は時間で下がり、クーラーで保持する。

**English:** Waterside detection obtains typed water bodies — sea (polygons built from coastlines), lakes and ponds (natural=water), rivers and channels (waterway), harbors — and checks whether the player faces or is within a distance of one. Catch tables are selected by water type, time, and season from per-region candidate tables built by fetching, validating, and caching FishBase taxonomy/distribution and GBIF occurrence records. Occurrence records are not a probability of catching fish now, and an absence of records does not mean no fish. Protected species are handled as sightings rather than catches. Freshness decreases over time and is preserved by coolers.

## 13. 実施形態H：ひとり旅・マルチ・待ち合わせ・旅程・追体験（提案） / Embodiment H: solo, multiplayer, meeting, planning, and replay (proposed)

**日本語：** ひとり旅（1P）では他者を表示せず、位置配信・在席情報からも除外する。マルチでは、近くのプレイヤーの位置だけを地域単位で毎秒数回（例：5〜10回）送り、間を補間する。待ち合わせは、郵便番号・施設・待ち合わせコードで合流地点を指定し、フレンド限定とする（自宅の郵便番号は住む地域を相手に伝えるため、施設やコードも用意する）。知人限定の会話は頭上の吹き出し1個で、返信まで残り、終了で消え、本文をサーバーに保存しない。

**English:** In solo play (1P), others are not displayed and the player is excluded from position broadcasts and presence. In multiplayer, only nearby players' positions are sent per area several times per second (for example 5–10) and interpolated. Meetings specify a point by postal code, facility, or meeting code, limited to friends (facilities and codes are offered because a home postal code reveals one's neighborhood). Acquaintance-only conversation uses one speech bubble above each character that remains until a reply, disappears when ended, and is not stored on servers.

**日本語：** 旅程づくりでは、目的地・寄り道・経路をゲーム内で計画する。追体験では、ゲーム内の移動記録・旅程・端末の位置履歴（GPX等）を、ブラウザ内で処理してサーバーへ送らずに読み込み、行った道をゲーム内でたどる。鉄道・フェリー・高速道路など歩けない区間は乗り物演出で区間移動するか、道路へ寄せる（マップマッチング）か、通行判定を外した再生とする。当日の時刻・天気の再現には過去天気データを使う。

**English:** Trip planning lets the player plan destinations, detours, and routes in-game. Replay loads in-game travel logs, plans, or device location history (such as GPX), processed in the browser without sending it to servers, and retraces the route in-game. Non-walkable sections such as railways, ferries, and expressways are traversed with vehicle presentation, snapped to roads (map matching), or replayed without traversal checks. Historical weather data reproduces the day's time and weather.

## 14. 実施形態D：時間・天気・テーマ（提案、一部実装） / Embodiment D: time, weather, and themes (proposed, partly implemented)

**日本語：** v1.0の内容（時刻源から時刻帯と季節、演出時計と徴収時計の分離、天気セルとキャッシュ、障害時のプリセット、見た目の切替で道路接続を変えない）を維持する。水面と波打ち際の動き（第9節）は実装済み。

**English:** The v1.0 content (time-of-day and season from a time source, separate presentation and billing clocks, weather cells with caching, presets on failure, appearance changes never altering connectivity) is retained. Water and shoreline animation (Section 9) is implemented.

## 15. 実施形態J：起動・保存・配信と検証結果 / Embodiment J: startup, persistence, delivery, and verification results

### 15.1 起動失敗の案内・保存・配信（実装） / Boot guard, persistence, and delivery (implemented)

**日本語：** HTMLにゲーム本体のJS・CSSに依存しない案内画面と小さなスクリプトを置く。起動完了の印が立つ前に、スクリプト・スタイルの読み込み失敗、実行時エラー、未処理のPromise拒否、または一定時間（例：20秒）の未起動を検知したら案内と再読み込みボタンを出す。自動再読み込みはしない。WebGLが使えない端末では理由を出す。起動後の地図タイルの失敗はゲーム内で間隔を空けて知らせる。

**English:** The HTML contains a notice and a small script independent of the game's JS and CSS. Before a boot-complete flag is set, failure to load scripts or styles, runtime errors, unhandled promise rejections, or no boot within a time limit (for example 20 s) shows the notice with a reload button. No automatic reload is performed. Devices without WebGL receive a reason. Tile failures after boot are reported in-game at limited intervals.

**日本語：** 位置・高さ・表示設定・言語を版付きで端末に保存し（数秒ごと、設定変更時、画面を離れる時）、読み込み時に値を検証する。読めない保存は消さずに無視して知らせる。配信では、版ごとのフォルダへ全ファイルを送り終えてから公開中を指すリンクを一度に切り替え、ハッシュ付きファイルは旧版の分も残す。これにより、更新の瞬間に開いていた画面が旧ファイルを読めずに白画面になることを防ぎ、旧版へ即座に戻せる。HTMLは毎回更新を確認し、ハッシュ付きファイルは長期キャッシュとする。

**English:** Position, level, display settings, and language are saved locally with a version (every few seconds, on settings changes, and when leaving the page) and validated on load. Unreadable saves are ignored and reported, not deleted. For delivery, all files are uploaded to a per-release folder before the link pointing to the live release is switched at once, and hashed files from earlier releases are retained. This prevents pages open during an update from failing to load old files and enables immediate rollback. HTML is revalidated on every load; hashed files are cached long-term.

### 15.2 検証結果 / Verification results

**日本語：** 以下は2026-10-11〜16、香川県宇多津町周辺（一部ニューヨーク）、開発用デスクトップのブラウザで測定した結果であり、他の地域・端末・データ版での性能や正確性を保証しない。

**English:** The following were measured from 2026-10-11 to 2026-10-16 around Utazu, Kagawa (partly New York) in a desktop development browser; they do not guarantee performance or accuracy in other regions, devices, or data versions.

| 検証 / Check | 結果 / Result |
|---|---|
| 道路グラフの規模と構築時間 / Graph size and build time | 周囲約2km・約9,200線分・約43ms / about 9,200 segments around 2 km, about 43 ms |
| 連結性 / Connectivity | 道路約337kmのうち99%以上が一つの連結成分 / over 99% of about 337 km of roads in one component |
| 立体交差 / Grade separation | 車道の橋の上・橋の下の歩道・地下道の上で8方向に移動し、別の高さへの移行は24回中0回（道幅の接触による接続の追加後も0回） / Moving in eight directions on a road bridge, on a path beneath it, and above an underpass produced 0 of 24 level changes (still 0 after adding width-contact links) |
| 別線の歩道・入口 / Separately drawn sidewalks | 国道脇の行き止まり3か所で、遠回り（例：直線5.3mに対し16.5m、5.6mに対し26.6m）が直線と同じ距離に改善 / At three dead ends beside a national road, detours (e.g., 16.5 m for a 5.3 m gap, 26.6 m for 5.6 m) became straight-line distances |
| タップ移動 / Tap-to-walk | 60〜150m先への5回すべて到着 / all 5 trials to 60–150 m arrived |
| 色の統一 / Palette quantization | 画面の色数761→13、歩行中約120fps / colors on screen 761→13; about 120 fps while walking |
| 橋の下の表現 / Under-bridge occlusion | 橋の下でキャラ122ドット中64ドットのみ表示、橋の上で全表示 / 64 of 122 sprite dots visible beneath a bridge; all visible on it |
| 世界での開始 / Worldwide start | タイムズスクエアから約2秒で道に立つ / standing on a road at Times Square in about 2 s |
| 再開 / Resume | 再読み込み後、保存位置から約2m、言語も保持 / after reload, within about 2 m of the saved position, language retained |
| 壊れた保存 / Broken save | 消さずに無視し、タイトル画面で案内 / ignored without deletion, with a notice on the title screen |

## 16. 参照実装・工程・残る決定 / Reference implementation, stages, and open decisions

**日本語：** 参照実装はVite 8.3.4、JavaScript（フレームワークなし）、MapLibre GL JS 6.13.0、OpenFreeMapのベクタータイル（OpenMapTilesスキーマ）、Twemoji 17.0.3。v1.0で選択していたPixi.jsのタイル試作は旧試作として残し、本実装では使わない。Viteの依存の事前バンドルではMapLibreがWorkerの場所を見失うため、Worker URLを明示する。2026-10-16に実機確認版を https://roadvania.tarp.tokyo に公開した（検索エンジンに載せない設定）。

**English:** The reference implementation uses Vite 8.3.4, JavaScript without a framework, MapLibre GL JS 6.13.0, OpenFreeMap vector tiles (OpenMapTiles schema), and Twemoji 17.0.3. The Pixi.js tile prototype selected in v1.0 is kept as a legacy prototype and not used. Because Vite's dependency pre-bundling makes MapLibre lose its worker location, the worker URL is set explicitly. A device-test build was published at https://roadvania.tarp.tokyo on 2026-10-16 (excluded from search engines).

| 段階 / Stage | 内容 / Work | 状態 / Status |
|---|---|---|
| 試作C / Prototype C | ドット絵風の実地図、絵文字キャラ、道路限定移動 / Pixel-art real map, emoji character, road-constrained movement | 完了 / Done |
| M1 歩く土台 / Walking foundation | 道路グラフ・立体交差、入力 / Road graph, grade separation, input | 完了（パッド実機は未確認） / Done (physical gamepads unverified) |
| M2 見た目 / Visuals | 色の統一、施設、看板、Twemoji、水辺、橋の下 / Quantization, facilities, signs, Twemoji, water, under-bridge | 完了 / Done |
| M3 散歩MVP / Walking MVP | タイトル、保存、言語、設定、起動失敗の案内 / Title, save, languages, settings, boot guard | 完了 / Done |
| M4〜M9 | 旅の便利機能、ライト釣り、釣り・経済、マルチ・交流、時間・天気・追体験、運営 / Travel tools, light fishing, fishing and economy, multiplayer, time/weather/replay, operations | 未着手 / Not started |

**日本語：** 未決事項：地図タイルの自前配信と障害時の代替、ワープの制限方式、検索・郵便番号・経路の提供元、位置情報と位置履歴のプライバシー設計、釣りの判定距離と釣果表、FishBaseの利用許諾とGBIFのライセンス選別、売値・鮮度・RV収入、車の課金時計、マルチの同期設計とVPSの負荷試験、時刻源と天気の提供元、店舗コラボの発火条件、UIフレームワーク、端末間の引き継ぎ。技術例の記載は製品採用の決定ではない。

**English:** Open decisions: self-hosted tiles and failover, warp restrictions, providers for search, postal codes, and routing, privacy design for location and location history, fishing detection distance and catch tables, FishBase permission and GBIF license filtering, prices, freshness, and RV income, the vehicle billing clock, multiplayer synchronization and VPS load testing, time source and weather provider, store-collaboration triggers, UI framework, and cross-device transfer. Describing an embodiment does not adopt it as product policy.

## 17. データ・権利・運用の限界と公開記録 / Limitations and publication record

**日本語：** OSMの道路・施設・名前・通行条件・橋やトンネル・水域の情報には欠損・誤り・更新遅れがあり、ベクタータイルは最大ズームで形状が簡略化される。地図での疑似散歩は通行の安全・営業状況・私有地の通行許可を保証しない。OSMはODbL、OpenMapTilesのスキーマとスタイル由来部分には各ライセンス、OpenFreeMapの公開インスタンスには帰属表示（「OpenFreeMap © OpenMapTiles Data from OpenStreetMap」）が必要で、稼働保証はない。Twemojiのグラフィックは CC BY 4.0 で、画面に帰属を表示する。MapLibre GL JS・Vite等のソフトウェアは各ライセンスに従う。公開Nominatim等の無制限利用を前提にしない。FishBase・GBIFは用途・データごとの条件を確認する。地図中の名称は店舗ロゴ・商品画像の使用許諾ではない。「DP風」「Stardew Valley並み」は表現の参照であり、既存作品の名称・素材の使用を意味しない。[OSM copyright](https://www.openstreetmap.org/copyright)、[Tile policy](https://operations.osmfoundation.org/policies/tiles/)、[OpenFreeMap](https://openfreemap.org/)、[Twemoji](https://github.com/jdecked/twemoji)

**English:** OSM roads, facilities, names, access rules, bridge/tunnel and water data may be missing, incorrect, or outdated, and vector tiles simplify geometry at their maximum zoom. Virtual walking does not guarantee safe passage, business status, or permission to cross private land. OSM is under ODbL; OpenMapTiles schema- and style-derived parts carry their own licenses; OpenFreeMap's public instance requires attribution ("OpenFreeMap © OpenMapTiles Data from OpenStreetMap") and offers no service-level guarantee. Twemoji graphics are CC BY 4.0, with attribution shown on screen. MapLibre GL JS, Vite, and other software follow their licenses. Unlimited use of public Nominatim or similar services is not assumed. FishBase and GBIF terms are checked per use and dataset. Names in map data do not license store logos or product images. "Diamond/Pearl-like" and "as smooth as Stardew Valley" are stylistic references and imply no use of those works' names or assets. [OSM copyright](https://www.openstreetmap.org/copyright), [Tile policy](https://operations.osmfoundation.org/policies/tiles/), [OpenFreeMap](https://openfreemap.org/), [Twemoji](https://github.com/jdecked/twemoji)

**日本語：** 公開する場合は、ログイン不要で公衆が読める場所に本版を掲載し、実際の公開日時、永続URLまたはコミットID、公開版のSHA-256を記録する。SHA-256は内容の同一性確認に使えるが、単独では公開日時の証明にならない。旧公開版はリポジトリの履歴で保持する。本書の作成によって外部公開は行われていない。

**English:** When published, this edition is posted at a publicly readable location without login, recording the actual release time, persistent URL or commit ID, and SHA-256 of the released file. SHA-256 checks content identity but does not alone prove publication time. Prior public versions are preserved in the repository history. Preparing this file has not published it externally.

**日本語：** 本版は2026-10-16 15:55:14 JSTに上記リポジトリへ掲載した（commit d69356e）。下表への公開記録の追記は、その後の別コミットで行った。そのため現在のファイルのSHA-256は、表に記したcommit d69356e時点の値と一致しない。

**English:** This edition was posted to the repository above at 2026-10-16 15:55:14 JST (commit d69356e). The release record below was added in a later, separate commit, so the SHA-256 of the current file does not match the value recorded for commit d69356e.

| 記録項目 / Record field | v1.0 | v2.0 |
|---|---|---|
| 公開日時 / Public release time | 2026-10-09 19:26:53 JST 初回掲載、19:30:12 JST 現行版 / first posted 19:26:53 JST, current text 19:30:12 JST | 2026-10-16 15:55:14 JST |
| コミット / Commit | e092574fba811bf5060d82154bcd1e9630efc25c（初回）、3d0177d5bb7f86e36438c182122c89a0646c3605（現行） / (first), (current) | d69356e9385db7e42c445ea8db5939f98c5315f0 |
| 公開URL / Public URL | https://github.com/tarp-tokyo/spec/blob/3d0177d5bb7f86e36438c182122c89a0646c3605/roadvania.md | https://github.com/tarp-tokyo/spec/blob/d69356e9385db7e42c445ea8db5939f98c5315f0/roadvania.md |
| SHA-256 | c39f524b0d3dfac77b91e9ef38fc3e7694072a8ba3f25542cd47dfa5fa52518d（現行v1.0本文 / current v1.0 text） | 563edaac84fd2f980823cd0ff2f46774858c7ef03fd2b1ec68c4205a326829a3（commit d69356e時点の本文 / text as of commit d69356e） |
| 公開者 / Publisher | tarp.tokyo | tarp.tokyo |
| 文書ライセンス / Document license | TARP Tools License | TARP Tools License |

## 18. ライセンスと知的財産 / License and intellectual property

**日本語：** 本仕様書およびRoadvaniaプロジェクトの独自部分には、漢字世界と同じTARP Tools Licenseを指定する。無断複製・改変・再配布・逆コンパイルを禁止する方針とし、具体的な適用条件は[利用規約](https://tools.tarp.tokyo/terms/)を参照する。OSM、OpenMapTiles、OpenFreeMap、MapLibre GL JS、Twemoji、Viteその他の第三者データ・ソフトウェア・素材にはそれぞれのライセンスが適用され、本指定はそれらの条件を上書きしない。規約リンク先の本文は本改訂で全文確認していない。

**English:** This specification and the original portions of the Roadvania project are designated under the TARP Tools License, matching Kanji the World. The stated policy prohibits unauthorized copying, modification, redistribution, and decompilation; consult the [terms](https://tools.tarp.tokyo/terms/) for applicable conditions. OSM, OpenMapTiles, OpenFreeMap, MapLibre GL JS, Twemoji, Vite, and other third-party data, software, and assets remain subject to their respective licenses; this designation does not override those terms. The full text of the linked terms was not re-verified for this revision.

## 19. 変更履歴 / Revision history

| Version | Preparation date | Changes / 変更 |
|---|---|---|
| 1.0 | 2026-10-09 | 日英併記の防衛公開版。移動、ZIP/NAVI、地域経済、時間・天気の提案実施形態と制約 / Bilingual defensive publication; proposed movement, ZIP/NAVI, regional economy, time/weather embodiments and limitations |
| 2.0 | 2026-10-16 | 試作実装と検証を反映。追加：ベクタータイルのドット絵描画と色の統一（E）、絵文字のドット絵化と施設アイコン（F）、最大ズームの非表示地図からの道路グラフ構築・接続規則・道幅接触の接続・連結成分（A）、道幅内移動と橋の下の重なり表現（A）、入力統合とタップ移動の経路短縮（B）、地域の看板と多言語の名前選択（C）、釣り（G）、ひとり旅・マルチ・待ち合わせ・旅程・追体験（H）、起動失敗の案内・版付き保存・版切替配信（J）、検証結果、v1.0の公開記録。Pixi.jsのタイル方式からMapLibreのベクタータイル方式へ変更 / Reflects prototype implementation and verification. Adds pixel-art vector rendering with palette quantization (E), emoji pixel sprites and facility icons (F), road graph from a hidden max-zoom map with linking rules, width-contact links, and components (A), corridor movement and under-bridge occlusion (A), input integration and smoothed tap-to-walk (B), area signs and multilingual names (C), fishing (G), solo/multiplayer, meeting, planning, and replay (H), boot guard, versioned saves, and release switching (J), verification results, and the v1.0 release record. Moves from a Pixi.js tile approach to MapLibre vector tiles |
