# Project IYASAKA — System Specs

Status: Draft  
Last Updated: 2026-09-26  
Owner: Project IYASAKA  
Implementation Use: Prohibited  

## Role

System Specは、承認済みPDDで定義された対象について、実装に必要な内部挙動、状態遷移、データ、アルゴリズム、例外処理などを管理する。

System Specは新しいゲーム仕様を決定する文書ではない。

### Phase 1 implementation authority

Prototype 01 Phase 1は新運用へ移行済みである。Approved Phase 1 System Specを唯一の正式実装仕様とし、Implementation Handoff、追加仕様文書、追加承認Gateまたは形式的な開始許可を要求しない。Phase 1の個別System Specに記載されたAuthority規則は、本READMEに残る旧Handoff運用記述より優先する。

### Phase 3以降の新規Phase implementation authority

[D-024](../04-records/decision-log.md#d-024--phase-3以降のsystem-spec-authorityとphase-3仕様承認)により、Phase 3以降の新規Phaseでは原則としてImplementation Handoffを新規作成しない。Approved System Specを当該Phaseの唯一のNormative Implementation Authorityとする。これはPhase 1だけの例外ではなく、今後の新規Phaseに適用する正式運用である。

System SpecへDesign Intent、System Behavior、Responsibility、State／Lifecycle、Integration、Implementation Constraints、Automated Tests、Human Verification、Regression Mapping、Acceptance Mapping、Completion Evidenceを集約する。

承認後は `Status: Approved`、`Approved: Approved`、`Implementation Use: Permitted` とし、Approved Dateに実際の承認反映日を記録する。PermittedはCodex／Unity実装の正式な入力仕様として使用可能という意味であり、実装開始済みではない。実装進行は別に `Unity Implementation: Permitted / Not Started` 等で管理する。別途のユーザーによるImplementation Start Permissionを正式Gateとして要求しない。

Legacy Handoffは履歴として保持する。過去PhaseのAuthorityを一括で遡及変更せず、特にPhase 2の既存文書・Authorityは今回移行しない。旧文書の一般的なHandoff承認・開始許可必須記述をPhase 3以降へ適用しない。

## Responsibility boundary with PDD

- PDD：「何を作るか」「何を検証するか」「どこまでを対象とするか」を管理する。
- System Spec：PDDで承認された対象が「どのように動くか」を管理する。

PDDのScope、Out of Scope、Phase構成、完了条件をSystem Spec側で変更しない。変更が必要な場合は仕様監査へ戻す。

## Creation timing

System Specは対象Phaseの実装直前に、実装と検証に必要な範囲だけ作成する。

未着手Phaseの詳細を先行して確定せず、完成版向けの汎用設計を追加しない。

## Draft operation

- 新規System SpecはDraftとして作成する。
- Draftはレビューと承認が完了するまで実装判断に使用しない。
- Draftには `Implementation Use: Prohibited` を明記する。
- 承認前にApprovedまたは `Implementation Use: Permitted` へ変更しない。
- Phase 1は上記のSystem Spec単独Authority運用を適用する。
- Phase 3以降は上記のSystem Spec単独Authority運用を適用する。Phase 2の既存Authorityは維持する。

## Prototype 01

| Document | Status | Version | Approved | Implementation Use | Unity Implementation |
|---|---|---|---|---|---|
| [Phase 1 Foundation System Spec](./prototype-01/phase-01-foundation.md) | Approved | 2.2 | 2026-08-16 | Permitted | Permitted |
| [Phase 2 Pathfinding and Movement System Spec](./prototype-01/phase-02-pathfinding-and-movement.md) | Approved | 1.1 | 2026-08-04 | Permitted | Completed / Human Verification Passed |
| [Phase 3 Task System Spec](./prototype-01/phase-03-task-system.md) | Approved | 1.0 | 2026-09-16 | Permitted | Completed / Human Verification Passed / PR #6 Merged |
| [Phase 4 Gather and Wood System Spec](./prototype-01/phase-04-gather-and-wood.md) | Draft | 0.1 | Pending | Prohibited | Not Started |

Phase 1はSystem Spec v2.2を唯一の正式実装仕様とし、Camera v2の実装・Human Verification結果を同期済みである。Phase 1 Implementation Handoff v1.5はLegacy Recordであり、実装判断へ使用しない。Phase 2 System Spec v1.1は追加Human VerificationとCompletion Evidence・進捗の同期のみであり、承認済み仕様要件は変更しない。

### Progress evidence

Phase 2の追加Human Verification PASSは[Phase 2 Spec v1.1 §17.6](./prototype-01/phase-02-pathfinding-and-movement.md)へ正式記録済み。Human Verification Passed / Completion Completed。Camera v2のUnity PR #5はMerge済みで、Unity main `0333868e69eb0b7e84ce6f52067dd468babb315f` はVerified HEADと同一tree。OQ-P3-01／02はResolved。現状は[Roadmap](../02-prototypes/prototype-01/roadmap.md)と[Open Questions](../02-prototypes/prototype-01/open-questions.md)へ同期する。

Phase 3はv1.0 Approved / Implementation Use Permitted、Human Verification完了、Unity PR #6 Merged。既存結果とMerge同期は[Phase 3 Spec §18.1・§18.2](./prototype-01/phase-03-task-system.md)に集約する。仕様PR #29もMerge済み。基準SHAは[Roadmap](../02-prototypes/prototype-01/roadmap.md)を参照。

Phase 4の確定事項・挙動・検証要件は[Gather and Wood System Spec](./prototype-01/phase-04-gather-and-wood.md)へ集約した。仕様PRはDraftで提出し、Unity実装は未着手。新規Implementation Handoffは作成しない。
