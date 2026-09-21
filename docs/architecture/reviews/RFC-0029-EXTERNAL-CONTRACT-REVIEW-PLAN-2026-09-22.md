# RFC-0029 External Contract Review Plan

สถานะ: **READ-ONLY REVIEW PLAN — ยังไม่ใช่การแก้ไขหรือการอนุมัติ RFC-0029**

## Baseline and controlling sources

- Repository HEAD reviewed: `6a82f6c9c795119eb0b00eb4b7d5941c9cebcd70`
- Source: `docs/rfc/RFC-0029-external-world-interface.md`
- RFC-0029 status: `Draft` (line 3)
- Owner-approved RFC-0026 SHA-256: `d22360b289066dcbb72ba16d40e86fc315346427e8cfdd865967bd96b81ea1fb`
- Owner-approved RFC-0043 SHA-256: `11f10ded8e7c70bdc0b50c6f5216cedadfa4fd3e60df1111dd0d016548f52eee`
- RFC-0026 approval: [`RFC-0026-OWNER-APPROVAL-RECORD-2026-09-22.md`](RFC-0026-OWNER-APPROVAL-RECORD-2026-09-22.md)
- Controlling authority: Constitution, ADR Governance, accepted ADR-0001 and ADR-0002, RFC-0001 through RFC-0004, RFC-0026 and RFC-0043

The source was read-only during this review. No RFC-0029 bytes were changed.

## Controlling contracts

RFC-0026 requires verification before authoritative World commitment and binds a consequential verification receipt to exact parent World version, candidate transition hash, evidence set and verification policy version (`docs/rfc/RFC-0026-verification-engine.md:1075-1080,1148-1154`). Only the World Kernel performs the authoritative CAS commit and atomically exposes World version, `WORLD_COMMIT_RECEIPT` and durable delivery intent (`:1258-1292`).

RFC-0043 applies the same ordering to World Delta transitions and keeps `APPLIED` provisional. Chronicle and event persistence do not create World authority.

## Findings F-29-XX

### F-29-01 — Ambiguous World Update authority (P1)

- Source: `RFC-0029:3-4`, `:63-68`, `:886-906`, `:1968-1990`, `:2110-2128`
- Current wording: the lifecycle ends in `World Update` after verification; external events and Chronicle examples use the same label.
- Controlling requirement: RFC-0026 `:1258-1292`; ADR-0001 `:81-83`; ADR-0002 `:151-152`
- Problem: `World Update` can be read as direct mutation by the interface, adapter or Chronicle, rather than candidate submission to World Kernel.
- Consequence: downstream authority boundary and commit ordering are ambiguous.
- Minimal correction plan: replace/qualify these labels as `submit verified candidate to World Kernel`; show exact-parent applicability, World Kernel CAS and post-commit outcome delivery where the sequence is normative.
- New owner decision: No, if treated as propagation of approved Alternative A. Re-escalate if RFC-0029 intends another World authority.

### F-29-02 — External commit versus Veda World commit not explicitly separated (P1)

- Source: `RFC-0029:1096-1154` (sections 48–51)
- Current wording: `ExternalTransaction` includes `COMMITTED`; section 50 defines an external commit boundary and section 51 calls the resulting object a commit receipt.
- Controlling requirement: RFC-0026 `:1079-1080,1280-1292`; RFC-0002 atomic World commit contract
- Problem: external-system durability is distinct from `WORLD_COMMIT_RECEIPT`, but the distinction is not normative in RFC-0029.
- Consequence: an external receipt or external `COMMITTED` state could be mistaken for authoritative modeled World commitment.
- Minimal correction plan: define `external commit` as external-system state only; reserve `WORLD_COMMIT_RECEIPT` for successful World Kernel commit; require external receipt to remain evidence input.
- New owner decision: No, unless RFC-0029 proposes external authority over modeled World State.

### F-29-03 — EffectReceipt lacks explicit Alternative A linkage (P1)

- Source: `RFC-0029:1155-1169` (section 51), `:1865-1883` (section 86)
- Current wording: `EffectReceipt` contains external system, operation, status, reported effect and evidence references; it enters RFC-0026 as evidence.
- Controlling requirement: RFC-0026 receipt binding `:1058-1082`; RFC-0043 F-43-04 and F-43-09
- Problem: the document does not normatively state how external receipt evidence is bound to stable action/idempotency identity, candidate transition hash, exact parent World version and verification receipt.
- Consequence: evidence may be insufficient to prove which candidate and parent were verified, especially after retries or UNKNOWN outcomes.
- Minimal correction plan: preserve existing fields and require linkage metadata where a consequential transition exists: stable action/idempotency identity, candidate transition hash, exact parent World version, evidence reference and verification receipt reference. Do not make unavailable external fields unconditional.
- New owner decision: No for propagation; required if a new external-provider identity protocol is introduced.

### F-29-04 — UNKNOWN/reconciliation is not explicitly connected to fresh verification (P1)

- Source: `RFC-0029:606-658`, `:1011-1034`, `:1665-1711`, `:1992-2009`
- Current wording: UNKNOWN requires query/verify/recover; reconciliation states exist; failure pipeline routes to observation and verification or RFC-0027.
- Controlling requirement: RFC-0026 `:1148-1154,988-996`; RFC-0043 F-43-05/F-43-09
- Problem: no explicit MUST says that reconciliation after an uncertain effect invalidates applicability of a prior parent-bound verification and requires fresh candidate preparation/verification before World commit.
- Consequence: retry or reconciliation could accidentally reuse stale verification.
- Minimal correction plan: add explicit stale-parent invalidation and fresh verification requirements; prohibit blind re-execution and preserve truthful UNKNOWN/recovery evidence.
- New owner decision: No, as propagation of approved contract.

