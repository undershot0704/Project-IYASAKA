# Project IYASAKA — Prototype 01 Phase 1 Foundation System Spec

Status: Approved  
Version: 2.1  
Prototype: Prototype 01  
Phase: Phase 1  
Approved: 2026-08-15  
Implementation Use: Permitted  
Unity Implementation: Permitted  
Last Updated: 2026-08-15  
Owner: Project IYASAKA  
Single Source of Truth: GitHub  

## 1. Purpose

本書は、Prototype 01 Phase 1「基盤構築」で必要となる内部挙動、責務、状態、概念データ、例外処理、検証条件を定義する承認済みSystem Specである。

Phase 1の目的は、Prototype 01で使用する次の基盤を構築できる状態へ仕様化することである。

- 正方形グリッドによる空間表現
- 3D世界を観察するためのカメラ移動、Pan、Zoom、Orbit
- シミュレーション時間の一時停止、通常速度、高速化
- Phase 1の検証に必要な最低限の表示
- Phase 2で開始セルと目的セルを指定・確認するための基盤

本書は、承認済みPDDのPhase 1を内部仕様へ展開する。PDDのScope、Out of Scope、Phase構造、完了条件は変更しない。

本書は、Prototype 01 Phase 1における唯一の正式実装仕様である。`Status: Approved`かつ`Implementation Use: Permitted`である本書だけを実装Authorityとし、Implementation Handoffその他の追加仕様文書、追加承認Gateまたは形式的な開始許可を要求しない。Unity実装は本書のScope、Acceptance Criteria、Verification Plan、Completion Evidenceおよび明示された制約に従って開始できる。

## 2. Source of Truth and Traceability

本書は次の正式文書を参照する。

- [GDD v1.1](../../01-gdd/gdd.md)
- [Prototype 01 PDD v1.1](../../02-prototypes/prototype-01/pdd.md)
- [Prototype 01 README](../../02-prototypes/prototype-01/README.md)
- [Prototype 01 Roadmap](../../02-prototypes/prototype-01/roadmap.md)
- [Prototype 01 Open Questions](../../02-prototypes/prototype-01/open-questions.md)
- [System Specs README](../README.md)
- [Decision Log](../../04-records/decision-log.md)
- [Changelog](../../04-records/changelog.md)

Prototype 01 PDD v1.1のPhase 1が、本書の上位仕様である。

文書間で矛盾がある場合は、実装判断で補完せず、PDDまたは仕様監査へ戻して確認する。

## 3. Scope

本書が定義する対象は次のとおり。

- 正方形グリッド
- セル座標の表現
- グリッド設定
- セル座標とワールド座標の相互変換
- 有効セルの判定
- カメラ移動
- ズーム
- Pivot方式の360°Camera Orbit
- 一時停止
- 通常速度
- 高速化
- Phase 1の検証に必要な最低限の表示
- Phase 2用の開始セルと目的セルの指定・確認
- Phase 1の初期化順序
- Phase 1の概念的なRuntime Flow
- Phase 1の例外・境界条件
- Phase 1のAcceptance CriteriaとVerification Plan

## 4. Out of Scope

次はPhase 1および本書の対象外とする。

- 住民
- 住民移動
- 経路探索
- Task System
- Reservation System
- Resident FSM
- 採取（Gather）
- 運搬（Haul）
- 建設（Build）
- 木、木材、倉庫、建設予定地
- 道路
- 道路による経路コスト補正
- 道路による移動速度補正
- 自動マップ生成
- 地形差、高低差
- Camera Collision
- Follow Camera
- Focus Camera
- Building Transparency
- Minimap
- Cinemachine
- Middle Mouse Drag
- Touch Pan、Pinch Zoom、Gamepad Camera
- Camera慣性、Camera加減速、Camera境界
- Edge Scroll
- 完成版Input設定、Input Remapping UI
- 汎用Click／Drag Framework、汎用Camera Framework
- 新規Package、外部Asset、Tilemap
- Project Settings変更
- 完成版UI
- セーブ・ロード
- 完成版向けの汎用フレームワーク
- Phase 2以降の先行実装仕様
- Unityの具体的なクラス構成
- テストコードの作成

将来利用を想定した過剰な抽象化や、完成版向け機能を追加しない。

## 5. Design Principles

- Prototype検証に必要な最小構成を優先する。
- 完成版向けの過剰設計を避ける。
- Phase 2以降の仕様を先取りしない。
- グリッド座標を空間判断の基準とする。
- 表示処理とシミュレーション時間を分離する。
- 未確定の数値は変更可能な設定値として扱う。
- 無効な入力や設定を黙って有効値へ変換しない。
- 検証可能性と原因追跡のしやすさを優先する。
- 後で置き換え可能な単純な構造を使用する。

## 6. System Boundary

Phase 1の責務は次の5領域へ分ける。名称は責務を示すものであり、Unityのクラス名を確定するものではない。

| 領域 | 責務 | 主な入力 | 主な出力・保持状態 | 依存関係 | Phase 1で扱わないこと |
|---|---|---|---|---|---|
| Grid | グリッド設定、有効範囲、セル座標の基準を管理する | 幅、高さ、セルサイズ、原点 | Grid Configuration、有効セル判定 | なし | 地形、占有、移動コスト、セル内容 |
| Coordinate Conversion | セル座標とワールド座標を相互変換する | Grid Configuration、セル座標またはワールド座標 | 変換結果、成功・失敗 | Grid | 経路探索、最近傍探索、地形補正 |
| Camera Control | 3D世界観察用の移動、Pan、Zoom、Orbitを管理する | 移動、Pan、Zoom、Orbit要求 | カメラ位置、Pivot、Yaw、Pitch、ズーム値 | 入力抽象、設定値 | Collision、Follow、Focus、Transparency、Minimap |
| Simulation Time Control | Paused / Normal / Fastを管理する | 状態変更要求 | 現在状態、時間倍率 | なし | 昼夜、日付、季節、住民更新 |
| Debug / Validation Display | Phase 1の状態と座標を確認可能にする | 上記各領域の読み取り値、検証用セル指定 | 画面上または検証用表示 | 全領域 | 完成版UI、通知、統計画面 |

