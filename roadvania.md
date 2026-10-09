# Roadvania — 技術開示書 / Technical Disclosure

**実地図の道路接続に基づく探索ゲームと地域・環境連動システム**  
**A Real-Map Exploration Game Based on Road Connectivity and Regional Environmental State**

| 項目 / Field | 内容 / Value |
|---|---|
| 文書ID / Document ID | ROADVANIA-TD-001 |
| 版 / Version | 1.0 — 防衛公開版（公開待ち） / Defensive publication edition (pending release) |
| 文書作成日 / Preparation date | 2026-10-09, Asia/Tokyo |
| 著者・公開者 / Author and publisher | tarp.tokyo |
| 初回公開日（予定） / Initial publication date (scheduled) | 2026-10-09（日本時間 / Japan time） |
| 文書ライセンス / Document license | TARP Tools License — https://tools.tarp.tokyo/terms/ |
| 実際の公開日時 / Actual public release time | 未記録：公開時に記入 / Not recorded; to be entered upon publication |
| 公開URL・永続識別子 / Public URL or persistent identifier | 未設定 / Not assigned |
| 実装状態 / Implementation status | 設計・提案段階。実装完了や実測結果の主張なし / Design and proposal stage; no claim of completed implementation or measured results |

## 1. 目的・範囲 / Purpose and scope

**日本語：** 本書はRoadvaniaの技術構想と具体的な実施形態を、防衛公開に使用できる形で記録する。実地図の道路ネットワーク上で徒歩・車による探索を行い、郵便番号による目的地指定、経路案内・自動移動、地域経済、イベント、現実時間・天気、表示テーマを連携させる構成を開示する。本書自体のローカル作成は、公衆への公開を意味しない。作成日は優先日・発明日・公開日の証明ではない。

**English:** This document records the Roadvania technical concept and concrete embodiments in a form intended for defensive publication. It discloses exploration by walking or driving on a real-world road network, integrated with postal-code destination selection, route guidance and automatic movement, regional economy, events, real-world time and weather, and visual themes. Creating this document locally does not make it publicly available. Its preparation date is not evidence of a priority date, invention date, or publication date.

