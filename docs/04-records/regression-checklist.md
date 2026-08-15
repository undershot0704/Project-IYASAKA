# Project IYASAKA — Regression Checklist

Status: Active  
Last Updated: 2026-08-16  
Owner: Project IYASAKA  
Authority: [Prototype 01 Phase 1 Foundation System Spec v2.2](../03-system-specs/prototype-01/phase-01-foundation.md)  
Implementation Use: Verification Only  

## 1. Purpose

本書は、Prototype 01 Phase 1 Camera v2および関連するVerification表示の回帰確認項目を管理する独立文書である。新しいゲーム仕様、Camera仕様、Input、GameplayまたはScopeを追加しない。

## 2. Verified Baseline

| 項目 | 記録 |
|---|---|
| Unity PR | [Project-IYASAKA-Unity #5](https://github.com/undershot0704/Project-IYASAKA-Unity/pull/5) |
| Verified Unity HEAD | `50510897fec4c55874701ff75abe0ff30856fa13` |
| Human Verification | Passed |
| EditMode Tests | 107 / 107 Passed |
| PlayMode Tests | 64 / 64 Passed |
| Console | Error 0／Warning 0 |

## 3. Camera v2 Regression Matrix

CameraまたはVerification表示へ影響する変更後は、次の各Camera状態で対象表示が欠落せず、既存操作が正常であることを確認する。

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

本BaselineはUnity PR #5の最終Human Verification結果である。将来の回帰確認では、対象CommitまたはPRと結果をCompletion Evidenceへ記録する。
