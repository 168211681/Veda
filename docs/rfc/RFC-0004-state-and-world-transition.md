RFC-0004 — Veda State & World Transition

RFC: RFC-0004
Title: Veda State & World Transition
Status: Draft
Version: 0.2.0-proposed
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
Validated Factual Inputs + Authorization
   ↓
Prepare Candidate against Exact Parent
   ↓
Execution → Observation → Verification
   ↓
World Kernel Atomic Commit
   ↓
World(t+1) + WORLD_COMMIT_RECEIPT + Delivery Intent

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

Given the current World, validated factual inputs, authorization, and verification, what exactly changes?

The transition engine provides this answer.

The engine MUST prevent arbitrary state mutation.

Forbidden:

Agent
 ↓
Directly modify World

Required:

Agent → Action → Authorization
 ↓
Validated Factual Inputs
 ↓
Prepare Candidate at Exact Parent
 ↓
Execution → Observation → Verification
 ↓
World Kernel Atomic Commit
 ↓
Post-Commit Successful Outcome Event

⸻

2. Core Transition Equation

The canonical transition is:

W(t+1) = T(W(t), I, V)

Where:

* W(t) = current World.
* I = validated factual inputs, applicable authorization, and candidate transition.
* V = verification receipt bound to the exact parent, candidate hash, evidence set, and verification policy version.
* T = deterministic transition function.
* W(t+1) = resulting World.

If the Event is invalid:

T(W(t), E) = REJECT

The authoritative World MUST remain unchanged.

⸻

3. Transition Pipeline

Validated Inputs
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
Candidate Transition Preparation
 ↓
Execution
 ↓
Observation
 ↓
Postcondition Check
 ↓
Verification
 ↓
World Kernel Atomic Commit
 ↓
Successful Outcome Event

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

Chronicle remains authoritative for immutable historical facts and receipts.

The World Kernel remains authoritative for current modeled World State. Replay derives a state projection only from valid World commit receipts and their bound inputs, not from every historical fact.

⸻

6. Transition Function

Conceptual implementation:

def transition(world, inputs, verification):
    validate_inputs(inputs)
    validate_authorization(inputs)
    validate_preconditions(world, inputs)
    validate_invariants(world, inputs)
    validate_verification_binding(world.version, inputs, verification)
    next_world = apply_candidate(world, inputs)
    validate_postconditions(next_world, inputs)
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

Only the World Kernel may commit authoritative modeled World State. A Transition Engine may prepare, validate, and apply a candidate within the controlled boundary, but it has no independent commit authority.

Forbidden:

LLM → World Mutation
Agent → World Mutation
Tool → World Mutation

Required:

Validated Inputs
 ↓
Transition Engine
 ↓
World Kernel Commit Boundary
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

A World commit MUST make the following visible as one conceptual atomic unit:

1. the new authoritative World version;
2. an immutable `WORLD_COMMIT_RECEIPT`; and
3. a durable committed-event delivery intent.

All three become visible or none do. A broader logical transaction SHOULD be atomic where technically possible.

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

If the same action is submitted twice, Veda MUST detect the duplicate when consequential effects are possible. Re-delivery of a committed-event intent MUST NOT re-execute the transition.

⸻

21. Rollback

Rollback creates a new corrective World version whose modeled contents restore a previous valid condition where technically possible.

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
W103 (content derived from W101)

The rollback or recovery attempt MUST itself create durable factual and audit records. An attempted correction is not a successful outcome: a `WORLD_COMMIT_RECEIPT` and post-commit successful outcome event MAY be produced only if the corrective transition completes the required Execution → Observation → Verification lifecycle, its verification receipt is applicable to the exact current parent and candidate, and the World Kernel commits it successfully within the atomic World-version/receipt/delivery-intent boundary. A failed, rejected, conflicted, or indeterminate rollback MUST instead produce truthful failure or recovery-required evidence, preserve the original history, and MUST NOT claim successful completion or emit a successful outcome event.

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
Rollback	Create a new version restoring a prior modeled condition
Compensation	Perform corrective action
Retry	Repeat operation
Abort	Stop operation

These mechanisms MUST NOT be treated as interchangeable.

⸻

24. Verification

Verification evaluates whether observed outcomes and the candidate World match expectations before authoritative World commitment.

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

Every verification receipt for a consequential transition MUST bind the exact parent World version, candidate transition hash, evidence set, and verification policy version. It authorizes no commit by itself.

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

The World Kernel commit boundary MUST detect whether they conflict.