各領域は、自身の責務外の状態を直接変更しない。

- Camera ControlはSimulation Time Controlを変更しない。
- Simulation Time Controlはカメラと検証表示を停止しない。
- Debug / Validation Displayは、検証用の開始セル・目的セル指定を除き、各領域の状態を変更しない。
- Coordinate ConversionはGrid Configurationを変更しない。

## 7. Grid Model

### 7.1 Grid Configuration

Grid Configurationは次の設定値を持つ。

| 項目 | 意味 | 制約 |
|---|---|---|
| Width | X方向のセル数 | 1以上の整数 |
| Height | Y方向のセル数 | 1以上の整数 |
| Cell Size | 1セルの一辺に対応するワールド長 | 0より大きい有限値 |
| Origin | セル`(0, 0)`の左下境界に対応するワールド座標 | 有限値 |

Width、Height、Cell Sizeの具体値は、本書の制約内で実装時に設定し、実装PRのCompletion Evidenceへ記録する。完成版仕様として固定しない。

### 7.2 Cell Coordinate

セル座標は整数の組`(x, y)`で表す。

有効セルの条件は次のとおり。

```text
0 <= x < Width
0 <= y < Height
```

- `(0, 0)`をグリッドの基準セルとする。
- Xは右方向へ増加する。
- Yは上方向へ増加する。
- 範囲外の整数座標は無効セルとする。
- 無効セルを自動的に有効範囲へ丸めない。

### 7.3 Cell Representation

Phase 1では、セルが保持するゲーム状態を設けない。

セルは次の情報によって識別する。

- 整数セル座標
- 対応するセル中心のワールド座標
- 有効または無効の判定

地形、占有、通行可否、移動コスト、道路効果はPhase 1で保持しない。

### 7.4 Grid Extent

グリッドのワールド上の範囲は、次の半開区間とする。

```text
originX <= worldX < originX + Width * Cell Size
originY <= worldY < originY + Height * Cell Size
```

セル`(x, y)`が占める範囲も、次の半開区間とする。

```text
originX + x * Cell Size <= worldX < originX + (x + 1) * Cell Size
originY + y * Cell Size <= worldY < originY + (y + 1) * Cell Size
```

- 最小境界は範囲に含める。
- 最大境界は範囲に含めない。
- 各セルは`[min, max)`として扱う。
- グリッド全体の最大外周境界上の座標は範囲外とする。

### 7.5 Floating-Point Policy

- Cell to Worldで生成したセル中心座標は、World to Cellで元のセルへ戻らなければならない。
- グリッドの最小外周境界は有効範囲に含める。
- セル間の境界は、座標が増加する側のセルに属する。
- グリッドの最大外周境界は範囲外とする。
- 浮動小数点誤差を理由に、範囲外座標を黙って有効セルへ丸めない。
- 誤差対策の具体的な実装方法は、上記の期待結果を満たす範囲で実装判断に委ねる。

## 8. Coordinate Conversion

### 8.1 Cell to World

有効なセル`(x, y)`のワールド座標はセル中心を返す。

```text
worldX = originX + (x + 0.5) * cellSize
worldY = originY + (y + 0.5) * cellSize
```

- 有効セルを入力した場合は、変換成功とセル中心座標を返す。
- 無効セルを入力した場合は、変換失敗を返す。
- 無効セルを自動的にClampしない。

### 8.2 World to Cell

ワールド座標を含むセルは次の基準で求める。

```text
x = floor((worldX - originX) / cellSize)
y = floor((worldY - originY) / cellSize)
```

- World座標のいずれかがNaN、Positive Infinity、Negative Infinityの場合は入力を拒否し、変換失敗を返す。
- 変換後のセルが有効範囲内なら、変換成功とセル座標を返す。
- 変換後のセルが範囲外なら、変換失敗を返す。
- 範囲外座標を暗黙に最寄りの有効セルへ丸めない。
- 拒否理由は検証可能なログまたは状態表示で確認できるようにする。

### 8.3 Valid Cell Check

Valid Cell Checkは、セル座標がGrid Configurationの有効範囲内にあるかを返す。

変換処理と有効判定は同じ境界規則を使用する。

### 8.4 Round-Trip Expectations

- 有効セルに対する`Cell to World → World to Cell`は、元のセルと一致する。
- 有効なワールド座標に対する`World to Cell → Cell to World`は、そのワールド座標を含むセルの中心を返す。
- 無効入力は成功結果として扱わない。

## 9. Camera Control

### 9.1 Responsibilities

Camera Controlは次を担当する。

- WASDによるカメラ移動
- Left Mouse DragによるCamera Pan
- Mouse WheelによるZoom
- Right Mouse DragによるPivot方式のCamera Orbit
- Left ClickとLeft Drag、およびRight ClickとRight Dragの競合防止
- 現在のカメラ位置、Pivot位置、Yaw、Pitch、ズーム値の保持
- 移動、Pan、Zoom、Orbitに必要な設定値の検証
- ZoomとPitchの有効範囲内への制限

### 9.2 Input Boundary

Prototype 01 Phase 1 Camera v2の標準割り当ては次のとおり。

| Input | Camera Operation |
|---|---|
| WASD | Camera移動 |
| Left Mouse Drag | Pan |
| Mouse Wheel | Zoom |
| Right Mouse Drag | 360°Orbit |

Camera Controlは、入力処理から次の抽象的な要求を受け取る。

- 二軸の移動方向
- Left PointerのDown、Position／Delta、Upから構成されるPan要求
- 標準ホイール刻みへ正規化したZoom増減量
- Right PointerのDown、Position／Delta、Upから構成されるOrbit要求

Phase 1検証用の短いLeft ClickによるStart Cell指定と短いRight ClickによるDestination Cell指定は維持する。ClickとDragはPixel基準Thresholdで区別し、Drag開始後は同じPointer操作をCell指定として処理しない。判定責務は一つのPointer操作につき一箇所へ集約する。

### 9.3 Movement and Pan

