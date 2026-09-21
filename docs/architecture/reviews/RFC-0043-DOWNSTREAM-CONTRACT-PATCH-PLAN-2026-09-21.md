# VEDA — RFC-0043 Downstream Contract Patch Plan

Status: **PROPOSED — OWNER REVIEW REQUIRED**
Scope: correction plan only; no RFC source, Constitution, ADR, status, WM-12 gate, or RFC Freeze state is changed.

## Baseline

- Repository: `168211681/Veda`
- Evidence baseline: `e2dc7fc9811049252227bd913547d208e026e51b`
- Audited source: `docs/rfc/RFC-0043-world-delta-protocol.md`
- Owner-approved upstream Draft hashes are recorded in `docs/architecture/reviews/WM-12-OWNER-APPROVAL-RECORD-2026-09-21.md`.

## Controlling contracts

- Constitution: World Kernel is the sole authority for authoritative modeled World State (`docs/architecture/ARCHITECTURE_CONSTITUTION.md:158-174`); verification precedes commitment (`:414-428`).
- RFC-0002: atomic unit is World version + `WORLD_COMMIT_RECEIPT` + committed-event delivery intent (`docs/rfc/RFC-0002-world-model.md:680-690`).
- RFC-0003: pre-commit factual records are distinct from post-commit successful outcome events (`docs/rfc/RFC-0003-event-model.md:483-493`, `:1059-1063`).
- RFC-0004: canonical lifecycle is Execute → Observe → Verify → Commit; verification is exact-parent and CAS conflicts require re-preparation/re-verification (`docs/rfc/RFC-0004-state-and-world-transition.md:929-947`, `:620-630`).

## Findings to correct

### F-43-01 — Verification ordering

Source: `docs/rfc/RFC-0043-world-delta-protocol.md:1475-1493` and `:1497-1513`.

Current text places “หลัง commit” before external observation and verification and lists `APPLIED → VERIFYING → COMMITTED`.

Patch intent:

```text
Candidate Delta
  → Execution
  → Observation
  → Evidence
  → Verification
  → Exact-parent check
  → World Kernel CAS commit
```

Verification failure produces truthful failure/recovery evidence and no receipt for the uncommitted transition.

### F-43-02 — Define `APPLIED`

Sources: `:120-126`, `:202-220`, `:635-647`, `:1548-1570`.

Define `APPLIED` normatively as provisional application to a controlled candidate transition. It is not authoritative World commitment, verification success, external-effect success, or a successful outcome event. External execution facts must be recorded separately.

### F-43-03 — Add the atomic triple

Source: `:651-667` currently lacks the receipt/delivery boundary.

Require every authoritative commit to make visible as one conceptual atomic unit:

1. resulting authoritative World version;
2. immutable `WORLD_COMMIT_RECEIPT`;
3. committed-event delivery intent.

If the triple is incomplete, no authoritative commit exists.

### F-43-04 — Bind verification evidence

Sources: `:132-140`, `:1922-1924`.

Require verification receipt binding to:

- exact parent World version;
- candidate transition hash;
- evidence set;
- verification policy version.

### F-43-05 — CAS conflict handling

On parent mismatch: reject closed, persist conflict evidence, invalidate prior verification applicability, re-prepare against the current parent, and obtain new verification before retry.

### F-43-06 — Event/Chronicle semantics

Sources: `:1088-1100`, `:1819-1832`.

Separate pre-commit factual records (`DeltaApplied`, verification-started, rejected, failed, conflict, recovery-required) from post-commit `DeltaCommitted`. The latter must reference the receipt, resulting World version, and delivery intent. Durable record persistence is not World commitment.

### F-43-07 — Truthful failure evidence

Every failed, rejected, denied, conflicted, partial, or indeterminate attempt must create immutable durable evidence and must not claim successful completion or emit a successful outcome event.

### F-43-08 — Recovery/rollback gating

Recovery and rollback attempts must follow Execute → Observe → Verify → Commit. Only a corrective transition that verifies and commits within the atomic triple may produce a receipt and successful outcome. Failed recovery produces truthful failure or `RECOVERY_REQUIRED` evidence.

### F-43-09 — External-effect reconciliation

Extend `UNKNOWN` handling (`:2000-2004`) with reconciliation states. After a crash or uncertain external result, do not blindly retry; use the idempotency key or external authority to reconcile and record the result.

## Proposed lifecycle

```text
PROPOSED
  → VALIDATING
  → VALID
  → AUTHORIZED
  → EXECUTING / APPLYING (provisional)
  → OBSERVED
  → VERIFYING
  → VERIFIED (receipt bound to exact parent/candidate/evidence/policy)
  → WORLD KERNEL CAS COMMIT
  → COMMITTED (atomic triple visible)
  → post-commit successful outcome delivery
```

Failure paths remain append-only and truthful: `REJECTED`, `UNAUTHORIZED`, `STALE`, `CONFLICTED`, `FAILED`, `UNKNOWN`, `RECOVERY_REQUIRED`, or `ROLLED_BACK` as applicable.

## Dependency impact

| Document | Required review impact |
|---|---|
| RFC-0001 | Map `APPLIED` to provisional factual evidence; never commit authority. |
| RFC-0001A | Delta/APPLIED cannot grant capability or authorization. |
| RFC-0002 | Map WDP commit to the atomic triple and canonical World lineage. |
| RFC-0003 | Normalize pre-commit facts versus post-commit successful outcomes. |
| RFC-0004 | Apply canonical ordering, receipt binding, CAS rejection, and recovery gating. |
| RFC-0026 | Bind verification receipt fields and stale rejection. |
| RFC-0027 | Propagate truthful failed rollback and corrective-transition rules. |
| RFC-0031 | Separate factual trace records from delivery intent and World authority. |
| RFC-0032 | Require committed Chronicle records to reference canonical receipt/version. |
| RFC-0029 | Define reconciliation for uncertain external effects. |

## Acceptance tests for the later owner-authorized patch

1. No RFC-0043 normative text places verification after authoritative commit.
2. `APPLIED` cannot advance canonical World version or claim success.
3. Every successful commit has the atomic triple.
4. Verification receipt fields match the exact parent, candidate hash, evidence, and policy version.
5. Parent conflict makes old verification inapplicable and forces re-preparation/re-verification.
6. Failed/rejected/denied/recovery attempts remain durably auditable and truthful.
7. Failed rollback never emits a successful receipt/outcome event.
8. Delivery retry cannot re-execute World transition.
9. External uncertainty enters reconciliation, not blind duplicate execution.
10. Constitution, accepted ADR semantics, and upstream owner-approved RFC bytes remain unchanged.

## Owner decision required

Approve or reject the correction scope F-43-01 through F-43-09, specifically:

1. Confirm `APPLIED` means provisional candidate application.
2. Authorize an exact RFC-0043 Draft diff.
3. Authorize downstream propagation review for RFC-0026, RFC-0027, RFC-0031, RFC-0032, and RFC-0029.

Until explicit approval, RFC-0043 remains unchanged; WM-12 remains `OPEN`; RFC Freeze remains `BLOCKED`; SPEC and implementation remain unauthorized.
