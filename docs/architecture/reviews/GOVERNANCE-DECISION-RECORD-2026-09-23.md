# VEDA — Governance Decision Record

วันที่บันทึก: `2026-09-23`

สถานะบันทึก: **RECORDED — OWNER DECISIONS D1–D5**

## Authority and decision source

- Decision authority: **Veda Project Owner**
- Decision source: explicit Owner confirmation supplied in the current task
- Scope: record the five governance decisions below only
- This record does not amend the Constitution, Governance source, ADR source,
  RFC source, roadmap, SPEC guide, architecture version metadata or lifecycle
  status.

## Decisions

### D1 — WM-12 authority

The current WM-12 proposal is selected as the canonical WM-12 governance gate
through this Owner-approved governance decision record. This decision does not
close WM-12. Closure still requires the criteria and evidence identified by the
canonical proposal and subsequent governance review.

Controlling evidence:

- `docs/architecture/WM-12-EVENT-WORLD-COMMIT-ORDERING-PROPOSAL-2026-09-21.md:251-266`
- `docs/architecture/reviews/WM-12-GOVERNANCE-GATE-AUDIT-2026-09-21.md:17-28`

### D2 — RFC Freeze definition

RFC Freeze is to be canonicalized as a formal Governance gate. The gate must
distinguish, as separate states and decisions:

1. exact-text Owner approval;
2. RFC acceptance;
3. source commit authorization/state; and
4. RFC Freeze.

This decision authorizes a later, separately scoped Governance-source change
to define that gate. It does not itself complete or unblock RFC Freeze.

Controlling evidence:

- `docs/adr/GOVERNANCE.md:427-439`
- `docs/adr/README.md:244-249`
- `ARCHITECTURE_VERSION.yaml:1-6`

### D3 — ADR-0001 RFC traceability

The canonical mapping for ADR-0001 is:

- Primary RFC dependency: `docs/rfc/RFC-0002-world-model.md`
- Supporting RFC dependency: `docs/rfc/RFC-0004-state-and-world-transition.md`

This mapping records traceability only and does not change the semantics of
Accepted ADR-0001.

Supporting evidence:

- `docs/adr/ADR-0001-world-kernel.md:79-105`
- `docs/adr/ADR-0001-world-kernel.md:251-257`
- `docs/rfc/RFC-0002-world-model.md:135-151`
- `docs/rfc/RFC-0004-state-and-world-transition.md:269-283`

### D4 — `Status: Architecture`

`Status: Architecture` is a document classification / architecture-phase
metadata value. It is not an RFC lifecycle state and is not equivalent to
`Proposed`, `Accepted` or `Implemented`.

No RFC status is changed by this record. Existing `Status: Architecture`
metadata remains unchanged until a separately authorized normalization is
performed.

Supporting evidence:

- `docs/adr/GOVERNANCE.md:99-110`
- `docs/rfc/RFC-0039-identity.md:3`
- `docs/rfc/RFC-0043-world-delta-protocol.md:3`
- `docs/architecture/reviews/RFC-0043-OWNER-APPROVAL-RECORD-2026-09-21.md:37-49`

### D5 — SPEC numbering

The canonical SPEC mapping is:

- `SPEC-0002` = **World Kernel API**
- `SPEC-0003` = **Chronicle Event Schema**

No SPEC files are created or authorized by this record. Existing conflicting
references remain unchanged until a separately authorized documentation
normalization.

Supporting evidence:

- `docs/architecture/VEDA-ARCHITECTURE-ROADMAP.md:11-13`
- `docs/spec/SPEC-0000-IMPLEMENTATION-GUIDE.md:9-11`

## Scope restrictions

This record does **not**:

- modify any RFC source;
- modify the Architecture Constitution;
- modify `docs/adr/GOVERNANCE.md`;
- modify any accepted ADR;
- modify `ARCHITECTURE_VERSION.yaml`;
- modify `docs/adr/README.md`;
- modify the architecture roadmap or SPEC guide;
- change any lifecycle status;
- close WM-12;
- unblock RFC Freeze;
- declare ADR Freeze complete;
- authorize SPEC work; or
- authorize implementation.

RFC-0031 and RFC-0032 remain Draft with exact-text Owner approval only.
Their source commits remain unauthorized.

## Permitted follow-up, not performed here

After this record, any source correction requires its own scoped authorization,
independent review and evidence. The dependency-safe order is:

1. formalize the D1/D2 governance definitions;
2. record D3 traceability and D4 classification mapping;
3. reconcile D5 references before creating SPECs;
4. review downstream RFC propagation;
5. perform independent evidence review;
6. evaluate WM-12 closure;
7. evaluate RFC Freeze;
8. evaluate ADR Freeze.

## Current governance state

```text
RFC-0031: Draft, exact text Owner-approved
RFC-0032: Draft, exact text Owner-approved
WM-12: OPEN
RFC Freeze: BLOCKED
ADR Freeze: NOT ASSUMED COMPLETE
SPEC: NOT AUTHORIZED
Implementation: NOT AUTHORIZED
```