- 移動方向がない場合は位置を変更しない。
- 斜め方向の入力で軸ごとの合成により意図せず速くならないよう、入力方向の大きさを最大1として扱う。
- カメラ移動速度は0以上の有限な設定値とする。
- WASD移動は現在のCamera方位に対して画面上で自然な水平移動となるよう扱い、意図せず高さまたはPitchを変更しない。
- Left Mouse Dragは、Pointer Downからの画面上の移動距離がPixel基準のDrag Thresholdを超えた時点で開始する。
- 画面上で右へドラッグした場合はマップ内容が右へ移動して見える、マップを掴んで動かす向きとする。
- PanはCameraとPivotを同じワールド差分だけ移動し、現在のYaw、Pitch、Zoomを維持する。
- Drag開始後は、そのPointer操作をStart Cell指定として扱わない。
- Keyboard移動とPanはフレームレートおよびSimulation Time倍率に依存しない。
- Paused中もKeyboard移動とLeft Mouse Dragを使用できる。
- Pan中およびPan終了時は既存のStart Cell／Destination Cellを変更しない。
- Phase 1ではCamera移動境界を追加しない。

### 9.4 Zoom

- Mouse WheelでZoom In／Outできる。
- Zoom Minimum、Zoom Maximum、Initial Zoomは変更可能な設定値とする。
- Zoom MinimumとZoom Maximumは有限値であり、`Zoom Minimum <= Zoom Maximum`を満たさなければならない。
- Initial ZoomがNaN、Positive Infinity、Negative Infinityの場合は設定を拒否する。
- 有限なInitial Zoomが有効範囲外の場合は有効範囲内へ制限し、その結果を検証表示で確認可能にする。
- Zoom入力値がNaN、Positive Infinity、Negative Infinityの場合は入力を無視し、最後の有効なZoom値を維持する。
- Mouse Scroll Yは入力デバイス差を考慮して標準的なホイール刻みへ正規化し、連続スクロールでも極端な飛躍や操作不能を起こさない。
- Zoom要求後の値は必ず有効範囲内へ制限する。
- Zoom Stepは正の有限値とし、非有限値または0以下を正常設定として扱わない。
- Zoom操作によってPivot、Yaw、Pitchを意図せず変更しない。
- Zoomはフレームレート、Simulation Time倍率から独立し、Paused中も使用できる。
- 3D CameraにおけるZoomの具体方式、単位、範囲、Stepは、本書のCamera要件を変更しない範囲で実装時に決定し、実装PRへ記録する。

### 9.5 Orbit

- Right Mouse DragでPivotを中心にCameraをOrbitする。
- Yawは連続した360°回転を許可し、方位による停止境界を設けない。
- Pitchは設定された最小値と最大値へ制限する。
- Pitch範囲は真上および真下の視点を含まず、視点反転や垂直方向の特異状態を起こさない。
- Orbit中はPivot位置とZoomを維持し、Camera位置と向きだけをPivotの周囲へ更新する。
- OrbitはフレームレートおよびSimulation Time倍率に依存しない。
- Paused中もRight Mouse DragによるOrbitを使用できる。
- Right Drag開始後は、そのPointer操作をDestination Cell指定として扱わない。
- Orbit中およびOrbit終了時は既存のStart Cell／Destination Cellを変更しない。
- Orbit感度は正の有限値とし、無効値を正常設定として扱わない。

### 9.6 Phase 1 Limitations

Phase 1 Camera v2では次を実装しない。

- Camera Collision
- Follow Camera
- Focus Camera
- Building Transparency
- Minimap
- Cinemachine
- Camera慣性、Camera加減速、Camera移動境界
- 完成版Input設定およびInput Remapping UI

Cameraは住民、経路、建物の状態を参照しない。OrbitのためのPivot以外に、対象追従または自動Focusの責務を持たない。

### 9.7 Implementation Detail Boundary

次は、本書の正式要件を変更しない範囲で実装時に決定できる。

- 具体的なCamera移動速度
- PanおよびOrbitの感度
- Zoom Minimum、Zoom Maximum、Initial Zoom、Zoom Step
- Pitch Minimum、Pitch Maximum、Initial Yaw、Initial Pitch
- Pivotの初期位置およびCameraとPivotの初期関係
- Pixel基準のLeft／Right Drag Threshold
- 3D CameraのProjection、Zoom方式および具体的なUnity Camera設定
- 既存Input Actionsへの最小限の割り当て

実装時の決定は実装PRへ記録し、Human Verificationで確認する。別のImplementation Handoff、設計文書または承認Gateを必要としない。

実装判断では次を変更できない。

- Blenderベースの3D世界を観察するCameraであること
- Pivot方式であること
- Yaw 360°、Pitch制限付きであること
- WASD、Left Drag、Mouse Wheel、Right Dragという標準操作体系
- Paused中も操作可能で、Simulation Time非依存であること
- Phase 1 Scope外のCamera機能を追加しないこと

## 10. Simulation Time Control

### 10.1 Time States

Simulation Time Controlは次の3状態を持つ。

| 状態 | 意味 | 時間倍率 |
|---|---|---|
| Paused | シミュレーション時間を進めない | 0 |
| Normal | 基準速度で進める | 1 |
| Fast | 高速で進める | 1より大きい設定値 |

Fastの具体的な倍率は、本書の制約内で実装時に設定し、実装PRのCompletion Evidenceへ記録する。完成版仕様として固定しない。

### 10.2 Initial State

初期状態はNormalとする。

初期化完了後、現在状態と対応する時間倍率を参照できなければならない。

### 10.3 State Transitions

- Simulation Time Controlは、Pause直前の非Paused状態を復帰先として保持する。
- 保持対象はNormalまたはFastとし、新しいSimulation Time状態は追加しない。
- 非Paused状態でPause要求を受けた場合、現在のNormalまたはFastを保存してPausedへ移行する。
- Paused中にResume要求を受けた場合、保存した直前状態へ復帰する。
- NormalからPauseした場合はNormalへ、FastからPauseした場合はFastへ復帰する。
- Paused中もNormalまたはFastを直接指定でき、その状態へ移行する。
- Paused中にNormalまたはFastへ変更した後、次回Pause時はその新しい非Paused状態を記憶する。
- 初期状態はNormalであり、復帰先が未定義の場合は防御的にNormalへ復帰する。
- 同じ非Paused状態を再指定した場合は、その状態を維持する。
- Pause、Resume、Normal、Fastの状態切り替えでSimulation Elapsed Timeをリセット、逆行、または不正に飛躍させない。
- 不正な状態要求は拒否し、最後の有効状態を維持する。
- Fast倍率が1以下、非数、無限値の場合は設定不正とする。

