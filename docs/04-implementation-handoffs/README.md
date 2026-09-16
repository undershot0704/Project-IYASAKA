# Implementation Handoffs

Status: Draft  
Owner: Project IYASAKA  
Implementation Use: Prohibited

Implementation Handoffは、旧運用で承認済みSystem Specを実装担当へ渡すために使用した文書である。

Prototype 01 Phase 1はSystem Spec単独Authorityの新運用へ移行済みであり、Phase 1 HandoffはLegacy Recordとしてのみ保持する。以下の旧運用規則はPhase 1およびPhase 3以降の新規Phaseへ適用しない。

Phase 3以降の新規Phaseは[D-024](../04-records/decision-log.md#d-024--phase-3以降のsystem-spec-authorityとphase-3仕様承認)に従い、原則として新規Handoffを作成しない。Approved System Specを唯一のNormative Implementation Authorityとし、追加Handoff承認・Implementation Start Permissionを要求しない。Legacy文書は履歴として保持し、Phase 2の既存Authorityは今回移行しない。

### 旧運用の履歴（Phase 2の既存Authorityを今回変更しない）

- 承認前のHandoffは実装判断に使用しない。
- Handoffのmainへの保存だけではUnity実装を開始しない。
- Unity実装には、承認済みSystem Spec、承認済みImplementation Handoff、ユーザーによる明示的な実装許可が必要である。

## Prototype 01

| Phase | Document | Status | Version | Approved | Implementation Use | Unity Implementation |
|---|---|---|---|---|---|---|
| Phase 1 — Foundation | [Legacy Implementation Handoff](prototype-01/phase-01-foundation.md) | Legacy | 1.5 | Historical | Prohibited | Historical Record |

Phase 1 Implementation Handoff v1.5はCamera v1までの移行記録として凍結し、Camera v2以降の正式仕様または実装Authorityとして使用しない。Phase 1の正式実装仕様はApproved System Specだけとする（現在v2.2）。Phase 2に関する既存記録と運用は本変更の対象外とする。
