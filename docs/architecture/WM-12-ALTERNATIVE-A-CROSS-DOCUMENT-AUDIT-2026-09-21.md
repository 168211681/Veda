# WM-12 Alternative A — Cross-Document Audit

Date: 2026-09-21

Status: REVIEW ONLY — WM-12 OPEN / RFC Freeze BLOCKED

Branch reviewed: `main`

Base HEAD: `40c7e73e2dd90de15c4c0558aa08203a2430b5a8`

Decision posture: owner-approved design direction for Draft RFC normalization; not RFC acceptance

## 1. Scope and verdict

This audit independently re-reads the normalized Draft text of RFC-0001, RFC-0001A, RFC-0002, RFC-0003, and RFC-0004 against the current Architecture Constitution, ADR-0001, ADR-0002, and ADR Governance.

Scoped result:

* unresolved P0 contradictions: **0 found**;
* unresolved P1 findings: **3** (listed in section 10);
* Constitution changes required: **none**;
* accepted ADR semantic changes made: **none**;
* RFC statuses: **all remain Draft**;
* WM-12: **OPEN**;
* RFC Freeze: **BLOCKED**.

This result is evidence for owner review only. It is not owner approval, RFC acceptance, WM-12 closure, or authorization to enter SPEC/implementation.

## 2. Constitutional interpretation

The normalization is consistent with the existing constitutional meaning:

1. The Constitution is highest authority and lower documents cannot contradict it (`docs/architecture/ARCHITECTURE_CONSTITUTION.md:28-50`).
2. Only the World Kernel is final authority for state transitions (`docs/architecture/ARCHITECTURE_CONSTITUTION.md:158-174`).
3. The canonical successful path is Execution → Verification → Commit → Event → Chronicle (`docs/architecture/ARCHITECTURE_CONSTITUTION.md:178-204`).
4. Verification must inspect actual resulting state before commitment (`docs/architecture/ARCHITECTURE_CONSTITUTION.md:414-428`).
5. Events must represent facts that occurred and cannot present an unverified intention as completion (`docs/architecture/ARCHITECTURE_CONSTITUTION.md:322-338`).
6. The Constitution independently requires durable evidence and distinguishes decision, execution, verification, commit, failure, and rollback (`docs/architecture/ARCHITECTURE_CONSTITUTION.md:267-316`).
7. Chronicle owns durable history while runtime delivery is a separate concern (`docs/architecture/ARCHITECTURE_CONSTITUTION.md:342-366`).

Therefore, a truthful record written after its represented pre-commit fact occurs does not change the Constitution's normative lifecycle. It is durable evidence of an intermediate fact, not the constitutional post-commit successful outcome Event. The post-commit success path remains Commit → Event → Chronicle. No constitutional amendment is required for this interpretation.

## 3. Canonical ordering under review

1. Record proposal/authorization/denial facts after they occur.
2. Prepare a candidate against an exact parent World version.
3. Execute authorized work and persist the execution fact.
4. Observe actual results and persist the observation fact.
5. Verify the candidate, persist the verification fact, and bind the receipt to parent version, candidate hash, evidence set, and verification policy version.
6. World Kernel performs compare-and-swap.
7. As one conceptual atomic boundary, make visible:
   * new World version;
   * immutable `WORLD_COMMIT_RECEIPT`;
   * durable committed-event delivery intent.
8. Materialize/deliver the successful outcome event idempotently from that intent.
9. Chronicle durably preserves the event; audit/read projections may follow.

Normative anchors: RFC-0001 (`docs/rfc/RFC-0001-constitution.md:236-273`, `docs/rfc/RFC-0001-constitution.md:326-356`), RFC-0002 (`docs/rfc/RFC-0002-world-model.md:678-690`, `docs/rfc/RFC-0002-world-model.md:824-835`), RFC-0003 (`docs/rfc/RFC-0003-event-model.md:795-830`), and RFC-0004 (`docs/rfc/RFC-0004-state-and-world-transition.md:929-990`).

## 4. Authority-boundary audit

| Question | Evidence | Result |
|---|---|---|
| Who owns current modeled World State? | Constitution `:158-174`; ADR-0001 `:81-105`; RFC-0002 `:678-690`; RFC-0004 `:267-285` | PASS — World Kernel only |
| Can an Event self-authorize or mutate World? | RFC-0001 `:140-154`; RFC-0002 `:680-690`; RFC-0003 `:820-830` | PASS — prohibited |
| Is Chronicle historical authority but not current World authority? | Constitution `:342-376`; ADR-0002 `:201-260`; RFC-0003 `:946-968` | PASS |
| Is Event Fabric delivery distinct from persistence/commit? | Constitution `:342-366`; ADR-0002 `:166-197`; RFC-0003 `:1049-1063` | PASS |
| Are external systems still authoritative for external state? | ADR-0001 `:107-142`; RFC-0002 `:775-791` | PASS |

