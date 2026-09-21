# RFC-0027 Recovery Contract Review Plan

สถานะ: **READ-ONLY REVIEW PLAN — RFC-0027 SOURCE NOT MODIFIED**

## Baseline

- Repository: `168211681/Veda`
- Branch: `main`
- Baseline/current HEAD for this review: `1e3ac000d0d87c69c1f49c967bd972568c04cf64`
- Source: `docs/rfc/RFC-0027-rollback-and-recovery.md`
- RFC-0027 SHA-256: `b9c7d972543ba8c29d2e661b9f876e60bfa50beac373d44bad19c1532fd1fc99`
- RFC-0027 status: `Draft`
- Approved RFC-0029 SHA-256 used by this review: `f84f990fad287552b44add3175b08e0505d2b0cd71e2ce87069a021d8d3fe373`

Controlling contracts include the Architecture Constitution, ADR Governance, accepted ADR-0001/0002, RFC-0001 through RFC-0004, owner-approved RFC-0026, owner-approved RFC-0029 and RFC-0043. Relevant contract references include:

- Constitution: `docs/rfc/RFC-0001-constitution.md:150,254`
- World authority/atomic triple: `docs/rfc/RFC-0002-world-model.md:680-688`
- Recovery rollback semantics: `docs/rfc/RFC-0004-state-and-world-transition.md:500,555,947`
- Verification receipt: `docs/rfc/RFC-0026-verification-engine.md:1058-1080,1260-1290`
- External reconciliation and atomic ordering: `docs/rfc/RFC-0029-external-world-interface.md:638-649,2005-2037,2630-2640`
- Downstream delta contract: `docs/rfc/RFC-0043-world-delta-protocol.md:702-726`

## Findings

### F-27-01 — Recovery authority and World Update ambiguity

- Source: RFC-0027 lines 71–83, 312–328, 614–638, 1549–1577, 1990–1999; also REC-20 at 1907–1909.
- Existing wording includes `Update World` after recovery and a diagram path `SUCCESS → WORLD UPDATE`.
- Controlling contract: RFC-0002:680–688; RFC-0004:269,414–415; RFC-0026:1260–1290; RFC-0029:116–120.
- Problem: the document does not state that Recovery Engine only prepares/proposes a corrective candidate and cannot independently commit authoritative modeled World State.
- Consequence: a reader could interpret recovery completion or `WORLD UPDATE` as Recovery Engine authority.
- Minimal correction: replace ambiguous World Update stages with “submit corrective candidate to World Kernel”; state that only World Kernel performs exact-parent CAS atomic commit and that recovery coordination has no commit authority.
- Severity: **P1** (authority ambiguity; not an explicit independent-commit claim).
- New owner decision: **No**, if this is propagation of approved Alternative A. New decision required only if Recovery Engine is intended to commit World directly.

### F-27-02 — Corrective-transition ordering is incomplete

- Source: lines 219–235, 616–638, 995–1017, 1549–1577, 1984–1999.
- Existing wording correctly calls compensation a new action and separates execution success from recovery success, but diagrams end at Verification/World Update without canonical exact-parent applicability and commit ordering.
- Controlling contract: RFC-0004:500,555,947; RFC-0026:1260–1290; RFC-0029:2020–2037.
- Problem: the full `Execution → Observation → Evidence → Verification → exact-parent check → CAS commit` lifecycle is not normative in recovery paths.
- Consequence: recovery could be treated as complete before authoritative commitment.
- Minimal correction: normalize corrective transitions to the approved lifecycle and make successful outcome delivery post-commit.
- Severity: **P1**.
- New owner decision: No.

### F-27-03 — Recovery receipt lacks required verification binding

- Source: `RecoveryReceipt` schema lines 1457–1476; verification section lines 995–1017.
- Existing fields include `evidence[]`, `verification`, `final_state`, but do not explicitly bind exact parent World version, candidate transition hash or verification policy version.
- Controlling contract: RFC-0001:254; RFC-0004:555; RFC-0026:1058–1080.
- Problem: a recovery receipt can be read as sufficient proof without the required applicability context.
- Consequence: stale or differently prepared recovery could be reused.
- Minimal correction: preserve existing fields and add/define Veda-owned linkage for exact parent version, candidate transition hash, evidence set and verification policy version; distinguish VerificationReceipt from WORLD_COMMIT_RECEIPT.
- Severity: **P1**.
- New owner decision: No.

### F-27-04 — Stale-parent and retry semantics are incomplete

- Source: lines 193–199, 302–328, 664–684, 758–792, 1829–1909.
- Existing text says rollback is version-aware, avoids stale snapshots and queries UNKNOWN state, but does not require rejection of a stale verified candidate followed by re-preparation and fresh verification.
- Controlling contract: RFC-0004:947; RFC-0026 stale-parent contract; RFC-0029:645–649.
- Problem: “version-aware” and “verify/recover” are not an explicit CAS applicability rule.
- Consequence: a recovery retry could silently reuse stale verification.
- Minimal correction: parent mismatch rejects the attempt, invalidates old VerificationReceipt applicability, requires a new candidate/current parent/fresh verification and authorization revalidation where required.
- Severity: **P1**.
- New owner decision: No.

