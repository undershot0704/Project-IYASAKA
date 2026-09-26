# Project IYASAKA — Regression Checklist

Status: Active  
Last Updated: 2026-09-26  
Owner: Project IYASAKA  
Authority: [Prototype 01 Phase 1 Foundation System Spec v2.2](../03-system-specs/prototype-01/phase-01-foundation.md)  
Implementation Use: Verification Only  

## 1. Purpose

本書は、Prototype 01 Phase 1 Camera v2および関連するVerification表示の回帰確認項目を管理する独立文書である。新しいゲーム仕様、Camera仕様、Input、GameplayまたはScopeを追加しない。

## 2. Verified Baseline

以下はUnity PR #5の履歴Baseline。今回のfocused結果で過去の全表示Matrixを再実施済みとは扱わない。

| 項目 | 記録 |
|---|---|
| Unity PR | [Project-IYASAKA-Unity #5](https://github.com/undershot0704/Project-IYASAKA-Unity/pull/5) |
| Verified Unity HEAD | `50510897fec4c55874701ff75abe0ff30856fa13` |
| Human Verification | Passed |
| EditMode Tests | 107 / 107 Passed |
| PlayMode Tests | 64 / 64 Passed |
| Console | Error 0／Warning 0 |

### 2026-09-22 focused verification

Unity [PR #6](https://github.com/undershot0704/Project-IYASAKA-Unity/pull/6)（Verification同期当時はOpen / Draft / 未Merge、現在はMerged）、Verified HEAD `ce147e768c05ec86bb0f6a19303e1abdaaa2cbc7`。Camera／Cell Selection B-1はRe-Review PASSと修正後Human Verification PASSによりResolved。実機Console Error 0／Warning 0、Fastはキー2で切替・Overlay `Time: Fast (4x)` を確認。Human結果の範囲・条件は[Phase 3 Spec §18.1](../03-system-specs/prototype-01/phase-03-task-system.md#181-phase-3-implementation-verification-record--2026-09-22-sync)に記録。

Automated Evidenceは別記録：EditMode 146/146、PlayMode 99/99、Failed／Skipped 0、Compiler Error／Warning 0、31ケース追加。今回再実行していない。下表のBaseline列および既存チェック済み項目はPR #5当時の結果を保持する。

Phase 3のMerge同期は[Phase 3 Spec §18.2](../03-system-specs/prototype-01/phase-03-task-system.md)を参照。Phase 4で実施するRegressionへのMappingは[Phase 4 Spec §16](../03-system-specs/prototype-01/phase-04-gather-and-wood.md)を正本とする。本Checklistの既存PASSをPhase 4で再実施済みとは扱わない。

## 3. Camera v2 Regression Matrix

CameraまたはVerification表示へ影響する変更後は、次の各Camera状態で対象表示が欠落せず、既存操作が正常であることを確認する。表示と選択を一体で確認し、画面上のvisibleな有効Cellをクリックした結果が同じlogical Cellになることを確かめる。Pitch下限とZoom上限の組合せ（今回の再現値20°／24）、代表Yaw、Pan／WASD後で中央・左右・手前を含める。数値は既存実装の再現条件であり、新しいCamera仕様ではない。

| Camera状態 | 確認内容 | Baseline |
|---|---|---|
| Orbit全域 | Yawを360°以上連続操作し、複数方位で確認する | Passed |
| Pitch下限 | Pitch Minimumで反転せず表示が欠落しない | Passed |
| Pitch上限 | Pitch Maximumで真上・真下へ到達せず表示が欠落しない | Passed |
| Zoom下限 | Zoom MinimumでCamera操作と表示を確認する | Passed |
| Zoom上限 | Zoom MaximumでCamera操作と表示を確認する | Passed |
| Pan後 | Left Drag Pan後の複数位置で確認する | Passed |
| WASD後 | WASD移動後の複数位置で確認する | Passed |

## 4. Verification Display Checklist

次の表示は、Orbit全域、Pitch上下限、Zoom上下限、Pan後およびWASD後のCamera可動域で欠落しないこと。

- [x] Grid外周およびCell境界
- [x] Path Line
- [x] Target Marker
- [x] Resident
- [x] Current Marker
- [x] Blocked Marker

## 5. Functional Regression

表示確認と同時に次を確認する。

- [x] Start CellおよびDestination Cellが維持される
- [x] Cell選択がCamera操作と競合しない
- [x] Pathfinding結果が変化しない
- [x] Resident PlacementおよびResident Movementが正常に動作する
- [x] Pause／Resume中もCamera操作と表示が正常に動作する
- [x] Console Error 0／Warning 0

### Visible Cell selection regression（再利用手順）

- 表示上のCell → screen selection → 同じlogical Cellを確認する。near-plane clipping回避のdisplay-only depth compensationをlogical Grid／Target座標へ混入させない。
- LMB short clickはStart、LMB dragはPanでStart／Destinationを保持。RMB clickはDestination、RMB dragはOrbitでselectionを保持する。
- Grid外clickを拒否し、既存の有効selectionを維持する。
- 選択したCellをPhase 2のP配置→Enter移動へ渡し、同じlogical Destinationへの到達を確認する。
- Phase 1の共有選択とPan、Phase 3 Scenario BでTask移動中の別Cell選択を確認し、Task Target／Assignment／Reservationを変えない。
- Paused／Normal／FastでCameraと選択を確認し、Fastの操作キーと実際のOverlay倍率を別々に記録する。

これらは既存仕様のRegression手順であり、新規実施結果を示すチェック済みリストではない。今回の実施範囲とPASSは§2のリンク先を参照する。

本BaselineはUnity PR #5の最終Human Verification結果である。将来の回帰確認では、対象CommitまたはPRと結果をCompletion Evidenceへ記録する。
