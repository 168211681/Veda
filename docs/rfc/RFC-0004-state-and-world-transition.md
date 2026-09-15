RFC-0004 — Veda State & World Transition

RFC: RFC-0004
Title: Veda State & World Transition
Status: Draft
Version: 0.1.0
Category: Core / Runtime / State Management
Depends on: RFC-0001, RFC-0001A, RFC-0002, RFC-0003

⸻

Abstract

RFC-0004 defines how Veda transforms one World State into another.

RFC-0002 defines what the World is.

RFC-0003 defines what Events are.

RFC-0004 defines the transition between them.

The fundamental operation is:

World(t)
   +
Event
   ↓
Validation
   ↓
Transition
   ↓
World(t+1)

This document defines:

* state representation,
* transition rules,
* preconditions,
* postconditions,
* invariants,
* transactions,
* commit,
* rollback,
* compensation,
* concurrency,
* conflict detection,
* world snapshots,
* branching,
* deterministic replay.

⸻

1. Motivation

A World Model without a formal transition system has no reliable mechanism for changing state.

Veda must be able to answer:

Given the current World and an Event, what exactly changes?

The transition engine provides this answer.

The engine MUST prevent arbitrary state mutation.

Forbidden:

Agent
 ↓
Directly modify World

Required:

Agent
 ↓
Action
 ↓
Event
 ↓
Transition Validation
 ↓
World Transition
 ↓
Verification
 ↓
Commit

⸻

2. Core Transition Equation

The canonical transition is:

W(t+1) = T(W(t), E)

Where:

* W(t) = current World.
* E = committed Event.
* T = deterministic transition function.
* W(t+1) = resulting World.

If the Event is invalid:

T(W(t), E) = REJECT

The authoritative World MUST remain unchanged.

⸻

3. Transition Pipeline

Event
 ↓
Schema Validation
 ↓
Identity Validation
 ↓
Authorization Validation
 ↓
Precondition Check
 ↓
Invariant Check
 ↓
State Transition
 ↓
Postcondition Check
 ↓
Verification
 ↓
Commit

A transition MUST NOT be committed if a required validation fails.

⸻

4. State Model

Every World contains state.

Conceptually:

world:
  version:
  entities:
  relationships:
  states:
  metadata:

State belongs to entities.

Example:

entity:
  id: device_t480
state:
  power: on
  cpu_usage: 0.42
  memory_usage: 0.71

⸻

5. State Is Not History

State represents the current or reconstructed condition.

History represents what happened.

Therefore:

State ≠ Event History

The Event Store remains authoritative for historical reconstruction.

The World State is a derived representation.

⸻

6. Transition Function

Conceptual implementation:

def transition(world, event):
    validate_event(event)
    validate_authorization(event)
    validate_preconditions(world, event)
    validate_invariants(world, event)
    next_world = apply_event(world, event)
    validate_postconditions(next_world, event)
    return next_world

The function MUST be deterministic for authoritative replay.

⸻

7. Preconditions

A precondition describes what MUST be true before an action changes the World.

Example:

preconditions:
  - entity_exists: repo_veda
  - branch_exists: main
  - permission: github.push

If any required precondition fails:

Transition = REJECTED

⸻

8. Postconditions

A postcondition describes what MUST be true after transition.

Example:

postconditions:
  - commit_exists: abc123
  - branch_head: abc123

If execution occurred but postconditions fail:

Execution Success ≠ Transition Success

The transition enters verification failure handling.

⸻

9. Invariants

Invariants are conditions that must always remain true.

Examples:

Entity IDs are unique.
Relationships reference valid entities.
Constitution cannot be modified by ordinary agents.
Unauthorized actions cannot change state.

Invariant violations MUST prevent commit.

⸻

10. Transition Result

A transition returns one of:

ACCEPTED
REJECTED
FAILED
CONFLICT
PENDING_VERIFICATION
COMMITTED
ROLLED_BACK

⸻

11. State Mutation Rules

Only the World Transition Engine may commit authoritative World state.

Forbidden:

LLM → World Mutation
Agent → World Mutation
Tool → World Mutation

Required:

Event
 ↓
Transition Engine
 ↓
World State

⸻

12. Entity Creation

Entity creation creates:

1. Entity identity.
2. Initial state.
3. Creation event.
4. World version.

Example:

World W1
   ↓
ENTITY_CREATED
   ↓
World W2

⸻

13. Entity Update

Entity updates MUST preserve identity.

Example:

Entity:
  id = device_001
Before:
  battery = 80%
After:
  battery = 79%

The ID remains unchanged.

⸻

14. Entity Deletion

Deletion is a state transition.

Historical events MUST remain.

Preferred semantic model:

ACTIVE
  ↓
DEACTIVATED

