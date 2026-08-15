# Project IYASAKA — System Specs

Status: Draft  
Last Updated: 2026-08-16  
Owner: Project IYASAKA  
Implementation Use: Prohibited  

## Role

System Specは、承認済みPDDで定義された対象について、実装に必要な内部挙動、状態遷移、データ、アルゴリズム、例外処理などを管理する。

System Specは新しいゲーム仕様を決定する文書ではない。

### Phase 1 implementation authority

Prototype 01 Phase 1は新運用へ移行済みである。Approved Phase 1 System Specを唯一の正式実装仕様とし、Implementation Handoff、追加仕様文書、追加承認Gateまたは形式的な開始許可を要求しない。Phase 1の個別System Specに記載されたAuthority規則は、本READMEに残る旧Handoff運用記述より優先する。

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
- `Status: Approved`かつ`Implementation Use: Permitted`の文書のみをImplementation Handoff作成の正式な入力として使用する。
- System Specが承認済みであっても、承認済みImplementation Handoffがない状態ではUnity実装を開始しない。

## Prototype 01

| Document | Status | Version | Approved | Implementation Use | Unity Implementation |
|---|---|---|---|---|---|
| [Phase 1 Foundation System Spec](./prototype-01/phase-01-foundation.md) | Approved | 2.2 | 2026-08-16 | Permitted | Permitted |
| [Phase 2 Pathfinding and Movement System Spec](./prototype-01/phase-02-pathfinding-and-movement.md) | Approved | 1.0 | Yes | Permitted | Prohibited |

Phase 1はSystem Spec v2.2を唯一の正式実装仕様とし、Camera v2の実装・Human Verification結果を同期済みである。Phase 1 Implementation Handoff v1.5はLegacy Recordであり、実装判断へ使用しない。Phase 2に関する既存記録と運用は本変更の対象外とする。
