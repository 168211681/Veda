# RFC-0031 Event Contract Review Plan

## Scope and baseline

- Source: `docs/rfc/RFC-0031-event-audit-trace-fabric.md`
- Source SHA-256: `a16e114d50b270857e6f9e01ff728d490576c95485627ecfee90d706d5aaa3c3`
- Status: `Draft`
- Baseline HEAD: `a2181f4c0338d40567f2670ad8a3dfdab4cff1af`
- Review mode: read-only; no RFC-0031 source edit is authorized

Controlling contracts reviewed include the Architecture Constitution, ADR Governance, accepted ADR-0001/0002, owner-approved RFC-0001 through RFC-0004, RFC-0026, RFC-0027, RFC-0029 and RFC-0043, plus the WM-12/downstream governance evidence. RFC-0032 is a read-only dependency.

## Findings

### F-31-01 — World-update authority is underspecified (P1)

- Evidence: lines 446–460 (`WorldStateUpdated` as a domain event), 522–542 (`world.update` in the trace tree), 1680–1698 (World transition trace), and 2720–2760 (reference flow `WORLD UPDATE` inside the fabric).
- Controlling contract: RFC-0002 World Kernel authority and RFC-0004 exact-parent CAS/atomic triple; RFC-0027 lines 1098–1107 and 2101–2122.
- Issue: The examples can be read as Event Fabric or an emitted event performing the modeled-World update.
- Consequence: A consumer or replay process could infer commit authority from event publication.
- Minimal correction: qualify these labels as candidate submission/commit observation; state that only World Kernel performs the authoritative CAS and that `WORLD_COMMIT_RECEIPT` is created only at that boundary.
- Owner decision: none expected if correction merely propagates approved contracts.

### F-31-02 — Event Bus consumer authority boundary is incomplete (P1)

- Evidence: lines 1248–1268 list `World Model` as an Event Bus consumer without a commit-authority restriction; lines 242–256 define the fabric as creating and processing events.
- Controlling contract: RFC-0002 and RFC-0003; RFC-0027 lines 1093–1096.
- Issue: The consumer list does not distinguish projection/observation from authoritative World commit.
- Consequence: A downstream consumer could treat delivery or projection as a commit operation.
- Minimal correction: state that World Model consumers may project or request candidates only; World Kernel alone commits current modeled World State.
- Owner decision: none expected.

### F-31-03 — Canonical commit ordering and atomic triple are not carried into all pipelines (P1)

- Evidence: lines 446–460, 522–542, 1010–1050 (recovery trace), 1680–1698 (World transition trace), and 2720–2760 (reference flow) use `Verification → World`/`World Update` without explicit exact-parent check, atomic triple, or post-commit delivery.
- Controlling contract: RFC-0004 and RFC-0027 lines 645–653, 1098–1107, 1655–1666.
- Issue: Diagrams and lifecycle examples are less precise than the approved canonical ordering.
- Consequence: A reader may emit a successful outcome before commit or treat verification as commitment.
- Minimal correction: normalize relevant diagrams to `Execution → Observation → Evidence → Verification → VerificationReceipt → exact-parent check → World Kernel CAS atomic commit (World version + WORLD_COMMIT_RECEIPT + delivery intent) → post-commit outcome delivery`.
- Owner decision: none expected.

### F-31-04 — Delivery modes and exactly-once wording need authority qualification (P1)

- Evidence: lines 1272–1288 permit `EXACTLY_ONCE_SEMANTICS` and propose idempotency/deduplication, while no explicit distinction is made between delivery semantics, consumer idempotency and external effects.
- Controlling contract: RFC-0003/RFC-0004 and RFC-0029 external-effect reconciliation; RFC-0029 approved contract prohibits unsupported external exactly-once claims.
- Issue: The wording may be read as an external exactly-once guarantee.
- Consequence: Duplicate external effects or unsafe retry assumptions.
- Minimal correction: limit exactly-once language to scoped delivery/consumer semantics and stable event identities; explicitly exclude external-effect exactly-once without an external-system guarantee.
- Owner decision: required only if a stronger exactly-once protocol is proposed; propagation alone needs none.

### F-31-05 — Event object lacks explicit canonical commit lineage (P1)

- Evidence: lines 262–295 include `world_version`, `evidence_refs` and `verification_refs`, but no explicit candidate transition hash, parent World version or `WORLD_COMMIT_RECEIPT` linkage field.
- Controlling contract: RFC-0004/RFC-0026/RFC-0027 exact-parent receipt binding and atomic commit receipt.
- Issue: Event traceability is not sufficient to distinguish factual records from authoritative committed outcomes.
- Consequence: Replay/reconstruction may promote a verification or event into a World commit.
- Minimal correction: add optional, semantically separated Veda-owned lineage references for candidate hash, parent version, VerificationReceipt and WORLD_COMMIT_RECEIPT, with conditional presence and no provider-owned requirements.
- Owner decision: none expected if fields remain linkage metadata, not new authority.

### F-31-06 — Replay and reconstruction need canonical-receipt constraints (P1)

- Evidence: lines 2077–2107 prohibit automatic external replay, but lines 1790–1815 (World Reconstruction) describe `initial_snapshot + validated world events` without requiring canonical commit receipts.
- Controlling contract: RFC-0032 Chronicle and RFC-0002/RFC-0004 commit receipts.
- Issue: “validated world events” is broader than authoritative commit evidence.
- Consequence: Historical factual events could be replayed as World mutations.
- Minimal correction: reconstruct authoritative World only from valid commit receipts and bound inputs; replay factual events as history/projection and never execute effects automatically.
- Owner decision: none expected.

### F-31-07 — Failure and recovery facts are not uniformly tied to post-commit outcome gating (P1)

