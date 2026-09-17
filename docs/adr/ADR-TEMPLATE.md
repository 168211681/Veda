



⸻

id: ADR-XXXX
title: Decision Title
status: Proposed
owner: Phupha
created: YYYY-MM-DD
updated: YYYY-MM-DD
review_cycle: Quarterly
architecture_stage: ADR_DRAFT

Context

Describe the architectural problem, system condition, constraints, and background that require a decision.

The context must explain the problem without prescribing the solution prematurely.

Decision Drivers

List the properties that materially influence the decision.

Priority	Driver	Reason
P0/P1/P2/P3	Driver	Why it matters

Non-Goals

Explicitly state what this ADR does not define.

* Implementation details not relevant to the architectural decision.
* Database schemas unless they are independently architectural.
* UI behavior.
* Programming-language or framework details.
* Other concerns intentionally deferred to SPEC.

Decision

State the architectural decision clearly and unambiguously.

Describe:

* what is being decided;
* which component owns the responsibility;
* which authority boundary is established;
* which constraints become mandatory;
* what behavior is explicitly prohibited.

Alternatives Considered

Document the meaningful alternatives considered before selecting the decision.

Alternative A

Description:

Advantages:

Disadvantages:

Reason not selected:

Alternative B

Description:

Advantages:

Disadvantages:

Reason not selected:

Alternative C

Description:

Advantages:

Disadvantages:

Reason not selected:

Consequences

Positive

Document expected architectural benefits.

Negative

Document costs, limitations, trade-offs, or operational burden.

Neutral

Document consequences that are neither inherently positive nor negative.

Risks

Document material risks introduced or accepted by this decision.

Risk	Impact	Mitigation
Risk	Low/Medium/High	Mitigation

Dependencies

Document dependencies using stable architecture identifiers where possible.

Category	Artifact	Relationship
Requires	RFC-XXXX / ADR-XXXX / SPEC-XXXX	Must exist first
Constrains	RFC-XXXX / ADR-XXXX / SPEC-XXXX	Limits this decision
Referenced By	ADR-XXXX / SPEC-XXXX	Downstream dependency
Implements	SPEC-XXXX	Implementation contract

Revisit Conditions

Define objective conditions that require this decision to be reviewed.

Examples:

* A constitutional invariant changes.
* An upstream ADR is superseded.
* A security boundary becomes insufficient.
* A required capability cannot be implemented under this decision.
* A major operational constraint changes.
* New evidence invalidates a core assumption.

Architectural Invariants

List the rules that become mandatory because of this ADR.

Each invariant should be explicit and testable where possible.

INV-XXXX-001

Statement:

INV-XXXX-002

Statement:

INV-XXXX-003

Statement:

Traceability

Document the relationship between this ADR and the surrounding architecture.

Constitutional References

* docs/architecture/ARCHITECTURE_CONSTITUTION.md
* Relevant constitutional principle(s):

Upstream RFCs

* RFC-XXXX

Related ADRs

* ADR-XXXX
* ADR-XXXX

Downstream SPECs

* SPEC-XXXX

Implementation

* Implementation path/module:

Verification

* Test/specification reference:
* Audit reference:

Decision Metadata

Decision status:

Proposed

Architecture stage:

ADR_DRAFT

Supersession metadata, when applicable:

supersedes:
superseded_by:

Review Record

Review	Reviewer	Date	Result
Architecture Review		YYYY-MM-DD	Pending
Constitution Traceability		YYYY-MM-DD	Pending
Final Acceptance		YYYY-MM-DD	Pending