### 10.4 Separation from Presentation

- Simulation Timeは、将来の住民や町の処理が参照できる共通の時間基盤とする。
- Phase 1では住民や町の更新処理を実装しない。
- カメラ操作とDebug / Validation DisplayはPaused中も機能する。
- カメラ移動速度はSimulation Timeの倍率によって変化しない。

### 10.5 Simulation Elapsed Time

Phase 1では、時間状態の実動作を観測するため、検証用の累積Simulation Elapsed Timeを公開する。

- 初期値は0とする。
- Paused中は増加しない。
- Normal中は通常倍率で増加する。
- Fast中は指定されたFast倍率で増加する。
- 状態変更時に値をリセットせず、累積値の連続性を維持する。
- 住民、生産、建築などの後続システムを観測対象として使用しない。
- ゲーム内の日付、時刻、季節を表すものではない。
- 具体的な保持形式は、上記の観測結果を満たす範囲で実装判断に委ねる。

## 11. Debug and Validation Display

### 11.1 Purpose

Phase 1の内部状態を人間が確認し、座標変換、カメラ、時間制御の問題を追跡できるようにする。

完成版UIとして設計しない。

### 11.2 Required Information

最低限、次を確認できるようにする。

- セル境界
- セル中心
- 有効グリッド範囲
- 現在指している、または選択・指定したセル座標
- 対応するワールド座標
- World座標から変換されたセル座標
- セルが有効か無効か
- グリッドの幅、高さ、セルサイズ、原点
- 現在のカメラ位置
- 現在のPivot位置
- 現在のYaw
- 現在のPitch
- 現在のズーム値
- 現在の時間状態
- 現在の時間倍率
- 累積Simulation Elapsed Time
- Phase 2用の開始セル
- Phase 2用の目的セル

セル境界、セル中心、有効グリッド範囲、指定セルは、画面上で互いを識別できるPhase 1検証用表示とする。Game Viewではグリッド外周とセル境界をCamera移動およびZoomに追従して表示し、Start CellとDestination Cellを別セル・同一セルのどちらでも識別可能にする。完成版UI、完成版アート、将来用ゲーム基盤として設計しない。

### 11.3 Game View Visibility

Game Viewの検証表示は、常時表示する空間・セル表示と、切替可能なVerification Overlayに分離する。

常時表示する対象は次のとおり。

- Grid外周
- セル境界
- Start Cell
- Destination Cell
- Start CellとDestination Cellを同一Cellへ指定した状態の識別表示

これらはPhase 1の検証対象そのものであり、Verification Overlayを非表示にしても表示を維持する。

Verification OverlayはGame View左上に配置し、次の数値、状態、座標、結果およびその他Debug情報を表示する。

- Grid Width、Grid Height、Cell Size、Origin
- Valid Cell Range、Grid状態
- Camera Position、Pivot Position、Yaw、Pitch、Zoom
- Simulation Time State、Time Multiplier、Elapsed Time
- World Position、Cell Position
- Start Cell座標、Destination Cell座標
- Invalid操作結果
- その他Debug情報

表示切替はVerification Overlayだけへ適用し、Game View Grid、Start Cell、Destination Cell、同一Cell表示の描画状態を変更しない。具体的な入力割り当ては、本書の入力要件を変更しない範囲で実装時に決定する。

### 11.4 Validation Interaction

Phase 1では、検証用として有効セルを開始セルまたは目的セルに指定できるようにする。

- 無効セルは開始セルまたは目的セルとして確定しない。
- 指定済みのセルは画面上または検証表示で識別できる。
- 指定方法の具体的な入力割り当ては、本書の入力要件を変更しない範囲で実装時に決定する。
- この操作は住民移動を開始しない。
- 経路探索を実行しない。

## 12. State and Data

次は概念データであり、Unityのクラス、コンポーネント、アセット形式、シリアライズ方式を確定しない。

| データ | 内容 | 更新主体 |
|---|---|---|
| Grid Configuration | Width、Height、Cell Size、Origin | 初期化時の設定 |
| Camera Position | 現在のカメラ位置 | Camera Control |
| Camera Pivot | OrbitとPanの基準となる現在位置 | Camera Control |
| Camera Yaw / Pitch | 現在のOrbit角度 | Camera Control |
| Current Zoom | 現在のズーム値 | Camera Control |
| Camera Settings | 移動速度、Pan／Orbit感度、最小／最大／初期Zoom、Zoom Step、Pitch範囲、Drag Threshold | 初期化時の設定 |
| Current Time State | Paused / Normal / Fast | Simulation Time Control |
| Last Non-Paused Time State | Pause直前のNormalまたはFast。Resume復帰先 | Simulation Time Control |
| Current Time Scale | 現在状態に対応する倍率 | Simulation Time Control |
| Fast Time Scale | Fast状態の設定倍率 | 初期化時の設定 |
| Simulation Elapsed Time | 時間状態と倍率に従って増加する検証用累積値 | Simulation Time Control |
| Selected Start Cell | Phase 2用の開始セル。未指定を許容 | Debug / Validation Display |
| Selected Destination Cell | Phase 2用の目的セル。未指定を許容 | Debug / Validation Display |
| Last Validation Result | 最後に確認した座標と成功・失敗 | Debug / Validation Display |

Phase 1で次のデータは持たない。

- 住民状態
- 経路
- セル占有
- 通行可否
- 移動コスト
- 道路状態
- 建物状態
- 資源状態

## 13. Error and Edge Cases