rather than physically erasing history.

Hard deletion MAY occur for privacy requirements where constitutionally permitted.

⸻

15. Relationship Transition

Relationships may be:

* created,
* modified,
* terminated,
* expired.

Example:

User
  owns
   ↓
Project

If ownership changes:

User A
  owns
   ↓
Project
       ↓
User B
  owns
   ↓
Project

Historical ownership remains reconstructable.

⸻

16. State Version

Every committed World state receives a version.

Example:

W100
 ↓
W101
 ↓
W102

Each version references its parent.

⸻

17. World Commit

A World transition becomes authoritative only after commit.

Candidate World
      ↓
Validation
      ↓
Verification
      ↓
COMMIT
      ↓
Authoritative World

Before commit, the state is provisional.

⸻

18. Atomicity

A logical transaction SHOULD be atomic.

Example:

Transaction T1
1. Create file
2. Update project state
3. Record event
4. Verify result

Either:

COMMIT

or:

ABORT

Partial state MUST NOT become authoritative unless explicitly modeled as an intermediate state.

⸻

19. Transaction Model

Conceptual:

transaction:
  id: tx_001
  base_world: W100
  events:
    - evt_101
    - evt_102
  status: pending

Lifecycle:

BEGIN
 ↓
PREPARE
 ↓
VALIDATE
 ↓
EXECUTE
 ↓
VERIFY
 ↓
COMMIT

Failure:

ABORT

⸻

20. Idempotency

A transition MUST support idempotency for actions where duplicate execution is possible.

Example:

Action A
idempotency_key = github_push:commit123

If the same action is submitted twice, Veda SHOULD detect the duplicate.

⸻

21. Rollback

Rollback returns the World to a previous valid state where technically possible.

W100
 ↓
W101
 ↓
W102
 ↓
Failure
 ↓
Rollback
 ↓
W101

Rollback MUST itself create events.

History is never erased.

⸻

22. Compensation

When direct rollback is impossible, Veda uses compensation.

Example:

External payment
      ↓
Cannot rollback
      ↓
Compensating transaction

Compensation creates new events.

⸻

23. Rollback vs Compensation

Mechanism	Meaning
Rollback	Restore prior state
Compensation	Perform corrective action
Retry	Repeat operation
Abort	Stop operation

These mechanisms MUST NOT be treated as interchangeable.

⸻

24. Verification

Verification evaluates whether the resulting World matches expectations.

Example:

Expected:
file_exists = true
Observed:
file_exists = true
Result:
VERIFIED

If:

Expected ≠ Observed

then:

VERIFICATION_FAILED

⸻

25. Expected State

Actions SHOULD produce expected state descriptions.

Example:

expected:
  - file:
      path: /veda/test.txt
      exists: true

The verification engine compares expected and observed state.

⸻

26. State Delta

Transitions SHOULD generate a World Delta.

Example:

delta:
  world_before: W100
  world_after: W101
  changes:
    - entity: file_test
      field: exists
      before: false
      after: true

⸻

27. Delta Validation

A Delta MUST:

* reference a valid base World.
* reference valid entities.
* specify changed fields.
* preserve invariants.
* identify originating event.

⸻

28. Concurrency

Multiple agents may attempt changes simultaneously.

Example:

Agent A → W100
Agent B → W100

Both produce changes.

The World Engine MUST detect whether they conflict.

⸻

29. Optimistic Concurrency

Veda SHOULD use optimistic concurrency by default.

An action references:

base_world = W100

If current World is:

W101

the transition engine evaluates whether the proposed delta remains valid.

⸻

30. Conflict

A conflict occurs when two changes cannot safely coexist.

Example:

Agent A:
name = Veda
Agent B:
name = VEDA

The Conflict Engine evaluates the changes.

Possible results:

MERGE
REJECT
HUMAN_REVIEW

⸻

31. Conflict Detection

Conflicts may include:

* same field changed differently,
* deleted entity modified,
* relationship contradiction,
* authorization invalidation,
* stale state,
* incompatible schema.

⸻

32. Merge

Compatible changes MAY be merged.

Example:

Agent A:
cpu_limit = 2
Agent B:
memory_limit = 4GB

Result:

cpu_limit = 2
memory_limit = 4GB

⸻

33. Human Conflict Resolution

High-impact unresolved conflicts SHOULD escalate to human review.

Example:

Conflict
 ↓
Simulation
 ↓
No safe automatic resolution
 ↓
Human Review

Human decision becomes an Event.

⸻

34. World Branching

World may branch for simulation.

             W100
              |
             W101
            /    \
        Branch A Branch B

Branches allow:

* experimentation,
* simulation,
* counterfactual reasoning,
* planning.

Only the selected branch may become authoritative.

