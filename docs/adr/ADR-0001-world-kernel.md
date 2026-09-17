



⸻

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

Context

Veda requires a canonical authority for the current modeled state of the world.

Multiple subsystems may observe, reason about, remember, simulate, or attempt to change the world. Without a single authoritative owner, different components can produce conflicting representations of the same state.

The architecture therefore requires a dedicated World Kernel that owns the authoritative current modeled World State.

The World Kernel represents Veda’s modeled understanding of the world. It does not claim ownership of external reality.

Problem

Without a World Kernel:

* different agents may maintain conflicting world states
* observations may be mistaken for verified state
* brain outputs may be treated as authoritative
* tools may directly mutate internal state
* concurrent updates may silently overwrite one another
* simulations may contaminate real state
* failures may produce partially committed state
* historical events may be incorrectly treated as current truth

The architecture therefore requires explicit separation between observation, evidence, verification, events, history, projection, and authoritative current state.

Decision Drivers

The decision is driven by the following requirements:

1. One authoritative owner for current modeled World State.
2. Separation of intelligence from authority.
3. Explicit distinction between external reality and Veda’s model of reality.
4. Deterministic and auditable state transitions.
5. Safe handling of concurrency.
6. Explicit handling of uncertainty.
7. Prevention of partial authoritative commits.
8. Isolation of simulation from real state.
9. Support for multiple agents sharing one coherent modeled world.
10. Full observability and traceability of state-changing actions.
11. Compatibility with replaceable models, tools, and providers.
12. Human authority remains above the system.

Non-Goals

The World Kernel does not:

* control external reality
* replace external systems of record
* become the reasoning or intelligence layer
* become the memory system
* execute arbitrary tools directly
* decide authorization by itself
* treat model output as authoritative truth
* make simulations authoritative
* guarantee that the modeled world is identical to reality

Decision

Veda will implement a World Kernel as the sole authoritative owner of the current modeled World State.

Only the World Kernel may commit authoritative World State transitions.

The following authority separation is mandatory:

Component	Authority
Human	Ultimate authority
Brain	Proposes and reasons
Planner	Produces plans/goals
Authorization	Determines permitted action
Tools	Act on external systems
Observation	Reports observed information
Evidence	Represents supported information
Verification	Validates claims/results
Event Fabric	Transports events
Chronicle	Preserves durable history
World Projection	Builds modeled state
World Kernel	Commits authoritative current modeled state
Simulation	Produces hypothetical state only

The fundamental rule is:

Brain may propose. Tools may act. External systems may change. Observations may report. Verification may validate. Only the World Kernel commits authoritative modeled World State.

World Is Not Reality

The World Kernel represents Veda’s modeled state of the world.

Therefore:

World ≠ Reality

External systems remain authoritative for their own external state.

The World Kernel must never silently claim that its internal representation is equivalent to external reality.

State Ownership

The World Kernel owns:

* authoritative current modeled entities
* authoritative modeled relationships
* current modeled status
* state versions
* state transitions
* consistency rules
* conflict handling
* uncertainty state

External systems remain owners of:

* bank balances
* cloud resources
* GitHub repositories
* physical devices
* external databases
* third-party services
* other externally authoritative state

Veda may observe and model external state but does not become the owner of that external state merely by observing it.

World Kernel Responsibilities

The World Kernel is responsible for:

1. Maintaining authoritative current modeled World State.
2. Validating state transitions.
3. Enforcing state invariants.
4. Maintaining state versions.
5. Detecting conflicting transitions.
6. Handling concurrency.
7. Representing uncertainty explicitly.
8. Rejecting invalid transitions.
9. Preventing partial authoritative commits.
10. Maintaining provenance for committed state.
11. Exposing controlled read interfaces.
12. Exposing controlled state-transition interfaces.
13. Producing auditable state-change records.
14. Supporting deterministic reconstruction where required.

World State Transition

Every authoritative state transition must contain sufficient information to identify and audit the transition.

A transition must carry, where applicable:

* transition_id
* world_id
* previous_version
* new_version
* actor_id
* event_id
* causation_id
* authorization_ref
* verification_ref
* timestamp
* delta
* provenance

A state transition must not silently overwrite an existing authoritative state.

Read Path

Consumers must not directly access the authoritative World State database as an architectural shortcut.

