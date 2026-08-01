# Project IYASAKA — Prototype 01 Roadmap

Status: Draft  
Prototype: Prototype 01  
Approved: Pending  
Implementation Use: Prohibited  
Owner: Project IYASAKA  

## Purpose

Prototype 01のPhase進行状況と、次Phaseへの移行条件を管理する。

Prototypeの目的、Scope、各Phaseの仕様および完了条件は[PDD](./pdd.md)を参照する。

## Current Status

| Item | Current value |
|---|---|
| Prototype | Prototype 01 |
| Current Phase | Phase 1 |
| Phase Status | Awaiting Explicit Unity Implementation Authorization |
| PDD Status | Active |
| PDD Version | 1.0 |
| Phase 1 System Spec | Approved / 1.0 |
| Phase 1 Implementation Handoff | Approved / 1.0 |
| Implementation Use | Allowed for Specification |
| Codex Prompt Preparation | Permitted |
| Unity Implementation | Prohibited |

Current Phaseは現在の計画上の対象を示す。PDD v1.0、Phase 1 System Spec v1.0、Phase 1 Implementation Handoff v1.0は承認済みであり、Codex Prompt Preparationは許可されている。Unity実装はまだ開始できず、ユーザーによる明示的な実装開始許可が別途必要である。

## Phase Overview

| Phase | Name | Status |
|---|---|---|
| Phase 1 | 基盤構築 | Awaiting Explicit Unity Implementation Authorization |
| Phase 2 | 住民移動 | Planned |
| Phase 3 | 仕事システム | Planned |
| Phase 4 | 生活ループ | Planned |
| Phase 5 | 物流 | Planned |
| Phase 6 | 建築 | Planned |
| Phase 7 | ゲームループ完成 | Planned |

詳細スケジュールは本書で定義しない。

## Phase 1 Readiness

完了済みの準備項目：

- PDD v1.0 Approved
- Phase 1 System Spec v1.0 Approved
- Phase 1 Implementation Handoff v1.0 Approved
- Unity実装リポジトリ初期化済み
- Codex Prompt Preparation: Permitted

未完了項目：

- Codex実装プロンプト作成
- Unity実装リポジトリ基準HEAD再確認
- ユーザーによる明示的な実装開始許可
- Phase 1 Unity実装
- 自動テスト
- Human Verification
- Completion Evidence提出

Unity Implementationは`Prohibited`であり、明示的な実装開始許可を受けるまでUnityファイルを変更しない。

## Phase Transition Conditions

次Phaseへ移行するには、次を満たす必要がある。

- PDDで定める現在Phaseの完了条件を満たしている
- Unity上で人間による動作確認を完了している
- 正常動作、定義された主要な失敗、次Phaseに必要な出力を確認している
- 次Phaseの検証を妨げる既知の問題が残っていない

次PhaseでSystem Specが必要な場合は、実装開始前に必要な範囲だけ作成し、承認する。Phase 1のUnity実装開始には、承認済みのPhase 1 System SpecとImplementation Handoffに加え、ユーザーによる明示的な実装開始許可が必要である。未承認または後続Phaseの実装を先行しない。