| ケース | 必須の扱い |
|---|---|
| WidthまたはHeightが1未満 | Grid初期化を失敗とし、利用可能なGridとして公開しない |
| Cell Sizeが0以下、NaN、Positive Infinity、Negative Infinity | Grid初期化を失敗とする |
| OriginがNaN、Positive Infinity、Negative Infinity | Grid初期化を失敗とする |
| 範囲外セル | 無効として返し、自動Clampしない |
| 範囲外ワールド座標 | 変換失敗として返し、有効セルとして扱わない |
| World座標がNaN、Positive Infinity、Negative Infinity | World to Cell入力を拒否し、変換失敗として理由を確認可能にする |
| Zoom MinimumまたはZoom MaximumがNaN、Positive Infinity、Negative Infinity | Camera Control初期化を失敗とする |
| ズーム最小値が最大値を超える | Camera Control初期化を失敗とする |
| Initial ZoomがNaN、Positive Infinity、Negative Infinity | Camera Control初期化を失敗とする |
| 有限なInitial Zoomが有効範囲外 | 有効範囲内へ制限し、検証表示で確認可能にする |
| ズーム入力値がNaN、Positive Infinity、Negative Infinity | 入力を無視し、最後の有効値を維持して理由を確認可能にする |
| Pitch MinimumまたはPitch Maximumが非有限、順序不正、真上／真下を含む | Camera Control初期化を失敗とする |
| Orbit感度が0以下、非数、無限値 | Camera Control初期化を失敗とする |
| カメラ移動速度が負、非数、無限値 | Camera Control初期化を失敗とする |
| Fast倍率が1以下、非数、無限値 | Simulation Time Control初期化を失敗とする |
| 不正な時間状態要求 | 要求を拒否し、最後の有効状態を維持する |
| 必要な依存領域が未初期化 | 依存する領域を利用可能にせず、原因を確認可能にする |
| 開始セルまたは目的セルが無効 | 指定を確定せず、既存の有効指定を維持する |
| 初期化前の参照 | 利用可能な結果として返さず、原因を確認可能にする |

例外時にプロセス全体を無条件で停止させない。ただし、無効な基盤を正常として継続利用しない。

エラー表示の具体的な方式は固定しない。最低限、開発中に原因を特定できるログまたは状態表示を残す。

NaN、Positive Infinity、Negative Infinityによって未定義動作、クラッシュ、または無効な内部状態への遷移を発生させない。

## 14. Initialization Order

Phase 1は次の順序で初期化する。

1. Grid Configurationの検証
2. Gridの初期化
3. Coordinate Conversionの利用可能化
4. Camera Settingsの検証
5. Camera Controlの初期化
6. Simulation Time Settingsの検証
7. Simulation Time Controlの初期化
8. Debug / Validation Displayの初期化
9. Phase 1全体の利用可能状態を確認

依存関係のない内部処理を過度に結合しない。

- Coordinate ConversionはGrid初期化完了後に利用可能とする。
- Debug / Validation Displayは、参照対象となる領域の初期化後に利用可能とする。
- 一つの領域が失敗した場合、依存領域を正常として開始しない。

## 15. Runtime Flow

通常フレームにおける概念的な流れは次のとおり。

1. 入力要求を取得する。
2. カメラ移動、Pan、Zoom、Orbit要求を処理する。
3. 時間状態の変更要求を処理する。
4. 現在のSimulation Time状態と倍率に従ってSimulation Elapsed Timeを更新する。
5. 現在のSimulation Time状態、倍率、累積値を公開する。
6. 検証対象のワールド座標とセル座標を確認する。
7. 開始セル・目的セルの指定要求があれば有効性を確認する。
8. Debug / Validation Displayを更新する。

この順序は責務と依存関係を示す。Unityのイベント関数や具体的な実行順序を固定するものではない。

## 16. Acceptance Criteria

Phase 1 System Specの完了条件は次のとおり。

### Grid and Coordinates

- 有効範囲内の任意セル座標を安定して取得できる。
- Cell to WorldとWorld to Cellの往復結果が一致する。
- セル中心と表示位置が一致する。
- 最小境界を含み、最大境界を含まない半開区間として判定できる。
- セル間境界を座標が増加する側のセルとして判定できる。
- 最小境界セルと最大境界直前のセルを正しく扱える。
- 範囲外セルを有効セルとして認識しない。
- 範囲外ワールド座標を有効セルとして認識しない。
- 非有限なWorld座標を変換成功として扱わない。
- 無効なGrid Configurationを正常として利用しない。

### Camera

- WASDでCameraを上下左右へ移動できる。
- Left Mouse Dragでマップを掴む向きにPanできる。
- Mouse WheelでZoom In／Outでき、Zoom値が設定範囲を超えない。
- Right Mouse DragでPivotを中心にOrbitできる。
- Yawを停止境界なく360°回転できる。
- Pitchが設定範囲内へ制限され、真上・真下、視点反転、垂直方向の特異状態へ到達しない。
- Orbit中にPivotとZoomを維持する。
- PanでCameraとPivotが同じワールド差分だけ移動し、Yaw、Pitch、Zoomを維持する。
- ZoomでPivot、Yaw、Pitchを意図せず変更しない。
- Drag Threshold未満の短いLeft ClickでStart Cellを指定できる。
- Left Drag開始後は同じPointer操作でStart Cellを指定しない。
- Drag Threshold未満の短いRight ClickでDestination Cellを指定できる。
- Right Drag開始後は同じPointer操作でDestination Cellを指定しない。
- Pan／Orbit前のStart Cell／Destination Cellを操作中および操作終了後も維持する。
- Paused中もWASD、Pan、Zoom、Orbitが機能する。
- Simulation Time倍率によってCamera移動、Pan、Zoom、Orbitの操作量が変化しない。
- Phase 1ではグリッド範囲を越えてCameraを移動できる。
- Camera Collision、Follow、Focus、Building Transparency、Minimap、Cinemachineが混入していない。

### Simulation Time

- Pausedへ変更できる。
- Normalへ変更できる。
- Fastへ変更できる。
- 各状態から別の状態へ変更できる。
- 不正な状態または倍率を正常状態として適用しない。
- Paused中はSimulation Elapsed Timeが増加しない。
- Normal中はSimulation Elapsed Timeが通常倍率で増加する。
- Fast中はSimulation Elapsed Timeが指定された倍率で増加する。
- 状態変更後もSimulation Elapsed Timeの累積値がリセットされず、逆行または不正な飛躍を起こさず、連続性を維持する。
- NormalからPause／ResumeするとNormalへ復帰する。
- FastからPause／ResumeするとFastへ復帰する。
- Paused中にNormalまたはFastを選択できる。
- 状態変更後の次回Pauseでは、新しい非Paused状態を復帰先として記憶する。
- 復帰先が未定義の場合はNormalへ安全に復帰する。
- NormalとFastで公開される倍率が設定と一致する。

