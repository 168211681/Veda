RFC-0002 — Veda World Model

RFC: RFC-0002

Title: Veda World Model

Status: Draft

Version: 0.1.0

Category: Core / Ontology / Runtime

Depends on: RFC-0001, RFC-0001A

⸻

Abstract

The Veda World Model defines how Veda represents reality.

Unlike traditional operating systems that organize computation around files, processes, windows, and applications, Veda organizes computation around World State.

Everything Veda knows, remembers, predicts, observes, plans, verifies, or modifies exists inside a unified World.

This RFC defines:

* the primitives of reality,
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

1. Represent physical and digital reality together.
2. Every object has a persistent identity.
3. Reality evolves through events.
4. Time is first-class.
5. Evidence is attached to knowledge.
6. Every change is traceable.
7. World history is replayable.
8. Multiple agents share compatible reality.
9. World can be simulated.
10. World supports rollback and branching.

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

State is mutable.

⸻

Primitive D — Event

Something happened.

Examples:

* File created.
* Build failed.
* User spoke.
* Model loaded.
* Book imported.

Events change state.

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

Primitive F — Evidence

Evidence explains why Veda believes something.

Example:

evidence:
  source: sensor.camera
  confidence: 0.97

⸻

Primitive G — Causality

Events may cause future events.

Example:

Disk Full
    ↓
Build Failed

Correlation and causation are different.

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

Everything belongs somewhere inside this ontology.

⸻

Chapter 3 — Entity System

Entity Identity

Every entity receives a globally unique identifier.

Example:

entity:
  id: ent_project_veda
  type: Project

Identity NEVER changes.

Names may change.

⸻

Entity Types

Core categories include:

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
* Memory
* Knowledge
* Task
* Goal
* Event
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
* Knowledge
* Planning
* Memory

Each subsystem defines additional states.

⸻

State Snapshot

The World Engine may snapshot world state.

Snapshots enable:

* rollback
* replay
* debugging
* simulation

⸻

Chapter 6 — Event Model

Events are append-only.

Example:

event:
  id: evt_build_completed
  actor: coding_agent
  target: runtime
  action: build

Events NEVER mutate history.

⸻

Event Categories

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

Event Ordering

Events have:

* timestamp
* sequence
* trace id
* parent event
* causal event

Ordering MUST be deterministic.

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

Chapter 8 — Evidence Layer

Evidence attaches support to claims.

Evidence includes:

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

Truth Status

Every claim has status.

* Verified
* Observed
* Inferred
* Predicted
* Unknown
* Contradicted

Status is mutable through new evidence.

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

World versions form a chain.

⸻

Replay

Given events:

World0 + Events = WorldN

Replay reconstructs reality.

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

Causal edges are distinct from relationships.

⸻

Counterfactual Support

Future engine may ask:

What if Event A never happened?

World Model supports alternative histories.

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

Every entity MUST have exactly one immutable ID.

⸻

Rule W2

Every relationship MUST reference valid entities.

⸻

Rule W3

Events MUST NOT mutate history.

⸻

Rule W4

State changes MUST originate from events.

⸻

Rule W5

Every world version MUST reference its parent.

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

World integrity violations MUST generate security events.

⸻

Reference World Object

Conceptual structure:

world:
  version:
  entities:
  relationships:
  states:
  events:
  evidence:
  causality:

Every subsystem reads from this object.

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

Applications become views of the world rather than owners of data.

⸻

Conformance Requirements

A subsystem conforms to RFC-0002 only if it:

1. Uses immutable entity IDs.
2. Records state changes through events.
3. Supports temporal queries.
4. Preserves world history.
5. Maintains evidence provenance.
6. Produces deterministic world versions.
7. Supports replay from event history.
8. Preserves integrity rules.

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

Status

Draft v0.1.0

The World Model is the foundational ontology of Project Veda. All future runtime, memory, planner, agent, simulation, and operating-system components MUST share this representation of reality.
