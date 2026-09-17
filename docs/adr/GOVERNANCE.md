VEDA ADR Governance

Version: 1.0.0

Architecture Stage: ADR_FREEZE

Status: Active

Last Updated: 2026-09-16

⸻

Purpose

This document defines how Architecture Decision Records (ADRs) are created, reviewed, approved, implemented, superseded, and archived inside the Veda project.

The goal is to ensure that architectural decisions remain stable, traceable, and auditable throughout the lifetime of the project.

⸻

Governance Principles

The ADR system follows these principles:

1. Architectural decisions are immutable after acceptance.
2. Historical decisions are never deleted.
3. Breaking architectural changes require a new ADR.
4. Implementation details belong to SPEC documents.
5. Every architectural change must be traceable.

⸻

Architecture Documentation Hierarchy

RFC
 ↓
ADR
 ↓
SPEC
 ↓
CODE
 ↓
TEST

Layer	Responsibility
RFC	Requirements, contracts, architecture proposals.
ADR	Permanent architecture decisions.
SPEC	Implementation contracts and schemas.
CODE	Executable implementation.
TEST	Verification of implementation contracts.

⸻

ADR Lifecycle

Lifecycle States

State	Meaning
Proposed	Draft decision under review.
Accepted	Official architecture decision.
Implemented	Implemented in repository code.
Deprecated	No longer recommended for new implementations.
Superseded	Replaced by another ADR.

Lifecycle Diagram

Proposed
   │
   ▼
Architecture Review
   │
   ▼
Accepted
   │
   ├──────────────► Implemented
   │
   ├──────────────► Deprecated
   │
   └──────────────► Superseded

⸻

ADR Ownership

Role	Responsibility
Repository Owner	Final architectural authority.
Architecture Owner	Maintains ADR consistency.
Human Reviewer	Reviews architectural reasoning.
AI Architecture Auditor	Reviews consistency and traceability.

Current ownership:

Role	Owner
Repository Owner	Phupha
Architecture Owner	VEDA Architecture Council
Human Reviewer	Phupha
AI Architecture Auditor	ChatGPT Architecture Auditor

⸻

ADR Review Process

Standard Workflow

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

Review Checklist

Every ADR must answer:

* Why is this decision necessary?
* What alternatives were considered?
* What are the consequences?
* What architectural invariants are introduced?
* Which RFCs require this ADR?
* Which SPECs implement this ADR?

⸻

Acceptance Criteria

An ADR may become Accepted only if:

* Context is complete.
* Decision Drivers exist.
* Alternatives are documented.
* Consequences are documented.
* Risks are documented.
* Dependencies are documented.
* Revisit conditions are documented.

⸻

Breaking Change Policy

A new ADR is REQUIRED when changing:

* World Kernel semantics.
* Chronicle ownership.
* Authorization model.
* Capability model.
* Protocol layering.
* Security boundaries.
* Federation semantics.

Implementation-only changes do not require a new ADR.

⸻

Supersession Rules

When replacing an ADR:

Old ADR:

status: Superseded
superseded_by: ADR-0018

New ADR:

status: Accepted
supersedes: ADR-0009

Historical ADRs remain inside the repository permanently.

⸻

ADR Numbering Rules

1. Numbers are immutable.
2. Numbers are never reused.
3. Numbers increase sequentially.
4. Reserved numbers remain reserved.

Example:

ADR-0011
ADR-0012
ADR-0013

⸻

ADR File Requirements

Every ADR must begin with YAML Frontmatter.

Required fields:

id:
title:
status:
owner:
created:
updated:
review_cycle:
architecture_stage:

Required sections:

* Context
* Decision Drivers
* Decision
* Alternatives Considered
* Consequences
* Risks
* Dependencies
* Revisit Conditions
* Architectural Invariants

⸻

Decision Drivers

Decision Drivers explain why the decision exists.

Priority scale:

Priority	Meaning
P0	Fundamental architecture invariant.
P1	Important architectural property.
P2	Performance, usability, maintainability.
P3	Optional optimization.

⸻

Non-Goals Policy

Every ADR must explicitly describe what it does not define.

Examples:

* Implementation details.
* Database schema.
* API payload formats.
* UI behavior.
* Programming language decisions.

These belong to SPEC documents.

⸻

Risk Register Policy

Every ADR maintains a local risk table.

Risk	Mitigation
Replay cost	Snapshot strategy
Capability drift	Capability Registry
Policy ambiguity	Constitution validation

⸻

Dependency Rules

Dependencies are classified into four categories.

Category	Meaning
Requires	Must exist first.
Constrains	Limits another decision.
Referenced By	Downstream architecture depends on it.
Implements	SPEC implementing this ADR.

⸻

Compatibility Policy

Each protocol-related ADR must specify compatibility.

Compatibility	Description
Backward Compatible	Older implementations continue working.
Forward Compatible	Older implementations ignore newer fields safely.
Breaking Change	Requires new major architecture version.

⸻

Review Cadence

Review	Trigger
Quarterly Review	Every architecture milestone.
ADR Freeze Review	Before SPEC Freeze.
Security Review	New capability or protocol.
Federation Review	New distributed architecture.

⸻

ADR Freeze Policy

Architecture enters ADR Freeze when:

* RFC Freeze is complete.
* ADR audit passes.
* Dependency graph is complete.
* Governance is complete.

During ADR Freeze:

* No new ADRs unless architecture-breaking.
* Implementation moves to SPEC.

⸻

Architecture Versioning

Architecture versions are independent from code versions.

Example:

Artifact	Version
Architecture	0.1.0
RFC Set	1.0.0
ADR Set	1.0.0
SPEC Set	0.0.0
Codebase	0.0.0

⸻

Audit Requirements

Every architecture milestone requires an audit.

Audit categories:

* Architecture consistency.
* Security boundaries.
* Dependency integrity.
* Traceability.
* Documentation completeness.

Audit output must be stored in:

docs/architecture/

⸻

Repository Invariants

These rules are repository-wide invariants.

GOV-001

Accepted ADRs are immutable.

GOV-002

Historical ADRs are never deleted.

GOV-003

Breaking architecture changes require new ADRs.

GOV-004

Implementation belongs to SPEC.

GOV-005

Every ADR maps to one or more RFCs.

GOV-006

Every SPEC maps back to one or more ADRs.

GOV-007

Every implementation maps to one or more SPECs.

GOV-008

Every architectural decision is auditable.

⸻

Exit Criteria

Governance v1.0 is complete when:

* ADR-0001 through ADR-0010 conform to this document.
* ADR README contains dependency graph.
* ADR Template exists.
* Architecture version file exists.
* SPEC layer exists.

At that point the repository enters ADR Freeze v1.0 and development proceeds to SPEC Phase.