⸻

29. Optimistic Concurrency

Veda SHOULD use optimistic concurrency by default.

An action references:

base_world = W100

If current World is:

W101

the World Kernel compare-and-swap MUST reject the commit. The previous verification receipt is inapplicable because its parent binding is stale. Any retry or merge MUST prepare a new candidate against `W101` and obtain new verification.

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

Compatible changes MAY be merged only by preparing a new candidate against the current parent and obtaining new verification. A merge MUST NOT reuse stale verification from either original candidate.

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

The World Kernel MUST reject undefined state transitions.

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
  - pre_commit_facts_durable
  - verification_passed

⸻

42. Deterministic Replay

Given identical:

Initial World
+
Identical Canonical World Commit Receipts and Bound Inputs

the resulting World MUST be identical.

Replay(W0, Receipts)
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

Before commit, the verification receipt MUST be checked against the exact current parent World version, candidate transition hash, evidence set, and verification policy version. The World Kernel then performs compare-and-swap and MUST atomically make visible the World version, `WORLD_COMMIT_RECEIPT`, and committed-event delivery intent. A parent mismatch fails closed, records the conflict, and requires re-preparation and re-verification.

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
* `WORLD_COMMIT_RECEIPT` records,
* committed-event delivery intents and delivery acknowledgments.

Recovery MUST determine authority from the atomic triple, never from outcome-event delivery alone. If the triple is absent, the candidate did not commit; if it is present, the World version is authoritative and delivery resumes idempotently. An absent triple may be retried only after rechecking authorization, current parent, and verification applicability. The runtime then determines whether to:

RETRY_COMMIT_AFTER_REVALIDATION
COMPENSATE
RECOVER
ABORT

⸻

51. Write-Ahead Strategy

Implementations SHOULD record necessary transaction intent before applying irreversible state changes.

Conceptually:

Intent
 ↓
Pre-Execution Intent / Authorization Facts
 ↓
Execution Fact → Observation Fact → Verification Fact
 ↓
Atomic World Version + Receipt + Delivery Intent
 ↓
Post-Commit Outcome Event Delivery

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
* World commit receipt ID.
* committed-event delivery intent ID and delivery status.

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
WT-1	Every authoritative state change originates from validated factual inputs and a valid verification receipt.
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
WT-13	Only the World Kernel commits authoritative modeled World State.
WT-14	World version, WORLD_COMMIT_RECEIPT, and delivery intent become visible atomically.
WT-15	Verification binds exact parent version, candidate hash, evidence set, and policy version.
WT-16	A parent-version conflict invalidates verification applicability and requires re-preparation and re-verification.
WT-17	Pre-commit factual records and failed paths remain durable without claiming successful World commitment.

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
    receipt_id: ver_001
    parent_world_version: W100
    candidate_transition_hash: sha256:...
    evidence_set_hash: sha256:...
    verification_policy_version: verification-policy@7
  result:
    status: COMMITTED
  resulting_world:
    version: W101
    world_commit_receipt_id: wcr_101
    committed_event_delivery_intent_id: delivery_101

⸻

62. Reference State Transition

                 ┌───────────────┐
                 │   World W100  │
                 └───────┬───────┘
                         │
                 Validated Inputs
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
               COMMIT        ABORT / RECOVER
                  │
                  ▼
              World W101
              + Receipt
              + Delivery Intent

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
13. Explicit Execution → Observation → Verification lifecycle.
14. Parent-specific verification binding and stale-verification rejection.
15. Atomic World version, `WORLD_COMMIT_RECEIPT`, and committed-event delivery intent.
16. Idempotent post-commit outcome-event delivery.
17. Durable failed, rejected, denied, conflict, and recovery evidence.

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
Execution
    ↓
Observation
    ↓
Verification
    ↓
World Kernel Atomic Commit
    ↓
World(t+1)
    ↓
Successful Outcome Event → Chronicle

This sequence is the fundamental state-transition law of Veda.

⸻

67. Status

Draft v0.2.0-proposed

This owner-directed normalization remains proposed Draft text. It does not accept this RFC, close WM-12, or complete RFC Freeze.

RFC-0004 defines the state-transition semantics of Veda and establishes the mechanism by which validated factual inputs, authorization, observation, and verification are committed by the World Kernel as authoritative modeled World State.

Without RFC-0004, Veda can describe a world and record events.

With RFC-0004, Veda can formally transform, verify, reconstruct, branch, recover, and reason about that world.