The preferred read path is:

Consumer → World Query / World View → World Kernel

World Views may provide derived representations, but they must remain traceable to the authoritative World State.

Write Path

The canonical state transition path is:

Observation → Evidence → Verification → Event → Chronicle → World Projection → World Kernel → Current World

Not every operation necessarily requires every stage, but bypassing the World Kernel for authoritative state mutation is prohibited.

Privileged execution follows the broader control-plane pipeline:

Intent → Planning/Goal → Authorization → Lease → Capability → Execution → Verification → Commit → Event → Chronicle

The World Kernel is the authority responsible for committing the modeled world transition after required validation and authorization conditions have been satisfied.

Event and History Relationship

Events represent occurrences in the system and are not automatically equivalent to current truth.

The Chronicle preserves durable historical records.

The World Kernel represents authoritative current modeled state.

Therefore:

* Event ≠ Current World State
* Chronicle ≠ Current World State
* Current World State is derived and governed separately from historical records

A historical event must not automatically mutate authoritative current state without passing the required validation and projection rules.

External State Boundary

When Veda interacts with an external system:

1. Veda may issue an authorized action.
2. The external system may accept, reject, or partially process the action.
3. Veda must observe the resulting external state where possible.
4. The result must be verified according to the operation’s requirements.
5. The World Kernel may then update the modeled state.

Execution success does not automatically mean outcome success.

Therefore:

Execution Success ≠ Outcome Success

Concurrency

Concurrent state transitions must be explicitly handled.

The World Kernel may use the following outcomes:

* ACCEPT
* MERGE
* REJECT
* HUMAN_REVIEW

Silent overwrite is prohibited.

State versioning must be used to detect stale transitions and conflicting updates.

Uncertainty

Unknown information must remain explicitly unknown.

The system must not convert:

* missing information into certainty
* stale information into current truth
* model inference into verified fact
* failed observation into a negative assertion

Where the system cannot establish the current state with sufficient confidence, the World Kernel must preserve an explicit uncertainty state.

Multi-Agent World

Multiple agents may reason about the same modeled world.

Agents must not maintain independent authoritative versions of the shared World State.

The World Kernel provides the shared authoritative modeled state.

Agents may maintain local working memory or hypotheses, but those representations do not become authoritative World State until committed through the World Kernel.

Simulation

Simulation must be isolated from authoritative World State.

A simulation may:

* create hypothetical entities
* modify hypothetical state
* test possible transitions
* evaluate plans
* predict potential outcomes

Simulation must not silently modify real authoritative World State.

The boundary between simulation state and real state must be explicit.

Failure Handling

The World Kernel must prevent partially committed authoritative state.

If a transition cannot be completed safely:

* the authoritative transition must not be partially committed
* the failure must be observable
* the reason must be recorded
* uncertainty must be represented where appropriate
* recovery must be possible
* human review must be available for unresolved conflicts

The system must prefer an explicit unknown or rejected transition over silently corrupting authoritative state.

Alternatives Considered

Brain as World Authority

Rejected.

The Brain is an intelligence and reasoning layer. Intelligence does not grant authority.

Allowing the Brain to directly own World State would violate the separation between capability and authority.

Event Log as World Authority

Rejected.

Events are historical records and runtime messages. They are not inherently the canonical current state.

The current modeled state requires explicit projection and ownership.

Database as World Authority

Rejected as an architectural concept.

A database is a storage mechanism, not an authority model.

Authority belongs to the World Kernel. Storage is an implementation concern governed separately by the storage architecture.

Multiple Agents Owning Independent World States

Rejected.

Independent authoritative states would create conflicting realities inside the system.

Agents may maintain local hypotheses, but shared authoritative state must have one owner.

Consequences

Positive Consequences

* clear state ownership
* reduced ambiguity between reasoning and authority
* safer multi-agent coordination
* explicit external-state boundaries
* auditable state transitions
* controlled concurrency
* better failure recovery
* simulation isolation
* easier model/provider replacement
* stronger architectural governance

Negative Consequences

* increased implementation complexity
* additional validation steps
* additional state metadata
* greater storage and logging requirements
* more complex concurrency handling
* additional latency for some state-changing operations
* more engineering required before autonomous execution can be trusted

These costs are accepted because authoritative state corruption is more expensive than architectural complexity.

Risks

