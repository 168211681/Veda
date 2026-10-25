RFC-0002 — Veda World Model

RFC: RFC-0002

Title: Veda World Model

Status: Draft

Version: 0.2.0-proposed

Category: Core / Ontology / Runtime

Depends on: RFC-0001, RFC-0001A

⸻

Abstract

The Veda World Model defines how Veda represents its modeled context of reality.

Unlike traditional operating systems that organize computation around files, processes, windows, and applications, Veda organizes computation around World State.

The World provides shared modeled identities, relationships, state references, time, and provenance across Veda. Domain systems such as Knowledge, Memory, Planning, Authorization, and external systems of record retain ownership of their own semantics and authoritative data. Referencing an object in the World does not transfer that ownership to the World Kernel.

This RFC defines:

* the primitives of modeled World State,
* entity identity,
* relationships,
* state transitions,
* time,
* evidence,
* causality,
* world versioning,
* world integrity.

The World Model is the foundation of every future subsystem.

⸻

Motivation

Traditional computers answer questions like:

* Which file?
* Which application?
* Which process?

Veda answers different questions:

* Which entity?
* What relationship exists?
* What changed?
* Why did it change?
* What evidence supports it?
* What version of the world is this?

This shift enables intent-centric computing instead of application-centric computing.

⸻

Design Goals

The World Model MUST satisfy the following goals.

1. Represent modeled physical and digital context together without claiming ownership of external reality.
2. Every modeled object has a stable Veda identity and may reference separate external identities.
3. Model changes from validated transition inputs without granting events commit authority.
4. Time is first-class.
5. Evidence is attached to knowledge.
6. Every change is traceable.
7. World history is replayable.
8. Multiple agents share compatible reality.
9. World can be simulated.
10. World supports traceable recovery to a new version and isolated non-authoritative branching.

⸻

Chapter 1 — World Primitive

Everything begins from seven primitives.

Primitive A — Entity

A thing that exists.

Examples:

* Human.
* AI model.
* Book.
* Project.
* File.
* Server.
* GPU.
* Sensor.
* Company.
* Task.

Every entity has identity.

⸻

Primitive B — Relationship

A typed connection between entities.

Examples:

* owns
* depends_on
* created_by
* contains
* connected_to
* located_in
* trained_from
* reads
* writes

Relationships are directional unless specified otherwise.

⸻

Primitive C — State

The current properties of an entity.

Example:

state:
  cpu_usage: 0.35
  battery: 0.91
  build_status: PASS

Authoritative modeled State evolves only through a World Kernel committed transition.

⸻

Primitive D — Event Reference

Something happened.

Examples:

* File created.
* Build failed.
* User spoke.
* Model loaded.
* Book imported.

Events record occurrences and may provide inputs to a proposed transition. Events do not self-authorize or directly mutate authoritative World State. Only the World Kernel may commit an authoritative modeled transition, subject to the applicable authorization and verification requirements. RFC-0003 owns the detailed event contract and lifecycle; RFC-0004 owns transition semantics.

⸻

Primitive E — Time

Every observation belongs to time.

Time supports:

* timestamp
* interval
* duration
* ordering
* recurrence
* world version

⸻

Primitive F — Evidence Reference

Evidence references explain why a modeled claim is supported or challenged. RFC-0012 owns evidence schemas, evaluation, status, and lifecycle.

Example:

evidence:
  source: sensor.camera
  confidence: 0.97

⸻

Primitive G — Causal Claim Reference

Veda may represent a supported causal claim or hypothesis between events, states, or conditions.

Example:

Disk Full
    ↓
Build Failed

Correlation and causation are different. A confidence value is not proof of causation, and causal claims MUST preserve evidence, uncertainty, and alternative hypotheses. RFC-0022 owns the detailed causal model.

⸻

Chapter 2 — World Ontology

The world is represented as:

World
├── Entities
├── Relationships
├── States
├── Events
├── Time
├── Evidence
├── Causality
└── Integrity Rules

