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

ADR Freeze Gate

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