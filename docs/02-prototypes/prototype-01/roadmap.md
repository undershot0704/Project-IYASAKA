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
| Current Phase | Phase 2 |
| Phase Status | Ready for Phase 2 Specification |
| PDD Status | Active |
| PDD Version | 1.0 |
| Phase 1 System Spec | Approved / 1.4 |
| Phase 1 Implementation Handoff | Approved / 1.5 |
| Phase 1 Unity Implementation | Completed |
| Phase 2 System Spec | Not Created |
| Phase 2 Implementation Handoff | Not Created |
| Implementation Use | Allowed for Specification |
| Unity Implementation | Prohibited for Phase 2 |

Phase 1は正式完了しており、Phase 2の仕様設計を開始できる。Phase 2のSystem SpecとImplementation Handoffは未作成であるため、Phase 2のUnity実装はまだ開始しない。

## Phase Overview

| Phase | Name | Status |
|---|---|---|
| Phase 1 | 基盤構築 | Completed |
| Phase 2 | 住民移動 | Ready for Specification |
| Phase 3 | 仕事システム | Planned |
| Phase 4 | 生活ループ | Planned |
| Phase 5 | 物流 | Planned |
| Phase 6 | 建築 | Planned |
| Phase 7 | ゲームループ完成 | Planned |

詳細スケジュールは本書で定義しない。

## Phase 1 Completion

完了済み：

- PDD v1.0 Active
- Phase 1 System Spec v1.4 Approved
- Phase 1 Implementation Handoff v1.5 Approved
- Decision D-016 Adopted
- Phase 1 Unity実装、100件の自動テスト、Console確認、Human Verification、Completion Evidence完了
- Unity Repository main `548b3c8f8e87b4545e8f408f0955bc9e2fc15eaf`へ反映済み
- Phase 1を阻害するBlocking Open Questionなし

## Phase 2 Readiness

開始可能：

- PDD v1.0にPhase 2の目的、検証内容、実装対象、非対象、完了条件を定義済み
- Phase 1からPhase 2へ必要な基盤を引き渡し済み
- Phase 2 System Specの設計開始を阻害するBlocking Open Questionなし

未完了：

- Phase 2 System Specの作成、レビュー、承認
- Phase 2 Implementation Handoffの作成、レビュー、承認
- ユーザーによる明示的なPhase 2 Unity実装開始許可

## Phase Transition Conditions

次Phaseへ移行するには、次を満たす必要がある。

- PDDで定める現在Phaseの完了条件を満たしている
- Unity上で人間による動作確認を完了している
- 正常動作、定義された主要な失敗、次Phaseに必要な出力を確認している
- 次Phaseの検証を妨げる既知の問題が残っていない

次PhaseでSystem Specが必要な場合は、実装開始前に必要な範囲だけ作成し、承認する。Phase 2のUnity実装開始には、承認済みのPhase 2 System SpecとImplementation Handoffに加え、ユーザーによる明示的な実装開始許可が必要である。未承認または後続Phaseの実装を先行しない。