### F-27-05 — Atomic triple and successful outcome gating are missing

- Source: lines 1045–1059, 1407–1476, 1984–1999; REC-19/REC-20/REC-30 at 1903–1949.
- Existing text emits recovery events and a RecoveryReceipt but does not require World version + WORLD_COMMIT_RECEIPT + delivery intent as one all-or-none World Kernel boundary.
- Controlling contract: RFC-0002:680–688; RFC-0003:34,493,828–830,1063; RFC-0004:414–415,947.
- Problem: `RecoveryCompleted`, `RecoveryVerified` or `final_state` could be misread as successful modeled-World commitment.
- Consequence: false successful outcome or recovery history without authoritative commit.
- Minimal correction: require the atomic triple inside one World Kernel CAS boundary; create successful outcome only after commit; preserve factual recovery events before commit.
- Severity: **P1**.
- New owner decision: No.

### F-27-06 — UNKNOWN and irreversible external effects need explicit reconciliation contract

- Source: lines 736–816, 1288–1305, 1360–1370, 1583–1660.
- Existing text prohibits blind retry in examples and distinguishes exactly-once delivery/effect, but does not explicitly require stable action identity, external-authority reconciliation and no blind re-execution for every UNKNOWN recovery path.
- Controlling contract: RFC-0029:638–649; RFC-0043:702–726.
- Problem: implementation guidance is distributed across examples rather than a single normative recovery rule.
- Consequence: duplicate irreversible effects or unsupported exactly-once assumptions.
- Minimal correction: state stable action/idempotency identity, external reconciliation before retry, explicit UNKNOWN until resolved, and no exactly-once claim without external guarantee.
- Severity: **P1**.
- New owner decision: No.

### F-27-07 — Event/Chronicle authority boundary is underspecified

- Source: lines 1045–1080, 1407–1476, 1519–1545.
- Existing wording requires recovery world events and Chronicle history, but does not distinguish pre-commit factual recovery records from post-commit successful outcomes or state that Chronicle cannot commit current World State.
- Controlling contract: RFC-0003:493,828–830,1063; RFC-0002:426,680–688; RFC-0029:2223–2233.
- Problem: `WorldReconciled`, `RecoveryCompleted` and Chronicle persistence may be interpreted as commitment.
- Consequence: event persistence could be mistaken for authority.
- Minimal correction: classify recovery events as factual pre-commit records unless they reference a successful WORLD_COMMIT_RECEIPT; state Chronicle is historical authority only.
- Severity: **P1**.
- New owner decision: No.

### F-27-08 — Durable failure evidence is implied but not fully normative

- Source: lines 862–930, 1407–1476, 1519–1545, REC-16–REC-18/REC-21 at 1891–1917.
- Existing invariants require observability, explicit partial/unrecoverable states and evidence preservation, but there is no single requirement covering failed, rejected, partial, UNKNOWN, indeterminate and recovery-required attempts linked to stable action identity.
- Controlling contract: RFC-0004:500; RFC-0029:1751–1758,2537–2554.
- Problem: failure evidence requirements are distributed and linkage identity is not explicit.
- Consequence: audit reconstruction may be incomplete.
- Minimal correction: add durable truthful factual/audit evidence requirement linked to recovery/action identity; prohibit successful outcome claims on failed or indeterminate recovery.
- Severity: **P1**.
- New owner decision: No.

No P0 is asserted: RFC-0027 does not explicitly claim that the Recovery Engine itself commits World State. The current issues are material P1 ambiguities/missing normative propagation that must be corrected before downstream acceptance or freeze.

## Minimal correction plan

1. Replace every normative recovery `World Update` endpoint (lines 71–83, 320, 636, 1575, 1996 and REC-20) with a World Kernel submission/commit contract.
2. Define corrective transition as Authorization → Execution → Observation → Evidence → VerificationReceipt → exact-parent check → World Kernel CAS atomic commit → post-commit outcome.
3. Extend RecoveryReceipt/linkage without removing existing fields; bind parent version, candidate hash, evidence set and policy version.
4. Require stale-parent rejection, re-preparation, fresh verification and authorization revalidation.
5. Define atomic triple: resulting World version + immutable WORLD_COMMIT_RECEIPT + durable delivery intent, all-or-none.
6. Separate factual recovery events from post-commit successful outcomes; Chronicle remains historical authority.
7. Make UNKNOWN/partial/irreversible paths require reconciliation, stable identity and truthful durable evidence.
8. Preserve existing compensation, rollback, bounded retry, simulation, escalation, idempotency and no-history-rewrite semantics.

