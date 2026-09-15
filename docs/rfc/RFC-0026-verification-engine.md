RFC-0026 — Verification Engine

Status: Draft
Layer: 10 — Verification & Reality
Depends On: RFC-0002, RFC-0003, RFC-0004, RFC-0008, RFC-0012, RFC-0013, RFC-0014, RFC-0015, RFC-0018, RFC-0020, RFC-0021, RFC-0022, RFC-0023, RFC-0024, RFC-0025

⸻

1. Abstract

RFC-0026 defines the Verification Engine of Veda.

Its purpose is to determine whether an expected result, predicted state, action outcome, plan, simulation, claim, or system condition corresponds to evidence observed from the real World.

The fundamental transformation is:

Expected State
      ↓
Actual Observation
      ↓
Evidence
      ↓
Verification
      ↓
Verified / Failed / Partial / Unknown

The Verification Engine exists because:

Action Success ≠ Outcome Success

and:

Agent Claim ≠ Evidence

and:

Expected Result ≠ Actual Result

The Verification Engine MUST therefore operate as an independent reality-checking boundary.

⸻

2. Motivation

A naive agent loop looks like:

Think
 ↓
Act
 ↓
"I succeeded."

This is unacceptable for Veda.

The correct loop is:

Think
 ↓
Plan
 ↓
Authorize
 ↓
Act
 ↓
Observe
 ↓
Collect Evidence
 ↓
Verify
 ↓
Update World

An action returning:

HTTP 200

does not necessarily mean:

Business operation succeeded.

A command returning:

exit code 0

does not necessarily mean:

Desired World state exists.

A model responding:

"Done."

is not evidence of completion.

⸻

3. Core Principle

Veda MUST distinguish:

Intent
≠
Action
≠
Execution Result
≠
Observed State
≠
Verified Outcome

The authoritative progression is:

Expected
    ↓
Observed
    ↓
Evidence
    ↓
Verified

⸻

4. Design Goals

The Verification Engine MUST support:

1. precondition verification
2. postcondition verification
3. action verification
4. plan verification
5. goal verification
6. state verification
7. event verification
8. evidence validation
9. identity verification
10. authority verification
11. temporal verification
12. causal verification
13. simulation-vs-reality comparison
14. prediction calibration
15. partial verification
16. failed verification
17. unknown verification
18. independent verification
19. replay
20. tamper detection
21. deterministic checks
22. human verification
23. cryptographic evidence
24. verification provenance
25. complete audit trail

⸻

5. Non-Goals

The Verification Engine MUST NOT:

* invent evidence
* assume success because an action returned successfully
* modify reality merely to make verification pass
* silently change expected outcomes
* rewrite failed results
* grant authority
* execute arbitrary actions
* convert model confidence into evidence
* delete contradictory evidence
* claim certainty when evidence is insufficient

⸻

6. Verification Object

Verification {
    verification_id
    version
    subject
    subject_type
    expected_state
    expected_outcome
    actual_state
    actual_outcome
    evidence_refs
    verifier
    verifier_type
    method
    criteria
    temporal_context
    causal_context
    confidence
    completeness
    discrepancies
    status
    provenance
    created_at
    completed_at
    expires_at
}

⸻

7. Verification Status

The engine MUST support at least:

UNVERIFIED
VERIFYING
VERIFIED
PARTIALLY_VERIFIED
FAILED
CONTRADICTED
INCONCLUSIVE
UNKNOWN
EXPIRED
INVALID

These states MUST NOT be collapsed into:

PASS / FAIL

because reality is inconveniently more complicated than a checkbox.

⸻

8. Verification Levels

Veda SHOULD support multiple verification levels.

Level 0 — Self Report

Example:

Agent:
"Task completed."

This has minimal evidentiary value.

⸻

Level 1 — Execution Confirmation

Example:

Process exited with code 0.

Confirms execution behavior, not necessarily desired outcome.

⸻

Level 2 — State Verification

Example:

File exists.
Database row exists.
Service is running.

⸻

Level 3 — Outcome Verification

Example:

Expected:
    user account created
Actual:
    account exists
    correct permissions
    correct metadata

⸻

Level 4 — Independent Verification

