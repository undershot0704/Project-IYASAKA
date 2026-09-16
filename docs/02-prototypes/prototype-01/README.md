# Project IYASAKA — Prototype 01

## Entry document

- [Prototype 01 PDD v1.1](./pdd.md)

PDDは目的、Scope、Phase構成、完了条件の正本。進捗・確認証跡は[Roadmap](./roadmap.md)、未確認事項は[Open Questions](./open-questions.md)を参照する。

## Current status

| Item | Current value |
|---|---|
| PDD | Active / 1.1 / Approved |
| Phase 1 | Completed / System Spec 2.2 Approved / Camera v2 Human Verification Passed |
| Phase 1 Implementation Handoff | Legacy Record / Implementation Use Prohibited |
| Phase 2 | System Spec 1.1 Approved / Human Verification Passed / Completed |
| Active Phase | Phase 3（Specification作業） |
| Phase Status | Specification Approved |
| Phase 3 System Spec | Approved / 1.0 / Implementation Use Permitted |
| Phase 3 Implementation Handoff | Not Required / Not Created |
| Unity Implementation Status（Phase 3） | Permitted / Not Started |

Phase 2の追加Human VerificationをSystem Spec §17.6へ正式記録済み。Camera v2 PR #5もMerge済みで、Unity main `0333868e69eb0b7e84ce6f52067dd468babb315f`への反映を確認した。OQ-P3-01／02はResolved。Phase 3 Specification ReviewはPassed / Blocker None（2026-09-16）。D-024によりApproved System Specが唯一のNormative Implementation Authorityとなり、Implementation Use Permittedは実装入力として使用可能という意味。Unity実装は未開始。

## Next steps

- PR #28の承認反映差分を確認し、Mergeを判断する（本作業ではMergeしない）
- 今回はUnity実装を開始しない

## Related documents

- [System Spec運用ルール](../../03-system-specs/README.md)
- [Phase 1 System Spec v2.2](../../03-system-specs/prototype-01/phase-01-foundation.md)
- [Phase 2 System Spec v1.1](../../03-system-specs/prototype-01/phase-02-pathfinding-and-movement.md)
- [Phase 3 Task System Spec v1.0](../../03-system-specs/prototype-01/phase-03-task-system.md)
- [Decision Log](../../04-records/decision-log.md)
- [Changelog](../../04-records/changelog.md)
