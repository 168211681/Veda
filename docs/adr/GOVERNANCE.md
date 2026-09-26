Veda ADR Governance

Version: 1.1.0

Architecture Stage: ADR_FREEZE

Status: Active

Last Updated: 2026-09-17

⸻

Purpose

This document defines how Architecture Decision Records (ADRs) are created, reviewed, accepted, implemented, superseded, and audited in the Veda project.

The ADR system exists to ensure that architectural decisions remain stable, traceable, consistent, and auditable throughout the lifetime of Veda.

⸻

Authority and Documentation Hierarchy

The Architecture Constitution is the highest architectural authority.

No RFC, ADR, SPEC, implementation, agent, model, tool, or external system may override the Architecture Constitution.

The canonical documentation hierarchy is:

ARCHITECTURE CONSTITUTION
        ↓
       RFC
        ↓
       ADR
        ↓
      SPEC
        ↓
 IMPLEMENTATION
        ↓
       TEST

Layer Responsibilities

Layer	Responsibility
Constitution	Highest-level architectural invariants, authority boundaries, and governing principles.
RFC	Requirements, architecture proposals, scope, and contracts under consideration.
ADR	Permanent record of accepted architectural decisions and their rationale.
SPEC	Implementation contracts, schemas, interfaces, and detailed technical behavior.
Implementation	Executable system behavior.
Test	Verification evidence for implementation and specified behavior.

Tests verify implementation. They do not define architectural authority.

⸻

Governance Principles

The Veda ADR system follows these principles:

1. The Architecture Constitution is the highest architectural authority.
2. Capability does not imply authority.
3. Knowledge does not imply authority.
4. Intelligence does not imply authority.
5. Intent does not imply authority.
6. Human authority remains ultimate.
7. The Brain is non-authoritative.
8. Authoritative current World State belongs to the World Kernel.
9. Consequential execution must pass through the defined authorization and execution controls.
10. Accepted architectural decisions are immutable in meaning.
11. Historical decisions are never deleted.
12. Breaking architectural changes require a new ADR.
13. Implementation details belong in SPEC documents.
14. Every architectural decision must be traceable.
15. Every meaningful consequential action must remain observable and auditable.
16. No architecture phase may proceed without validation of the current phase.

⸻

ADR Lifecycle

The canonical ADR lifecycle is:

Proposed
   ↓
Architecture Review
   ↓
Accepted
   ↓
Implementation
   ↓
Architecture Audit

An accepted ADR may later become:

Accepted
   ├──→ Implemented
   ├──→ Deprecated
   └──→ Superseded

Lifecycle States

State	Meaning
Proposed	Draft decision under review.
Accepted	Official architectural decision.
Implemented	The accepted decision has corresponding implementation.
Deprecated	Historical decision remains valid but is no longer recommended for new work.
Superseded	Decision has been replaced by a newer ADR.

Implemented does not modify the architectural decision.

The accepted decision remains historically immutable.

⸻

ADR Ownership

Role	Responsibility
Repository Owner	Final human architectural authority.
Architecture Owner	Maintains architectural consistency.
Human Reviewer	Reviews architectural reasoning and acceptance.
AI Architecture Auditor	Reviews consistency, traceability, completeness, and contradictions.

Current ownership:

Role	Owner
Repository Owner	Phupha
Architecture Owner	VEDA Architecture Council
Human Reviewer	Phupha
AI Architecture Auditor	ChatGPT Architecture Auditor

AI review does not replace human architectural authority.

⸻

ADR Review Process

The standard workflow is:

Idea
 ↓
RFC
 ↓
ADR Draft
 ↓
Architecture Review
 ↓
Accepted
 ↓
SPEC
 ↓
Implementation
 ↓
Architecture Audit

Every ADR review must establish:

* why the decision is necessary;
* which requirements or RFCs it satisfies;
* which alternatives were considered;
* consequences of the decision;
* risks and mitigations;
* dependencies;
* architectural invariants;
* revisit conditions;
* traceability to upstream and downstream artifacts;
* consistency with the Architecture Constitution;
* consistency with accepted ADRs.

⸻

Acceptance Criteria

An ADR may become Accepted only when:

* required YAML frontmatter is complete;
* Context is complete;
* Decision Drivers are explicit;
* Non-Goals are explicit;
* Decision is unambiguous;
* Alternatives are documented;
* Consequences are documented;
* Risks and mitigations are documented;
* Dependencies are documented;
* Revisit Conditions are documented;
* Architectural Invariants are explicit;
* Traceability is documented;
* no unresolved contradiction with the Constitution exists;
* no unresolved contradiction with an accepted ADR exists.