## 5. Crash and failure-boundary analysis

| Boundary / failure | Durable state | Authoritative World | Recovery | Audit evidence | Duplicate execution | Required invariant |
|---|---|---|---|---|---|---|
| Before permission receipt | No permitting receipt | Parent unchanged | Re-evaluate permission | Optional attempt telemetry; no effective grant | No authorized execution | No receipt → no consequential grant |
| After permission receipt, before execution | Decision receipt only | Parent unchanged | Resume or expire/revoke under policy | Durable decision | Possible retry, controlled by action idempotency key | Decision receipt does not imply execution |
| After execution, before observation | Execution fact/intent may exist | Parent unchanged | Inspect external effect; do not blindly re-execute | Execution-start/failure facts | Possible unless external action is idempotent | Execution acknowledgement is not success |
| After observation, before verification | Execution and observation facts | Parent unchanged | Verify from retained evidence or obtain a new observation | Observation/evidence records | No World duplicate; external retry prohibited until reconciled | Observation is not verification |
| After verification, before CAS | Verification receipt exists | Parent unchanged | Attempt CAS only if all bindings still match | Verification receipt | No World duplicate | Receipt is parent- and candidate-specific |
| CAS finds stale parent | Conflict fact; old receipt retained | Current newer parent remains authoritative | Re-prepare and re-verify | Conflict plus superseded applicability | No commit from stale candidate | Stale verification cannot be reused |
| Crash during atomic World commit | Atomic triple is either absent or fully visible | Old version if absent; new version if present | Inspect triple; never infer from delivery | Transaction/receipt evidence | No duplicate World execution | World + receipt + intent are all-or-none |
| Triple visible, outcome event not delivered | Triple and pending delivery intent | New version authoritative | Resume delivery | Receipt + pending intent | Delivery may repeat; execution must not | Stable intent/event identity and idempotent consumer |
| Outcome delivered, Chronicle append not acknowledged | Triple plus delivery attempt | New version authoritative | Retry same event identity | Delivery attempt; Chronicle ack absent | Duplicate delivery possible, duplicate transition impossible | Chronicle deduplicates immutable event identity |
| Chronicle append succeeded, ack lost | Triple plus durable Chronicle fact | New version authoritative | Retry and receive deduplicated acknowledgment | Chronicle record plus repeated delivery | Duplicate delivery possible, duplicate fact prohibited | Append is idempotent by event id/hash |
| Verification/rejection/denial/failure | Corresponding truthful facts | Parent unchanged unless a prior independent commit exists | Abort, compensate, or start a new authorized transition | Mandatory durable failure-path evidence | Retry only with policy/idempotency controls | Failure record cannot masquerade as success |
| Recovery/correction | New recovery facts and, if committed, a new receipt | History preserved; corrective version becomes current only after commit | Append new facts; never rewrite history | Complete recovery trace | Duplicate recovery blocked by transition/intent identity | Recovery creates new history |

Crash/recovery anchors: RFC-0001A (`docs/rfc/RFC-0001A-permission-matrix.md:181-198`, `docs/rfc/RFC-0001A-permission-matrix.md:718-750`), RFC-0003 (`docs/rfc/RFC-0003-event-model.md:1225-1238`), and RFC-0004 (`docs/rfc/RFC-0004-state-and-world-transition.md:951-990`).

## 6. Replay, idempotency, and traceability

* Authoritative replay uses the canonical `WORLD_COMMIT_RECEIPT` sequence and each receipt's bound inputs; rejected facts and delivery retries remain history but do not advance World (`docs/rfc/RFC-0003-event-model.md:582-624`; `docs/rfc/RFC-0004-state-and-world-transition.md:820-849`).
* Re-delivery cannot re-execute a World transition (`docs/rfc/RFC-0003-event-model.md:1049-1063`, `docs/rfc/RFC-0003-event-model.md:1231-1238`).
* Recovery and rollback append new history rather than rewriting old versions (`docs/rfc/RFC-0001-constitution.md:654-672`; `docs/rfc/RFC-0004-state-and-world-transition.md:484-502`).
* Every consequential denial, rejection, failure, conflict, and recovery path remains durable (`docs/rfc/RFC-0001-constitution.md:258-273`; `docs/rfc/RFC-0003-event-model.md:834-848`).

Result: PASS at the architecture-contract level. Physical transaction, outbox, storage, and consumer mechanisms remain intentionally unspecified until a later authorized SPEC phase.