⸻

35. Branch Isolation

Simulation branches MUST NOT automatically modify the primary World.

Example:

Primary World
     |
     +---- Simulation A
     |
     +---- Simulation B

Changes remain isolated until explicitly merged.

⸻

36. Merge Rules

A branch may be merged only when:

* base World is known,
* changes are validated,
* conflicts are resolved,
* authorization exists,
* required verification passes.

⸻

37. State Machine

Generic entity state machine:

CREATED
   ↓
ACTIVE
   ↓
MODIFIED
   ↓
DEACTIVATED
   ↓
ARCHIVED

Specific entities MAY define specialized state machines.

⸻

38. Illegal Transitions

Example:

ARCHIVED → ACTIVE

may be prohibited unless explicitly defined.

The World Engine MUST reject undefined state transitions.

⸻

39. Transition Policies

Policies determine:

* who may transition,
* which states are valid,
* which capabilities are required,
* which risk class applies.

Policies are evaluated before commit.

⸻

40. Transition Preconditions

Examples:

preconditions:
  - actor_authorized
  - target_exists
  - world_version_matches
  - lease_valid
  - required_resource_available

⸻

41. Transition Postconditions

Examples:

postconditions:
  - target_state_changed
  - event_committed
  - verification_passed

⸻

42. Deterministic Replay

Given identical:

Initial World
+
Identical Event Stream

the resulting World MUST be identical.

Replay(W0, Events)
=
Wn

This is essential for debugging and trust.

⸻

43. Non-Deterministic Inputs

Some events originate from non-deterministic systems:

* sensors,
* external APIs,
* model inference,
* network responses.

These observations MUST be captured as Events.

Replay uses the recorded observation rather than re-querying the external world.

⸻

44. External World

The real world may change independently of Veda.

Therefore:

Veda World ≠ Reality

Veda World is a model of relevant reality.

The difference between predicted and observed reality is important.

⸻

45. World Reconciliation

Veda MAY reconcile its World against external observations.

Veda Prediction
      ↓
External Observation
      ↓
Difference
      ↓
World Correction Event

Corrections MUST preserve history.

⸻

46. Stale State

World state may become stale.

Each state SHOULD have:

* observed_at,
* valid_from,
* valid_until,
* confidence.

Stale information MUST NOT silently be treated as current fact.

⸻

47. State Confidence

Example:

state:
  battery:
    value: 72
    confidence: 0.98
    observed_at: 2026-09-16T03:30:00Z

Confidence does not equal truth.

⸻

48. World Integrity Check

Before commit:

Check:
 ├── Identity
 ├── Relationships
 ├── State
 ├── Authorization
 ├── Preconditions
 ├── Invariants
 └── Schema

Any critical failure prevents commit.

⸻

49. Commit Protocol

Canonical commit sequence:

PROPOSE
   ↓
VALIDATE
   ↓
PREPARE
   ↓
EXECUTE
   ↓
OBSERVE
   ↓
VERIFY
   ↓
COMMIT

The commit operation MUST be atomic within the authoritative World Store.

⸻

50. Crash Recovery

If Veda crashes during a transition:

Transition Started
      ↓
CRASH

On restart Veda examines:

* transaction state,
* event store,
* world version,
* commit markers.

The runtime determines whether to:

COMMIT
ROLLBACK
RECOVER
ABORT

⸻

51. Write-Ahead Strategy

Implementations SHOULD record necessary transaction intent before applying irreversible state changes.

Conceptually:

Intent
 ↓
Event Record
 ↓
State Change
 ↓
Commit Marker

This improves crash recovery.

⸻

52. Snapshot Strategy

Snapshots SHOULD be created periodically.

A snapshot contains:

snapshot:
  world_id:
  world_version:
  created_at:
  state_hash:
  storage_reference:

Snapshots are derived artifacts.

They do not replace event history.

⸻

53. State Hash

World snapshots MAY contain a cryptographic state hash.

Conceptually:

world_hash =
HASH(canonical_world_representation)

This allows integrity comparisons.

⸻

54. Canonical Representation

The same World MUST produce the same canonical representation regardless of:

* map ordering,
* serialization implementation,
* storage engine.

Canonical serialization is REQUIRED for deterministic hashing.

⸻

55. Transition Observability

Every transition SHOULD expose:

* transition ID,
* event ID,
* world before,
* world after,
* duration,
* result,
* verification state.

⸻

56. Transition Trace

Example:

Trace T001
W100
 ↓
Action Proposed
 ↓
Authorization
 ↓
Event E101
 ↓
Transition
 ↓
Candidate W101
 ↓
Verification
 ↓
COMMIT
 ↓
W101

⸻

57. Security Requirements

The Transition Engine MUST:

* validate authorization,
* enforce scope,
* reject unauthorized state mutation,
* preserve audit records,
* reject invalid transitions,
* protect constitutional state.

No AI model may bypass it.

⸻

58. Privacy Requirements

Transition payloads SHOULD minimize sensitive information.

Sensitive state MAY be referenced indirectly.

Example:

secret_ref: vault://secret/123

rather than embedding secrets into events.

⸻

59. Performance

The World Transition Engine SHOULD support:

* incremental updates,
* snapshots,
* indexed events,
* cached state,
* batch transitions where safe.

Performance optimizations MUST NOT violate deterministic replay or integrity.

⸻

60. Transition Invariants

The following invariants are mandatory.

ID	Invariant
WT-1	Every authoritative state change originates from a valid Event.
WT-2	Invalid Events cannot mutate authoritative World state.
WT-3	Every committed World version has a valid parent.
WT-4	Entity identity remains stable.
WT-5	Historical Events remain immutable.
WT-6	Significant transitions require verification.
WT-7	Unauthorized transitions cannot commit.
WT-8	Rollback never deletes history.
WT-9	Simulation branches cannot silently modify primary World.
WT-10	Deterministic replay must reproduce authoritative state.
WT-11	Conflicts must be detected before unsafe merge.
WT-12	Constitutional state cannot be autonomously modified.

⸻

61. Reference Transition

Example:

transition:
  id: tr_001
  base_world: W100
  event:
    id: evt_101
    type: FILE_CREATED
  preconditions:
    - workspace_exists
    - actor_authorized
    - lease_valid
  changes:
    - entity: file_001
      state:
        exists:
          before: false
          after: true
  postconditions:
    - file_exists
  verification:
    status: PASSED
  result:
    status: COMMITTED
  resulting_world:
    version: W101

⸻

62. Reference State Transition

                 ┌───────────────┐
                 │   World W100  │
                 └───────┬───────┘
                         │
                       Event
                         │
                         ▼
                ┌─────────────────┐
                │    Validate     │
                └────────┬────────┘
                         │
                    Preconditions
                         │
                         ▼
                ┌─────────────────┐
                │ Apply Transition│
                └────────┬────────┘
                         │
                         ▼
                 ┌───────────────┐
                 │ Candidate W101│
                 └───────┬───────┘
                         │
                     Verify
                         │
                  ┌──────┴──────┐
                  │             │
                PASS           FAIL
                  │             │
                  ▼             ▼
               COMMIT        ROLLBACK
                  │
                  ▼
              World W101

⸻

63. Relationship With Previous RFCs

RFC-0001

Defines:

What Veda must never violate.

RFC-0001A

Defines:

Who may perform which capability.

RFC-0002

Defines:

What exists in the World.

RFC-0003

Defines:

What happened.

RFC-0004

Defines:

How what happened changes the World.

Together:

Constitution
      ↓
Permission
      ↓
World
      ↓
Event
      ↓
Transition
      ↓
New World

⸻

64. Future RFC Dependencies

RFC-0004 provides the foundation for:

* RFC-0005 — Intent Model
* RFC-0006 — Goal Model
* RFC-0007 — Process Model
* RFC-0008 — Action Model
* RFC-0010 — Authorization & Policy
* RFC-0017 — Memory Model
* RFC-0020 — Planner
* RFC-0021 — Temporal Model
* RFC-0022 — Causal Model
* RFC-0023 — Future & Scenario Engine
* RFC-0024 — Simulation & Counterfactual Engine
* RFC-0026 — Verification Engine
* RFC-0027 — Rollback & Recovery
* RFC-0043 — World Delta Protocol

⸻

65. Conformance

An implementation conforms to RFC-0004 if it provides:

1. Formal World transitions.
2. Preconditions.
3. Postconditions.
4. Invariant checking.
5. Transaction handling.
6. Commit semantics.
7. Rollback or compensation.
8. Conflict detection.
9. Deterministic replay.
10. World versioning.
11. Crash recovery.
12. Transition auditability.

⸻

66. Final Principle

The Veda World MUST NOT change merely because an AI said that it should change.

The correct sequence is:

Intelligence
    ↓
Intent
    ↓
Goal
    ↓
Plan
    ↓
Action
    ↓
Authorization
    ↓
Event
    ↓
Transition
    ↓
Verification
    ↓
Commit
    ↓
World(t+1)

This sequence is the fundamental state-transition law of Veda.

⸻

67. Status

Draft v0.1.0

RFC-0004 defines the state-transition semantics of Veda and establishes the mechanism by which historical Events become authoritative World State.

Without RFC-0004, Veda can describe a world and record events.

With RFC-0004, Veda can formally transform, verify, reconstruct, branch, recover, and reason about that world.
