VEDA Architecture Decision Records (ADR)

Architecture Version: 0.1.0
Architecture Stage: ADR_FREEZE
Last Updated: 2026-09-16

⸻

Purpose

Architecture Decision Records (ADRs) document the irreversible architectural decisions of the Veda project.

They capture why a decision exists, not how it is implemented.

The repository follows a four-layer architecture documentation model:

Layer	Purpose
RFC	Architectural requirements and contracts.
ADR	Long-lived architecture decisions.
SPEC	Implementation contracts and schemas.
CODE	Executable implementation.

Rule: RFC → ADR → SPEC → CODE

⸻

ADR Lifecycle

Status	Meaning
Proposed	Draft architectural decision under review.
Accepted	Approved architectural decision.
Implemented	Decision has been implemented in code.
Deprecated	Decision is no longer recommended.
Superseded	Replaced by a newer ADR.

Lifecycle Flow

Proposed
    ↓
Architecture Review
    ↓
Accepted
    ↓
Implemented
    ↓
Deprecated / Superseded

⸻

ADR Status Summary

Status	Count
Accepted	10
Proposed	0
Implemented	0
Deprecated	0
Superseded	0

⸻

ADR Index

ADR	Title	Status
ADR-0001	World Kernel	Accepted
ADR-0002	Event / Chronicle Boundary	Accepted
ADR-0003	Knowledge / Memory / Experience Boundary	Accepted
ADR-0004	Brain Non-Authority	Accepted
ADR-0005	Execution Control Plane	Accepted
ADR-0006	Common Object Envelope	Accepted
ADR-0007	Storage Architecture	Accepted
ADR-0008	Books & Knowledge Library Architecture	Accepted
ADR-0009	Protocol Layering	Accepted
ADR-0010	MVP Vertical Slice	Accepted

⸻

ADR Dependency Matrix

ADR	Requires	Referenced By
ADR-0001 World Kernel	RFC-0001, RFC-0002	ADR-0002, ADR-0004, ADR-0005, ADR-0007, ADR-0010
ADR-0002 Event / Chronicle Boundary	ADR-0001	ADR-0007, ADR-0010
ADR-0003 Knowledge / Memory / Experience Boundary	ADR-0001	ADR-0008
ADR-0004 Brain Non-Authority	ADR-0001	ADR-0005, ADR-0009, ADR-0010
ADR-0005 Execution Control Plane	ADR-0001, ADR-0004	ADR-0006, ADR-0009, ADR-0010
ADR-0006 Common Object Envelope	ADR-0005	ADR-0009, SPEC-0001
ADR-0007 Storage Architecture	ADR-0001, ADR-0002	ADR-0008, ADR-0010
ADR-0008 Books & Knowledge Library Architecture	ADR-0003, ADR-0007	SPEC-0008
ADR-0009 Protocol Layering	ADR-0005, ADR-0006	SPEC-0002, SPEC-0009, ADR-0010
ADR-0010 MVP Vertical Slice	ADR-0001 → ADR-0009	SPEC-0001, SPEC-0005, MVP Implementation

⸻

Architecture Dependency Graph

graph TD
RFC[RFC Layer]
RFC --> ADR1[ADR-0001 World Kernel]
RFC --> ADR2[ADR-0002 Chronicle]
RFC --> ADR3[ADR-0003 Knowledge]
ADR1 --> ADR4[ADR-0004 Brain]
ADR4 --> ADR5[ADR-0005 Execution Control Plane]
ADR5 --> ADR6[ADR-0006 Object Envelope]
ADR1 --> ADR7[ADR-0007 Storage]
ADR3 --> ADR8[ADR-0008 Library]
ADR5 --> ADR9[ADR-0009 Protocol Layering]
ADR6 --> ADR9
ADR1 --> ADR10[ADR-0010 Vertical Slice]
ADR2 --> ADR10
ADR5 --> ADR10
ADR7 --> ADR10
ADR9 --> ADR10
ADR10 --> SPEC[SPEC Layer]
SPEC --> CODE[Implementation Layer]

⸻

Architecture Layers

RFC Layer

Defines architectural requirements, contracts, invariants, and subsystem boundaries.

Examples:

* Event Model
* Capability Model
* World Delta Protocol
* Federation Protocol

⸻

ADR Layer

Defines long-lived architectural decisions.

Characteristics:

* Stable.
* Rarely changed.
* Versioned through supersession.
* Never rewritten after acceptance.

⸻

SPEC Layer

Defines implementation contracts.

Examples:

* JSON Schemas
* State Machines
* APIs
* Database Schemas
* Capability Interfaces
* Verification Contracts

⸻

Code Layer

Executable implementation following SPEC contracts.

⸻

Repository Rule

RFC
  ↓
ADR
  ↓
SPEC
  ↓
Implementation
  ↓
Tests

Every implementation must trace back to at least one SPEC.

Every SPEC must trace back to one or more ADRs.

Every ADR must trace back to one or more RFCs.

⸻

ADR Numbering Rules

1. ADR numbers are immutable.
2. Deleted ADR numbers are never reused.
3. Superseded ADRs remain in history.
4. New architectural decisions always receive the next available number.
5. ADRs must not contain implementation-specific code beyond illustrative examples.

⸻

Review Policy

Review Type	Trigger
Quarterly Review	Architecture milestone.
Breaking Review	RFC semantic change.
Implementation Review	Before SPEC Freeze.
Security Review	New capability or protocol.

⸻

Ownership

Role	Owner
Repository Owner	Phupha
Architecture Owner	VEDA Architecture Council
Primary Reviewer	Human Architect
Secondary Reviewer	AI Architecture Auditor

⸻

Related Documents

RFC Layer

Location:

docs/rfc/

ADR Layer

Location:

docs/adr/

SPEC Layer

Location:

docs/spec/

Architecture Maps

Location:

docs/architecture/

⸻

Current Architecture Milestone

Milestone	Status
RFC Freeze	✅ Complete
ADR Freeze	🟡 In Progress
SPEC Freeze	⏳ Not Started
MVP Vertical Slice	⏳ Planned
Reference Implementation	⏳ Planned