This ontology is a shared modeled context and reference layer. It does not absorb the ownership or lifecycle rules of Knowledge, Memory, Planning, Authorization, Evidence, Events, or external systems.

⸻

Chapter 3 — Entity System

Entity Identity

Every modeled entity receives a stable identifier unique within a declared Veda namespace.

Example:

entity:
  id: ent_project_veda
  type: Project

The Veda identity is immutable for the lifetime of that modeled entity.

Names may change.

External identities MUST be represented separately as namespaced references, such as a repository ID owned by GitHub or a resource ID owned by a cloud provider. Veda MUST NOT rewrite or claim ownership of those external identifiers. Identity equivalence across namespaces MUST be explicit and evidence-backed.

⸻

Entity Types

Core modeled categories may include:

* Human
* Organization
* Device
* Hardware
* Software
* Service
* Model
* Agent
* Project
* Repository
* Document
* Book
* Memory Reference
* Knowledge Reference
* Task
* Goal
* Event Reference
* Capability
* Policy
* World

Future RFCs may extend categories.

⸻

Entity Metadata

Every entity contains immutable and mutable fields.

Immutable:

* id
* creation time
* creator
* type

Mutable:

* name
* description
* tags
* status
* attributes

⸻

Chapter 4 — Relationship Graph

Relationships form a graph.

Example:

Human
 │ owns
 ▼
Project Veda
 │ contains
 ▼
Repository
 │ contains
 ▼
RFC-0002

⸻

Relationship Types

Required relationship primitives:

* owns
* contains
* uses
* produces
* depends_on
* derived_from
* references
* verifies
* contradicts
* observes
* controls
* authorized_by

Relationships are versioned.

⸻

Graph Rules

An entity may have many relationships.

Relationships:

* have timestamps.
* may expire.
* may include evidence.
* may include confidence.

⸻

Chapter 5 — State Model

State is NOT identity.

Example:

entity: GPU
state:
  utilization: 78%
  temperature: 63

Identity persists while state changes.

⸻

State Categories

* Operational
* Physical
* Logical
* Security
* Knowledge Reference
* Planning Reference
* Memory Reference

Each domain subsystem defines and owns its additional state semantics. A World reference to domain state is not an authoritative replacement for the owning domain record.

⸻

State Snapshot

The World Kernel may create or adopt snapshots of modeled World State according to an approved transition and persistence policy.

Snapshots enable:

* recovery to a new canonical version derived from a prior valid state
* replay
* debugging
* isolated simulation

A snapshot is a derived optimization, not historical authority. Recovery or rollback MUST create a traceable transition and MUST NOT erase immutable history or imply that an external-world effect has been undone. Simulation snapshots and branches are non-authoritative unless a separately authorized, verified transition is committed by the World Kernel.

⸻

Chapter 6 — World Event References

This chapter defines only the World Model's dependency on event references. RFC-0003 defines event envelopes, categories, ordering, and lifecycle. RFC-0031 and RFC-0032 define audit/event trace and Chronicle concerns where approved.

Committed World event references are append-only.

Example:

event:
  id: evt_build_completed
  actor: coding_agent
  target: runtime
  action: build

Events NEVER mutate history.

⸻

Illustrative Event Categories

* Observation
* Action
* Verification
* Failure
* Recovery
* Security
* Learning
* Memory
* Knowledge
* Evolution

⸻

Event Reference Ordering

Events have:

* timestamp
* sequence
* trace id
* parent event
* causal event

Ordering used to derive an authoritative World projection MUST be deterministic or must produce an explicit conflict. Consequential failed executions, denied proposals, rejected transitions, and recovery attempts MUST remain durably auditable; they MUST NOT be represented as successful state changes.

A truthful pre-commit factual record MAY be durably persisted only after the fact it represents has occurred. Such a record MAY describe authorization, denial, execution, observation, verification, rejection, conflict, failure, or recovery, but it MUST NOT claim successful World commitment, authorize mutation, or advance authoritative modeled World State. A successful outcome event MUST be created only after the corresponding authoritative World commit and MUST reference its `WORLD_COMMIT_RECEIPT` and resulting World version.

