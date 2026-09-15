RFC-0003 — Veda Event Model

RFC: RFC-0003
Title: Veda Event Model
Status: Draft
Version: 0.1.0
Category: Core / Event / Audit / Runtime
Depends on: RFC-0001, RFC-0001A, RFC-0002

⸻

Abstract

The Veda Event Model defines how Veda represents, records, orders, verifies, and processes events.

An Event represents something that happened, was observed, was proposed, was executed, or was verified within the Veda World.

Events form the historical foundation of Veda.

The World Model describes the current representation of reality.

The Event Model describes the sequence of changes that produced that representation.

The fundamental relationship is:

World(t)
   ↓
Event
   ↓
World(t+1)

Veda MUST preserve sufficient event history to reconstruct significant world states.

⸻

1. Motivation

A conventional application log is usually designed for debugging.

Veda requires something stronger.

The event system must support:

* audit,
* world reconstruction,
* memory,
* verification,
* rollback,
* diagnostics,
* learning,
* causality,
* multi-agent coordination,
* simulation,
* security investigation.

Therefore:

Log ≠ Event

A log is a record intended primarily for observation.

An Event is a first-class object in the Veda World.

⸻

2. Design Goals

The Event System MUST provide:

1. Immutable historical records.
2. Deterministic event identity.
3. Causal relationships.
4. Trace relationships.
5. Actor attribution.
6. Authorization provenance.
7. Before/after state references.
8. Verification status.
9. Idempotency.
10. Replay capability.
11. Tamper detection.
12. Human-readable history.
13. Machine-readable structure.

⸻

3. Event Primitive

The minimum conceptual Event is:

event:
  id:
  type:
  timestamp:
  actor:
  target:
  action:
  payload:
  result:

Production events SHOULD contain substantially more metadata.

⸻

4. Event Identity

Every Event MUST have a globally unique event_id.

Example:

evt_01JXYZ8M7A9...

Event IDs MUST NOT be reused.

An event identifier remains stable for the entire lifetime of the event.

⸻

5. Event Types

Veda defines the following core categories.

5.1 Observation

Something was observed.

OBSERVATION

Examples:

* CPU temperature observed.
* File detected.
* User message received.

⸻

5.2 Intent

A human or agent expressed an intent.

INTENT_CREATED

⸻

5.3 Goal

A goal was created or modified.

GOAL_CREATED
GOAL_UPDATED
GOAL_COMPLETED
GOAL_FAILED

⸻

5.4 Planning

A plan was generated or changed.

PLAN_CREATED
PLAN_UPDATED
PLAN_REJECTED

⸻

5.5 Action

An action was proposed or executed.

ACTION_PROPOSED
ACTION_AUTHORIZED
ACTION_STARTED
ACTION_COMPLETED
ACTION_FAILED
ACTION_ABORTED

⸻

5.6 Authorization

A permission decision occurred.

AUTHORIZATION_GRANTED
AUTHORIZATION_DENIED
AUTHORIZATION_REVOKED

⸻

5.7 Capability

A capability changed.

CAPABILITY_GRANTED
CAPABILITY_REVOKED
LEASE_CREATED
LEASE_EXPIRED

⸻

5.8 Verification

An outcome was evaluated.

VERIFICATION_STARTED
VERIFICATION_PASSED
VERIFICATION_FAILED

⸻

5.9 World

The World changed.

WORLD_CREATED
WORLD_UPDATED
WORLD_MERGED
WORLD_BRANCHED

⸻

5.10 Memory

Memory changed.

MEMORY_CREATED
MEMORY_UPDATED
MEMORY_INVALIDATED
MEMORY_DELETED

⸻

5.11 Knowledge

Knowledge changed.

KNOWLEDGE_CREATED
KNOWLEDGE_VALIDATED
KNOWLEDGE_CONTRADICTED
KNOWLEDGE_RETRACTED

⸻

5.12 Security

A security event occurred.

SECURITY_ALERT
POLICY_VIOLATION
ACCESS_DENIED
PRIVILEGE_ESCALATION_ATTEMPT

⸻

5.13 Evolution

Veda changed itself through an authorized evolution process.

EVOLUTION_PROPOSED
EVOLUTION_APPROVED
EVOLUTION_DEPLOYED
EVOLUTION_ROLLED_BACK

⸻

6. Event Envelope

Every event SHOULD use a common envelope.

