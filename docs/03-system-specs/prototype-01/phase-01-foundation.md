# Project IYASAKA — Prototype 01 Phase 1 Foundation System Spec

Status: Draft  
Version: 0.1  
Prototype: Prototype 01  
Phase: Phase 1  
Approved: Pending  
Implementation Use: Prohibited  
Last Updated: 2026-07-27  
Owner: Project IYASAKA  
Single Source of Truth: GitHub  

## 1. Purpose

本書は、Prototype 01 Phase 1「基盤構築」で必要となる内部挙動、責務、状態、概念データ、例外処理、検証条件を定義するDraft System Specである。

Phase 1の目的は、Prototype 01で使用する次の基盤を構築できる状態へ仕様化することである。

- 正方形グリッドによる空間表現
- マップを観察するためのカメラ移動とズーム
- シミュレーション時間の一時停止、通常速度、高速化
- Phase 1の検証に必要な最低限の表示
- Phase 2で開始セルと目的セルを指定・確認するための基盤

本書は、承認済みPDDのPhase 1を内部仕様へ展開する。PDDのScope、Out of Scope、Phase構造、完了条件は変更しない。

本書はDraftであり、Unity実装開始を許可しない。Phase 1のUnity実装には、本書のレビューと承認によるActive化、および承認済みImplementation Handoffが必要である。

## 2. Source of Truth and Traceability

本書は次の正式文書を参照する。

- [GDD v1.0](../../01-gdd/gdd.md)
- [Prototype 01 PDD v1.0](../../02-prototypes/prototype-01/pdd.md)
- [Prototype 01 README](../../02-prototypes/prototype-01/README.md)
- [Prototype 01 Roadmap](../../02-prototypes/prototype-01/roadmap.md)
- [Prototype 01 Open Questions](../../02-prototypes/prototype-01/open-questions.md)
- [System Specs README](../README.md)
- [Decision Log](../../04-records/decision-log.md)
- [Changelog](../../04-records/changelog.md)

Prototype 01 PDD v1.0のPhase 1が、本書の上位仕様である。

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
- カメラ回転
- 対象へのフォーカス機能
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
| Camera Control | マップ観察用の移動とズームを管理する | 移動要求、ズーム要求 | カメラ位置、ズーム値 | 入力抽象、設定値 | 回転、フォーカス、完成版入力設定 |
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

Width、Height、Cell Sizeの具体値は、Phase 1 Implementation Handoffで指定可能な設定値とする。完成版仕様として固定しない。

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

- 上下左右のカメラ移動
- ズームイン
- ズームアウト
- 現在のカメラ位置の保持
- 現在のズーム値の保持
- 移動速度、最小ズーム、最大ズームの設定
- ズーム値の有効範囲内への制限

### 9.2 Input Boundary

入力デバイスの割り当ては固定しない。

Camera Controlは、入力処理から次の抽象的な要求を受け取る。

- 二軸の移動方向
- ズーム増減量

キーボード、マウスなどの具体的な割り当ては、Phase 1 Implementation Handoffで指定する。

### 9.3 Movement

- 移動方向がない場合は位置を変更しない。
- 斜め方向の入力で軸ごとの合成により意図せず速くならないよう、入力方向の大きさを最大1として扱う。
- カメラ移動速度は0以上の有限な設定値とする。
- カメラ移動はSimulation Timeの倍率に依存しない。
- Paused中もカメラを移動できる。

### 9.4 Zoom

- GDDで定義された2D固定俯瞰の視覚的前提を維持する。
- Zoom Minimum、Zoom Maximum、Initial Zoomは変更可能な設定値とする。
- Zoom MinimumとZoom Maximumは有限値であり、`Zoom Minimum <= Zoom Maximum`を満たさなければならない。
- Initial ZoomがNaN、Positive Infinity、Negative Infinityの場合は設定を拒否する。
- 有限なInitial Zoomが有効範囲外の場合は有効範囲内へ制限し、その結果を検証表示で確認可能にする。
- ズーム入力値がNaN、Positive Infinity、Negative Infinityの場合は入力を無視し、最後の有効なズーム値を維持する。
- ズーム要求後の値は有効範囲内へ制限する。
- 拒否または無視した理由は検証可能なログまたは状態表示で確認できるようにする。
- Unity Cameraの具体的な設定値とズーム値の単位は、2D固定俯瞰を維持する範囲でImplementation Handoffに委ねる。