### F-29-05 — Human approval described as final commit boundary (P1)

- Source: `RFC-0029:2204-2220` (section 100)
- Current wording: for high-risk physical/social effects, human approval “may become the final commit boundary.”
- Controlling requirement: ADR-0001 `:81-83`; RFC-0001A approval/permission semantics; RFC-0002 World Kernel sole commit authority
- Problem: “final commit boundary” is ambiguous between authorization approval and authoritative modeled World commit.
- Consequence: it could imply human or interface authority bypassing World Kernel.
- Minimal correction plan: qualify human approval as an authorization/permission gate; retain World Kernel as sole authority for modeled World State commit.
- New owner decision: No for clarification; required if the project intends human-owned modeled World commits.

### F-29-06 — External event and Chronicle pipelines use successful-looking World Update (P1)

- Source: `RFC-0029:852-906`, `:1932-2009`, `:2110-2128`
- Current wording: external events, boundary pipelines and Chronicle history flow directly to `World Update` after evidence/verification.
- Controlling requirement: RFC-0003 factual records, RFC-0004 verification-before-commit, ADR-0002 Chronicle is history and World Kernel owns current state
- Problem: pre-commit facts, verified observations and post-commit outcomes are not separated in these pipelines.
- Consequence: event persistence/Chronicle projection may be interpreted as World commitment.
- Minimal correction plan: label pre-commit factual records separately; route verified candidates to World Kernel; show post-commit outcome only after successful atomic commit; keep Chronicle historical.
- New owner decision: No, if this is terminology propagation.

### F-29-07 — Irreversible/partial external effects lack explicit durable failure linkage (P2)

- Source: `RFC-0029:1220-1259`, `:1684-1711`, `:2250-2284`
- Current wording: effect classes include irreversible/financial/physical effects; partial effects route to retry, rollback, reconcile or human review.
- Controlling requirement: RFC-0026 durable truthful evidence; RFC-0027 recovery semantics; RFC-0043 F-43-07/F-43-08
- Problem: durable failure/recovery-required record and its linkage to the attempted action are implied rather than stated as a normative record requirement.
- Consequence: auditability and recovery after partial/irreversible effects may be incomplete.
- Minimal correction plan: require durable factual evidence for failed, partial, UNKNOWN, rejected and recovery-required outcomes, without claiming successful World commit.
- New owner decision: No for propagation.

## Dependency impact

| Dependency | Impact of later RFC-0029 patch |
|---|---|
| RFC-0026 | External observations/EffectReceipt become evidence bound to candidate, parent and policy; VerificationReceipt remains pre-commit |
| RFC-0027 | Recovery/reconciliation consumes stable action identity and must not reuse stale verification |
| RFC-0031 | External requests, responses, observations, verification and failures become factual audit records; successful outcome follows commit |
| RFC-0032 | Chronicle stores durable history and reconstructs from valid commit receipts; it does not commit current World |
| RFC-0043 | External delta evidence must preserve provisional `APPLIED`, exact-parent verification and truthful UNKNOWN paths |

## Minimal section-by-section patch plan (future, not authorized here)

1. Sections 1–3, 36, 88–90, 95: replace ambiguous `World Update` with World Kernel candidate/commit terminology and add canonical ordering.
2. Sections 48–51: distinguish external-system commit/receipt from `WORLD_COMMIT_RECEIPT`.
3. Sections 24–26, 44, 76–85, 91: bind UNKNOWN, timeout and partial-effect reconciliation to stable identity, fresh verification and durable factual evidence.
4. Sections 86–87: add conditional linkage fields for consequential EffectReceipt evidence without making unavailable provider metadata unconditional.
5. Section 100: clarify human approval as authorization, not World Kernel commit authority.
6. Sections 105–107: add invariants and diagrams for World Kernel-only commit, atomic triple and post-commit outcome delivery.

## Acceptance criteria for a later patch

- External execution, external effect, external verification and modeled World commit are distinct.
- `UNKNOWN`, timeout and partial effect never authorize blind retry or successful outcome.
- Effect evidence is linked to stable action identity and, when consequential, candidate hash, exact parent version and verification receipt.
- World Kernel alone commits authoritative modeled World State.
- World version, `WORLD_COMMIT_RECEIPT` and delivery intent are one atomic boundary; outcome delivery follows it.
- Human approval is an authorization gate, not a competing World commit authority.
- Failed, rejected, partial, UNKNOWN and recovery-required paths retain durable truthful evidence.
- Chronicle and event persistence do not confer World authority.
- No exactly-once external execution or physical atomicity is claimed without an explicit external/runtime proof.

## Governance disposition

- RFC-0029: `Draft`; no source modification authorized in this review
- Owner approval of RFC-0026 applies only to its exact approved bytes
- WM-12: `OPEN`
- RFC Freeze: `BLOCKED`
- SPEC: `NOT AUTHORIZED`
- Implementation: `NOT AUTHORIZED`
- This plan is a read-only proposal and does not resolve any finding or grant approval