⸻

Mandatory ADR Structure

Every ADR must contain YAML frontmatter.

Required fields:

id:
title:
status:
owner:
created:
updated:
review_cycle:
architecture_stage:

Optional supersession fields:

supersedes:
superseded_by:

Every ADR must contain the following sections in this order:

# Context
# Decision Drivers
# Non-Goals
# Decision
# Alternatives Considered
# Consequences
# Risks
# Dependencies
# Revisit Conditions
# Architectural Invariants
# Traceability

The canonical ADR must contain architectural decision content only.

The following must not appear in canonical ADRs:

* Patch Instructions
* editor instructions
* temporary implementation notes
* copy/paste instructions
* AI prompts
* procedural instructions unrelated to the decision itself

⸻

Decision Drivers

Decision Drivers explain why a particular architectural decision is required.

Priority levels:

Priority	Meaning
P0	Fundamental architecture invariant, authority boundary, or safety property.
P1	Important architectural property.
P2	Performance, usability, maintainability, or operational concern.
P3	Optional optimization.

⸻

Non-Goals Policy

Every ADR must explicitly state what it does not define.

Typical non-goals include:

* database schema;
* API payload details;
* UI behavior;
* programming language selection;
* framework selection;
* implementation-specific algorithms;
* deployment-specific configuration.

These belong in SPEC or implementation documentation unless they represent an independent architectural decision.

⸻

Risk Register Policy

Every ADR must maintain a local risk register.

The risk register must identify:

* the risk;
* its impact;
* its mitigation or control.

Example:

Risk	Impact	Mitigation
Replay cost	High	Snapshot strategy
Capability drift	High	Capability Registry
Policy ambiguity	High	Constitution validation

⸻

Dependency Rules

ADR dependencies are classified as:

Category	Meaning
Requires	Artifact must exist before this decision can function.
Constrains	This decision limits another architectural decision.
Referenced By	Downstream architecture depends on this decision.
Implements	SPEC or implementation realizes this decision.

Dependencies should use stable identifiers whenever available.

Examples:

RFC-0001
ADR-0001
ADR-0002
SPEC-0001

⸻

Traceability Rules

Veda architecture must maintain bidirectional traceability.

The required relationship is:

Constitution
     ↓
    RFC
     ↓
    ADR
     ↓
   SPEC
     ↓
Implementation
     ↓
   Tests

Rules:

1. Every ADR maps to one or more RFCs.
2. Every SPEC maps to one or more ADRs.
3. Every implementation maps to one or more SPECs.
4. Tests verify implementation and SPEC behavior.
5. Architectural authority must trace back to the Constitution.
6. Traceability must not silently bypass an architectural layer.
7. Missing traceability is a governance failure.

⸻

Breaking Change Policy

A new ADR is required when changing architectural semantics including:

* World Kernel authority;
* World State ownership;
* Chronicle ownership;
* event semantics;
* authorization model;
* capability model;
* execution-control semantics;
* Brain authority boundaries;
* protocol layering;
* security boundaries;
* federation semantics;
* persistence ownership;
* authority boundaries between Veda subsystems.

Implementation-only changes do not require a new ADR when they preserve all existing architectural contracts.

⸻

Supersession Rules

Architectural decisions are never silently replaced.

When an ADR is superseded, the old ADR remains in the repository.

Example:

# Old ADR
status: Superseded
superseded_by: ADR-0018

The new ADR contains:

# New ADR
status: Accepted
supersedes: ADR-0009

The new ADR must explain:

* what changed;
* why it changed;
* which previous decision is replaced;
* migration or compatibility implications.

⸻

ADR Numbering Rules

1. ADR numbers are immutable.
2. ADR numbers are never reused.
3. Numbers increase sequentially.
4. Reserved numbers remain reserved.
5. Deleted ADR numbers must never be reused.

Examples:

ADR-0011
ADR-0012
ADR-0013

⸻

Compatibility Policy

Protocol-related ADRs must document compatibility.

Compatibility	Meaning
Backward Compatible	Older implementations continue working.
Forward Compatible	Older implementations safely tolerate newer additions.
Breaking Change	Existing architectural contracts require migration or replacement.

⸻

Review Cadence

Review	Trigger
Milestone Review	Every major architecture milestone.
ADR Freeze Review	Before entering SPEC Freeze.
Security Review	New capability, authority, or protocol boundary.
Federation Review	New distributed or cross-node architecture.
Constitutional Review	Any change affecting constitutional principles.

⸻

RFC Governance Gates

RFC governance records must distinguish these separate decisions and facts:

1. Exact-text Owner approval;
2. RFC lifecycle acceptance;
3. RFC source commit authorization and commit state; and
4. RFC Freeze.

None of these decisions or facts is implied automatically by another. Exact-text Owner approval applies only to the identified exact bytes and approved scope. Changed bytes require renewed review under the applicable process. RFC acceptance is a lifecycle/governance decision and does not authorize committing the RFC source. Source commit authorization must be granted separately. A committed RFC is not thereby Accepted or Frozen. RFC Freeze does not silently alter any RFC lifecycle state. `Status: Architecture` is document classification / architecture-phase metadata, not RFC lifecycle acceptance. Audit records must keep Draft status, exact-text approval, source commit authorization/state, and freeze status distinguishable.

WM-12 Governance Gate

The canonical WM-12 governance gate is the proposal selected by Owner Decision D1:

- Source: `docs/architecture/WM-12-EVENT-WORLD-COMMIT-ORDERING-PROPOSAL-2026-09-21.md`
- SHA-256: `c44f3d129ebbe486cda1b339483ea225c3fb765278f1328ea0ee4205df5e2bad`
- Materialization commit: `57cce1f2d572e2547ae264a87a9a0d392a6ff933`
- Owner decision: `docs/architecture/reviews/GOVERNANCE-DECISION-RECORD-2026-09-23.md`
- Canonical source materialization record: `docs/architecture/reviews/WM-12-CANONICAL-SOURCE-MATERIALIZATION-RECORD-2026-09-24.md`

The proposal's twelve closure criteria are incorporated as the WM-12 closure criteria:

1. The Owner explicitly selects an ordering alternative or approves a corrected canonical ordering.
2. The proposal's eight key terms are defined without ambiguous use of `commit`.
3. RFC-0001, RFC-0001A, RFC-0002, RFC-0003, and RFC-0004 state one normative ordering.
4. The World Kernel remains the sole authority that commits authoritative modeled World State.
5. Required verification has a durable reference before World commitment.
6. The resulting World version, immutable `WORLD_COMMIT_RECEIPT`, and durable committed-event delivery intent share one all-or-none atomic visibility boundary.
7. Consequential rejection and failure paths have a mandatory durable audit receipt.
8. Replay, idempotency, conflict, and crash-recovery requirements pass cross-document audit.
9. Accepted ADR-0001 and ADR-0002 semantics remain unchanged, or a new ADR is approved through its applicable process and clearly supersedes affected semantics.
10. The Owner confirms the proposal's interpretation of the constitutional post-commit `Event → Chronicle` relationship, or a separately governed Constitutional amendment resolves it.
11. Governance validation finds no unresolved P0 ordering contradiction.
12. RFC lifecycle changes occur only through each RFC's applicable approval process; RFC-0002 remains Draft until that process is complete.

Criterion 6 is interpreted together with later Owner-approved contracts and MUST NOT weaken the three-part atomic boundary stated above. This Governance section references and incorporates the canonical proposal; it does not create a competing WM-12 definition or amend the proposal. The proposal's historical status wording, including `PROPOSED — OWNER DECISION REQUIRED`, remains historical. Later Owner records control current governance state; historical artifacts must not be rewritten merely to appear current. The Owner is the final authority for WM-12 closure. Audits and validation provide evidence only and cannot close the gate.

WM-12 gate statuses are:

WM-12 remains `OPEN` unless and until an explicit closure decision is recorded under this gate.

- `OPEN`: one or more closure requirements or required evidence remain outstanding.
- `READY_FOR_REVIEW`: the required evidence package appears complete enough for Owner/governance closure review; this is not closure.
- `CLOSED`: the Owner/governance decision explicitly records closure after review of every criterion and its evidence.

The proposal's historical `RESOLVED` wording denotes that its criteria have been addressed; it does not itself transition this governance gate. A gate transition to `CLOSED` requires the explicit closure record described above. No audit result, source commit, RFC lifecycle change, exact-text approval, metadata change, or RFC Freeze decision may automatically close WM-12. WM-12 closure does not automatically complete RFC Freeze. RFC Freeze is not a prerequisite for WM-12 closure.

RFC Freeze Gate

RFC Freeze is a separate architecture governance milestone with these statuses:

RFC Freeze remains `BLOCKED` unless and until an explicit decision changes its gate status under this section.

- `BLOCKED`: one or more applicable prerequisites below remain unmet or lack required evidence.
- `READY_FOR_REVIEW`: evidence is assembled for an explicit Owner/governance freeze decision; this is not completion.
- `COMPLETE`: an explicit Owner/governance decision records that all applicable prerequisites passed.