Risk	Mitigation
World Kernel becomes a monolith	Keep intelligence, execution, storage, and projection responsibilities separated
Stale modeled state	Versioning, timestamps, provenance, verification
Incorrect observations	Evidence and verification gates
Concurrent updates	Version checks and explicit conflict outcomes
External state divergence	External-state boundary and re-observation
Partial commits	Atomic transition handling
Model hallucination	Brain outputs remain non-authoritative
Event/state confusion	Explicit separation between Event, Chronicle, and World State
Simulation contamination	Separate simulation state
Unauthorized mutation	Controlled write path and authorization references

Dependencies

This ADR depends on and interacts with:

* ADR-0002: Event Fabric and Chronicle
* ADR-0003: Evidence, Knowledge, Memory, and Experience
* ADR-0004: Brain Non-Authority
* ADR-0005: Execution Control Plane
* ADR-0007: Storage Architecture
* ADR-0010: MVP Vertical Slice

The exact RFC identifiers governing the World Model / World State layer have not yet been established in the repository.

This is a traceability gap and must be resolved before the ADR Freeze Gate is considered complete.

Revisit Conditions

This decision must be revisited if:

1. Veda adopts a fundamentally different world-state architecture.
2. A distributed authoritative state model becomes necessary.
3. World State can no longer be safely owned by one logical authority.
4. External systems require a different synchronization model.
5. The authority model defined by the Constitution changes.
6. New execution requirements invalidate the current transition model.
7. Multi-agent coordination requires a different consistency model.
8. A future architecture introduces a formally equivalent or stronger authority boundary.

Any change to this decision must follow the repository governance process and may require a new ADR rather than silently modifying this decision.

Architectural Invariants

The following invariants are mandatory:

WK-001
There is one logical authoritative owner of current modeled World State.

WK-002
Only the World Kernel may commit authoritative World State transitions.

WK-003
Brain intelligence does not grant World State authority.

WK-004
External systems remain authoritative for their own external state.

WK-005
World State must not be treated as identical to Reality.

WK-006
Authoritative state transitions must be versioned and traceable.

WK-007
Silent overwrite of conflicting authoritative state is prohibited.

WK-008
Unknown information must remain explicitly unknown.

WK-009
Simulation state must not silently modify authoritative World State.

WK-010
Authoritative state must not be partially committed.

WK-011
Execution success must not automatically be treated as outcome success.

WK-012
Authoritative state-changing actions must remain observable and auditable.

Traceability

Constitutional Traceability

This ADR implements the following constitutional principles:

* Human remains ultimate authority.
* Capability does not imply authority.
* Knowledge does not imply authority.
* Intelligence does not imply authority.
* Brain is non-authoritative.
* World Kernel owns authoritative current modeled World State.
* AI outputs are untrusted until validated.
* Autonomy must be bounded, observable, and recoverable.
* Every meaningful action must be observable.
* There must be one authoritative owner per state.

RFC Traceability

The repository does not currently expose established RFC identifiers for the World Kernel / World State decision.

No RFC identifier is invented here.

RFC mapping must be added once the corresponding RFC artifacts exist.

ADR Traceability

Related decisions:

* ADR-0002: Event Fabric and Chronicle
* ADR-0003: Evidence, Knowledge, Memory, and Experience
* ADR-0004: Brain Non-Authority
* ADR-0005: Execution Control Plane
* ADR-0007: Storage Architecture
* ADR-0010: MVP Vertical Slice

Decision Metadata

Field	Value
ADR ID	ADR-0001
Status	Accepted
Owner	Phupha
Architecture Stage	ADR_ACCEPTED
Review Cycle	Quarterly
Supersedes	None
Superseded By	None

Review Record

Date	Reviewer	Result	Notes
2026-09-17	Architecture Review	Accepted with traceability gap	RFC mapping remains unresolved and must be established before ADR Freeze

Freeze Status

This ADR is Accepted but does not by itself indicate that the repository-wide ADR Freeze Gate has passed.

Repository-wide Freeze requires validation of:

1. ADR structural compliance.
2. Constitutional traceability.
3. RFC traceability.
4. ADR dependency integrity.
5. Decision graph integrity.
6. Governance compliance.
7. Repository-wide architectural consistency.

No subsequent architecture phase should begin until the current phase has been validated.