**日本語：** 防衛公開は技術内容を公衆に利用可能とすることを目的とするが、先行技術としての評価は公開時期・内容・法域・対象請求項などに依存する。本書は新規性、特許性、非侵害、他者による特許取得の全面的阻止を保証しない。公開は自己の将来の特許出願にも影響し得る。公開と、著作権・ソフトウェア・素材の利用許諾は別であり、本書の利用条件は第14節のTARP Tools Licenseに従い、第三者素材への許諾や包括的な権利放棄を意味しない。[WIPO Patent FAQ](https://www.wipo.int/en/web/patents/faq_patents)、[JPO 新規性・進歩性](https://www.jpo.go.jp/system/laws/rule/guideline/patent/tukujitu_kijun/ht/03_0200.html)

**English:** Defensive publication aims to make technical information available to the public, but its treatment as prior art depends on timing, disclosure content, jurisdiction, and the claims being evaluated. This document does not guarantee novelty, patentability, non-infringement, or prevention of all later patents. Publication may also affect the originator's future patent applications. Public disclosure is distinct from copyright, software, and asset licensing; use of this document is governed by the TARP Tools License in Section 14 and grants no permission for third-party assets or blanket waiver of rights. [WIPO Patent FAQ](https://www.wipo.int/en/web/patents/faq_patents), [JPO novelty and inventive-step guidelines](https://www.jpo.go.jp/system/laws/rule/guideline/patent/tukujitu_kijun/ht/03_0200.html)

## 2. 由来と状態区分 / Provenance and status categories

**日本語：** 本書は「Roadvania 統合仕様書」v1.0と取得可能な「Roadvania 構想」の会話、およびセウが明示した要件に基づく。過去会話の全履歴は取得できていない。本書で具体化したデータ構造・処理順序・例示値は提案実施形態であり、過去にすべて考案・承認・実装されていたとの主張ではない。

**English:** This document is based on version 1.0 of the Roadvania integrated specification, the available Roadvania concept conversation, and requirements explicitly stated by Seu. The complete earlier conversation was not available. Data structures, processing sequences, and example values developed here are proposed embodiments, not assertions that every detail had previously been conceived, approved, or implemented.

| 状態 / Status | 内容 / Content |
|---|---|
| 選択済み方針 / Selected direction | Vite vanilla JavaScript + Pixi.js。道限定の自由移動と実地図。オリジナル通貨RV。車種別維持費と地域価格を構想 / Vite vanilla JavaScript + Pixi.js; road-constrained free movement on real maps; original RV currency; vehicle-dependent maintenance and regional prices |
| 構想要件 / Concept requirements | 車・徒歩、ゲームパッド、イベント、ZIP/NAVI、時間・天気、スキン、管理画面 / Driving and walking, gamepad, events, ZIP/NAVI, time and weather, skins, administration |
| 提案実施形態 / Proposed embodiments | 以下に記載する具体的な処理、構造、計算、代替方式 / The concrete procedures, structures, calculations, and alternatives below |
| 未決 / Open decisions | 対象地域、最終操作感、供給元、価格、時計、規制反映、公開先・ライセンス / Initial region, final movement feel, providers, prices, clock authority, regulation coverage, publication venue and licenses |

## 3. 技術分野・課題・概要 / Technical field, problems, and abstract

**日本語：** 技術分野はブラウザ地図ゲーム、道路グラフ処理、入力制御、経路探索、地理状態に応じたゲーム処理である。二次元地図をそのままゲーム化すると、高架と地上道路の誤接続、道路外にある郵便番号代表点、表示ズームによる距離・燃料計算の変化、地域境界でのイベント連発、天気API障害への依存などが起こり得る。

**English:** The technical field includes browser-based map games, road-graph processing, input control, route search, and geographically conditioned game processing. Direct conversion of a two-dimensional map into a game may create false connections between elevated and ground-level roads, postal-code points outside traversable roads, zoom-dependent distance or fuel calculations, repeated boundary events, and dependence on weather API availability.

**日本語：** 提案構成では、地理位置・道路接続・移動状態を表示から独立して保持する。移動と経路探索は同一の通行可能道路モデルに従う。確定した道路移動から距離・地域変化・イベントを生成し、地域価格、時刻、キャッシュされた天候、テーマを参照してゲーム状態と表示を更新する。これらは期待する設計上の効果であり、実測済みの改善結果ではない。

**English:** The proposed arrangement stores geographic position, road connectivity, and movement state independently of presentation. Manual movement and route search follow a common traversable-road model. Committed road movement produces distance, region transitions, and events; regional prices, time, cached weather, and themes are then used to update game state and presentation. These are intended design effects, not measured improvements.

## 4. システム構成 / System architecture

**日本語：** クライアントは入力正規化、移動制御、カメラ、Pixi.js描画、HUD、ZIP/NAVI操作、任意のローカル保存を担当する。地図前処理は道路・行政境界・施設をゲーム用データへ変換する。任意のサーバーは天気代理取得、共有設定、価格管理、経路処理、保存を担当する。固定データのみの初期MVPではサーバーを必須としない。

**English:** The client handles input normalization, movement, camera control, Pixi.js rendering, HUD, ZIP/NAVI interaction, and optional local storage. Map preprocessing converts roads, administrative boundaries, and facilities into game data. An optional server provides weather proxying, shared configuration, price administration, routing, and persistence. A fixed-data initial MVP does not require a server.

```text
Map sources -> preprocessing -> road graph + regions + landmarks
Keyboard / gamepad -> normalized input -> movement controller
ZIP -> address candidates -> approximate point -> road attachment -> NAVI
Movement controller + road graph -> committed position and distance
Committed movement -> region/event/economy updates -> game state
Clock + weather cache + theme settings -> presentation / optional motion modifiers
Game state -> Pixi.js world layers + HTML HUD -> optional persistence
Admin interface -> versioned configuration -> validated state updates
```

## 5. データモデル / Data model

**日本語：** 以下はJSONまたはDBへ格納する一実施形態。座標参照系、単位、取得元、データ版を明示する。地図原本と独自の補正・イベントを別管理にして、地図更新時に独自編集を保持する。

**English:** The following is an embodiment stored as JSON or database records. Coordinate reference system, units, sources, and data versions are explicit. Original map data is separated from local corrections and events so that updates can preserve local edits.

| Record | Fields / フィールド |
|---|---|
| RoadNode | id, geographicPosition, adjacency |
| RoadEdge | id, fromNode, toNode, polyline, lengthM, name, ref, allowedModes, direction, bridge, tunnel, layer, sourceVersion |
| TurnRule | incomingEdge, outgoingEdge, mode, permitted, condition |
| PlayerState | edgeId, offsetM, lateralOffsetM(optional), mode, heading, RV, fuelL, vehicleId, inventory, saveVersion |
| Region | id, boundaryGeometry, priority, pricePolicyId |
| Landmark | id, geographicPosition, entranceEdgeId, entranceOffsetM, displayName, category, eventIds |
| Vehicle | id, purchasePriceRV, efficiencyKmPerL, capacityL, speedLimitMps, monthlyCostRV |
| EconomyPolicy | regionId, itemMultipliers, fuelPriceRVPerL, effectiveAt, version |
| WeatherCell | cellId, condition, observationTime, fetchedAt, expiresAt, provider, status |
| Event | id, trigger, conditions, repeatPolicy, cooldown, effect |
| WorldConfiguration | version, themeSelection, seasonMode, eventOverride, clockPolicy |

**日本語：** OSMの`layer`は相対的な上下関係で、高度メートルではない。道路IDと接続関係が移動の根拠であり、描画上の上下順だけで道路を接続しない。

**English:** OSM `layer` describes relative vertical ordering, not altitude in meters. Road identifiers and connectivity govern movement; visual stacking alone does not establish road connections.

## 6. 実施形態A：道限定移動 / Embodiment A: road-constrained movement

**日本語：** 道路グラフ上の方式ではプレイヤーを道路辺IDと辺上距離で表す。入力方向から現在道路の前進・後退または接続先を選ぶ。速度と経過時間から移動距離を求め、辺の端まで進め、余りの距離を通行可能な接続辺に適用する。候補がなければ端で止まる。二次元で交差するだけの別道路へスナップしない。

**English:** In a graph-based embodiment, a player is represented by a road-edge identifier and distance along that edge. Input direction selects forward/backward travel or an outgoing connected edge. Speed and elapsed time determine travel distance. Movement proceeds to an edge endpoint, with remaining distance applied to a permitted outgoing edge. If none is available, movement stops at the endpoint. Mere two-dimensional overlap does not cause snapping to another road.

```text
advance(player, input, dt):
  remainingM = speed(player.mode, environment) * dt
  while remainingM > 0:
    segment = travelOnCurrentEdge(player, remainingM)
    commit(segment)
    remainingM -= segment.distanceM
    if endpointReached:
      candidates = connectedEdges(currentEndpoint)
      candidates = filterByModeDirectionAndTurnRules(candidates, player)
      nextEdge = selectByInputOrRoute(candidates, input)
      if nextEdge is absent: stop
      else: enter(nextEdge)
  emitCommittedTravelSegments()
```

**日本語：** 実装では時間差分の上限やループ回数上限を設け、長い停止後の飛び越しや不正なゼロ長辺での無限反復を防ぐ。未検証の接続は補正・除外対象とする。徒歩と車で通行条件を分け、描画層と衝突・接続モデルは独立させる。

**English:** An implementation limits elapsed time or iteration count to prevent large jumps after a pause and infinite traversal of invalid zero-length edges. Unverified connections are corrected or excluded. Walking and driving use different access conditions, while rendering layers remain separate from collision and connectivity models.

**日本語：** 代替方式は、道路幅を持つ通行領域内の連続移動である。この場合も現在道路や階層を保持し、接続領域を介する遷移だけを許可する。試作用タイル方式では候補セルがroadか確認してから移動する。これらは代替実施形態であり、最終操作方式は未決。

**English:** An alternative uses continuous movement within road-width corridors. It still retains road or level identity and permits transitions only through connected regions. A prototype tile embodiment checks that a destination cell is a road before committing movement. These are alternatives; the final control model remains open.

## 7. 入力・車と徒歩・縮尺 / Input, driving and walking, and scale

**日本語：** キーボードとゲームパッドを同じ移動ベクトル・操作アクションへ変換する。ゲームパッドにはデッドゾーンと接続解除処理を設ける。斜め入力を正規化し、入力欄・メニュー・フォーカス喪失時には移動入力を解除する。車と徒歩で速度、通行道路、燃料使用を切り替える。乗降は許可された地点で行う案とする。

**English:** Keyboard and gamepad signals are converted into common motion vectors and actions. Gamepad processing includes a dead zone and disconnection handling. Diagonal input is normalized, and movement is cleared during text entry, menus, or focus loss. Driving and walking change speed, eligible roads, and fuel use. Boarding or leaving a vehicle at designated positions is a proposed option.

**日本語：** 地理座標またはメートル系の世界位置を保持し、表示倍率と物理距離を分離する。カメラズームや徒歩・車の表示切替で走行距離や燃料消費を変えない。32pxゲームタイルについて、緯度34度・256px地図タイル基準のWebメルカトルではz19約7.9m、z18約15.8mという例があるが、固定縮尺ではなく採用未決である。

**English:** Geographic or metric world position is retained independently of display scale. Camera zoom or walking/driving display changes do not alter traveled distance or fuel consumption. Example scales for a 32-pixel game tile at latitude 34 degrees, using a 256-pixel Web Mercator map-tile convention, are approximately 7.9 m at z19 and 15.8 m at z18. They are illustrative and not adopted fixed scales.

## 8. 実施形態B：ZIPからNAVIへ / Embodiment B: ZIP-to-NAVI processing

**日本語：** ZIPは郵便番号に基づく目的地選択機能として具体化する。7桁番号を文字列で保持し、先頭の0を失わない。日本郵便の住所対応データと、別途条件を確認したジオコーディングデータから候補を求める。郵便番号は道路上の一点や建物入口を保証せず、複数候補や近似代表点を扱う。

**English:** ZIP is embodied as postal-code-based destination selection. A seven-digit code is stored as a string to preserve leading zeros. Address candidates are obtained from postal address data and separately licensed geocoding data. A postal code does not guarantee a precise road point or building entrance; multiple candidates and approximate representative points are supported.

**日本語：** 選択した代表位置の近傍から、移動モードで通行可能な道路上の候補点を求める。各候補について距離、到達可能性、既知の階層・入口条件を確認する。設定距離上限を超える、階層が曖昧、到達不能の場合は黙って遠方へ移さず候補再選択または失敗表示とする。妥当な接続先を利用者に示して確定し、近似位置であることを表示する。

**English:** Candidate attachment points are generated on nearby roads traversable in the selected mode. Distance, reachability, and known level or entrance constraints are checked for each candidate. If a configured distance limit is exceeded, level identity is ambiguous, or no route exists, the system requests another selection or reports failure rather than silently relocating the target far away. A suitable attachment is shown for confirmation and labeled approximate.

**日本語：** NAVIは同じ道路グラフからA*などで経路を求める。歩行・車に応じた重み・通行条件・方向規制を適用する。案内のみ、または道路辺列に沿う自動移動を選べる実施形態とする。自動移動は手動入力・キャンセルで中断し、燃料切れ・無効経路・モード変更で停止または再探索する。ZIPによる瞬間移動は別案であり必須要素ではない。

**English:** NAVI searches the same road graph, for example using A*. It applies mode-dependent costs, access constraints, and direction rules. An embodiment provides guidance alone or automatic movement along the resulting edge sequence. Manual input or cancellation interrupts automatic movement; fuel exhaustion, invalid routes, or mode changes trigger stopping or replanning. ZIP-based teleportation is a separate optional concept, not a required element.

## 9. 実施形態C：地域イベントと経済 / Embodiment C: regional events and economy

**日本語：** 確定した移動区間を行政境界・施設入口と照合して地域変化や発見を生成する。道路名・番号は道路辺変更で更新する。長い1フレーム移動でも境界通過を見落とさないよう、始点と終点だけでなく移動区間を評価する。イベントIDごとの発火履歴、クールダウン、境界ヒステリシスなどにより境界付近の連発を抑制する案を開示する。

**English:** Committed travel segments are evaluated against administrative boundaries and facility entrances to produce region changes or discoveries. Road names and route references update when the road edge changes. Segments, rather than endpoints alone, are evaluated to detect crossings during a long frame. Proposed suppression mechanisms include per-event histories, cooldowns, and boundary hysteresis.

**日本語：** RVを独自通貨として保持し、地域と店舗の係数から商品価格を求める。地方は安いが店舗が少なく、都市は高いが品物が豊富というゲーム上のバランスを設定できる。車購入後は車種別の月額税・維持費を持ち、燃料代を別計算する。現実の地域価格の再現やRV換金はこの実施形態の要件ではない。

**English:** RV is retained as an original game currency. Item prices are calculated using regional and store coefficients. Game balancing can make rural regions cheaper but less supplied, and urban regions more expensive but better supplied. Purchased vehicles carry vehicle-specific monthly taxes or maintenance costs, with fuel charged separately. Reproducing actual market prices or redeeming RV for real currency is not required by this embodiment.

```text
fuelUsedL = committedDistanceM / 1000 / efficiencyKmPerL
refuelCostRV = purchasedFuelL * regionalFuelPriceRVPerL
itemPriceRV = round(basePriceRV * regionMultiplier * storeMultiplier)
monthlyCostRV = vehicleMonthlyCostRV
```

**日本語：** 例として、燃費10km/Lの車で2km移動すると0.2Lを消費する。2RV/Lで1L購入すると2RVを支払う。ズーム変更ではこの計算は変わらない。数値は説明用で、確定バランスではない。燃料残量に応じて移動距離を制限し、負の燃料を生成しない。

**English:** For illustration, traveling 2 km with a vehicle rated at 10 km/L consumes 0.2 L. Purchasing 1 L at 2 RV/L costs 2 RV. Zoom changes do not alter the calculation. These values are explanatory, not adopted balance settings. Travel is limited by available fuel so that negative fuel is not generated.

**日本語：** 月額徴収の実施案では`playerId + vehicleId + billingPeriod`を一意キーとして二重徴収を防ぐ。現実月かゲーム月か、休止中の徴収、猶予、救済は未決。管理者の価格変更は版と適用時刻を持ち、取引開始時に使用する版を確定して計算途中の価格混在を防ぐ案とする。

**English:** One monthly-billing embodiment uses `playerId + vehicleId + billingPeriod` as a unique key to prevent duplicate charges. Real-world versus game months, offline charges, grace periods, and recovery remain open. Administrative price updates carry a version and effective time; a transaction fixes the version it uses to avoid mixing prices during calculation.

## 10. 実施形態D：時間・天気・テーマ / Embodiment D: time, weather, and themes

**日本語：** 時刻源から時刻帯と季節を求め、色調・テーマ・音を選ぶ。端末時間、旅先時間、サーバー時間は選択肢であり、演出時計と経済徴収時計は別設定にできる。指定日による旅の再現は代替案で、過去天気の再現には履歴データが必要となる。

**English:** Time-of-day and season are derived from a time source and used to select color, theme, and audio. Device time, destination-local time, and server time are alternatives; presentation and economic billing can use separately configured clocks. Replay for a selected date is an alternative requiring historical data for historical weather.

**日本語：** 地図中心またはプレイヤー位置を天気セルへ変換する。有効キャッシュがあれば利用し、期限切れの場合はサーバーが提供者から取得する。同一セルの同時要求をまとめる案とし、観測時刻・取得時刻・期限を別に保持する。10分TTL・ジオハッシュは例示で、セル寸法は緯度と符号長に依存する。障害時は表示用の直近値またはプリセットへ切り替え、手動移動を継続できるようにする。

**English:** The map center or player location is mapped to a weather cell. Valid cached data is reused; expired data is refreshed by the server from a provider. Concurrent requests for the same cell may be coalesced. Observation time, fetch time, and expiration time are stored separately. A ten-minute TTL and geohash cells are examples; cell dimensions depend on latitude and code length. On failure, presentation uses a prior value or preset so that manual movement can continue.

**日本語：** 雨・雪・風は独立した描画レイヤーとして適用する。速度・制動への反映は任意とし、古い天気を物理へ適用するかも明示設定する。テーマは色数モード、季節、管理イベントを組み合わせられる。イベント優先などのルールは設定化し、見た目の切替で道路接続を変えない。

**English:** Rain, snow, and wind are applied through separate visual layers. Speed or braking effects are optional, with explicit policy for stale weather. Themes can combine palette mode, season, and administrative events. Priority rules, such as an event override, are configurable; changing appearance does not change road connectivity.

## 11. 統合処理例 / Integrated processing example

**日本語：** 徒歩で郵便番号を入力し、住所候補から近似目的地を選ぶ。徒歩で到達可能な道路入口へ接続し、NAVIが経路を生成する。移動中は確定区間から距離と地域境界を処理する。車へ切り替える場合は通行条件を再評価して経路を再生成する。給油時はその地域の価格版を用い、雨の有効キャッシュがあれば雨表示を選ぶ。高架下を通っても接続ランプがなければ高架へ移らない。ズーム・季節テーマの変更では距離・燃料・接続関係を変更しない。

**English:** A walking player enters a postal code and selects an approximate destination from address candidates. The destination attaches to a walk-reachable road entrance, and NAVI computes a route. Committed travel segments drive distance and boundary processing. Switching to driving re-evaluates access rules and replans the route. Refueling uses the price version for the current region, while valid rain data selects rain presentation. Passing beneath an elevated road does not transfer the player to it without a connecting ramp. Zoom or seasonal theme changes do not alter distance, fuel, or connectivity.

**日本語：** この例の組合せに加えて、ZIPのみ、案内のみ、自動移動なし、天気演出のみ、固定時刻、オフライン固定地図、サーバー経路処理、道路幅内移動などの部分構成を開示する。機能は個別または組合せで実施できるが、未記載のあらゆる方式を具体的に開示したと主張するものではない。

**English:** In addition to this combination, disclosed alternatives include ZIP alone, guidance alone, no automatic movement, weather presentation without physics, fixed time, offline fixed maps, server-side routing, and movement within road-width corridors. Features may be implemented individually or in combinations; this is not an assertion that every unspecified implementation has been concretely disclosed.

## 12. データ・権利・運用の限界 / Data, rights, and operational limitations

**日本語：** OSMの道路、店、幅、名前、通行条件、橋・トンネル情報には地域差、欠損、誤り、更新遅れがある。共有ノード・タグを確認し、代表的な立体交差を検証する。OSMだけで全施設・全道路の完全な再現を保証しない。[OSM bridge](https://wiki.openstreetmap.org/wiki/Key:bridge)、[OSM layer](https://wiki.openstreetmap.org/wiki/Key:layer)

**English:** OSM roads, facilities, widths, names, access rules, and bridge/tunnel attributes vary in coverage and may be missing, incorrect, or outdated. Shared nodes and tags are inspected, and representative grade-separated crossings are verified. OSM alone does not guarantee complete reproduction of all roads or facilities. [OSM bridge](https://wiki.openstreetmap.org/wiki/Key:bridge), [OSM layer](https://wiki.openstreetmap.org/wiki/Key:layer)

**日本語：** 郵便番号データは住所対応であり緯度経度の保証ではない。代表点の位置誤差に全国一律の数値を置かない。[日本郵便データ説明](https://www.post.japanpost.jp/service/search/zipcode/download/readme.html)

**English:** Postal-code data associates codes with addresses; it does not guarantee geographic coordinates. No uniform nationwide error bound is assigned to representative points. [Japan Post data description](https://www.post.japanpost.jp/service/search/zipcode/download/readme.html)

**日本語：** OSMのODbLと帰属表示、加工DBの扱いを確認し、公開タイル・検索・経路サービスの利用条件をデータライセンスと別に確認する。公開サーバーの無制限利用を前提にしない。[OSM copyright](https://www.openstreetmap.org/copyright)、[Tile policy](https://operations.osmfoundation.org/policies/tiles/)

**English:** ODbL obligations, attribution, and treatment of adapted databases are reviewed, separately from the usage policies of tile, search, and routing services. Unlimited use of public servers is not assumed. [OSM copyright](https://www.openstreetmap.org/copyright), [Tile policy](https://operations.osmfoundation.org/policies/tiles/)

**日本語：** 地図中の名称は店舗ロゴ・車ブランド・商品画像の使用許諾ではない。独自のアイコン・車・架空店舗を使用する案がある。音楽の著作権と録音音源の権利を分け、商用ゲーム、Web配信、改変、クレジット、実況利用などを素材ごとに確認する。「無料」「レトロ風」は許諾の根拠にならない。[文化庁著作権解説](https://www.bunka.go.jp/seisaku/chosakuken/taisetsu/point/index.html)

**English:** A name in map data does not authorize store logos, vehicle branding, or product imagery. Original icons, vehicles, and fictional stores are an option. Musical-work rights and recording rights are checked separately, including commercial game use, web distribution, modification, attribution, and streaming permissions for each asset. Being free or retro-styled does not establish permission. [Agency for Cultural Affairs guidance](https://www.bunka.go.jp/seisaku/chosakuken/taisetsu/point/index.html)

**日本語：** VPS容量・費用・同時接続数は未見積もり。地図原本、変換データ、経路データ、素材、DB、ログ、バックアップ、更新一時領域を合算し、転送量・メモリ・同時処理を測定する。10分天気キャッシュだけで低負荷を保証しない。小地域から計測し、全国自前経路処理は別評価とする。

**English:** VPS capacity, cost, and supported concurrency have not been estimated. Storage includes source maps, converted and routing data, assets, databases, logs, backups, and temporary update space. Transfer, memory, and concurrent processing are measured. A ten-minute weather cache alone does not guarantee low load. Measurement starts with a small region; nationwide self-hosted routing is evaluated separately.

## 13. 参照実装・MVP工程 / Reference implementation and MVP stages

**日本語：** 選択済みのVite vanilla JS + Pixi.jsで初期化・入力・移動・描画を分ける。公式v8方式では`new Application()`後に`await app.init(...)`し、`app.canvas`を追加する。画像は`Assets.load`で読み込む。導入時に実際の最新版と対応条件を確認し依存版を固定する。[Pixi.js Application](https://pixijs.com/8.x/guides/components/application)、[v8 migration](https://pixijs.com/8.x/guides/migrations/v8)

**English:** The selected Vite vanilla JS + Pixi.js implementation separates initialization, input, movement, and rendering. In the official v8 pattern, `new Application()` is followed by `await app.init(...)`, then attachment of `app.canvas`. Images are loaded with `Assets.load`. The actual current release and compatibility requirements are checked at implementation time, and dependency versions are locked. [Pixi.js Application](https://pixijs.com/8.x/guides/components/application), [v8 migration](https://pixijs.com/8.x/guides/migrations/v8)

```js
import { Application, Assets, Container, Sprite } from 'pixi.js';
async function start() {
  const app = new Application();
  await app.init({ width: 640, height: 360, background: 0x0f172b });
  document.querySelector('#app').appendChild(app.canvas);
  const mapLayer = new Container();
  const actorLayer = new Container();
  app.stage.addChild(mapLayer, actorLayer);
  const texture = await Assets.load('/assets/player.png');
  actorLayer.addChild(new Sprite(texture));
}
start().catch(console.error);
```

**日本語：** 上記は初期化の例で、移動実装済みのゲームではない。マップ再生成時にプレイヤーを消さないよう描画コンテナを分け、開始位置を道路上とする。移動ごとの全地図再描画は避ける。元サンプルの10列×5行を10×10と扱わない。

**English:** The example illustrates initialization, not a completed movement game. Rendering containers are separated to avoid removing the player during map reconstruction, and the spawn position lies on a road. Full map reconstruction on every move is avoided. The original ten-column, five-row example is not treated as a ten-by-ten map.

| 段階 / Stage | 内容と検証 / Work and verification |
|---|---|
| M0–M1 | 固定マップ、1キャラ、道限定操作、境界・道外拒否、本番ビルド / Fixed map, one actor, road-constrained input, boundary/off-road rejection, production build |
| M2–M3 | 連続移動・カメラ、小地域実地図、立体交差検証 / Continuous motion, camera, small real-map area, grade-separation tests |
| M4–M5 | 車・徒歩、ゲームパッド、施設、イベント、保存 / Driving/walking, gamepad, facilities, events, persistence |
| M6–M7 | ZIP/NAVI、RV、給油、地域価格、月額処理 / ZIP/NAVI, RV, refueling, regional pricing, monthly processing |
| M8–M9 | 時間・天気・スキン、管理権限、権利確認、性能計測 / Time, weather, themes, administrative authorization, rights review, performance measurement |

**日本語：** 完了検証には高架へ誤遷移しないこと、ランプで接続できること、ズームで燃料が変わらないこと、郵便番号候補の曖昧さを表示すること、手動で自動移動を中断できること、天気障害で移動が止まらないこと、同一月の二重徴収がないことを含める。これらの検証はまだ実施していない。

**English:** Acceptance checks include preventing false transfer to elevated roads, permitting connected ramps, zoom-independent fuel use, disclosure of postal-code ambiguity, manual interruption of automatic movement, continued movement during weather failure, and no duplicate monthly billing. These checks have not yet been performed.

## 14. 残る決定と公開記録 / Remaining decisions and publication record

**日本語：** 初期地域、道路中心線／幅内移動、規制再現、ゲームパッド対応、ZIP瞬間移動、経路提供者、経済価格、徴収時計、天気提供者、テーマ優先、保存・認証、VPS構成は未決。技術例の記載は製品採用の決定ではない。著者・公開者はtarp.tokyo、文書ライセンスはTARP Tools License、初回公開予定日は2026-10-09（日本時間）とする。公開先と実際の公開日時は公開時に記録する。

**English:** Open decisions include the initial region, centerline versus corridor motion, regulatory coverage, gamepad support, ZIP teleportation, routing provider, economic prices, billing clock, weather provider, theme priority, persistence/authentication, and VPS configuration. Describing an embodiment does not adopt it as product policy. The author and publisher is tarp.tokyo, the document license is the TARP Tools License, and the scheduled initial publication date is 2026-10-09 (Japan time). The venue and actual release time are recorded upon publication.

**日本語：** 公開する場合は、ログイン不要で公衆が読める場所にこの版を掲載し、実際の公開日時、永続URLまたは識別子、公開版の保存コピーを記録する案とする。SHA-256は内容の同一性確認に使えるが、単独では公開日時の証明にならない。更新版では変更履歴を追記し、旧公開版を保持する。ここで述べる公開手順は提案であり、本書の作成によって外部公開は行われていない。

**English:** A proposed release procedure is to publish this version at a publicly readable location without login, recording the actual release time, persistent URL or identifier, and a preserved copy of the released version. SHA-256 can check content identity but does not alone prove publication time. Revisions add a change log and preserve prior public versions. This is a proposed procedure; preparing this file has not published it externally.

| 記録項目 / Record field | 値 / Value |
|---|---|
| Public release timestamp | Not yet recorded / 未記録 |
| Public URL / persistent identifier | Not yet assigned / 未設定 |
| Publisher attribution | tarp.tokyo |
| Initial publication date (scheduled) | 2026-10-09, Asia/Tokyo |
| Document license | TARP Tools License |
| Released-file SHA-256 | Record separately at release / 公開時に別途記録 |

### ライセンスと知的財産 / License and intellectual property

**日本語：** 本仕様書およびRoadvaniaプロジェクトの独自部分には、漢字世界と同じTARP Tools Licenseを指定する。無断複製・改変・再配布・逆コンパイルを禁止する方針とし、具体的な適用条件は[利用規約](https://tools.tarp.tokyo/terms/)を参照する。OSM、Pixi.jsその他の第三者データ・ソフトウェア・素材にはそれぞれのライセンスが適用され、本指定はそれらの条件を上書きしない。

**English:** This specification and the original portions of the Roadvania project are designated under the TARP Tools License, matching Kanji the World. The stated policy prohibits unauthorized copying, modification, redistribution, and decompilation; consult the [terms](https://tools.tarp.tokyo/terms/) for applicable conditions. OSM, Pixi.js, and other third-party data, software, and assets remain subject to their respective licenses; this designation does not override those terms.

**日本語：** 著者名とライセンス名は[漢字世界の公開仕様書](https://github.com/tarp-tokyo/spec/blob/main/kanji-the-world.md)を確認して反映した。規約リンク先の本文は本作業では取得できていないため、その内容を全文確認済みとはしない。

**English:** The author and license names were checked against the [published Kanji the World specification](https://github.com/tarp-tokyo/spec/blob/main/kanji-the-world.md). The linked terms could not be retrieved during this preparation, so no claim is made that their full text was verified.

## 15. 変更履歴 / Revision history

| Version | Preparation date | Changes / 変更 |
|---|---|---|
| 1.0 | 2026-10-09 | 著者tarp.tokyo、TARP Tools License、初回公開予定日2026-10-09を反映した日英併記の防衛公開版。移動、ZIP/NAVI、地域経済、時間・天気の提案実施形態と制約を記載 / Bilingual defensive publication edition with tarp.tokyo attribution, TARP Tools License, and scheduled publication date 2026-10-09; proposed movement, ZIP/NAVI, regional economy, time/weather embodiments and limitations |
