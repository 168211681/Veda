---
id: ADR-0001
title: World Kernel
status: Accepted
owner: Phupha
created: 2026-09-16
updated: 2026-09-17
review_cycle: Quarterly
architecture_stage: ADR_ACCEPTED
supersedes: null
superseded_by: null
---

# Context

Veda requires a canonical authority for the current modeled state of the world.

Multiple subsystems may observe, reason about, remember, simulate, or attempt to change the world. Without a single authoritative owner, different components may produce conflicting representations of the same state.

The architecture therefore requires a dedicated World Kernel that owns the authoritative current modeled World State.

The World Kernel represents Veda's modeled understanding of the world. It does not claim ownership of external reality.

## Problem

Without a World Kernel:

- different agents may maintain conflicting world states;
- observations may be mistaken for verified state;
- Brain outputs may be treated as authoritative;
- tools may directly mutate internal state;
- concurrent updates may silently overwrite one another;
- simulations may contaminate real state;
- failures may produce partially committed state;
- historical events may be incorrectly treated as current truth.

The architecture therefore requires explicit separation between observation, evidence, verification, events, history, projection, and authoritative current state.

# Decision Drivers

The decision is driven by the following requirements:

1. One authoritative owner for current modeled World State.
2. Separation of intelligence from authority.
3. Explicit distinction between external reality and Veda's model of reality.
4. Deterministic and auditable state transitions.
5. Safe handling of concurrency.
6. Explicit handling of uncertainty.
7. Prevention of partial authoritative commits.
8. Isolation of simulation from real state.
9. Support for multiple agents sharing one coherent modeled world.
10. Full observability and traceability of state-changing actions.
11. Compatibility with replaceable models, tools, and providers.
12. Human authority remains above the system.

# Non-Goals

The World Kernel does not:

- control external reality;
- replace external systems of record;
- become the reasoning or intelligence layer;
- become the memory system;
- execute arbitrary tools directly;
- decide authorization by itself;
- treat model output as authoritative truth;
- make simulations authoritative;
- guarantee that the modeled world is identical to reality.

The following are implementation concerns and are outside this ADR unless independently established as architectural decisions:

- database schema;
- programming language;
- framework selection;
- deployment configuration;
- UI behavior;
- API payload implementation details.

# Decision

Veda will implement a World Kernel as the sole authoritative owner of the current modeled World State.

Only the World Kernel may commit authoritative World State transitions.

## Authority Separation

| Component | Authority |
|---|---|
| Human | Ultimate authority |
| Brain | Proposes and reasons |
| Planner | Produces plans and goals |
| Authorization | Determines permitted action |
| Tools | Act on external systems |
| Observation | Reports observed information |
| Evidence | Represents supported information |
| Verification | Validates claims and results |
| Event Fabric | Transports events |
| Chronicle | Preserves durable history |
| World Projection | Builds modeled state |
| World Kernel | Commits authoritative current modeled state |
| Simulation | Produces hypothetical state only |

The fundamental rule is:

> Brain may propose. Tools may act. External systems may change. Observations may report. Verification may validate. Only the World Kernel commits authoritative modeled World State.

## World Is Not Reality

The World Kernel represents Veda's modeled state of the world.

Therefore:

**World State != Reality**

External systems remain authoritative for their own external state.

The World Kernel must never silently claim that its internal representation is equivalent to external reality.

## State Ownership

The World Kernel owns:

- authoritative current modeled entities;
- authoritative modeled relationships;
- current modeled status;
- state versions;
- state transitions;
- consistency rules;
- conflict handling;
- uncertainty state.

External systems remain owners of their own external state, including:

- financial balances;
- cloud resources;
- GitHub repositories;
- physical devices;
- external databases;
- third-party services;
- other externally authoritative state.

Veda may observe and model external state but does not become the owner of that external state merely by observing it.

# Alternatives Considered

## Brain as World Authority

Rejected.

The Brain is an intelligence and reasoning layer. Intelligence does not grant authority.

Allowing the Brain to directly own World State would violate the separation between capability and authority.

## Event Log as World Authority

Rejected.

Events are historical records and runtime messages. They are not inherently the canonical current state.

Current modeled state requires explicit projection and ownership.

## Database as World Authority

Rejected as an architectural concept.

A database is a storage mechanism, not an authority model.

Authority belongs to the World Kernel. Storage is an implementation concern governed separately by the storage architecture.

## Multiple Agents Owning Independent World States

Rejected.

Independent authoritative states would create conflicting representations of the shared world.

Agents may maintain local hypotheses, but shared authoritative state must have one owner.

# Consequences

## Positive Consequences