⸻

Chapter 7 — Time System

Time is first-class.

Supported concepts:

* Instant.
* Duration.
* Interval.
* Recurrence.
* World Version Time.
* Logical Clock.

⸻

Temporal Queries

Examples:

* Current state.
* Yesterday.
* Before build.
* After deployment.
* During experiment.

World supports temporal reasoning.

⸻

Chapter 8 — Evidence References

This chapter requires World claims and transitions to retain evidence references. RFC-0012 owns evidence fields, classes, quality evaluation, and lifecycle; RFC-0013 owns claim and knowledge semantics.

Evidence attaches support to claims.

An evidence reference identifies evidence that may include:

* source
* timestamp
* reliability
* confidence
* provenance
* signature (future)

⸻

Evidence Classes

* Sensor
* Human
* System
* Document
* Model
* Experiment
* External API

Evidence quality differs by source.

⸻

Epistemic Status Reference

Modeled claims MUST preserve their epistemic status as defined by the owning claim, evidence, and knowledge contracts.

* Verified
* Observed
* Inferred
* Predicted
* Unknown
* Contradicted

Observed, Verified, Inferred, Predicted, Unknown, Stale, and Contradicted MUST NOT be collapsed into a generic truth flag. New evidence may cause reevaluation through the owning domain lifecycle; it MUST NOT silently overwrite contradictory history.

⸻

Chapter 9 — World Versioning

World evolves through versions.

World(0)
 ↓
Event A
 ↓
World(1)
 ↓
Event B
 ↓
World(2)

History is immutable.

Current world is derived.

⸻

Version Object

world_version:
  id: W10482
  parent: W10481
  timestamp:

Authoritative World versions form one canonical lineage. A root version has no parent; every later canonical version references its canonical parent. Isolated simulations and counterfactuals may branch from a version, but those branches are explicitly non-authoritative and MUST NOT overwrite or be confused with the canonical lineage.

⸻

Replay

Given canonical World commit receipts and their bound inputs:

World0 + Receipts + Bound Inputs = WorldN

Replay reconstructs modeled World State to the extent permitted by retained events, evidence references, snapshots, schemas, and deterministic transition rules. Replay MUST preserve provenance and surface missing, stale, contradictory, or unverifiable information as explicit limitations. Replay never reconstructs or rewrites external reality.

⸻

Chapter 10 — World Delta

World Delta represents only changes.

Example:

delta:
  base_world: W10482
  changes:
    - entity: GPU
      field: utilization
      from: 21
      to: 78

Deltas reduce synchronization cost.

⸻

Delta Rules

A delta MUST include:

* base version
* target version
* affected entities
* changed fields
* evidence

⸻

Chapter 11 — Causality Engine

Causality explains change.

Example:

Package Updated
      ↓
Compiler Changed
      ↓
Build Failed

⸻

Causal Edge

Fields:

* cause
* effect
* evidence
* confidence

Causal edges are distinct from ordinary relationships. They are evidence-linked claims or hypotheses, not guaranteed facts. Detailed causal strength, confidence semantics, alternative hypotheses, and lifecycle are defined by RFC-0022.

⸻

Counterfactual Support

Future engine may ask:

What if Event A never happened?

The World Model supports isolated, non-authoritative alternative histories. A simulation result cannot become canonical merely because it exists or has high confidence.

⸻

Chapter 12 — Physical + Digital World

Veda models both worlds.

Physical entities:

* Human
* Laptop
* Phone
* GPU
* Microphone
* Camera

Digital entities:

* File
* Repository
* Docker Container
* API
* Memory
* Model

Relationships connect physical and digital worlds.

⸻

Chapter 13 — Identity Layer

Everything has identity.

Examples:

* Human identity.
* Device identity.
* Model identity.
* Capability identity.
* Policy identity.
* Agent identity.

Identity survives state changes.

⸻

Chapter 14 — World Integrity Rules

