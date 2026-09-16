# Project IYASAKA — Prototype 01 Roadmap

Status: Draft  
Prototype: Prototype 01  
Approved: Pending  
Implementation Use: Prohibited  
Last Updated: 2026-09-16  
Owner: Project IYASAKA  

## Purpose

Prototype 01のPhase進行状況と、次Phaseへの移行条件を管理する。Prototypeの目的、Scope、Phase仕様、完了条件は[PDD](./pdd.md)を正本とする。

## Current Status

| Item | Current value |
|---|---|
| Active Phase | Phase 3（Specification作業） |
| Phase Status | Specification Approved |
| PDD | Active / 1.1 / Approved |
| Phase 1 System Spec | Approved / 2.2 |
| Phase 1 Implementation Handoff | Legacy Record / Implementation Use Prohibited |
| Phase 1 completion | Completed / Camera v2 Human Verification Passed |
| Phase 2 System Spec | Approved / 1.1 |
| Phase 2 implementation | Unity PR #4 Merged |
| Phase 2 completion | Human Verification Passed / Completed |
| Phase 3 System Spec | Approved / 1.0 / Approved 2026-09-16 |
| Phase 3 readiness | Specification Review Passed / Blocker None |
| Phase 3 Implementation Handoff | Not Required / Not Created |
| Phase 3 Implementation Use | Permitted |
| Unity Implementation Status（Phase 3） | Permitted / Not Started |

## Phase Overview

| Phase | Name | Status |
|---|---|---|
| Phase 1 | 基盤構築 | Completed |
| Phase 2 | 住民移動 | Completed |
| Phase 3 | 仕事システム | Specification Approved / Implementation Not Started |
| Phase 4 | 生活ループ | Planned |
| Phase 5 | 物流 | Planned |
| Phase 6 | 建築 | Planned |
| Phase 7 | ゲームループ完成 | Planned |

## Verified Progress and Evidence

2026-09-15にGitHubを確認。仕様Base main HEAD: `265b0cdcf1999078fb930e5fb84377361eeaecba`。

- Phase 1基盤の正式完了を維持。現行[System Spec v2.2](../../03-system-specs/prototype-01/phase-01-foundation.md) §17.6にはCamera v2実装・最終Human Verification Passedを記録済み。
- Phase 2 [Unity PR #4](https://github.com/undershot0704/Project-IYASAKA-Unity/pull/4)はMerge済み。Merge Commitは `453f625ae79ba8e6cb6e8b3ac46b28eb59a2a8a8`。
- 今回ユーザーの追加Human Verification PASSを[Phase 2 System Spec v1.1 §17.6](../../03-system-specs/prototype-01/phase-02-pathfinding-and-movement.md)へ正式記録した。検証対象はUnity PR #5 HEAD `50510897fec4c55874701ff75abe0ff30856fa13`。既存自動テスト・回帰記録と合わせてHuman Verification Passed / Completion Completed。
- PR #4の当時のPendingは今回の追加Evidenceで補完。自動テストはPR #5提出結果のEditMode 107／107、PlayMode 64／64、Compiler Error／Warning 0を参照し、今回再実行した結果とは扱わない。
- Camera v2 [Unity PR #5](https://github.com/undershot0704/Project-IYASAKA-Unity/pull/5)は通常Merge CommitでMerge済み。Merge Commit／Unity mainは `0333868e69eb0b7e84ce6f52067dd468babb315f`。Verified HEADを親に含みtree完全一致を確認した。

## Phase 3 Readiness

[PDD §14](./pdd.md)を正本として[Phase 3 Task System Spec v1.0](../../03-system-specs/prototype-01/phase-03-task-system.md)を正式承認（2026-09-16、Specification Review Passed / Blocker None）。前半は一人のTask Lifecycle／Resident FSM、後半は二人のAssignment／Target Reservation競合・無効化解放を検証する。Phase構成は変更しない。

[OQ-P3-01／02](./open-questions.md)はResolved。次工程はPR #28の承認反映差分確認・Merge判断。Phase 3のImplementation UseはPermittedだが、実装進行はNot Startedであり、Phase 2完了条件を免除しない。

## Implementation Authority

Phase 1はD-022によりApproved System Specが唯一の実装Authority。Legacy Handoffを実装判断に使用しない。D-022はPhase 2まで一括移行したDecisionではない。

D-024によりPhase 3以降の新規PhaseはApproved System Specを唯一のNormative Implementation Authorityとし、原則新規Handoffを作成しない。実装仕様・Verification・Evidenceを集約し、別途Implementation Start Permissionを正式Gateとして要求しない。Phase 2の既存Authorityは移行せず、旧Handoff運用をPhase 3へ転用しない。Permittedは実装入力として使用可能という意味であり、本作業でUnity実装は開始しない。

## Phase Transition Conditions

次Phaseへ移行するには、次を満たす必要がある。

- PDDで定める現在Phaseの完了条件を満たしている
- Unity上で人間による動作確認を完了している
- 正常動作、定義された主要な失敗、次Phaseに必要な出力を確認している
- 次Phaseの検証を妨げる既知の問題が残っていない

次Phaseの実装前に必要範囲のSystem Specを作成・レビュー・承認する。Phase 3仕様は承認済み。未承認の仕様による実装・後続Phase先行実装は許可されていない。