event:
  event_id: evt_01JXYZ
  event_type: ACTION_COMPLETED
  timestamp:
    observed_at:
    recorded_at:
  actor:
    id:
    type:
  target:
    id:
    type:
  trace:
    trace_id:
    parent_event_id:
    causation_id:
  authorization:
    authorization_id:
    policy_id:
    lease_id:
  action:
    action_id:
    capability:
  world:
    world_id:
    world_version_before:
    world_version_after:
  payload:
  result:
    status:
    summary:
  verification:
    status:
    verification_id:
  integrity:
    sequence:
    previous_hash:
    event_hash:

⸻

7. Event Fields

7.1 event_id

Unique immutable identifier.

⸻

7.2 event_type

Defines what occurred.

⸻

7.3 timestamp

At minimum:

timestamp:
  observed_at:
  recorded_at:

The distinction matters because an event may occur before Veda records it.

⸻

7.4 actor

The entity responsible for producing the event.

Examples:

human
veda-core
coding-agent
research-agent
sensor
external-service

⸻

7.5 target

The primary entity affected or observed.

⸻

7.6 trace

Connects the event to a larger operation.

Example:

trace_001

may contain:

Intent
 ↓
Goal
 ↓
Plan
 ↓
Action
 ↓
Verification

⸻

8. Causation vs Correlation

Veda MUST distinguish:

causation

from:

correlation

Example:

Package Updated
      ↓
Build Failed

The system may observe temporal correlation.

It MUST NOT automatically claim causal certainty.

Causal confidence SHOULD be represented separately.

⸻

9. Parent Event

Events MAY have parent events.

Example:

ACTION_STARTED
      ↓
PROCESS_STARTED
      ↓
PROCESS_EXITED
      ↓
ACTION_COMPLETED

The parent relationship helps reconstruct execution trees.

⸻

10. Causation ID

causation_id identifies the event that directly caused the current event.

Example:

evt_build_failed
caused_by
evt_dependency_updated

This is distinct from parent_event_id.

⸻

11. Trace ID

A trace_id groups events belonging to one logical operation.

Example:

trace_123

may contain:

INTENT_CREATED
GOAL_CREATED
PLAN_CREATED
ACTION_PROPOSED
AUTHORIZATION_GRANTED
ACTION_STARTED
ACTION_COMPLETED
VERIFICATION_PASSED

A trace represents the lifecycle of a task.

⸻

12. Action Correlation

Events related to the same action MUST reference the same action_id.

Example:

ACTION_PROPOSED
ACTION_AUTHORIZED
ACTION_STARTED
ACTION_COMPLETED
VERIFICATION_PASSED

All share:

action_id = act_001

⸻

13. Event State

Events themselves have lifecycle states.

CREATED
 ↓
RECORDED
 ↓
VALIDATED
 ↓
COMMITTED

Invalid events MUST NOT become part of authoritative world history.

⸻

14. Append-Only Principle

Committed events MUST be immutable.

Veda MUST NOT edit historical events.

If an event was incorrect, Veda creates another event.

Example:

KNOWLEDGE_CREATED
      ↓
KNOWLEDGE_RETRACTED

History remains intact.

⸻

15. Event Correction

Corrections are represented through new events.

Forbidden:

Edit old event

Required:

Old Event
   ↓
Correction Event

This preserves auditability.

⸻

16. Event Ordering

Events require deterministic ordering.

Veda SHOULD use:

* physical timestamp,
* logical sequence,
* causal ordering.

When timestamps conflict, causal relationships take precedence.

⸻

17. Sequence Number

Each Event Store partition SHOULD maintain a monotonic sequence.

Example:

1001
1002
1003
1004

Sequence gaps MUST be detectable.

A gap does not automatically mean data loss, but MUST trigger investigation when the storage policy requires contiguous history.

⸻

18. Event Store

Veda requires an append-oriented Event Store.

Conceptually:

Event Store
│
├── evt_001
├── evt_002
├── evt_003
├── evt_004
└── evt_005

The Event Store is the authoritative historical source for committed events.

⸻

19. Event Sourcing

The World MAY be reconstructed from events.

Initial World
     +
Event 1
     +
Event 2
     +
Event 3
     =
World N

Conceptually:

world = initial_world()
for event in event_stream:
    world = apply(world, event)

The resulting state MUST be deterministic for authoritative replay.