World consistency rules.

Rule W1

Every modeled entity MUST have exactly one immutable Veda ID within a declared namespace. External identities MUST remain separate, namespaced references.

⸻

Rule W2

Every relationship MUST reference valid entities.

⸻

Rule W3

Events MUST NOT mutate history.

⸻

Rule W4

Only the World Kernel may commit an authoritative modeled state transition. An event or other transition input MUST NOT self-authorize or directly mutate authoritative World State. Every committed transition MUST be traceable to its validated inputs, applicable authorization, verification outcome, and durable audit evidence.

The World Kernel commit boundary MUST make the following visible as one conceptual atomic unit:

1. the new authoritative World version;
2. its immutable `WORLD_COMMIT_RECEIPT`; and
3. a durable committed-event delivery intent.

The unit MUST become visible in full or not at all. The receipt MUST bind the exact parent World version, candidate transition hash, evidence set, verification policy version, applicable authorization and verification references, resulting World version, and delivery intent identifier. The delivery intent is not itself a successful outcome event and MUST NOT be interpreted as proof of downstream delivery.

If compare-and-swap detects that the bound parent World version is no longer current, the prior verification receipt is inapplicable to the new parent. The attempted transition MUST NOT commit. The conflict MUST remain durably auditable, and any retry MUST re-prepare the candidate against the current parent and obtain new verification before a new commit attempt.

⸻

Rule W5

Every non-root canonical world version MUST reference exactly one canonical parent. Simulation branches MUST identify their branch and base version and remain non-authoritative.

⸻

Rule W6

Evidence MUST reference an identifiable source.

⸻

Rule W7

Contradictory evidence MUST coexist until resolved.

⸻

Rule W8

Deleting an entity does not delete historical events.

⸻

Rule W9

Time ordering MUST remain deterministic.

⸻

Rule W10

Every World integrity violation MUST generate an integrity/audit record. It MUST also be classified as a security event when the violation has security implications; integrity failure alone does not imply a security incident.

⸻

World Read and Commit Boundaries

Conceptual read projection:

world_projection:
  version:
  entities:
  relationships:
  states:
  events:
  evidence:
  causality:

Subsystems read through bounded World Query, snapshot, or projection contracts appropriate to their authorization and consistency needs. These are conceptual boundaries, not concrete API endpoints.

No shared projection grants mutation authority. All authoritative writes pass through the single controlled World Kernel commit boundary; cached, derived, observed, predicted, hypothetical, and simulated views MUST be distinguishable from authoritative current modeled World State.

⸻

World Computing Principle

Traditional OS:

Files
Processes
Applications

Veda OS:

World
Entities
Relationships
Events
Time
Evidence
Intent
Goals
Knowledge

Applications may provide views of modeled context without becoming owners of data held by the World Kernel, domain systems, or external systems of record.

⸻

Conformance Requirements

A subsystem conforms to RFC-0002 only if evidence demonstrates that it:

1. Uses immutable namespaced Veda entity IDs and keeps external identities separate.
2. Prevents direct authoritative mutation outside the World Kernel commit boundary.
3. Rejects event, Brain, Tool, model, and simulation inputs as self-authorizing writes.
4. Exposes bounded read projections that distinguish authoritative, derived, cached, observed, predicted, and hypothetical data.
5. Supports temporal queries without treating old, stale, unknown, or contradictory data as current verified state.
6. Preserves canonical version lineage and immutable historical references; recovery creates a new traceable version.
7. Keeps simulation and counterfactual branches isolated from the canonical lineage.
8. Maintains evidence and provenance references while delegating evidence lifecycle to RFC-0012.
9. Replays modeled World State deterministically from valid World commit receipts and their bound inputs when those inputs permit, and reports explicit limitations when they do not.
10. Produces audit records for committed, rejected, failed, and recovery paths as required, without falsely reporting unsuccessful work as a state change.
11. Records every integrity violation and security-classifies only those with security implications.
12. Preserves external systems as authoritative for their own external state.
13. Atomically makes visible the World version, `WORLD_COMMIT_RECEIPT`, and committed-event delivery intent for every authoritative World commit.
14. Binds verification to the exact parent World version, candidate transition hash, evidence set, and verification policy version, and rejects stale verification after a parent-version conflict.
15. Delivers post-commit successful outcome events idempotently from the durable delivery intent and treats duplicate delivery as delivery replay, not duplicate World execution.

