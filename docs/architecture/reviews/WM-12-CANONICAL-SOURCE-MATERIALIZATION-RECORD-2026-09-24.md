# WM-12 Canonical Source Materialization Record

Record creation date: 2026-09-24 UTC

## Authority

- Decision authority: Veda Project Owner.
- Governance decision reference: [Governance Decision Record D1–D5](https://github.com/168211681/Veda/blob/d8fed35869814f8ff44bb01e4f69965a97c663b8/docs/architecture/reviews/GOVERNANCE-DECISION-RECORD-2026-09-23.md).
- This record additively records the canonical source identity after the Owner's exact-byte confirmation and separate source-materialization authorization. It does not change the historical record or close any governance gate.

## Canonical source

- Path: `docs/architecture/WM-12-EVENT-WORLD-COMMIT-ORDERING-PROPOSAL-2026-09-21.md`
- SHA-256: `c44f3d129ebbe486cda1b339483ea225c3fb765278f1328ea0ee4205df5e2bad`
- Materialization commit: [`57cce1f2d572e2547ae264a87a9a0d392a6ff933`](https://github.com/168211681/Veda/commit/57cce1f2d572e2547ae264a87a9a0d392a6ff933)
- Byte size: 38,026
- Line count: 283

The materialization commit contains only the proposal source. The committed blob was independently materialized and hashed; its SHA-256 matches the Owner-confirmed value above.

## Owner decision context

D1 selected the current WM-12 proposal as the canonical WM-12 governance gate while keeping WM-12 open. The Owner subsequently confirmed that the exact bytes identified by the SHA-256 above are the proposal selected by D1. The Owner separately authorized materializing those exact bytes in the repository.

Materialization establishes a durable source identity; it does not satisfy closure criteria or resolve WM-12. It also does not imply RFC acceptance, RFC Freeze, ADR Freeze, SPEC authorization, or implementation authorization.

## Historical chronology

The earlier records describe the proposal's state at their respective measurement baselines and remain accurate historical evidence:

1. The proposal was originally untracked.
2. The exact bytes were reviewed and recorded in the [exact-byte evidence report](https://github.com/168211681/Veda/blob/9ccd0b37515531f9ad8f710be9ada2a56197b389/docs/architecture/reviews/evidence/WM-12-CANONICAL-PROPOSAL-EXACT-BYTES-2026-09-24.md) and its [SHA-256 sidecar](https://github.com/168211681/Veda/blob/9ccd0b37515531f9ad8f710be9ada2a56197b389/docs/architecture/reviews/evidence/WM-12-CANONICAL-PROPOSAL-EXACT-BYTES-2026-09-24.sha256).
3. At that measurement, Owner exact-byte confirmation and source materialization were pending; the Owner later supplied the exact-byte confirmation and separately authorized materialization.
4. The confirmed proposal bytes were materialized in commit `57cce1f2d572e2547ae264a87a9a0d392a6ff933`.
5. D1 now has a durable canonical source identity: the path, exact SHA-256, and materialization commit recorded above.
6. WM-12 nevertheless remains OPEN.

Accordingly, historical statements that the proposal was untracked or that confirmation/materialization was pending are not current-state errors and are not rewritten by this record. The proposal's historical status text, `PROPOSED — OWNER DECISION REQUIRED`, is likewise preserved.

## Current canonical identity and change control

Future D1/D2 Governance normalization may reference the proposal by its source path, exact SHA-256, and materialization commit. Any later change to the proposal bytes requires a new Owner review and a new canonical identity decision; this record does not transfer approval to changed bytes.

## Criterion 6 compatibility

Historical WM-12 criterion 6 must be interpreted together with later Owner-approved contracts. It MUST NOT weaken the current all-or-none atomic commit triple:

1. resulting authoritative World version;
2. immutable `WORLD_COMMIT_RECEIPT`;
3. durable committed-event delivery intent.

This compatibility note does not modify the proposal.

## Current governance state

```text
WM-12: OPEN
RFC Freeze: BLOCKED
ADR Freeze: NOT ASSUMED COMPLETE
SPEC: NOT AUTHORIZED
Implementation: NOT AUTHORIZED
```

## Limitations

This record is NOT WM-12 closure, RFC acceptance, RFC Freeze completion, ADR Freeze completion, runtime verification, SPEC authorization, or implementation authorization. Materialization and exact-byte identity do not establish runtime atomicity, crash recovery, delivery reliability, or exactly-once behavior.