⸻

20. Snapshotting

Replay from the beginning of history may become expensive.

Veda MAY create World snapshots.

Example:

Events 1 ─────────────── 10000
             ↓
        Snapshot W10000
             ↓
Events 10001 ─────────── 20000
             ↓
        Snapshot W20000

Snapshots are optimizations.

Events remain the historical authority.

⸻

21. Idempotency

An action may accidentally execute more than once.

Veda MUST support idempotency.

Each externally consequential action SHOULD have:

idempotency_key

Example:

push_repo:veda:commit:abc123

If the same action is received twice, Veda SHOULD detect the duplicate.

⸻

22. Event Deduplication

Duplicate events MAY occur because of:

* network retry,
* process restart,
* distributed execution,
* agent retry.

The Event Store MUST support duplicate detection where required.

⸻

23. Transactions

Multiple events may belong to one logical transaction.

Example:

Transaction T1
1. ACTION_STARTED
2. FILE_WRITTEN
3. BUILD_STARTED
4. BUILD_COMPLETED
5. VERIFICATION_PASSED
6. TRANSACTION_COMMITTED

If a transaction fails:

TRANSACTION_ABORTED

or a compensation workflow begins.

⸻

24. Compensation

Not every operation is physically reversible.

In those cases Veda MAY use compensating actions.

Example:

SEND_EMAIL
     ↓
Cannot "unsend"
     ↓
SEND_CORRECTION_EMAIL

Compensation is not equivalent to time travel.

The original event remains in history.

⸻

25. Event Integrity

Committed events SHOULD be tamper-evident.

Recommended structure:

Event N
   ↓
Hash(Event N)
   ↓
Event N+1
   ↓
Hash(Event N+1 + Previous Hash)

This creates a hash chain.

⸻

26. Event Hash

Conceptually:

event_hash =
HASH(
    event_payload
    + previous_event_hash
)

Changing an earlier event invalidates subsequent hashes.

⸻

27. Event Signatures

Future implementations MAY support digital signatures.

Signatures may prove:

* event origin,
* actor identity,
* system authenticity.

Signatures are complementary to authorization.

A signed event is not automatically an authorized event.

⸻

28. Event Provenance

Every important event SHOULD answer:

Who?
What?
Why?
When?
Where?
Under whose authority?
Using which capability?
With what result?
Verified by whom?

⸻

29. Event Payload

Payload contains event-specific information.

Example:

payload:
  file:
    path: /veda/project/main.py
    size_before: 1024
    size_after: 1400

Payload schemas SHOULD be versioned.

⸻

30. Before / After State

State-changing events SHOULD contain references to:

state:
  before:
  after:

Large states SHOULD NOT be duplicated unnecessarily.

References or hashes MAY be used.

⸻

31. Verification Link

Every significant action SHOULD link to a verification event.

Example:

ACTION_COMPLETED
       ↓
VERIFICATION_STARTED
       ↓
VERIFICATION_PASSED

Execution without verification is incomplete for significant operations.

⸻

32. World Transition

A committed event may produce a World transition.

World W1
   +
Event E1
   =
World W2

The World Engine MUST reject events that violate World integrity rules.

⸻

33. Invalid Events

An event may be rejected because:

* schema invalid,
* actor unknown,
* authorization invalid,
* capability unavailable,
* scope violation,
* causal inconsistency,
* world integrity violation.

Rejected events MUST NOT mutate authoritative World state.

They SHOULD still be recorded as rejected attempts when security/audit policy requires it.

⸻

34. Security Events

Security events have elevated priority.

Examples:

ACCESS_DENIED
POLICY_VIOLATION
INVALID_AUTHORIZATION
LEASE_EXPIRED
PRIVILEGE_ESCALATION_ATTEMPT

Security events MUST NOT be silently discarded.

⸻

35. Human Events

Human actions are first-class events.

Examples:

USER_APPROVED
USER_REJECTED
USER_PAUSED
USER_STOPPED
USER_REVOKED

Human approval MUST be represented as an event.

⸻

36. Agent Events

Agents produce events through the same Event Model as other actors.

Agents MUST NOT receive a separate hidden event channel.

This ensures that agent activity remains auditable.

⸻

37. Model Events

Model inference MAY produce metadata events.

Example:

model_event:
  model_id:
  model_version:
  prompt_hash:
  input_context_hash:
  output_hash:
  latency:

Sensitive raw prompts SHOULD NOT automatically be stored in plaintext.

⸻

38. Privacy

Event storage MUST respect data minimization.

Sensitive payloads MAY use:

* encryption,
* references,
* hashes,
* redaction,
* access-controlled storage.

Auditability MUST NOT require unnecessary exposure of private information.

⸻

39. Event Retention

Retention policies MAY differ by event category.

Example:

Event	Retention
Security	Long-term
Authorization	Long-term
Constitutional	Permanent
Debug	Configurable
Telemetry	Short-term
Cache	Disposable

Deletion policies MUST NOT silently destroy constitutionally required audit history.

⸻

40. Chronicle

The Veda Chronicle is a human-readable interpretation of Event history.

Example:

2026-09-16 03:15
User requested:
"Build RFC-0003."
Veda created:
Goal G-1042
Planner generated:
Plan P-883
Authorization:
Granted
Action:
Created RFC-0003
Verification:
Passed
Result:
Committed

Chronicle is a view over authoritative events.

Chronicle MUST NOT become the authoritative source itself.

⸻

41. Event Query

The Event System SHOULD support queries such as:

What happened?
Who did it?
Why did it happen?
What changed?
What caused this?
What happened before this?
What happened after this?
Which actions failed?
Which permissions were denied?
Which agent caused the change?

⸻

42. Event Replay

Given:

Initial World
+
Event Stream

Veda SHOULD be able to reconstruct historical World states.

Example:

Replay to:
2026-09-15 10:00

The system reconstructs the corresponding World version.

⸻

43. Branching History

Veda MAY support alternate histories.

             W100
              |
             E101
              |
             W101
            /    \
         E102    E202
          |        |
        W102     W202

This enables:

* simulation,
* counterfactuals,
* experiments,
* what-if analysis.

Branches MUST NOT silently modify the primary world.

⸻

44. Event Priority

Events MAY have priority.

Example:

CRITICAL
HIGH
NORMAL
LOW
TELEMETRY

Security and emergency events SHOULD receive elevated processing priority.

⸻

45. Event Delivery

The Event System MAY provide:

* synchronous delivery,
* asynchronous delivery,
* subscriptions,
* queues,
* streams.

Authoritative commit MUST be distinguished from eventual delivery.

An event being delivered to an agent does not mean it has been committed.

⸻

46. Event Consumers

Consumers may include:

* World Engine
* Memory Engine
* Chronicle
* Audit Engine
* Security Engine
* Learning Engine
* Analytics
* Diagnostics
* Planner

Consumers MUST treat events as immutable.

⸻

47. Event Schema Versioning

Every event type SHOULD have a schema version.

Example:

event_type: ACTION_COMPLETED
schema_version: 1

Breaking changes require a new schema version.

Historical events MUST remain readable.

⸻

48. Event Failure Handling

If an event consumer fails:

Event
 ↓
Consumer
 ↓
Failure
 ↓
Retry

Retries MUST NOT create duplicate effects.

Consumers SHOULD use idempotent processing.

⸻

49. Event Dead Letter

Events that repeatedly fail processing MAY enter:

Dead Letter Queue

Example:

EVENT
 ↓
PROCESSING FAILED
 ↓
RETRY × N
 ↓
DEAD LETTER

Dead-letter events remain auditable.

⸻

50. Event Security Boundary

No AI model may directly modify authoritative Event history.

The Event Store MUST be controlled by the runtime.

Forbidden:

LLM → Event Store

Required:

LLM
 ↓
Action / Proposal
 ↓
Runtime
 ↓
Event Store

⸻

51. Event-to-Memory Pipeline

Events may become experiences.

Event
 ↓
Experience Extraction
 ↓
Reflection
 ↓
Lesson
 ↓
Memory

Events themselves remain historical facts.

Interpretation occurs separately.

⸻

52. Event-to-Knowledge Pipeline

Events may contribute to knowledge.

Events
 ↓
Evidence
 ↓
Claim
 ↓
Validation
 ↓
Knowledge

No automatic promotion from event to knowledge is permitted.

⸻

53. Event-to-Learning Pipeline

Learning may analyze event patterns.

Examples:

Repeated Build Failure
        ↓
Pattern Detection
        ↓
Hypothesis
        ↓
Experiment
        ↓
Validated Lesson