### 9.5 Phase 1 Limitations

- カメラ回転を行わない。
- 対象へのフォーカス機能を実装しない。
- 完成版の入力設定を作らない。
- Phase 1ではカメラ移動範囲を制限しない。
- 最終マップ境界はPhase 1で確定せず、仮の境界制限によって後続仕様を拘束しない。
- 後続Phaseでカメラ境界を追加可能な構造を妨げない。
- カメラは住民、経路、建物を参照しない。

### 9.6 Implementation Handoff Boundary

Implementation Handoffでは次を指定できる。

- 具体的なカメラ移動速度
- Zoom Minimum
- Zoom Maximum
- Initial Zoom
- 入力割り当て
- Unity Cameraの具体的な設定値

Implementation Handoffでは次を変更できない。

- 2D固定俯瞰という基本視点
- ゲームプレイ中の自由回転を導入しない方針
- Phase 1 Scopeを超えるCamera機能を追加しない方針

## 10. Simulation Time Control

### 10.1 Time States

Simulation Time Controlは次の3状態を持つ。

| 状態 | 意味 | 時間倍率 |
|---|---|---|
| Paused | シミュレーション時間を進めない | 0 |
| Normal | 基準速度で進める | 1 |
| Fast | 高速で進める | 1より大きい設定値 |

Fastの具体的な倍率は、Phase 1 Implementation Handoffで指定可能な設定値とし、完成版仕様として固定しない。

### 10.2 Initial State

初期状態はNormalとする。

初期化完了後、現在状態と対応する時間倍率を参照できなければならない。

### 10.3 State Transitions

- Paused、Normal、Fastの各状態から、任意の別状態へ変更できる。
- 同じ状態を再指定した場合は、その状態を維持する。
- Pausedから再開する際は、明示的に指定されたNormalまたはFastへ移行する。
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
- 現在のズーム値
- 現在の時間状態
- 現在の時間倍率
- 累積Simulation Elapsed Time
- Phase 2用の開始セル
- Phase 2用の目的セル

セル境界、セル中心、有効グリッド範囲、指定セルは、画面上で互いを識別できるPhase 1検証用表示とする。完成版UIとして設計しない。

### 11.3 Validation Interaction

Phase 1では、検証用として有効セルを開始セルまたは目的セルに指定できるようにする。

- 無効セルは開始セルまたは目的セルとして確定しない。
- 指定済みのセルは画面上または検証表示で識別できる。
- 指定方法の具体的な入力割り当てはImplementation Handoffで決定する。
- この操作は住民移動を開始しない。
- 経路探索を実行しない。

## 12. State and Data

次は概念データであり、Unityのクラス、コンポーネント、アセット形式、シリアライズ方式を確定しない。

| データ | 内容 | 更新主体 |
|---|---|---|
| Grid Configuration | Width、Height、Cell Size、Origin | 初期化時の設定 |
| Camera Position | 現在のカメラ位置 | Camera Control |
| Current Zoom | 現在のズーム値 | Camera Control |
| Camera Settings | 移動速度、最小ズーム、最大ズーム | 初期化時の設定 |
| Current Time State | Paused / Normal / Fast | Simulation Time Control |
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
2. カメラ移動とズーム要求を処理する。
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

- カメラを上下左右へ移動できる。
- ズームインとズームアウトができる。
- ズーム値が設定範囲を超えない。
- 非有限なズーム設定または入力によって無効な内部状態へ遷移しない。
- Paused中もカメラ移動とズームが機能する。
- Simulation Time倍率によってカメラ移動速度が変化しない。
- Phase 1ではグリッド範囲を越えてカメラを移動できる。
- 2D固定俯瞰を維持し、ゲームプレイ中に自由回転できない。

### Simulation Time

- Pausedへ変更できる。
- Normalへ変更できる。
- Fastへ変更できる。
- 各状態から別の状態へ変更できる。
- 不正な状態または倍率を正常状態として適用しない。
- Paused中はSimulation Elapsed Timeが増加しない。
- Normal中はSimulation Elapsed Timeが通常倍率で増加する。
- Fast中はSimulation Elapsed Timeが指定された倍率で増加する。
- 状態変更後もSimulation Elapsed Timeの累積値がリセットされず、連続性を維持する。
- NormalとFastで公開される倍率が設定と一致する。