- clear state ownership;
- reduced ambiguity between reasoning and authority;
- safer multi-agent coordination;
- explicit external-state boundaries;
- auditable state transitions;
- controlled concurrency;
- better failure recovery;
- simulation isolation;
- easier model and provider replacement;
- stronger architectural governance.

## Negative Consequences

- increased implementation complexity;
- additional validation steps;
- additional state metadata;
- greater storage and logging requirements;
- more complex concurrency handling;
- additional latency for some state-changing operations;
- more engineering required before autonomous execution can be trusted.

These costs are accepted because authoritative state corruption is more expensive than the additional architectural complexity.

# Risks

| Risk | Impact | Mitigation |
|---|---|---|
| World Kernel becomes a monolith | High | Keep intelligence, execution, storage, and projection responsibilities separated |
| Stale modeled state | High | Versioning, timestamps, provenance, and verification |
| Incorrect observations | High | Evidence and verification gates |
| Concurrent updates | High | Version checks and explicit conflict outcomes |
| External state divergence | High | External-state boundary and re-observation |
| Partial commits | High | Atomic transition handling |
| Model hallucination | High | Brain outputs remain non-authoritative |
| Event/state confusion | Medium | Explicit separation between Event, Chronicle, and World State |
| Simulation contamination | High | Separate simulation state |
| Unauthorized mutation | Critical | Controlled write path and authorization references |

# Dependencies

## Requires

- Architecture Constitution
- ADR-0002: Event / Chronicle Boundary
- ADR-0004: Brain Non-Authority
- ADR-0005: Execution Control Plane
- ADR-0007: Storage Architecture

## Constrains

This decision constrains:

- Brain authority;
- execution control;
- event projection;
- persistence ownership;
- multi-agent shared state;
- simulation boundaries.

## Referenced By

The World Kernel decision is referenced by:

- ADR-0002: Event / Chronicle Boundary
- ADR-0003: Evidence, Knowledge, Memory, and Experience
- ADR-0004: Brain Non-Authority
- ADR-0005: Execution Control Plane
- ADR-0010: MVP Vertical Slice

## RFC Dependency

The repository does not currently expose an established RFC identifier for the World Kernel / World State decision.

No RFC identifier is invented in this ADR.

This is an unresolved traceability gap and must be resolved before the repository-wide ADR Freeze Gate can pass.

# Revisit Conditions

This decision must be revisited if:

1. Veda adopts a fundamentally different World State architecture.
2. A distributed authoritative state model becomes necessary.
3. World State can no longer be safely owned by one logical authority.
4. External systems require a fundamentally different synchronization model.
5. The authority model defined by the Architecture Constitution changes.
6. New execution requirements invalidate the current transition model.
7. Multi-agent coordination requires a different consistency model.
8. A future architecture introduces a formally equivalent or stronger authority boundary.

Any change to the architectural meaning of this ADR must follow the repository governance process.

If the change is breaking, a new ADR must be created rather than silently rewriting the accepted decision.

# Architectural Invariants

## WK-001

There is one logical authoritative owner of current modeled World State.

## WK-002

Only the World Kernel may commit authoritative World State transitions.

## WK-003

Brain intelligence does not grant World State authority.

## WK-004

External systems remain authoritative for their own external state.

## WK-005

World State must not be treated as identical to Reality.

## WK-006

Authoritative state transitions must be versioned and traceable.

## WK-007

Silent overwrite of conflicting authoritative state is prohibited.

## WK-008

Unknown information must remain explicitly unknown.

## WK-009

Simulation state must not silently modify authoritative World State.

## WK-010

Authoritative state must not be partially committed.

## WK-011

Execution success must not automatically be treated as outcome success.

## WK-012

Authoritative state-changing actions must remain observable and auditable.

# Traceability

## Constitutional Traceability

This ADR implements the following constitutional principles:

- Human remains ultimate authority.
- Capability does not imply authority.
- Knowledge does not imply authority.
- Intelligence does not imply authority.
- Intent does not imply authority.
- Brain is non-authoritative.
- World Kernel owns authoritative current modeled World State.
- AI outputs are untrusted until validated.
- Autonomy must be bounded, observable, and recoverable.
- Every meaningful consequential action must be observable.
- There must be one authoritative owner per state.

## RFC Traceability

**Status: BLOCKED**

The repository currently does not expose an established RFC identifier for the World Kernel / World State decision.

No RFC identifier is fabricated to satisfy the governance requirement.

Required future relationship:

```text
Architecture Constitution
        |
        v
Relevant RFC
        |
        v
ADR-0001 World Kernel
        |
        v
SPEC
        |
        v
Implementation
        |
        v
Test