⸻

Future RFC Dependencies

RFC-0002 enables:

* RFC-0003 Event Model.
* RFC-0004 World Transition Engine.
* RFC-0005 Intent Model.
* RFC-0013 Knowledge Model.
* RFC-0017 Memory Model.
* RFC-0021 Temporal Model.
* RFC-0022 Causal Model.
* RFC-0043 World Delta Protocol.

⸻

Authority and Traceability

Normative authority chain for this proposed revision:

* `docs/architecture/ARCHITECTURE_CONSTITUTION.md`: World Kernel authority, one owner per authoritative state, verification, auditability, and no silent mutation.
* RFC-0001 and RFC-0001A: upstream constitutional and permission constraints; both remain Draft and therefore block RFC Freeze evidence.
* RFC-0003: detailed event semantics and durable event lifecycle.
* RFC-0004: detailed state-transition semantics.
* RFC-0012: detailed Evidence model.
* RFC-0013: detailed claim and Knowledge model.
* RFC-0017: detailed Memory model.
* RFC-0022: detailed Causal model.
* ADR-0001: downstream World Kernel decision candidate. Its accepted status does not replace the required upstream RFC validation and owner approval.

For this proposed Draft normalization, the canonical ordering is:

1. persist applicable proposal, authorization, or denial facts after they occur;
2. prepare a candidate transition against an exact parent World version;
3. execute applicable external or internal work and persist the execution fact;
4. observe the actual result and persist the observation fact;
5. verify the candidate outcome, persist the verification fact, and bind its receipt to the parent version, candidate hash, evidence set, and verification policy version;
6. let only the World Kernel atomically make visible the new World version, `WORLD_COMMIT_RECEIPT`, and committed-event delivery intent;
7. materialize and idempotently deliver a post-commit successful outcome event that references the receipt; and
8. project the event into Chronicle and other audit/read projections without granting those projections World mutation authority.

This ordering is an owner-directed design direction for Draft RFC normalization, not acceptance of this RFC. WM-12 remains open and RFC Freeze remains blocked pending complete cross-document validation, independent audit evidence, and the required governance review.

⸻

Proposed Semantic Change Summary

Compared with Draft v0.1.0, this owner-directed Draft normalization proposal:

1. limits World scope to modeled context and preserves domain/external ownership;
2. makes the World Kernel the sole authoritative commit owner;
3. changes replay from reconstructing reality to reconstructing modeled World State with explicit limitations;
4. separates canonical lineage from isolated simulation branches;
5. replaces a universal shared mutable object with bounded read projections and one commit boundary;
6. separates Veda identity from namespaced external identity;
7. treats causality as an evidence-linked claim or hypothesis;
8. delegates detailed Evidence and Event contracts to RFC-0012 and RFC-0003;
9. distinguishes integrity audit records from security classification; and
10. adds testable conformance and traceability requirements;
11. distinguishes truthful pre-commit factual records from post-commit successful outcome events;
12. defines the conceptual atomic World commit unit as World version plus `WORLD_COMMIT_RECEIPT` plus committed-event delivery intent; and
13. makes verification parent-specific and requires re-preparation and re-verification after a parent-version conflict.

This change summary records proposed Draft RFC text implementing the owner-approved design direction. It does not assert approval of this RFC text, RFC acceptance, completion of RFC Freeze, or closure of WM-12.

⸻

Status

Draft v0.2.0-proposed

The World Model is the foundational shared modeled-context ontology of Project Veda. Future components MUST interoperate through its identity, reference, provenance, read, and controlled-commit boundaries without surrendering their own domain authority or treating modeled World State as external reality.