A separate verifier or independent source checks the result.

Executor
    ↓
Verifier

The verifier SHOULD use an independent method when possible.

⸻

Level 5 — Cryptographically Verifiable Evidence

Evidence may include:

hash
signature
attestation
Merkle proof
signed receipt

Cryptographic integrity proves that evidence was not altered, but it does not automatically prove that the underlying claim is true.

⸻

9. Verification Contract

Before executing a consequential action, Veda SHOULD define:

VerificationContract {
    verification_id
    target
    expected_preconditions
    expected_postconditions
    evidence_requirements
    acceptable_variance
    timeout
    verifier_requirements
    minimum_verification_level
    failure_policy
}

This creates:

Expectation

before:

Execution

This prevents the system from moving the goalposts after the action finishes.

⸻

10. Precondition Verification

Before action execution:

Preconditions
      ↓
Verify
      ↓
Action

Example:

Required:
    file exists
    backup exists
    permission valid
    disk space sufficient

If preconditions fail:

Action MUST NOT execute

unless explicitly permitted by policy.

⸻

11. Postcondition Verification

After action execution:

Action
 ↓
Observe
 ↓
Verify Postconditions

Example:

Expected:
    service_version = 2.4

Verification:

Actual:
    service_version = 2.4

Result:

VERIFIED

⸻

12. Goal Verification

A plan can execute successfully while the goal remains unsatisfied.

Example:

Plan:
    increase storage
Action:
    purchase storage
Execution:
    successful
Goal:
    storage shortage resolved

Verification MUST test the goal:

Storage shortage resolved?

not merely:

Purchase completed?

⸻

13. Action Verification

Every consequential Action SHOULD have:

Expected Preconditions
Expected Effects
Expected Postconditions
Verification Method

Example:

Action:
    deploy(version=2.4)

Expected:

service.version = 2.4
health = healthy
error_rate < threshold
latency < threshold

⸻

14. Plan Verification

A Plan is verified only when:

All required steps
+
Required outcomes
+
Goal conditions

are satisfied.

Therefore:

All actions succeeded

does not necessarily mean:

Plan succeeded

⸻

15. Evidence

Evidence is the primary input to verification.

Possible evidence:

direct observation
tool output
system state
database record
filesystem state
API response
sensor reading
signed artifact
cryptographic proof
human confirmation
external source
independent verifier
event log
execution trace

⸻

16. Evidence Hierarchy

Evidence SHOULD be ranked by:

Direct verified observation
    ↓
Independent observation
    ↓
Authenticated system record
    ↓
Signed artifact
    ↓
Trusted external report
    ↓
Agent/tool output
    ↓
Model inference
    ↓
Unverified claim

The exact ranking is domain-specific.

⸻

17. Evidence ≠ Truth

Evidence supports a claim.

It does not automatically establish absolute truth.

Example:

API says:
    payment completed

This is evidence.

It may still conflict with:

Bank ledger:
    payment missing

The Verification Engine MUST preserve the conflict.

⸻

18. Multiple Evidence Sources

Verification SHOULD use multiple sources when risk is high.

Example:

Source A:
    API response
Source B:
    database state
Source C:
    external ledger

Agreement increases confidence.

Disagreement creates:

Verification Conflict

which MAY invoke RFC-0014.

⸻

19. Evidence Independence

Two evidence sources are not necessarily independent.

For example:

Service A
Service B

may both read the same underlying database.

Therefore:

Two outputs ≠ Two independent confirmations

The verifier SHOULD track evidence dependencies.

⸻

20. Expected State

Verification requires an explicit expected state.

ExpectedState {
    entity
    attributes
    relationships
    temporal_scope
    conditions
    tolerance
}

Example:

service.status = HEALTHY
service.version = 2.4

⸻

21. Actual State

Actual state is derived from observation.

ActualState {
    entity
    attributes
    relationships
    observed_at
    source
    evidence_refs
}

It MUST NOT be inferred solely from the expected state.

⸻

22. State Comparison

The engine compares:

Expected State
       vs
Actual State

Possible outcomes:

MATCH
PARTIAL_MATCH
MISMATCH
UNKNOWN
CONTRADICTED

⸻

23. Tolerance