### Debug and Phase 2 Preparation

- セル境界、セル中心、有効グリッド範囲を画面上で識別できる。
- 選択または指定したセル座標と、World座標から変換されたセル座標を確認できる。
- 現在のセル座標と対応するワールド座標を確認できる。
- Grid Configuration、カメラ状態、時間状態、Simulation Elapsed Timeを確認できる。
- Phase 2用の開始セルと目的セルを指定・確認できる。
- 無効セルを開始セルまたは目的セルとして確定しない。
- 住民、経路探索、道路効果が混入していない。

各項目は、後続のUnity実装後に人間が確認可能な表現とする。本書のDraft作成時点ではUnity実装および動作確認を行わない。

## 17. Verification Plan

本節は、後続のImplementation HandoffおよびUnity実装時に行う確認方法を定義する。今回はテストコードを作成しない。

### 17.1 Automated Verification Candidates

後続工程で自動確認の候補とする項目は次のとおり。

- グリッド四隅と中央セルのValid Cell Check
- 有効セルのCell to World / World to Cell往復
- 最小境界を含み、セル間境界を増加側へ割り当て、最大外周境界を除外する半開区間の判定
- 異なるCell SizeとOriginでの往復変換
- 1未満のWidth・Height、およびNaN、Positive Infinity、Negative Infinityを含むCell Size・Originの拒否
- World to Cellへ渡すNaN、Positive Infinity、Negative Infinityの拒否
- Zoom Minimum、Zoom Maximum、Initial Zoomへ渡すNaN、Positive Infinity、Negative Infinityの拒否
- ズーム入力値がNaN、Positive Infinity、Negative Infinityの場合の入力無視と状態維持
- ズーム範囲制限
- Paused / Normal / Fastと時間倍率の対応
- Paused中にSimulation Elapsed Timeが増加しないこと
- Normal中とFast中にSimulation Elapsed Timeが各倍率で増加すること
- 時間状態変更後もSimulation Elapsed Timeが連続すること
- 不正なFast倍率と状態要求の拒否

### 17.2 Manual Verification

後続工程で人間が確認する項目は次のとおり。

- セル境界、セル中心、有効グリッド範囲の識別
- グリッド表示とセル位置の一致
- 選択・指定したセル座標とWorld座標から変換されたセル座標の一致
- カメラの上下左右移動
- グリッド範囲外への制限なしのカメラ移動
- 2D固定俯瞰の維持と自由回転がないこと
- ズームイン・ズームアウトと上下限
- Paused中のカメラ操作
- Paused / Normal / Fastの切り替え表示
- Simulation Elapsed Timeの停止、通常増加、高速増加、状態変更後の連続性
- カーソル位置のセル座標とワールド座標
- 有効セルと無効セルの表示差
- 非有限値を拒否または無視した理由のログまたは状態表示
- 開始セルと目的セルの指定・識別

### 17.3 Verification Records

確認結果はImplementation Handoffで指定する形式に記録する。

本書をActive化しただけではUnity実装を開始しない。承認済みImplementation Handoffが必要である。

## 18. Phase 2 Handoff Requirements

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

次はPhase 1 Implementation Handoffで指定可能な設定値として扱い、本書のBlocking Open Questionにはしない。

- Width
- Height
- Cell Size
- Origin
- カメラ初期位置
- カメラ移動速度
- 初期ズーム値
- Zoom Minimum
- Zoom Maximum
- Fastの時間倍率
- 具体的な入力割り当て
- Unity Cameraの具体的な設定値
- 検証表示の配置

これらの設定値は、検証目的に必要な範囲で変更可能にし、完成版仕様として固定しない。

## 20. Approval and Implementation Gate

現在の状態は次のとおり。

- Status: Draft
- Version: 0.1
- Approved: Pending
- Implementation Use: Prohibited
- Unity Implementation: Prohibited

本書はレビューと承認が完了するまで実装判断に使用しない。

承認後は本書をActiveへ変更できる。ただし、System SpecのActive化だけではUnity実装開始を許可しない。

Phase 1のUnity実装開始には、次の両方が必要である。

1. 本書がレビュー・承認済みのActive System Specであること
2. Phase 1 Implementation Handoffが作成・承認済みであること

条件が揃うまでUnity実装を開始しない。