- Evidence: lines 1360–1415 define audit/storage failure and buffering; lines 1010–1050 define recovery trace ending in `Recovered`, but do not state that failed/UNKNOWN/recovery-required records cannot become successful modeled-World outcomes.
- Controlling contract: RFC-0027 lines 1093–1107 and 1552–1559; RFC-0029 UNKNOWN/reconciliation contract.
- Issue: Durable factual recording is defined, but outcome gating is not explicit across all failure paths.
- Consequence: Recovery or delivery failure could be reported as successful completion.
- Minimal correction: require truthful durable failure/UNKNOWN/recovery-required records and reserve successful modeled-World outcome events for post-commit paths.
- Owner decision: none expected.

### F-31-08 — Existing traceability contracts are otherwise compatible (P2 clarification)

- Evidence: lines 299–332 (identity/versioning), 653–667 (deduplication), 850–891 (append-only correction), 102–106 (event validation), 2266–2288 (Chronicle handoff).
- Assessment: These provisions are compatible with the approved contracts and should be preserved.
- Minimal correction: add cross-references to the canonical commit/receipt rules rather than redesigning Event Fabric.
- Owner decision: none.

No P0 contradiction was found in the current Draft. The P1 findings are propagation/clarification requirements before downstream acceptance or RFC Freeze. Runtime atomicity, crash recovery, delivery reliability and external reconciliation remain unproven implementation assumptions.

## Minimal correction plan

1. Clarify authority and terminology in Sections 15, 19, 44, 54 and 135.
2. Add canonical lifecycle and atomic-triple language to Sections 41–44 and the reference flow.
3. Separate delivery intent, delivery attempts, acknowledgements, redelivery and consumer application in Sections 55, 58–60 and 110.
4. Add conditional Veda-owned receipt-lineage fields to the Event object without requiring external providers to know them.
5. Constrain World Reconstruction and replay to canonical commit receipts and non-effectful factual replay.
6. Add failure/UNKNOWN/recovery-required outcome gating and preserve append-only evidence.
7. Update conformance invariants and diagrams; preserve existing deduplication, retention, security, privacy, redaction and Chronicle handoff semantics.

## Future document-level acceptance criteria

1. Pre-commit factual records remain truthful (RFC-0003/RFC-0027 §37).
2. Factual records cannot claim modeled-World commitment (RFC-0002/RFC-0027 §37).
3. Successful outcomes require a successful World Kernel commit (RFC-0004/RFC-0027 §37).
4. World Kernel alone owns modeled-World commit (RFC-0002; RFC-0027 lines 1098–1107).
5. Exact-parent applicability is explicit (RFC-0026/RFC-0027 lines 809–814).
6. Atomic triple is one CAS boundary (RFC-0004/RFC-0027 lines 1098–1107).
7. Delivery intent is inside that boundary (RFC-0003/RFC-0027 lines 1101–1106).
8. Actual delivery follows the boundary (RFC-0027 lines 1105–1107).
9. Event transport has no World commit authority (RFC-0003/RFC-0027 lines 1093–1096).
10. Chronicle has historical authority only (RFC-0032; RFC-0027 lines 1093–1096).
11. Duplicate delivery cannot create a new World commit (RFC-0004/RFC-0027 atomic receipt identity).
12. Consumer idempotency is not external exactly-once (RFC-0029 reconciliation contract).
13. UNKNOWN effects require reconciliation (RFC-0029; RFC-0027 lines 816–820).
14. Failed delivery/recovery evidence is durable and truthful (RFC-0027 lines 1552–1559).
15. Receipt lineage is non-circular and conditional (RFC-0026/RFC-0027 RecoveryReceipt contract).
16. Existing Event Fabric identity, schema, deduplication and append-only contracts remain intact (RFC-0031 lines 299–332, 653–667, 850–891).
17. Diagrams and normative prose agree on ordering and authority (RFC-0031 diagrams; RFC-0004/RFC-0027).
18. No runtime guarantee is claimed without evidence (governance audits and RFC-0027 runtime limitation).

## RFC-0032 dependency impact

RFC-0032 must treat Event Fabric records as historical inputs, not commit authority; reconstruct current World only from canonical committed receipts and bound inputs; preserve pre-commit factual recovery/delivery history; and keep Chronicle projection separate from actual delivery and World Kernel commit. No RFC-0032 source change is authorized by this plan.

## Governance gate matrix

| Gate | Status | Evidence / missing condition | Authority / next action |
|---|---|---|---|
| RFC-0027 exact-text approval | PASS | Owner approval record for exact `cc36…a118d4` bytes | Owner/governance may continue review |
| RFC-0031 source review | BLOCKED | P1 findings F-31-01..07 require correction plan execution and independent review | Owner must authorize any source patch |
| RFC-0031 status | PASS | Remains Draft | No acceptance implied |
| Downstream consistency | BLOCKED | RFC-0032 propagation review remains | Governance review |
| WM-12 closure | BLOCKED | Cross-document evidence and gates incomplete | Owner/governance decision |
| RFC Freeze | BLOCKED | Drafts and P1 propagation remain | Governance authority |
| SPEC authorization | BLOCKED | Freeze/acceptance not complete | Owner/governance authority |
| Implementation authorization | BLOCKED | SPEC not authorized and runtime proof absent | Owner/governance authority |

## Outstanding owner decisions

- Authorize or reject a scoped RFC-0031 source correction for F-31-01..07.
- If stronger exactly-once semantics or any new receipt protocol is desired, make a separate owner architectural decision.
- Decide the downstream RFC-0032 propagation order after RFC-0031 correction.

## Exact next permitted action

Independent review of this plan, followed by explicit owner authorization before editing RFC-0031. This document does not authorize source modification, acceptance, WM-12 closure, RFC Freeze, SPEC or implementation.