Some values naturally vary.

Example:

Expected latency:
    < 100ms

Actual:

97ms

Verified.

For numerical values:

Tolerance {
    absolute
    relative
    range
    distribution
}

Tolerance MUST be explicit.

⸻

24. Temporal Verification

A condition may be correct at one time and incorrect later.

Therefore verification MUST include temporal context.

Example:

service healthy at 12:00
service failed at 12:03

A verification at 12:01 does not prove:

service remained healthy.

Temporal validity MUST follow RFC-0021.

⸻

25. Continuous Verification

Some systems require ongoing verification.

Example:

Deploy
 ↓
Healthy
 ↓
Monitor
 ↓
Healthy
 ↓
Healthy

Verification may therefore be:

POINT_IN_TIME
WINDOW
CONTINUOUS
EVENT_TRIGGERED

⸻

26. Verification Expiration

A verification may expire.

Example:

Database healthy at 10:00

does not prove:

Database healthy at 18:00

Verification MUST include:

verified_at
valid_until
freshness_requirement

⸻

27. Freshness

Evidence MAY become stale.

Evidence age
+
World volatility
=
Freshness risk

High-volatility systems require fresher evidence.

⸻

28. Independent Verifier

High-impact actions SHOULD use a verifier distinct from the executor.

Executor
    ↓
Result
    ↓
Independent Verifier
    ↓
Verification

The verifier SHOULD NOT simply repeat:

"Executor says it succeeded."

⸻

29. Deterministic Verification

Where possible, verification SHOULD be deterministic.

Example:

Expected:
    file hash = H
Actual:
    SHA256(file) = H

Result:

VERIFIED

This is stronger than asking an LLM:

"Does this file look correct?"

⸻

30. Semantic Verification

Some outcomes require semantic evaluation.

Example:

Expected:
    code implements authentication correctly

Possible verification:

tests
static analysis
security scanner
formal checks
independent model review
human review

Semantic verification SHOULD be combined with deterministic checks where possible.

⸻

31. Layered Verification

A high-risk result SHOULD use multiple layers:

Syntax
 ↓
Structure
 ↓
State
 ↓
Behavior
 ↓
Outcome
 ↓
Goal

Example software deployment:

Build passes
 ↓
Tests pass
 ↓
Process starts
 ↓
Health check passes
 ↓
Error rate acceptable
 ↓
User-facing behavior correct

⸻

32. Verification Depth

The engine SHOULD select verification depth based on:

Risk
Impact
Irreversibility
Uncertainty
Authority level
System criticality
Failure cost

Low-risk:

single deterministic check

High-risk:

multi-source
independent
continuous
human approval

⸻

33. Verification Failure

Failure MUST NOT be hidden.

Possible reasons:

POSTCONDITION_FAILED
MISSING_EVIDENCE
CONTRADICTORY_EVIDENCE
TIMEOUT
STALE_EVIDENCE
WRONG_STATE
WRONG_TARGET
WRONG_VERSION
UNAUTHORIZED_CHANGE
UNKNOWN_EXTERNAL_EFFECT
VERIFIER_FAILURE

⸻

34. Partial Verification

Some results may be partially verified.

Example:

Expected:
    deployment successful

Verified:

service started
version correct

Not verified:

long-term stability
external integrations

Result:

PARTIALLY_VERIFIED

The engine MUST preserve the missing portions.

⸻

35. Inconclusive

If evidence is insufficient:

INCONCLUSIVE

This means:

"We do not have enough evidence."

It does NOT mean:

"Probably successful."

⸻

36. Unknown

Unknown is a valid epistemic state.

UNKNOWN

must be supported independently from:

FAILED

Failure means evidence indicates the expected result did not occur.

Unknown means evidence is insufficient to determine the result.

⸻

37. Contradiction

If:

Source A:
    state = SUCCESS
Source B:
    state = FAILURE

the engine SHOULD create:

VerificationConflict

and invoke RFC-0014 where appropriate.

It MUST NOT silently select one source.

⸻

38. Verification Conflict

VerificationConflict {
    conflict_id
    verification_ref
    evidence_refs
    conflicting_claims
    source_dependencies
    severity
    resolution_strategy
    status
}