## 7. Compatibility with accepted ADRs

### ADR-0001

PASS. The normalized RFCs preserve the sole World Kernel commit authority and do not turn storage, events, projections, or verification into state owners. This matches ADR-0001's decision and authority table (`docs/adr/ADR-0001-world-kernel.md:79-105`) and its no-partial-commit/audit invariants (`docs/adr/ADR-0001-world-kernel.md:275-324`).

### ADR-0002

PASS with a documented interpretation. ADR-0002 separates transport, durable history, and current World authority (`docs/adr/ADR-0002-event-fabric-chronicle.md:147-162`, `docs/adr/ADR-0002-event-fabric-chronicle.md:201-260`) and explicitly exposes separate logical interfaces (`docs/adr/ADR-0002-event-fabric-chronicle.md:1102-1129`). Pre-commit factual history can feed projection; post-commit successful outcome events record the resulting commit. This adds ordering precision without changing ADR-0002's accepted boundary semantics.

No accepted ADR file was modified.

## 8. Changes by Draft RFC

| RFC | Normalized contract | Key references |
|---|---|---|
| RFC-0001 | Defines pre-commit factual records, successful outcome events, constitutional Commit meaning, explicit lifecycle, failure audit | `docs/rfc/RFC-0001-constitution.md:140-154`, `:236-273`, `:326-356`, `:654-701` |
| RFC-0001A | Makes permission decision receipt durable before execution; denial and audit fail closed | `docs/rfc/RFC-0001A-permission-matrix.md:85-119`, `:181-198`, `:718-750` |
| RFC-0002 | Defines sole World authority, atomic triple, verification bindings, CAS conflict, canonical ordering | `docs/rfc/RFC-0002-world-model.md:678-690`, `:775-791`, `:824-835` |
| RFC-0003 | Separates factual records from outcome events; defines Chronicle history authority, replay, outcome delivery idempotency | `docs/rfc/RFC-0003-event-model.md:481-499`, `:578-624`, `:795-848`, `:946-968`, `:1049-1063` |
| RFC-0004 | Defines transition ownership, verification-before-commit, atomic CAS commit, rollback-as-new-version, crash recovery | `docs/rfc/RFC-0004-state-and-world-transition.md:267-285`, `:391-417`, `:534-555`, `:618-670`, `:929-990` |

All five remain `Draft v0.2.0-proposed`; RFC-0002's pre-existing v0.2.0-proposed working-tree changes were preserved.

## 9. Validation evidence

The following checks were run after each phase and again across the combined change set:

* complete per-RFC `git diff` inspection;
* `git diff --check`;
* status/version scans confirming `Status: Draft` and `v0.2.0-proposed`;
* cross-document term/invariant searches for World authority, commit, verification, Chronicle, replay, and idempotency;
* protected-file diff check for the Constitution, ADR-0001, and ADR-0002;
* Markdown fence-balance and local source-reference validation;
* changed-file secret-pattern scan.

No repository-provided documentation validator, Markdown linter, or docs build configuration was found. Validation is therefore structural and semantic, not an implementation test.

## 10. Remaining findings

### P0

None found in the audited scope.

### P1

1. **Governance/acceptance remains incomplete.** Draft normalization has not received separate owner review as RFC text and cannot be treated as Accepted. Governance reserves final authority to the owner and requires contradiction-free acceptance evidence (`docs/adr/GOVERNANCE.md:114-130`, `docs/adr/GOVERNANCE.md:170-187`).
2. **Downstream contract propagation remains pending.** RFC-0026, RFC-0027, RFC-0031, RFC-0032, and RFC-0043 will eventually need review against the normalized receipt/outbox/replay vocabulary. This audit does not modify them because the authorization explicitly stops before further RFC/SPEC work.
3. **Implementation feasibility is conceptual, not proven.** The atomic triple can be implemented with a transactional store/outbox boundary, but no SPEC, implementation, concurrency test, crash-injection test, or storage proof exists yet. Entering that work requires later authorization.

## 11. Owner decision required

The owner must separately decide whether to approve the exact Draft RFC diffs for continued RFC governance review. That decision must not be inferred from approval of Alternative A as a design direction.

Even if the owner approves these diffs, changing any RFC to Accepted, closing WM-12, or unblocking RFC Freeze requires the repository's remaining governance conditions and any required independent reviewer evidence.

## 12. Next permitted action

Present this audit and the complete Draft RFC diff to the owner for review. Do not commit, push, change RFC status, close WM-12, declare RFC Freeze complete, or enter SPEC/implementation without a separate explicit authorization.