### Debug and Phase 2 Preparation

- セル境界、セル中心、有効グリッド範囲を画面上で識別できる。
- 選択または指定したセル座標と、World座標から変換されたセル座標を確認できる。
- 現在のセル座標と対応するワールド座標を確認できる。
- Grid Configuration、カメラ状態、時間状態、Simulation Elapsed Timeを確認できる。
- Phase 2用の開始セルと目的セルを指定・確認できる。
- Game Viewでグリッド外周とセル境界を識別でき、Camera移動とZoomに追従する。
- Game ViewでStart CellとDestination Cellを別セル・同一セルのどちらでも識別できる。
- Verification Overlayを非表示にしても、Grid外周、セル境界、Start Cell、Destination Cell、同一Cell表示を維持する。
- Verification Overlayを表示した場合は、Grid、Camera、Simulation Time、座標、指定Cell、Invalid操作結果およびその他Debug情報をGame View左上で確認できる。
- 64×64表示で実用上問題となる著しい操作遅延または描画負荷を発生させない。
- 無効セルを開始セルまたは目的セルとして確定しない。
- 住民、経路探索、道路効果が混入していない。

各項目は、後続のUnity実装後に人間が確認可能な表現とする。本書のDraft作成時点ではUnity実装および動作確認を行わない。

## 17. Verification Plan

本節は、Unity実装時に行う確認方法を定義する。本書がAutomated Verification、Human Verification、Completion EvidenceおよびAcceptance Mappingの正式なAuthorityである。今回はテストコードを作成しない。

### 17.1 Automated Verification Candidates

後続工程で自動確認の候補とする項目は次のとおり。

- グリッド四隅と中央セルのValid Cell Check
- 有効セルのCell to World / World to Cell往復
- 最小境界を含み、セル間境界を増加側へ割り当て、最大外周境界を除外する半開区間の判定
- 異なるCell SizeとOriginでの往復変換
- 1未満のWidth・Height、およびNaN、Positive Infinity、Negative Infinityを含むCell Size・Originの拒否
- World to Cellへ渡すNaN、Positive Infinity、Negative Infinityの拒否
- Zoom Minimum、Zoom Maximum、Initial Zoomへ渡すNaN、Positive Infinity、Negative Infinityの拒否
- Zoom入力値がNaN、Positive Infinity、Negative Infinityの場合の入力無視と状態維持
- Zoom範囲制限と、非有限または0以下のZoom Stepの拒否
- Left／Right Drag Threshold未満をClick、Threshold超過後をDragとして扱う判定
- Left Drag後にStart Cell指定を発火せず、Right Drag後にDestination Cell指定を発火しないこと
- Pan／Orbit後に既存のStart Cell／Destination Cellを維持すること
- PanでCameraとPivotが同一差分だけ移動し、Yaw／Pitch／Zoomを維持すること
- Yawが360°循環でき、Pitchが有効範囲へClampされること
- 無効なPitch範囲とOrbit感度の拒否
- OrbitでPivot／Zoomを維持し、Camera位置と向きを更新すること
- ZoomでPivot／Yaw／Pitchを維持すること
- Paused／Normal／FastでCamera移動、Pan、Zoom、Orbitの処理量が変化しないこと
- Paused / Normal / Fastと時間倍率の対応
- Paused中にSimulation Elapsed Timeが増加しないこと
- Normal中とFast中にSimulation Elapsed Timeが各倍率で増加すること
- 時間状態変更後もSimulation Elapsed Timeが連続すること
- 不正なFast倍率と状態要求の拒否
- NormalからPause、ResumeしてNormalへ戻る状態遷移
- FastからPause、ResumeしてFastへ戻る状態遷移
- Pause／ResumeでSimulation Elapsed Timeがリセット、逆行、または不正に飛躍しないこと
- Paused中のNormal／Fast選択と、次回Pause時の新しい復帰先記憶
- 復帰先未定義時のNormalへの安全な復帰
- Game View用Grid表示Componentまたは描画処理のScene接続
- Grid設定とGame View表示範囲の一致
- Game View表示処理がStart Cell／Destination Cell表示状態を受け取れること
- Verification Overlayの表示状態と、Game View Grid／Start Cell／Destination Cell／同一Cell表示の描画状態が分離されていること
- Verification Overlayを切り替えても常時表示対象の描画状態が変化しないこと
- 無効Grid設定を正常表示として扱わないこと

### 17.2 Manual Verification

後続工程で人間が確認する項目は次のとおり。

- セル境界、セル中心、有効グリッド範囲の識別
- グリッド表示とセル位置の一致
- 選択・指定したセル座標とWorld座標から変換されたセル座標の一致
- WASDによるCamera移動
- Left Mouse Dragによる自然なPanと、Camera／Pivotの同時移動
- Mouse WheelによるZoom In／Outと上下限、連続スクロールでの操作性
- Right Mouse DragによるPivot中心のOrbit
- Yawを360°連続して回転できること
- Pitch上限／下限で停止し、真上・真下、視点反転へ到達しないこと
- Orbit中にPivotとZoomが維持されること
- 複数のYaw／PitchでPanとZoomが自然に操作できること
- Left Click／Left DragとRight Click／Right Dragが誤分類されないこと
- Left DragでStart Cell、Right DragでDestination Cellが変更されないこと
- Pan／Orbit後も既存のStart Cell／Destination Cellが維持されること
- グリッド範囲外への制限なしのCamera移動
- Paused／Normal／Fastで同じCamera移動、Pan、Zoom、Orbit操作感であること
- Paused中のWASD、Pan、Zoom、Orbit
- Camera Collision、Follow、Focus、Building Transparency、Minimap、Cinemachineが存在しないこと
- Paused / Normal / Fastの切り替え表示
- Simulation Elapsed Timeの停止、通常増加、高速増加、状態変更後の連続性
- カーソル位置のセル座標とワールド座標
- 有効セルと無効セルの表示差
- 非有限値を拒否または無視した理由のログまたは状態表示
- 開始セルと目的セルの指定・識別
- Game Viewでのグリッド外周とセル境界
- Camera移動とZoomに追従するGame View Grid
- Game ViewでのStart Cell、Destination Cell、同一セル両指定の識別
- Verification Overlayを非表示にしてもGrid外周、セル境界、Start Cell、Destination Cell、同一Cell表示が維持されること
- Verification Overlayを表示するとGame View左上に必須Debug情報が表示されること
- Game View Grid表示中に著しい操作遅延または描画負荷がないこと
- Normal → Paused → NormalおよびFast → Paused → Fast
- Pause／Resume前後のSimulation Elapsed Timeの連続性
- Paused中のKeyboard 1／2によるNormal／Fast選択

