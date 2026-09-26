# Project IYASAKA — Prototype 01 Roadmap

Status: Draft  
Prototype: Prototype 01  
Approved: Pending  
Implementation Use: Prohibited  
Last Updated: 2026-09-26  
Owner: Project IYASAKA  

## Purpose

Prototype 01のPhase進行状況と、次Phaseへの移行条件を管理する。Prototypeの目的、Scope、Phase仕様、完了条件は[PDD](./pdd.md)を正本とする。

## Current Status

| Item | Current value |
|---|---|
| Active Phase | Phase 4（Specification） |
| Phase Status | Specification Draft / Unity Not Started |
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
| Unity Implementation Status（Phase 3） | Completed / Human Verification Passed / PR #6 Merged |

## Phase Overview

| Phase | Name | Status |
|---|---|---|
| Phase 1 | 基盤構築 | Completed |
| Phase 2 | 住民移動 | Completed |
| Phase 3 | 仕事システム | Completed / PR #6 Merged |
| Phase 4 | 生活ループ | Specification Draft / Unity Not Started |
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

## Phase 3 Current Verification Evidence

2026-09-22 GitHub current mainを再取得：仕様main／PR #29 Merge commitは `c62e072e5a696a8987057b74792a573fb550fdc3`。Unity PR #6はMerged、検証済みHEADは `ce147e768c05ec86bb0f6a19303e1abdaaa2cbc7`、Unity main／Merge commitは `31ed1932cff5070c0fb69fa46f27cd3896f069a8`。

[Phase 3 Spec §18.1・§18.2](../../03-system-specs/prototype-01/phase-03-task-system.md)に既存Human Verification PASS、B-1 Resolved、Fast 4x、提出Automated EvidenceとMerge同期を集約する。今回テスト・Human Verificationを再実行した結果ではない。

## Phase 3 Readiness

[PDD §14](./pdd.md)を正本として[Phase 3 Task System Spec v1.0](../../03-system-specs/prototype-01/phase-03-task-system.md)を正式承認（2026-09-16、Specification Review Passed / Blocker None）。前半は一人のTask Lifecycle／Resident FSM、後半は二人のAssignment／Target Reservation競合・無効化解放を検証する。Phase構成は変更しない。

[OQ-P3-01／02](./open-questions.md)はResolved。Phase 3はHuman Verification完了・Unity main反映済み。既存のPhase完了条件を変更しない。

## Phase 4 Specification

[Gather and Wood System Spec](../../03-system-specs/prototype-01/phase-04-gather-and-wood.md)を作成。Status: Draft / Implementation Use: Prohibited / Unity Not Started。Phase 4確定事項と検証を集約し、PDD §22の対象仮値をResolvedへ同期する。次工程は仕様Draft PRの確認。新規Handoffは作成しない。Phase 4のUnity実装・実装テストは未着手。

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