Resolution may include:

additional evidence
source ranking
reconciliation
human review
retry observation

⸻

39. Verification of External Systems

External systems may be outside Veda’s direct control.

Example:

Veda:
    requested payment

External system:

Payment provider

Veda SHOULD distinguish:

Request accepted

from:

Payment settled

and:

Recipient received funds

These are different verification levels.

⸻

40. Chain of Verification

For consequential workflows:

Identity
 ↓
Authority
 ↓
Approval
 ↓
Action
 ↓
Execution
 ↓
State
 ↓
Outcome
 ↓
Goal

Each link may require evidence.

⸻

41. Verification Chain Object

VerificationChain {
    chain_id
    request
    identity
    authority
    approval
    action
    execution
    observations
    evidence
    state_verification
    outcome_verification
    goal_verification
    final_status
}

A missing critical link SHOULD cause:

INCOMPLETE

rather than:

VERIFIED

This aligns with emerging agent-verification architectures that explicitly verify identity, authority, approval and policy before consequential actions rather than trusting an agent’s own claim. (EVE Verified)

⸻

42. Verification Receipt

A completed verification MAY produce:

VerificationReceipt {
    verification_id
    subject
    expected
    observed
    evidence_hashes
    verifier
    method
    timestamp
    status
    signature
}

The receipt SHOULD be independently verifiable.

⸻

43. Cryptographic Integrity

For high-value evidence:

Evidence
 ↓
Canonicalization
 ↓
Hash
 ↓
Optional Signature

The system MAY use:

SHA-256
Ed25519
Merkle trees
signed attestations

Cryptographic proof SHOULD establish evidence integrity, not magically turn questionable evidence into truth.

⸻

44. Replay Verification

A verification SHOULD be reproducible where possible.

Verification Input
+
Rules
+
Evidence
+
Version

should produce:

Same Verification Result

for deterministic verification.

Replay MUST detect:

rule drift
input drift
evidence drift
version drift
environment drift

⸻

45. Verification Versioning

Verification depends on:

Verifier Version
Rules Version
Evidence Version
World Version
Policy Version
Schema Version

Changing these MAY invalidate previous verification results.

⸻

46. Verification of Simulation

RFC-0024 predicts:

Expected Outcome

RFC-0026 compares it with reality.

Simulation
    ↓
Prediction
    ↓
Real Action
    ↓
Actual Outcome
    ↓
Verification
    ↓
Prediction Error

This creates calibration data.

⸻

47. Prediction Error

PredictionError {
    prediction_ref
    actual_observation_ref
    expected
    actual
    difference
    magnitude
    cause_hypotheses
    model_refs
    confidence
    created_at
}

This SHOULD feed RFC-0035 and RFC-0036.

⸻

48. Goal Drift Detection

Suppose the action completed successfully but the final result no longer satisfies the original goal.

Verification MUST detect:

Goal Drift

Example:

Goal:
    reduce cost
Action:
    migrated infrastructure
Result:
    infrastructure works
Actual:
    cost increased

Action verification:

PASS

Goal verification:

FAIL

This distinction is critical.

⸻

49. Success Taxonomy

Veda SHOULD distinguish:

EXECUTION_SUCCESS
STATE_SUCCESS
OUTCOME_SUCCESS
GOAL_SUCCESS

Example:

Command executed:
    SUCCESS
Expected state:
    SUCCESS
Desired outcome:
    SUCCESS
Original goal:
    FAILURE

This prevents false completion.

⸻

50. Verification and World Update

Only verified observations SHOULD update authoritative World state when the relevant policy requires verification.

Observation
 ↓
Evidence
 ↓
Verification
 ↓
World Update

Unverified information MAY exist as:

Candidate State
Belief
Observation
Pending Verification

but MUST NOT silently become authoritative fact.

⸻

51. Verification and Knowledge

Verified results MAY produce:

Knowledge

through RFC-0013.

Flow:

Reality
 ↓
Observation
 ↓
Evidence
 ↓
Verification
 ↓
Knowledge

However:

Verified once

does not mean:

Always true

Temporal validity remains important.

⸻

52. Verification and Memory

Verification outcomes MAY create:

Episodic Memory
Failure Memory
Procedural Memory
Semantic Knowledge

Example:

Deployment v2.4

produces:

Outcome:
    failed
Cause:
    database migration
Lesson:
    migration must be staged

The lesson itself requires a separate learning process.

Verification MUST NOT automatically turn every failure into a permanent rule.

⸻

53. Verification and Reflection

Verification produces evidence.

Reflection asks:

Why did reality differ from expectation?

Therefore:

Verification
    ↓
Prediction Error
    ↓
Reflection

⸻

54. Verification and Rollback

If verification fails:

Action
 ↓
Verification FAIL
 ↓
Recovery Policy
 ├── Retry
 ├── Rollback
 ├── Compensate
 ├── Replan
 └── Escalate

RFC-0027 governs rollback and recovery.

Verification does not itself perform rollback unless explicitly designed as a controlled recovery mechanism.

⸻

55. Continuous Verification

Critical systems MAY remain under verification after completion.

Example:

Deploy
 ↓
Verify
 ↓
Monitor
 ↓
Verify
 ↓
Verify
 ↓
Release

This supports:

event-triggered verification
periodic verification
threshold verification
anomaly-triggered verification

⸻

56. Verification Budget

Verification consumes:

CPU
RAM
network
API calls
time
money
human attention

The engine SHOULD optimize verification depth while respecting risk requirements.

It MUST NOT skip mandatory verification merely to save resources.

⸻

57. Human Verification

Some claims cannot be adequately verified automatically.

Example:

"Does this legal document reflect the user's actual intent?"

The system MAY request:

HUMAN_VERIFICATION_REQUIRED

Human verification MUST itself be recorded:

actor
timestamp
scope
decision
evidence

⸻

58. Human Verification Is Not Absolute Truth

Human confirmation is evidence from a human authority.

It is not equivalent to objective truth.

The system MUST preserve:

human-confirmed

as an epistemic category.

⸻

59. Fail-Closed Principle

For high-risk verification:

Evidence insufficient
        ↓
DO NOT ASSUME SUCCESS

The default should be:

BLOCK
ESCALATE
RETRY

according to policy.

This follows the broader principle used by verification systems where insufficient evidence causes refusal rather than optimistic guessing. (EVE Verified)

⸻

60. Verification Pipeline

Verification Request
        ↓
Resolve Expected State
        ↓
Resolve Criteria
        ↓
Resolve Evidence Requirements
        ↓
Collect Evidence
        ↓
Validate Evidence
        ↓
Check Freshness
        ↓
Compare Expected vs Actual
        ↓
Evaluate Completeness
        ↓
Resolve Conflicts
        ↓
Determine Status
        ↓
Create Verification Record
        ↓
Update World / Trigger Recovery

⸻

61. Verification Lifecycle

REQUESTED
    ↓
EXPECTATION_RESOLVED
    ↓
EVIDENCE_REQUIRED
    ↓
COLLECTING
    ↓
VALIDATING
    ↓
COMPARING
    ↓
EVALUATING
    ↓
VERIFIED

Alternative terminal states:

PARTIALLY_VERIFIED
FAILED
CONTRADICTED
INCONCLUSIVE
UNKNOWN
INVALID
EXPIRED
CANCELLED

⸻

62. Verification API

The engine SHOULD expose:

create_verification()
define_expectation()
define_postconditions()
define_evidence_requirements()
verify_preconditions()
verify_postconditions()
collect_evidence()
validate_evidence()
rank_evidence()
compare_state()
compare_outcome()
verify_goal()
check_freshness()
check_temporal_validity()
detect_conflicts()
resolve_conflict()
run_independent_verifier()
verify_signature()
verify_hash()
replay_verification()
create_receipt()
get_status()
get_result()
get_trace()
invalidate()
expire()

⸻

63. Verification Request

VerificationRequest {
    verification_id
    subject
    subject_type
    action_ref
    plan_ref
    goal_ref
    expected_state
    expected_outcome
    criteria
    evidence_requirements
    minimum_verification_level
    freshness_requirement
    tolerance
    timeout
    verifier_policy
    failure_policy
}

⸻

64. Verification Result

