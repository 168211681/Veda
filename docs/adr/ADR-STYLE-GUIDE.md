ADR Style Guide

Status: Active
Version: 1.1.0
Architecture Stage: ADR_FREEZE
Authority: Derived from docs/architecture/ARCHITECTURE_CONSTITUTION.md and docs/adr/GOVERNANCE.md

⸻

1. Purpose

This document defines the writing, structure, metadata, and review conventions for all Architecture Decision Records (ADRs) in Veda.

The purpose of this guide is to ensure that ADRs are:

* structurally consistent
* architecturally meaningful
* traceable to higher-level requirements and principles
* explicit about trade-offs and consequences
* auditable by humans and automated systems
* durable as historical architectural records

An ADR is an architectural decision record, not an implementation task list.

⸻

2. Authority

ADR documents MUST conform to the following authority hierarchy:

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

Higher layers constrain lower layers.

An ADR MUST NOT contradict the Architecture Constitution.

An ADR MUST NOT redefine constitutional authority unless the constitutional change process has explicitly been completed.

⸻

3. ADR Purpose and Scope

An ADR SHOULD document decisions that materially affect one or more of the following:

* system architecture
* authority boundaries
* ownership of authoritative state
* trust boundaries
* execution control
* persistence
* protocol boundaries
* security architecture
* observability
* recovery behavior
* model/provider architecture
* knowledge architecture
* interoperability
* long-term system constraints

Implementation-level details SHOULD NOT be placed in an ADR unless they are necessary to explain the architectural decision.

⸻

4. Required File Metadata

Every canonical ADR MUST contain YAML frontmatter with these fields:

---
id: ADR-XXXX
title: Decision Title
status: Proposed
owner: Phupha
created: YYYY-MM-DD
updated: YYYY-MM-DD
review_cycle: Quarterly
architecture_stage: ADR_DRAFT
---

Required fields

Field	Requirement
id	MUST uniquely identify the ADR
title	MUST describe the architectural decision
status	MUST use an approved lifecycle status
owner	MUST identify the accountable owner
created	MUST contain the creation date
updated	MUST contain the latest modification date
review_cycle	MUST define the review cadence
architecture_stage	MUST identify the ADR lifecycle stage

Metadata MUST describe the document’s current state accurately.

⸻

5. Mandatory Section Order

Every canonical ADR MUST contain these sections in this order:

1. Context
2. Decision Drivers
3. Non-Goals
4. Decision
5. Alternatives Considered
6. Consequences
7. Risks
8. Dependencies
9. Revisit Conditions
10. Architectural Invariants
11. Traceability

Additional sections MAY be added when necessary, but they MUST NOT obscure or replace the mandatory sections.

⸻

6. Context

The Context section explains:

* the architectural problem
* the system conditions that created the problem
* relevant constraints
* existing architectural relationships
* why a decision is necessary

Context MUST describe the problem without prematurely arguing for the chosen solution.

Avoid implementation instructions.

⸻

7. Decision Drivers

Decision Drivers identify the criteria that materially influenced the decision.

Typical drivers include:

* security
* authority separation
* correctness
* auditability
* reliability
* recoverability
* performance
* scalability
* interoperability
* maintainability
* cost
* simplicity
* replaceability

Drivers SHOULD be stated independently from the final decision.

⸻

8. Non-Goals

Non-Goals explicitly define what the ADR does NOT attempt to solve.

This section is important because architectural documents otherwise have a nasty habit of expanding until they become a second operating system.

Non-Goals MAY include:

* implementation details
* unrelated subsystem behavior
* future optimization
* product/UI decisions
* deployment-specific decisions
* unresolved questions intentionally deferred to another ADR or SPEC

A Non-Goal MUST NOT contradict the Decision.

⸻

9. Decision

The Decision section contains the authoritative architectural choice.

It MUST clearly state:

* what is being decided
* what component owns the responsibility
* what boundaries are established
* what behavior is required
* what behavior is prohibited

The Decision MUST be testable at the architectural level.

Avoid vague statements such as:

The system should be robust.

Prefer explicit architectural rules such as:

Only the World Kernel may commit authoritative current World State.

⸻

10. Alternatives Considered

Every ADR SHOULD identify meaningful alternatives that were considered.

For each alternative, document:

* the alternative
* why it was considered
* relevant advantages
* relevant disadvantages
* why it was not selected

Alternatives MUST be evaluated against the documented Decision Drivers.

Do not fabricate alternatives merely to satisfy the section requirement.

⸻

11. Consequences

Consequences document expected results of the decision.

Separate them into:

Positive

Benefits or capabilities created by the decision.

Negative

Costs, limitations, complexity, or constraints introduced by the decision.

Consequences SHOULD describe architectural effects rather than implementation tickets.

⸻

12. Risks

Risks MUST identify credible failure modes or architectural hazards.

Recommended format:

Risk	Impact	Mitigation
Example risk	High/Medium/Low	Mitigation strategy

Risks SHOULD be specific enough to be reviewed later.

Do not convert every inconvenience into a “risk.” Humanity already has enough paperwork.

⸻

13. Dependencies

Dependencies describe architectural relationships with other decisions or system layers.

Use the following categories where applicable:

* Requires
* Constrains
* Referenced By
* Implements

Example:

Requires:
- ADR-0001 World Kernel
Constrains:
- ADR-0004 Brain Non-Authority
Referenced By:
- ADR-0010 MVP Vertical Slice

Dependencies MUST NOT be used as a substitute for Traceability.

⸻

14. Revisit Conditions

This section defines objective conditions under which the decision should be reconsidered.

Examples:

* a constitutional constraint changes
* a required capability becomes technically infeasible
* a security assumption becomes invalid
* a dependency is removed
* a major scalability boundary is exceeded
* a new architectural requirement conflicts with the decision

A revisit condition does NOT automatically invalidate an accepted ADR.

It creates a trigger for architectural review.

⸻

15. Architectural Invariants

Architectural Invariants are rules that MUST remain true if the ADR is implemented.

Examples:

Only the authoritative owner may commit authoritative state.
AI-generated output is untrusted until validated.
Consequential actions MUST be observable and auditable.
Capability does not imply authority.

Invariants SHOULD be:

* explicit
* testable
* architecture-level
* stable over time

Implementation details SHOULD NOT be disguised as invariants.

⸻

16. Traceability

Every ADR MUST identify its relationship to higher and lower architectural artifacts where applicable.

Recommended structure:

Constitution:
- <constitutional principle>
RFC:
- <related RFC>
ADR:
- <related ADRs>
SPEC:
- <downstream specification>
Implementation:
- <implementation area, if known>

Traceability establishes architectural lineage.

An ADR MUST NOT claim traceability to an artifact that does not exist.

⸻

17. Lifecycle Status

Approved lifecycle statuses are:

Proposed
Under Review
Accepted
Implemented
Deprecated
Superseded

Proposed

The decision is being drafted and has not been accepted.

Under Review

The decision is undergoing architectural review.

Accepted

The architectural decision has been formally accepted.

Implemented

The accepted decision has been implemented.

Deprecated

The decision remains historical but should no longer be used for new architectural work.

Superseded

A newer ADR has replaced the decision.

A superseded ADR MUST remain in the repository as historical record.

⸻

18. Architecture Stage

architecture_stage identifies the document’s governance stage.

Expected values include:

ADR_DRAFT
ADR_REVIEW
ADR_ACCEPTED
ADR_FREEZE

The stage MUST reflect the actual governance state.

An ADR MUST NOT be marked ADR_FREEZE merely because its content has been written.

Freeze status requires the applicable governance validation and freeze gate to have passed.

⸻

19. Supersession Rules

When an ADR is superseded:

1. The original ADR MUST NOT be deleted.
2. Its status MUST become Superseded.
3. The replacement ADR MUST identify the superseded ADR.
4. The superseding ADR SHOULD explain the reason for the change.
5. The dependency and decision graph MUST be updated.
6. Traceability MUST remain intact.

Architectural history is immutable unless the governance process explicitly provides otherwise.

⸻

20. Patch Instructions Are Prohibited

Canonical ADRs MUST NOT contain sections such as:

# Patch Instructions
# Instructions to Agent
# Apply This Patch
# Copy This Content
# Implementation Prompt

These are workflow instructions, not architectural decisions.

Temporary editing instructions MUST be removed before an ADR becomes canonical.

If implementation work is required, record it in the appropriate SPEC, issue, task, or implementation artifact.

⸻

21. Implementation Boundary

ADR content SHOULD answer:

What architectural decision is being made, and why?

SPEC content SHOULD answer:

How will the architectural decision be implemented?

Code SHOULD answer:

What exact behavior does the implementation execute?

Tests SHOULD answer:

Does the implementation satisfy the defined requirements and invariants?

Do not collapse these layers into one document.

⸻

22. Writing Rules

ADR writing SHOULD be:

* precise
* declarative
* technically explicit
* concise where possible
* evidence-based
* free from marketing language
* free from unnecessary speculation

Avoid:

* vague architectural claims
* undocumented assumptions
* emotional language
* product slogans
* implementation instructions
* temporary notes
* duplicated requirements
* unexplained terminology

Use consistent names for architectural components.

For example:

World Kernel
Event Fabric
Chronicle
Brain
Execution Control Plane
Knowledge
Memory
Books & Knowledge Library

Names SHOULD match the canonical architecture documents.

⸻

23. Authority and Trust Language

ADR authors MUST preserve the distinction between:

Capability
Authority
Knowledge
Intelligence
Intent

These concepts MUST NOT be treated as interchangeable.

In particular:

Capability ≠ Authority
Knowledge ≠ Authority
Intelligence ≠ Authority
Intent ≠ Authority

AI components MUST NOT be described as authoritative merely because they possess:

* reasoning capability
* planning capability
* model intelligence
* knowledge
* tool access
* execution capability

Authority MUST be explicitly granted by the architecture.

⸻

24. Observability Requirement

Architectural decisions affecting meaningful system behavior SHOULD define observability requirements.

Consequential actions MUST remain:

observable
auditable
traceable
recoverable where applicable

Where an ADR introduces a new action, state transition, authority boundary, or external effect, the ADR SHOULD identify how that behavior can be observed and audited.

⸻

25. Review Checklist

Before an ADR enters acceptance review, verify:

* [ ]	YAML frontmatter is complete
* [ ]	ADR ID is unique
* [ ]	Context is clear
* [ ]	Decision Drivers are explicit
* [ ]	Non-Goals are defined
* [ ]	Decision is explicit
* [ ]	Alternatives are documented
* [ ]	Consequences are documented
* [ ]	Risks are documented
* [ ]	Dependencies are documented
* [ ]	Revisit Conditions are defined
* [ ]	Architectural Invariants are explicit
* [ ]	Traceability is present
* [ ]	No Patch Instructions remain
* [ ]	No temporary workflow instructions remain
* [ ]	No contradiction with the Constitution exists
* [ ]	No unauthorized authority transfer is introduced
* [ ]	Observability requirements are addressed
* [ ]	Architecture stage is accurate

⸻

26. Canonical Principle

An ADR records an architectural decision.

It does not grant authority merely by describing authority.

It does not become true merely because an AI generated it.

It becomes part of Veda’s architecture only through the defined governance process.