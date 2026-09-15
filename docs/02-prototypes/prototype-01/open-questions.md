# Project IYASAKA — Prototype 01 Open Questions

Status: Draft  
Prototype: Prototype 01  
Approved: Pending  
Implementation Use: Prohibited  
Last Updated: 2026-09-15  
Owner: Project IYASAKA  

## Purpose

Prototype Scope、システム構造、Phase進行へ影響する未決定・未確認事項を管理する。

## Current Assessment

PDD v1.1、Phase 1 System Spec v2.2、Phase 2 System Spec v1.0を確認し、Phase 3 System Spec v0.1をDraft作成した。Phase 1は完了、Phase 2実装はMerge済みだが全Human Verificationの正式証跡確認が残る。Phase 3 Draft作成を妨げる新しいゲームデザイン判断は確認していない。

## Implementation Readiness Questions

| ID | 未確認事項・根拠 | 解消条件 | 影響 |
|---|---|---|---|
| OQ-P3-01 | Unity PR #4はMerge済みだが本文のHuman VerificationはPending。コメント・Reviewにも全完了記録は未確認。Phase 1 Spec v2.2のPhase 2回帰PASSだけで全完了と推定しない | Phase 2全AcceptanceのHuman Verification結果と対象Commitを確認・記録する。未実施なら実施結果が必要 | Draftレビュー可、正式完了断定・Phase 3実装開始前に要解消 |
| OQ-P3-02 | Unity main `453f625ae79ba8e6cb6e8b3ac46b28eb59a2a8a8`にはCamera v2のPR #5が未Merge。仕様v2.2は同PR HEAD `50510897fec4c55874701ff75abe0ff30856fa13`を検証済み | Phase 3実装Baseに検証済みCamera／表示修正が含まれることを確認しSHAを記録する | Draftレビュー可、実装Base確定前に要解消。本作業でUnity Mergeしない |

確認日: 2026-09-15。詳細根拠は[Roadmap](./roadmap.md)および[Phase 3 Spec §2・§19](../../03-system-specs/prototype-01/phase-03-task-system.md)を参照。

## Management Policy

Phase 3の検証用数値・配置・最小実装判断はSystem Specで管理し、Legacy Handoffへ新規追加しない。Phase 1のAuthority移行をPhase 2全体の移行済みと読み替えない。

ゲーム体験、Prototype Scope、Phase構成に影響する判断が新たに必要になった場合は「弥栄企画壁打ちチャットで判断すべき事項」として登録する。現在の2項目は証跡・実装基準の確認であり、新ゲームデザイン判断ではない。

Unity Implementation Status（Phase 3）: Prohibited / Not Started。