VerificationResult {
    verification_id
    status
    expected
    observed
    evidence_refs
    matched_conditions
    failed_conditions
    unknown_conditions
    discrepancies
    confidence
    completeness
    freshness
    verifier
    method
    provenance
    receipt_ref
}

⸻

65. Discrepancy Model

Discrepancy {
    discrepancy_id
    expected
    actual
    type
    magnitude
    severity
    temporal_context
    evidence_refs
    suspected_causes
    status
}

Types:

VALUE_MISMATCH
MISSING_STATE
EXTRA_STATE
WRONG_TARGET
WRONG_VERSION
TIMING_ERROR
RELATIONSHIP_MISMATCH
BEHAVIOR_MISMATCH
GOAL_MISMATCH
UNKNOWN

⸻

66. Verification Events

The engine MUST emit:

VerificationRequested
ExpectationResolved
PreconditionVerificationStarted
PreconditionVerified
PreconditionFailed
EvidenceRequested
EvidenceCollected
EvidenceValidated
EvidenceRejected
EvidenceExpired
VerificationStarted
StateObserved
StateCompared
OutcomeCompared
VerificationConflictDetected
VerificationConflictResolved
VerificationPartiallyCompleted
VerificationCompleted
VerificationFailed
VerificationInconclusive
VerificationUnknown
GoalVerificationStarted
GoalVerified
GoalFailed
PredictionVerified
PredictionFailed
PredictionPartiallyVerified
SimToRealGapDetected
VerificationReceiptCreated
VerificationReplayStarted
VerificationReplayCompleted
VerificationReplayMismatch
VerificationExpired
VerificationInvalidated

⸻

67. Security Threats

VER-SEC-1

Fake evidence injection.

VER-SEC-2

Evidence poisoning.

VER-SEC-3

Stale evidence.

VER-SEC-4

Verifier collusion.

VER-SEC-5

Executor/verifier compromise.

VER-SEC-6

Post-hoc expectation modification.

VER-SEC-7

Verification bypass.

VER-SEC-8

Evidence deletion.

VER-SEC-9

Signature misuse.

VER-SEC-10

Hash substitution.

VER-SEC-11

Replay attack.

VER-SEC-12

Time manipulation.

VER-SEC-13

Source dependency deception.

VER-SEC-14

False success reporting.

VER-SEC-15

Goal verification bypass.

⸻

68. Post-Hoc Goalpost Protection

The system MUST prevent:

Expected:
    X

from becoming:

Expected:
    Y

after the action produces:

Y

Any modification to verification criteria after execution MUST be:

versioned
audited
authorized

and MUST NOT erase the original expectation.

⸻

69. Evidence Immutability

Completed verification evidence SHOULD be immutable.

Corrections create:

New Evidence Version

not:

Silent overwrite

The original remains part of history.

⸻

70. Independent Audit

A verification result SHOULD be independently inspectable.

At minimum:

Expected
Observed
Evidence
Method
Rules
Verifier
Timestamp
Status

should be reconstructable.

This makes the verification process auditable rather than dependent on a dashboard saying “green.” Event-sourced architectures similarly emphasize append-only records, replay and independent verification as mechanisms for proving what actually happened. (Fasten)

⸻

71. Example: File Operation

Action:

Delete temporary files.

Expected:

/tmp/cache/* no longer exists
protected files unchanged

Execution:

exit code = 0

Verification:

cache files:
    deleted
protected files:
    unchanged

Result:

VERIFIED

⸻

72. Example: False Success

Action:

Deploy v2.

Execution:

exit code = 0

Verification:

service version = v1

Result:

FAILED

Even though:

Execution = SUCCESS

because:

Outcome = FAILURE

⸻

73. Example: Partial Success

Expected:

Deploy 5 services.

Actual:

4 healthy
1 unhealthy

Result:

PARTIALLY_VERIFIED

The engine MUST NOT simplify this into:

SUCCESS

⸻

74. Example: Unknown

Expected:

External recipient received file.

Evidence:

Upload API returned success.

But no evidence exists that:

recipient actually received it.

Result:

UNKNOWN

Not:

VERIFIED

⸻

75. Example: Contradiction

Source A:

Database:
    payment = completed

Source B:

Bank:
    payment = rejected

Result:

CONTRADICTED

RFC-0014 handles the conflict.

Veda MUST NOT choose whichever source makes its previous decision look correct.

⸻

76. Example: Goal Failure

Goal:

Reduce monthly infrastructure cost.

Action:

Move to cheaper server.

Execution:

migration completed.

State:

new server running.

Outcome:

monthly cost increased due to bandwidth fees.

Result:

Action:
    VERIFIED
Goal:
    FAILED

This distinction is one of the most important responsibilities of the Verification Engine.

⸻

77. Architecture

                    REAL WORLD
                        │
                        ▼
                    OBSERVATION
                        │
                        ▼
                     EVIDENCE
                        │
             ┌──────────┴──────────┐
             │                     │
             ▼                     ▼
        DETERMINISTIC          INDEPENDENT
          VERIFIER              VERIFIER
             │                     │
             └──────────┬──────────┘
                        ▼
                 VERIFICATION
                        │
            ┌───────────┼───────────┐
            ▼           ▼           ▼
         VERIFIED     FAILED      UNKNOWN
            │           │           │
            ▼           ▼           ▼
       WORLD UPDATE   RECOVERY    ESCALATION

⸻

78. Complete Veda Closed Loop

After RFC-0026, the cognitive/action loop becomes:

REALITY
   ↓
OBSERVE
   ↓
WORLD MODEL
   ↓
ATTENTION
   ↓
INTENT
   ↓
GOAL
   ↓
PLAN
   ↓
FUTURE
   ↓
SIMULATION
   ↓
VALUE / DECISION
   ↓
AUTHORIZATION
   ↓
ACTION
   ↓
REALITY
   ↓
OBSERVATION
   ↓
EVIDENCE
   ↓
VERIFICATION
   ↓
WORLD UPDATE
   ↓
EXPERIENCE
   ↓
LEARNING

This is the actual foundation of Veda’s closed-loop intelligence.

⸻

79. Invariants

VER-1

Verification MUST be distinct from execution.

VER-2

Execution success MUST NOT imply outcome success.

VER-3

Agent self-report MUST NOT be sufficient evidence for high-impact outcomes.

VER-4

Verification MUST compare expected state with observed state.

VER-5

Expected conditions MUST be established before execution whenever possible.

VER-6

Post-hoc modification of expectations MUST be prevented or fully audited.

VER-7

Evidence MUST have provenance.

VER-8

Evidence freshness MUST be representable.

VER-9

Evidence independence MUST be representable.

VER-10

Conflicting evidence MUST NOT be silently discarded.

VER-11

Unknown MUST be distinct from Failed.

VER-12

Partial verification MUST be representable.

VER-13

Inconclusive verification MUST be representable.

VER-14

Verification MUST support deterministic checks.

VER-15

Verification SHOULD support independent verification.

VER-16

High-risk actions SHOULD require stronger verification.

VER-17

Verification MUST NOT grant authority.

VER-18

Verification MUST NOT modify reality to satisfy expectations.

VER-19

Verification results MUST be auditable.

VER-20

Verification results MUST be versioned.

VER-21

Verification evidence MUST be traceable to its source.

VER-22

Verification MAY trigger recovery but MUST respect authorization.

VER-23

Goal verification MUST be distinct from action verification.

VER-24

Simulation prediction MUST be compared against real outcome when applicable.

VER-25

Prediction error MUST be preserved.

VER-26

Verification MUST respect temporal validity.

VER-27

Verification MUST preserve contradictory evidence.

VER-28

Verification MUST NOT convert model confidence into evidence.

VER-29

Completed verification SHOULD be independently replayable when possible.

VER-30

No consequential action may be considered fully successful solely because the executor claims success.

⸻

80. Final Principle

Veda must never decide that reality agrees with it merely because Veda expected reality to agree.

The authoritative chain is:

Expectation
    ↓
Action
    ↓
Observation
    ↓
Evidence
    ↓
Verification
    ↓
Reality State

Therefore:

"I did it."
        ≠
"It happened."
        ≠
"It worked."
        ≠
"The goal was achieved."

Veda needs evidence for each transition.

That is the purpose of RFC-0026.