Observed frequency does not prove causality.

⸻

54. Event Integrity Invariants

The following invariants MUST hold.

ID	Invariant
E1	Event IDs are unique.
E2	Committed events are immutable.
E3	Historical events are append-only.
E4	Significant actions produce traceable events.
E5	Authorization events are auditable.
E6	World-changing events reference affected state.
E7	Verification events reference the action being verified.
E8	Rejected events cannot mutate authoritative World state.
E9	Event consumers must support duplicate-safe processing.
E10	AI models cannot directly rewrite Event history.
E11	Security events cannot be silently discarded.
E12	Historical events remain reconstructable.

⸻

55. Reference Event

Canonical example:

event:
  event_id: evt_01JXYZ123
  event_type: ACTION_COMPLETED
  schema_version: 1
  timestamp:
    observed_at: 2026-09-16T03:20:10Z
    recorded_at: 2026-09-16T03:20:11Z
  actor:
    id: agent_coding_01
    type: AGENT
  target:
    id: file_rfc_0003
    type: DOCUMENT
  trace:
    trace_id: trace_001
    parent_event_id: evt_action_started
    causation_id: evt_action_started
  authorization:
    authorization_id: auth_001
    policy_id: policy_workspace_write
    lease_id: lease_001
  action:
    action_id: act_001
    capability: filesystem.write
  world:
    world_id: world_primary
    world_version_before: W10482
    world_version_after: W10483
  result:
    status: SUCCESS
    summary: RFC-0003 created
  verification:
    status: PASSED
    verification_id: ver_001
  integrity:
    sequence: 10483
    previous_hash: sha256:...
    event_hash: sha256:...

⸻

56. Reference Event Lifecycle

                ┌───────────────┐
                │ Event Proposed│
                └───────┬───────┘
                        ↓
                ┌───────────────┐
                │ Schema Check  │
                └───────┬───────┘
                        ↓
                ┌───────────────┐
                │ Authorization │
                └───────┬───────┘
                        ↓
                ┌───────────────┐
                │ Event Recorded│
                └───────┬───────┘
                        ↓
                ┌───────────────┐
                │ Event Validated
                └───────┬───────┘
                        ↓
                ┌───────────────┐
                │ Event Committed
                └───────┬───────┘
                        ↓
                ┌───────────────┐
                │ World Updated │
                └───────┬───────┘
                        ↓
                ┌───────────────┐
                │ Consumers     │
                └───────────────┘

⸻

57. Relationship With RFC-0002

RFC-0002 defines:

What exists?

RFC-0003 defines:

What happened?

Together:

World Model
     +
Event Model
     =
World History

⸻

58. Relationship With RFC-0001A

RFC-0001A defines:

May this action happen?

RFC-0003 records:

What actually happened?

Therefore:

Permission
    ↓
Authorization
    ↓
Action
    ↓
Event

⸻

59. Future Dependencies

RFC-0003 provides foundations for:

* RFC-0004 — State & World Transition
* RFC-0017 — Memory Model
* RFC-0018 — Brain Architecture
* RFC-0021 — Temporal Model
* RFC-0022 — Causal Model
* RFC-0023 — Future & Scenario Engine
* RFC-0026 — Verification Engine
* RFC-0027 — Rollback & Recovery
* RFC-0031 — Event / Audit / Trace Fabric
* RFC-0032 — Veda Chronicle
* RFC-0034 — Self-Diagnostics
* RFC-0035 — Experience Model
* RFC-0036 — Reflection & Learning

⸻

60. Conformance

A Veda implementation conforms to RFC-0003 if it provides:

1. Immutable event identity.
2. Append-only historical storage.
3. Actor attribution.
4. Action correlation.
5. Trace correlation.
6. Causal references.
7. Authorization references.
8. World version references.
9. Verification references.
10. Event integrity protection.
11. Duplicate-safe processing.
12. Historical replay capability.
13. Auditability.

⸻

61. Status

Draft v0.1.0

RFC-0003 defines the historical event foundation of Veda.

The Event Model is not merely a logging subsystem.

It is the temporal record through which Veda understands:

What happened.
Who caused it.
Why it happened.
What changed.
What was expected.
What actually happened.
Whether it was authorized.
Whether it was verified.
What should be learned from it.

The Event Model therefore forms the bridge between World, Action, Memory, Verification, Learning, and Evolution.

⸻
