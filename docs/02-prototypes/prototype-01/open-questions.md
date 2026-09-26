# Project IYASAKA — Prototype 01 Open Questions

Status: Draft  
Prototype: Prototype 01  
Approved: Pending  
Implementation Use: Prohibited  
Last Updated: 2026-09-26  
Owner: Project IYASAKA  

## Purpose

Prototype Scope、システム構造、Phase進行へ影響する未決定・未確認事項を管理する。

## Current Assessment

PDD v1.1、Phase 1 System Spec v2.2、Phase 2 System Spec v1.1を確認し、Phase 3 System Spec v1.0を正式承認した（2026-09-16、ユーザー提示のSpecification Review Passed / Blocker None）。Phase 1は完了、Phase 2も追加Human Verificationを正式記録しHuman Verification Passed / Completion Completedとなった。Camera v2もUnity mainへ反映済み。OQ-P3-01／02はResolved、現在の未解決Blockerはない。Phase 3承認時点で新しいゲームデザイン判断は必要ない。

## Resolved Implementation Readiness Questions

| ID | Status | 当初の不足 | 解消根拠 |
|---|---|---|---|
| OQ-P3-01 | Resolved | PR #4の全Human Verification完了証跡不足 | ユーザーの追加Human Verification総合PASSを[Phase 2 Spec v1.1 §17.6](../../03-system-specs/prototype-01/phase-02-pathfinding-and-movement.md)へ正式記録。対象HEAD `50510897fec4c55874701ff75abe0ff30856fa13`。既存テスト・回帰Evidenceと合わせCompletion Completed |
| OQ-P3-02 | Resolved | Camera v2検証済み実装がUnity mainへ未反映 | Unity PR #5を通常Merge。Merge Commit／Unity main `0333868e69eb0b7e84ce6f52067dd468babb315f` はVerified HEADを親に含み、tree完全一致。Camera／表示修正反映済み |

解消記録日: 2026-09-15。詳細は[Roadmap](./roadmap.md)と[Phase 3 Spec §2・§19](../../03-system-specs/prototype-01/phase-03-task-system.md)を参照。OQ解消自体と仕様承認は区別する。2026-09-16の正式承認とD-024によりPhase 3はApproved / Implementation Use Permitted。現在の実装・Human Verification結果は[Phase 3 Spec §18.1](../../03-system-specs/prototype-01/phase-03-task-system.md#181-phase-3-implementation-verification-record--2026-09-22-sync)を参照。Unity PR #6はMerge済み。現在の基準SHAは[Roadmap](./roadmap.md)を参照。

## Phase 3 Verification Follow-up

2026-09-22同期：Camera／Cell Selection B-1は修正・Specification Diff Re-Review PASS・修正後Human Verification PASSによりResolved。B-2なし、Re-Review New Blockers None。Fast Evidenceは追加実機確認 `Time: Fast (4x)` とGitHub実装4xが一致し、訂正済みEvidenceを採用した。同期を止めていたEvidence Blockerは解消。新しいゲームデザイン判断はない。A01〜A07／B01〜B06の既存PASSを保持する。詳細はPhase 3 Spec §18.1に集約し、未提示結果のPASSは補完しない。

## Phase 4 Specification Assessment

Phase 3はHuman Verification完了・Unity main反映済み。Phase 4の木数・内外・半径・Gather時間・木材数量はユーザー確定事項としてResolvedとし、詳細は[Phase 4 System Spec §5・§8](../../03-system-specs/prototype-01/phase-04-gather-and-wood.md)を正本とする。具体的Fixture座標・検証キーは実装裁量であり、機械的にOpen Questionを追加しない。

Completion Evidenceの画像必須指定とD-016の差分は、ユーザー確認によりD-016を維持し、初期状態を含め画像任意として解消した。8つの指定整合観点はPhase 4 Spec §19へ集約。実装・Human Verificationは未実施である。

Phase 4の承認確認はResolved。2026-09-26、Specification Review PASS / Blocker Noneとユーザーの正式承認指示によりSystem Spec v1.0 Approved / Implementation Use Permittedへ更新した。承認記録はPhase 4 Spec §1へ集約する。新規ゲームデザイン判断や追加のImplementation Start Permission Gateはない。

## Management Policy

Phase 3の検証用数値・配置・最小実装判断はSystem Specで管理し、Legacy Handoffへ新規追加しない。D-024はPhase 3以降の新規Phaseへ適用し、Phase 2の既存Authorityを今回移行しない。

ゲーム体験、Prototype Scope、Phase構成に影響する判断が新たに必要になった場合は「弥栄企画壁打ちチャットで判断すべき事項」として登録する。解消済みの2項目は証跡・実装基準の確認であり、新ゲームデザイン判断ではない。

Unity Implementation Status（Phase 3）: Completed / PR #6 Merged。Phase 4: Specification Approved / Unity Not Started。