RFC Freeze review MUST verify and record:

1. The RFC inventory in scope is identified.
2. Document classification and RFC lifecycle state are distinguishable.
3. Exact-text approval state is recorded where applicable.
4. Source commit authorization and source commit state are recorded separately.
5. Required RFC acceptance decisions are recorded through the applicable process.
6. RFC source identities are stable and auditable.
7. Consistency with the Architecture Constitution.
8. Consistency with accepted ADRs.
9. Cross-RFC semantic consistency.
10. Dependency integrity.
11. Required traceability.
12. WM-12 is `CLOSED` under the WM-12 Governance Gate above.
13. No unresolved P0 contradiction applicable to the freeze remains.
14. Gate-blocking P1 findings that do not identify an unresolved contradiction with the Architecture Constitution, an accepted ADR, or another controlling normative contract must be resolved, demonstrated to be inapplicable with evidence, or accepted as residual risk through an authorized governance decision. Any P1 finding identifying such an unresolved normative contradiction must be resolved through the applicable governance process before RFC Freeze may be marked COMPLETE.
15. Required architecture milestone audit and validation evidence exists.
16. Exceptions and missing evidence are recorded truthfully.

P2 findings are not required to be closed by this gate. This gate does not add an implementation or runtime-proof requirement. Only an explicit Owner/governance decision based on recorded validation may mark RFC Freeze `COMPLETE`. Exact-text approval, `Accepted` status alone, source commit, an audit `PASS` alone, metadata, or an architecture-phase label MUST NOT be treated as RFC Freeze completion.

RFC Freeze completion does not itself accept Draft RFCs, authorize RFC source commits, complete ADR Freeze, authorize SPEC, authorize implementation, or prove runtime behavior. ADR Freeze remains governed by its existing gate and prerequisites. RFC Freeze completion is only the RFC Freeze prerequisite evaluated by that separate ADR Freeze Gate; it does not satisfy the other ADR Freeze criteria or transition ADR Freeze automatically.

⸻

ADR Freeze Gate

For this gate, `RFC Freeze is complete` means the RFC Freeze Gate above has status `COMPLETE`.

Veda may enter ADR Freeze only when:

* RFC Freeze is complete;
* ADR-0001 through the current ADR set pass structural audit;
* ADR semantics are consistent with the Constitution;
* ADR dependencies are complete;
* ADR traceability is complete;
* ADR Index is complete;
* Architecture Decision Graph is complete;
* Governance documents are internally consistent;
* no unresolved P0 architectural contradiction exists.

ADR Freeze does not eliminate validation.

Before entering every new architecture phase:

AUDIT CURRENT PHASE
        ↓
VALIDATION PASS
        ↓
NEXT PHASE

Never:

CURRENT PHASE
     ↓
NEXT PHASE

without validation.

Mandatory Workflow Rule

NO NEXT PHASE WITHOUT VALIDATION OF THE CURRENT PHASE.

⸻

Audit Requirements

Every architecture milestone requires an audit covering:

* Constitution consistency;
* ADR structural compliance;
* semantic consistency;
* authority boundaries;
* security boundaries;
* dependency integrity;
* traceability;
* documentation completeness;
* contradiction detection.

Audit outputs must be stored under:

docs/architecture/

⸻

Repository Invariants

GOV-001

Accepted ADR meaning is immutable.

GOV-002

Historical ADRs are never deleted.

GOV-003

Breaking architectural changes require new ADRs.

GOV-004

Implementation details belong to SPEC.

GOV-005

Every ADR maps to one or more RFCs.

GOV-006

Every SPEC maps to one or more ADRs.

GOV-007

Every implementation maps to one or more SPECs.

GOV-008

Every architectural decision is auditable.

GOV-009

The Architecture Constitution is the highest architectural authority.

GOV-010

AI reasoning cannot grant itself architectural or operational authority.

GOV-011

Consequential Veda actions must be observable and auditable.

GOV-012

No architecture phase may proceed without validation of the current phase.

⸻

Exit Criteria for ADR Freeze v1.0

The repository may proceed to SPEC Freeze only after:

* ADR-0001 through ADR-0010 conform to this governance;
* ADR Template conforms to this governance;
* ADR Style Guide conforms to this governance;
* ADR Index contains the canonical dependency and traceability map;
* Architecture Decision Graph represents actual architectural dependencies;
* Architecture version metadata exists;
* Constitution Traceability passes;
* Governance Validation passes;
* ADR Freeze Gate passes.

Until all gates pass, the SPEC layer must not be treated as architecturally frozen.