### 17.3 Completion Evidence

後続工程では、Human Verificationの実施記録を正式なCompletion Evidenceとして残す。自動テスト結果、Console結果、Commit履歴およびGitHub PR履歴と組み合わせ、実装完了を追跡可能にする。

#### Game View Grid

- Game ViewでGrid全体が視認できるHuman Verification結果
- Start CellとDestination Cellを別セルへ指定したHuman Verification結果
- 同一セルへStart CellとDestination Cellを指定したHuman Verification結果
- Verification Overlay OFFでGrid外周、セル境界、Start Cell、Destination Cell、同一Cell表示が維持されているHuman Verification結果
- Verification Overlay ONでGame View左上の必須Debug情報が表示されているHuman Verification結果
- Camera移動時にGrid表示が追従する確認結果
- Zoom時にGrid表示が追従する確認結果
- Grid表示によるConsole Error／Warningの有無
- 64×64のGrid表示中に著しい操作遅延または描画負荷がないことの確認結果

#### Camera Operation

- WASDによるCamera移動のHuman Verification結果
- Left DragによるPan前後と、Camera／Pivotの同時移動確認結果
- Mouse WheelによるZoom In／Outと上下限の確認結果
- Right DragによるOrbit前後のHuman Verification結果
- Yaw 360°回転の確認結果
- Pitch上限／下限と、真上・真下へ到達しないことの確認結果
- Orbit中のPivot／Zoom維持結果
- 異なるYaw／PitchでのPan／Zoom結果
- Left ClickによるStart Cell、Right ClickによるDestination Cell指定結果
- Left Drag後のStart Cell不変、Right Drag後のDestination Cell不変の記録
- Pan／Orbit後のStart Cell／Destination Cell維持結果
- Paused中のWASD、Pan、Zoom、Orbit結果
- Normal／Fast／Paused間でCamera操作量が変化しない確認結果
- Scope外Camera機能が混入していない確認結果
- Camera操作によるConsole Error／Warning、著しい操作遅延または負荷がない確認結果

#### Pause／Resume

- Normal → Paused → Normalの確認結果
- Fast → Paused → Fastの確認結果
- Pause／Resume前後でSimulation Elapsed Timeがリセットされない確認結果
- Pause／Resume前後でSimulation Elapsed Timeが逆行または不正に飛躍しない確認結果
- Paused中にSimulation Elapsed Timeが増加しない確認結果
- Paused中にKeyboard 1でNormalへ移行できる確認結果
- Paused中にKeyboard 2でFastへ移行できる確認結果
- Paused中もCamera移動、Pan、Zoom、Orbitが可能な確認結果

スクリーンショットはHuman Verificationを補助する任意の証跡であり、必要に応じて取得する。動画、GIF、その他の視覚的証跡も同様に任意とする。視覚的証跡が未取得であることだけを理由に、完了判定またはMergeを拒否しない。

具体的な記録形式、ファイル名、収集手順は実装PRで定義し、本節の必須Evidenceを欠落させない。

### 17.4 Acceptance Mapping

今回追加・変更したAcceptance Criteriaと検証方法の要件レベルの対応を次に示す。本Mappingを正式要件とし、具体的なテストファイル名、Human Verification手順番号、証拠収集手順は実装PRへ記録する。

#### Game View Grid

| Acceptance Criterion | Automated Test | Human Verification | Completion Evidence |
|---|---|---|---|
| Grid外周がGame Viewで視認できる | Scene接続とGrid設定・表示範囲の一致を確認 | Game Viewで外周を視認 | Grid全体のHuman Verification結果 |
| セル境界がGame Viewで視認できる | Scene接続とGrid設定・表示範囲の一致を確認 | Game Viewでセル境界を視認 | Grid全体のHuman Verification結果 |
| Camera移動へ追従する | 必須としない | Camera移動時の追従を確認 | Camera移動時の追従確認結果 |
| Zoomへ追従する | 必須としない | Zoom時の追従を確認 | Zoom時の追従確認結果 |
| Start Cellを識別できる | 表示処理がStart Cell状態を受け取れることを確認 | Game ViewでStart Cellを識別 | 別セル指定のHuman Verification結果 |
| Destination Cellを識別できる | 表示処理がDestination Cell状態を受け取れることを確認 | Game ViewでDestination Cellを識別 | 別セル指定のHuman Verification結果 |
| 別セル指定を識別できる | Start CellとDestination Cellの個別状態を確認 | Game Viewで両者を区別 | 別セル指定のHuman Verification結果 |
| 同一セルへの両指定を識別できる | 表示処理が両方の指定状態を受け取れることを確認 | Game Viewで両指定を識別 | 同一セル指定のHuman Verification結果 |
| Overlay OFFでも検証対象を常時表示する | Overlay状態とGrid／指定Cell描画状態の分離を確認 | Overlay OFFでGrid外周、セル境界、Start／Destination、同一Cell表示を確認 | Overlay OFFのHuman Verification結果 |
| Overlay ONで必須Debug情報を表示する | Overlay表示状態と表示モデルを確認 | Overlay ONで左上の必須情報を確認 | Overlay ONのHuman Verification結果 |
| 無効Grid設定を正常表示として扱わない | 無効Grid設定の拒否を確認 | 拒否理由と有効状態の維持を確認 | 自動テスト結果、状態保持結果、Console結果 |
| 著しい負荷がない | 必須としない | 64×64表示中の操作性と描画負荷を確認 | 操作遅延・描画負荷の確認結果 |