No new recovery protocol is proposed.

## Future patch acceptance criteria

| # | Criterion | Governing source |
|---:|---|---|
| 01 | Recovery Engine has no World commit authority | RFC-0002:680–688; RFC-0004:269 |
| 02 | Compensation is a new controlled transition | RFC-0027:203–227 |
| 03 | Corrective action requires authorization | RFC-0027:1188–1204; RFC-0001A |
| 04 | Execution precedes observation and verification | RFC-0027:995–1017; RFC-0004:500 |
| 05 | Verification binds exact parent and candidate | RFC-0001:254; RFC-0004:555 |
| 06 | Receipt binds evidence set and policy version | RFC-0026:1058–1080 |
| 07 | Stale parent invalidates applicability | RFC-0004:947; RFC-0029:645–649 |
| 08 | Retry requires fresh candidate and verification | RFC-0029:645–649 |
| 09 | VerificationReceipt differs from commit receipt | RFC-0026:1080–1090 |
| 10 | World Kernel owns single atomic triple | RFC-0002:680–688; RFC-0004:414–415 |
| 11 | Delivery intent is inside atomic boundary | RFC-0003:1063; RFC-0026:1284–1287 |
| 12 | Successful outcome follows successful commit | RFC-0001:150; RFC-0003:493,830 |
| 13 | Failed rollback never produces success | RFC-0004:500; RFC-0029:1751–1758 |
| 14 | UNKNOWN requires reconciliation | RFC-0029:638–649; RFC-0043:702–726 |
| 15 | Irreversible effects are not falsely undone | RFC-0029:1751–1758 |
| 16 | Failure/recovery evidence is durable | RFC-0002:426; RFC-0004:500 |
| 17 | Chronicle does not commit current World State | RFC-0003:1063; RFC-0029:2231–2233 |
| 18 | Event persistence does not confer authority | RFC-0002:680–688; RFC-0029:2034–2037 |
| 19 | Compatible recovery semantics remain intact | RFC-0027:158–171,203–237,1831–1949 |
| 20 | No runtime guarantee without proof | Governance/document review boundary |

All criteria are proposed acceptance checks for a later patch, not runtime test results.

## Governance gate matrix

| Gate | Status | Evidence / missing condition | Authority | Next permitted action |
|---|---|---|---|---|
| RFC-0029 exact-text owner approval | PASS | Approved hash recorded in owner record | Project Owner | Continue governance review |
| RFC-0029 document consistency | PASS | F-29-01..07 and F-29-R1 document-level PASS | Independent reviewer/Owner | Review downstream propagation |
| RFC-0027 downstream consistency | BLOCKED | F-27-01..08 require correction plan | Project Owner | Owner review plan |
| Recovery receipt traceability | BLOCKED | Required binding fields not explicit | Project Owner | Patch RFC-0027 after authorization |
| External reconciliation dependency | BLOCKED | RFC-0027 propagation needed | Project Owner | Coordinate RFC-0029/RFC-0027 review |
| Event/Chronicle authority | BLOCKED | Pre/post-commit distinction missing in RFC-0027 | Project Owner | Patch and re-audit |
| ADR traceability | BLOCKED | Existing gap remains | Governance/Owner | Resolve separately |
| Architecture lifecycle mapping | BLOCKED | Status metadata conflict remains | Governance/Owner | Decision required |
| Version/freeze metadata | BLOCKED | Existing manifest/freeze conflicts remain | Governance/Owner | Audit/decision required |
| WM-12 closure | BLOCKED | Downstream propagation and independent evidence incomplete | Project Owner | Keep OPEN |
| RFC Freeze | BLOCKED | Governance findings unresolved | Governance/Owner | Keep BLOCKED |
| SPEC authorization | BLOCKED | Freeze/acceptance not complete | Project Owner | Not authorized |
| Implementation authorization | BLOCKED | SPEC not authorized and runtime proof absent | Project Owner | Not authorized |

## Outstanding owner decisions

1. Approve or reject the minimal RFC-0027 correction scope.
2. Decide whether the `RecoveryReceipt` is a Veda-owned linkage/summary record or a canonical commit receipt; this plan assumes it is not a substitute for `WORLD_COMMIT_RECEIPT`.
3. Resolve existing lifecycle metadata, ADR traceability and freeze-manifest conflicts separately.
4. Authorize a later RFC-0027 source patch only after review of this plan.

## Validation scope

This document is read-only planning evidence. It does not modify RFC-0027 and does not claim crash injection, physical atomicity, delivery, reconciliation or runtime idempotency tests.

## Exact next permitted action

Independent owner review of this plan. If approved, authorize a separate scoped edit to RFC-0027 only; then perform a new diff review and downstream audit. Do not change RFC status, close WM-12, unblock RFC Freeze, enter SPEC or implementation.