#### Camera Operation

| Acceptance Criterion | Automated Test | Human Verification | Completion Evidence |
|---|---|---|---|
| WASDでCamera移動できる | 入力量と位置更新を確認 | 各方位で移動を確認 | Camera移動の確認結果 |
| Left DragでPanできる | Camera／Pivotの同一差分更新を確認 | 掴む向きと状態維持を確認 | Pan前後の確認結果 |
| Mouse WheelでZoomできる | 上下限、無効入力、状態維持を確認 | Zoom In／Outと上下限を確認 | Zoom確認結果 |
| Right DragでOrbitできる | Pivot中心の位置・向き更新を確認 | 複数方向から観察 | Orbit前後の確認結果 |
| Yaw 360° | Yawの循環を確認 | 360°連続回転を確認 | Yaw確認結果 |
| Pitch制限 | 範囲Clampと無効設定拒否を確認 | 上下限と非反転を確認 | Pitch確認結果 |
| Orbit中にPivot／Zoomを維持 | 状態維持を確認 | Orbit前後を比較 | Pivot／Zoom維持結果 |
| DragとCell Clickを競合させない | Threshold境界とClick非発火を確認 | 短いClickとDragを確認 | Cell指定不変記録 |
| 既存Cell指定を維持 | Start／Destination状態維持を確認 | Pan／Orbit前後を確認 | 指定維持結果 |
| Paused中も全Camera操作が可能 | Time状態別の入力処理を確認 | Paused中に確認 | Paused中の操作結果 |
| Simulation Timeから独立 | Time状態別の処理量を確認 | Normal／Fast／Pausedで比較 | Time状態別確認結果 |
| Scope外Camera機能を含めない | 依存と構成を確認 | Scene／挙動を確認 | Scope確認結果 |

#### Pause／Resume

| Acceptance Criterion | Automated Test | Human Verification | Completion Evidence |
|---|---|---|---|
| Normal → Paused → Normal | 状態遷移を確認 | Spaceによる往復を確認 | 遷移のテスト結果と確認結果 |
| Fast → Paused → Fast | 状態遷移を確認 | Spaceによる往復を確認 | 遷移のテスト結果と確認結果 |
| Pause直前の非Paused状態を保持 | Normal／Fastの復帰先保持を確認 | 両状態からの復帰結果を確認 | 状態保持のテスト結果と両遷移の確認結果 |
| 復帰先未定義時はNormalへfallback | Normalへの安全な復帰を確認 | 必須としない | fallbackの自動テスト結果 |
| Paused中にKeyboard 1でNormal | Paused中のNormal選択を確認 | Keyboard 1による遷移を確認 | テスト結果と確認結果 |
| Paused中にKeyboard 2でFast | Paused中のFast選択を確認 | Keyboard 2による遷移を確認 | テスト結果と確認結果 |
| Simulation Elapsed Timeをリセットしない | Pause／Resume前後の累積値を確認 | 表示値の連続性を確認 | テスト結果と連続性確認結果 |
| Simulation Elapsed Timeを逆行させない | Pause／Resume前後の単調性を確認 | 表示値の逆行・不正な飛躍がないことを確認 | テスト結果と連続性確認結果 |
| Paused中はSimulation Elapsed Timeが増加しない | Paused中の停止を確認 | Paused中の表示値を確認 | テスト結果と停止確認結果 |
| Paused中もCamera操作が可能 | 必要な場合にRuntime統合を確認 | Paused中のWASD、Pan、Zoom、Orbitを確認 | Camera操作の確認結果 |

### 17.5 Verification Records

確認結果は実装PRのCompletion Evidenceへ記録する。

本書はPhase 1の唯一の正式実装仕様であり、Approved Implementation Handoffまたは追加の形式的Gateを実装開始条件としない。

## 18. Phase 2 Input Requirements

Phase 2開始前に、Phase 1から次を利用可能な状態で引き渡す。

- 安定した整数グリッド座標
- 有効なGrid Configuration
- Cell to World
- World to Cell
- Valid Cell Check
- 開始セルの指定・確認
- 目的セルの指定・確認
- カメラによるマップ観察
- Paused
- Normal
- Fast
- Phase 1の検証表示
- Phase 1 Acceptance Criteriaの確認結果

Phase 2の経路探索、住民移動、通行可否、移動コストは本書で定義しない。

## 19. Open Questions

PDD、GDD、Phase構造またはScopeへ戻す必要があるBlocking Open Questionはない。

次は本書の制約内で実装時に決定可能な設定値として扱い、Blocking Open Questionにはしない。決定値は実装PRへ記録する。

- Width
- Height
- Cell Size
- Origin
- カメラ初期位置
- カメラ移動速度
- 初期ズーム値
- Zoom Minimum
- Zoom Maximum
- 1標準ホイール刻みあたりのZoom Step
- Pixel基準のDrag Threshold
- Fastの時間倍率
- 具体的な入力割り当て
- Unity Cameraの具体的な設定値
- 検証表示の配置

これらの設定値は、検証目的に必要な範囲で変更可能にし、完成版仕様として固定しない。

## 20. Implementation Authority

現在の状態は次のとおり。

- Status: Approved
- Version: 2.1
- Approved: 2026-08-15
- Implementation Use: Permitted
- Unity Implementation: Permitted

本書はPrototype 01 Phase 1における唯一の正式実装仕様である。

Phase 1の実装、Automated Verification、Human Verification、Completion EvidenceおよびScope判定は本書だけをAuthorityとする。Approved Implementation Handoff、追加の仕様文書、追加承認Gateまたは形式的な開始許可を必要としない。

[Phase 1 Implementation Handoff v1.5](../../04-implementation-handoffs/prototype-01/phase-01-foundation.md)はLegacy Documentであり、Camera v1までの移行記録としてのみ保持する。Camera v2以降の正式仕様、実装判断、停止条件または承認条件として使用しない。

実装時に本書のScope外要求、矛盾または不足を発見した場合は、Legacy Handoffで補完せず、本System Specを更新してレビュー・承認する。
