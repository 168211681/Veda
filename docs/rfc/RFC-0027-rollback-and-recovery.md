RFC-0027 — Rollback & Recovery

Status: Draft
Layer: 10 — Verification & Reality
Depends On: RFC-0001, RFC-0002, RFC-0003, RFC-0004, RFC-0008, RFC-0009, RFC-0010, RFC-0011, RFC-0020, RFC-0021, RFC-0022, RFC-0023, RFC-0024, RFC-0025, RFC-0026
Next: RFC-0028 — Tool & Capability Registry

⸻

1. Abstract

RFC-0027 defines the Rollback & Recovery Engine of Veda.

Its responsibility is to determine and execute safe recovery after:

* action failure
* execution failure
* verification failure
* unexpected state transition
* partial completion
* external system failure
* contradictory observations
* corrupted state
* timeout
* resource exhaustion
* capability failure
* plan failure
* goal failure
* security incident
* system crash

The Recovery Engine must determine whether an operation can be:

1. Rolled back
2. Compensated
3. Retried
4. Resumed
5. Replanned
6. Isolated
7. Escalated to a human
8. Aborted
9. Accepted as partially completed
10. Declared unrecoverable

The fundamental principle is:

Recovery is not the act of pretending the failure never happened. Recovery is the controlled transition from a failed or uncertain state toward a safe and verified state.

⸻

2. Core Problem

Veda operates in a world where actions are not always atomic.

Consider:

Create File
    ↓
Write Data
    ↓
Upload File
    ↓
Publish File
    ↓
Notify User

Suppose:

Create       ✓
Write        ✓
Upload       ✓
Publish      ✗
Notify       not executed

The system cannot simply say:

ROLLBACK

because the uploaded object may already exist externally.

Therefore:

Rollback ≠ Undo Everything

Instead:

Failure
   ↓
Determine Actual State
   ↓
Determine Reversibility
   ↓
Determine Recovery Strategy
   ↓
Execute Recovery
   ↓
Verify Recovery
   ↓
Update World

Distributed workflows commonly use compensating operations rather than a global database rollback. These compensations must be designed for retries and idempotency because the original operation may already have committed partial effects. (Stacksaga Docs)

⸻

3. Goals

RFC-0027 provides:

* failure classification
* recovery strategy selection
* rollback planning
* compensation
* retry
* resume
* checkpoint recovery
* state restoration
* partial-failure handling
* isolation
* escalation
* recovery verification
* recovery provenance
* recovery audit
* recovery safety
* recovery idempotency
* recovery budgets
* recovery loops prevention

⸻

4. Non-Goals

RFC-0027 does not:

* authorize arbitrary actions
* redefine Constitution
* modify user policy
* bypass human approval
* decide whether an action should originally have been performed
* guarantee that every action is reversible
* erase historical events
* rewrite reality
* silently delete evidence of failure

The Recovery Engine operates under:

Constitution
    ↓
Policy
    ↓
Authority
    ↓
Recovery Rules

It cannot override them.

⸻

5. Fundamental Distinctions

Veda MUST distinguish:

Rollback
Compensation
Retry
Resume
Replan
Isolation
Abort
Escalation

They are not interchangeable.

⸻

6. Rollback

Rollback means restoring a controlled state to a previous known-valid state.

Conceptually:

State A
   ↓
Action
   ↓
State B
   ↓
Failure
   ↓
Rollback
   ↓
State A'

Important:

State A' ≠ necessarily State A

because other valid changes may have occurred.

Therefore rollback MUST be version-aware.

⸻

7. Compensation

Compensation is a new action whose semantic effect counteracts a previous action.

Example:

Reserve Inventory
        ↓
Reservation Created
        ↓
Later Failure
        ↓
Release Inventory

This is not database rollback.

It is:

Forward Action
      ↓
Committed Effect
      ↓
Compensating Action
      ↓
New Verified State

A compensation may itself fail and therefore MUST support:

* retry
* idempotency
* verification
* checkpointing
* escalation

This follows the same principle used by Saga-style recovery systems. (AWS Documentation)

⸻

8. Retry

Retry means executing the failed operation again.

Retry is appropriate when failure is likely transient.

Examples:

* network timeout
* temporary service unavailable
* rate limit
* temporary lock
* provider unavailable
* hardware transient error

Retry MUST NOT be blindly used for:

* invalid parameters
* authorization failure
* destructive irreversible failure
* corrupted state
* repeated deterministic failure
* policy violation

⸻

9. Resume

Resume continues a partially completed workflow from a known checkpoint.

Example:

Step 1 ✓
Step 2 ✓
Step 3 ✓
Step 4 ✗
Step 5 not started

Recovery may resume:

Step 4
   ↓
Step 5

rather than restarting:

Step 1 → Step 2 → Step 3 → Step 4

This reduces:

* cost
* duplication
* risk
* unnecessary side effects

⸻

10. Replan

Replanning creates a new plan when the original plan is no longer valid.

Trigger examples:

* world changed
* dependency disappeared
* resource unavailable
* goal changed
* assumption invalidated
* verification contradicted prediction
* external environment changed

Flow:

Plan
 ↓
Failure
 ↓
Diagnose
 ↓
World Update
 ↓
Replan
 ↓
Simulate
 ↓
Authorize
 ↓
Execute

⸻

11. Isolation

Some failures should not immediately be repaired.

Example:

Tool produces suspicious output
        ↓
Verification failure
        ↓
Capability isolated
        ↓
Further calls blocked
        ↓
Diagnosis

Isolation prevents a potentially corrupted component from causing additional damage.

Possible isolated objects:

* tool
* capability
* provider
* model
* process
* agent
* memory source
* knowledge source
* external system
* network endpoint

⸻

12. Abort

Abort terminates the current operation when continuing is unsafe, impossible, or pointless.

Abort does not mean:

delete history

Instead:

Operation
   ↓
Abort
   ↓
Record Failure
   ↓
Determine Residual State
   ↓
Verify
   ↓
Recover or Escalate

⸻

13. Escalation

Human escalation is required when:

* irreversible effects occurred
* recovery confidence is low
* competing recovery strategies have similar risk
* authorization is required
* policy requires human approval
* external state cannot be determined
* compensation failed repeatedly
* security compromise is suspected
* financial/legal/physical consequences are significant

Human intervention becomes an explicit state transition.

RECOVERY_PENDING_HUMAN

⸻

14. Recovery Object

Recovery {
    recovery_id
    version
    incident_id
    source_action_refs[]
    source_process_ref
    source_plan_ref
    source_verification_ref
    failure_type
    failure_stage
    observed_state_ref
    expected_state_ref
    recovery_strategy
    rollback_target
    checkpoint_ref
    compensation_actions[]
    retry_policy
    isolation_policy
    risk
    reversibility
    authority
    approval_requirements
    expected_recovery_state
    verification_contract
    status
    attempt_count
    retry_count
    started_at
    completed_at
    provenance
}

⸻

15. Failure Classification

Veda MUST classify failures before selecting recovery.

Categories:

TRANSIENT
PERMANENT
DETERMINISTIC
UNKNOWN
PARTIAL
EXTERNAL
INTERNAL
AUTHORIZATION
POLICY
RESOURCE
DEPENDENCY
STATE_CORRUPTION
VERIFICATION
SECURITY
CONCURRENCY
TIMEOUT
CAPABILITY
MODEL
ENVIRONMENT

⸻

16. Failure Severity

Severity:

S0 INFORMATIONAL
S1 LOW
S2 MODERATE
S3 HIGH
S4 CRITICAL
S5 CATASTROPHIC

Severity is determined from:

Impact
×
Probability
×
Irreversibility
×
Uncertainty
×
Exposure

⸻

17. Recovery Strategies

Veda MAY select:

NO_ACTION
RETRY
RESUME
ROLLBACK
COMPENSATE
REPLAN
ISOLATE
ABORT
ESCALATE
RESTORE_FROM_CHECKPOINT
RESTORE_FROM_SNAPSHOT
FAILOVER
DEGRADE
WAIT
REQUEST_HUMAN

Multiple strategies may be combined.

Example:

Retry
   ↓
Failure
   ↓
Isolate
   ↓
Fallback Provider
   ↓
Retry
   ↓
Verify

⸻

18. Recovery Decision Hierarchy

Recovery decisions follow:

Constitution
      ↓
Safety Constraints
      ↓
Authority
      ↓
Policy
      ↓
Failure Severity
      ↓
Reversibility
      ↓
Current World State
      ↓
Available Recovery Strategies
      ↓
Risk
      ↓
Cost
      ↓
Expected Recovery Outcome

The Recovery Engine MUST NOT optimize recovery cost at the expense of hard safety constraints.

⸻

19. Reversibility Model

Every action SHOULD declare:

REVERSIBLE
PARTIALLY_REVERSIBLE
COMPENSATABLE
RETRYABLE
NON_REVERSIBLE
UNKNOWN

Example:

Action	Reversibility
Create temporary file	Reversible
Rename file	Reversible
Delete file	Partially reversible
Send email	Compensatable
Transfer money	Compensatable
Publish webpage	Compensatable
Physical movement	Potentially non-reversible
External side effect	Often unknown

⸻

20. Recovery Graph

Each action MAY define:

Action
 ├── Retry
 ├── Rollback
 ├── Compensation
 ├── Alternative
 ├── Isolation
 └── Escalation

Example:

Payment
 ├── Retry Payment
 ├── Cancel Payment
 ├── Refund Payment
 ├── Switch Provider
 └── Human Review

⸻

21. Recovery Plan

A recovery plan follows:

Failure
   ↓
Diagnosis
   ↓
State Reconstruction
   ↓
Strategy Generation
   ↓
Strategy Evaluation
   ↓
Simulation
   ↓
Authorization
   ↓
Recovery Execution
   ↓
Verification
   ↓
World Update

Recovery itself is therefore a controlled plan.

⸻

22. Checkpoints

Long-running processes SHOULD create checkpoints.

Checkpoint {
    checkpoint_id
    process_id
    world_snapshot_ref
    completed_steps[]
    pending_steps[]
    resource_state
    capability_state
    assumptions
    dependencies
    created_at
    integrity_hash
}

Checkpoint integrity MUST be verifiable.

⸻

23. Snapshot Recovery

A snapshot represents a recoverable state.

However:

Restore Snapshot

MUST NOT blindly overwrite newer valid changes.

Veda MUST first determine:

Snapshot State
        +
Current World
        +
Changes Since Snapshot
        ↓
Reconciliation

This prevents recovery from destroying legitimate concurrent work.

⸻

24. Concurrent Changes

Suppose:

Snapshot = S1
        ↓
Process changes X
        ↓
External actor changes Y
        ↓
Failure

Restoring S1 could destroy Y.

Therefore:

Rollback MUST be scoped.

Possible strategies:

Field-level rollback
Entity-level rollback
Transaction-level rollback
Step-level compensation
Version-aware merge
Conflict resolution
Human review

⸻

25. Recovery Transactions

Every recovery operation SHOULD have:

recovery_action_id
idempotency_key
source_action_id
attempt_number
expected_state
actual_state
verification_contract

This prevents duplicated compensation.

For distributed workflows, application-level compensation is not equivalent to ACID rollback. It must account for concurrent changes and may require domain-specific logic. (GitHub)

⸻

26. Idempotency

Recovery commands MUST be idempotent whenever possible.

Example:

ReleaseReservation(reservation_123)

If executed twice:

First → Released
Second → Already Released

NOT:

First → Released
Second → Error / Negative Inventory

Idempotency is especially important because failures can occur after an external system has completed the operation but before Veda receives confirmation.

⸻

27. Unknown Execution State

One of the most dangerous states is:

UNKNOWN

Example:

Veda sends payment
        ↓
Network timeout
        ↓
No response

Veda MUST NOT assume:

Payment failed

or:

Payment succeeded

Instead:

PAYMENT_STATE = UNKNOWN

Then:

Query External State
        ↓
Verify
        ↓
Recover

Blind retry could produce a duplicate payment.

⸻

28. Exactly-Once Effect

Veda SHOULD distinguish:

Exactly-once delivery

from:

Exactly-once effect

Network delivery may produce duplicate messages.

Veda therefore relies on:

Idempotency Key
+
Deduplication
+
State Verification

to achieve safe effects.

⸻

29. Recovery State Machine

DETECTED
   ↓
CLASSIFYING
   ↓
DIAGNOSING
   ↓
STATE_RECONSTRUCTION
   ↓
STRATEGY_GENERATION
   ↓
STRATEGY_EVALUATION
   ↓
SIMULATING
   ↓
AUTHORIZATION_REQUIRED
   ↓
APPROVED
   ↓
RECOVERING
   ↓
VERIFYING
   ↓
RECOVERED

Alternative states:

RETRYING
COMPENSATING
ROLLING_BACK
RESUMING
REPLANNING
ISOLATING
WAITING
ESCALATED
PARTIALLY_RECOVERED
UNRECOVERABLE
ABORTED

⸻

30. Recovery Failure

Recovery can fail.

Therefore:

Original Failure
      ↓
Recovery
      ↓
Recovery Failure
      ↓
Secondary Recovery

But Veda MUST impose a recovery budget.

Example:

max_attempts
max_time
max_cost
max_risk
max_compensation_depth

Without this, Veda can create an infinite loop of increasingly desperate attempts, which is apparently one of the many ways software resembles humans.

⸻

31. Recovery Loop Detection

Veda MUST detect patterns such as:

Retry
 → Fail
 → Retry
 → Fail
 → Retry

or:

Rollback
 → Verification Fail
 → Rollback
 → Verification Fail

or:

Provider A
 → Provider B
 → Provider A
 → Provider B

Loop detection MAY use:

state hash
action hash
strategy sequence
failure signature
attempt count
world version

⸻

32. Recovery Escalation

Escalation levels:

L0 Automatic
L1 Automatic with monitoring
L2 Automatic with notification
L3 Human approval
L4 Human execution
L5 Emergency shutdown

Example:

Low-risk file retry
→ L0
Payment compensation
→ L2/L3
Potential physical harm
→ L4/L5

⸻

33. Degraded Mode

If recovery cannot restore full capability, Veda MAY enter degraded mode.

Example:

Primary Model unavailable
        ↓
Local Model available
        ↓
DEGRADED_INTELLIGENCE

or:

Network unavailable
        ↓
Offline Mode

Degraded mode MUST explicitly expose its limitations to the Brain and Decision Engine.

⸻

34. Fail-Safe vs Fail-Operational

Veda must distinguish:

Fail-Safe

Stop the system when continuing is unsafe.

Unsafe
 ↓
STOP

Fail-Operational

Continue using a safe reduced capability.

Primary Failure
 ↓
Fallback
 ↓
Reduced Capability

Selection depends on risk.

⸻

35. Recovery Verification

Recovery is not complete when the recovery action executes.

It is complete only when verification succeeds.

Recovery Action
      ↓
Execution
      ↓
Observation
      ↓
Evidence
      ↓
Verification
      ↓
Recovered

Therefore:

Recovery Execution Success
≠
Recovery Success

⸻

36. Recovery Success Levels

RECOVERY_EXECUTION_SUCCESS
RECOVERY_STATE_SUCCESS
RECOVERY_OUTCOME_SUCCESS
RECOVERY_GOAL_SUCCESS

Example:

Refund API accepted
        ↓
Execution Success
Account balance corrected
        ↓
State Success
Customer actually received refund
        ↓
Outcome Success
Original business goal restored
        ↓
Goal Success

⸻

37. Recovery and World Model

Recovery MUST produce world events.

Example:

ActionFailed
RecoveryStarted
CompensationStarted
CompensationCompleted
RecoveryVerificationStarted
RecoveryVerified
WorldReconciled

Historical failure MUST NOT disappear.

⸻

38. Recovery and Chronicle

RFC-0032 Veda Chronicle will preserve:

Original Action
Failure
Diagnosis
Recovery Strategy
Approval
Recovery Action
Evidence
Verification
Final State

This provides a complete causal history.

⸻

39. Recovery and Learning

Recovery outcomes feed:

Experience Model
      ↓
Reflection
      ↓
Learning
      ↓
Future Planning

Example:

Provider A timeout
3 times
        ↓
Experience
        ↓
Reflection
        ↓
Reliability score decreases
        ↓
Router adapts

The system must not convert one failure into permanent truth without sufficient evidence.

⸻

40. Recovery and Simulation

Before risky recovery:

Failure
 ↓
Candidate Recovery
 ↓
Simulation
 ↓
Risk Evaluation
 ↓
Authorization
 ↓
Recovery

Especially important for:

* financial operations
* destructive filesystem operations
* infrastructure
* deployment
* security incidents
* physical devices
* irreversible external effects

⸻

41. Recovery and Causality

The system SHOULD determine:

What failed?

and:

Why did it fail?

These are different.

Example:

Deployment failed

Immediate cause:

Health check timeout

Root cause:

Database connection pool exhausted

Recovery should target the appropriate causal layer.

⸻

42. Recovery and Planning

Planner creates normal plans.

Recovery Engine creates recovery plans.

Planner
   ↓
Normal Plan
Recovery Engine
   ↓
Recovery Plan

Recovery plans may invoke Planner for:

Replanning
Alternative paths
Resource substitution
Dependency replacement

⸻

43. Recovery and Authorization

Recovery actions are still actions.

Therefore:

Recovery ≠ automatic authority

A failed operation does not grant Veda permission to perform arbitrary corrective actions.

Every recovery action MUST pass through:

Capability
+
Authorization
+
Policy

⸻

44. Human Override

Humans MAY:

Approve
Reject
Modify
Pause
Abort
Choose Strategy
Force Recovery
Disable Recovery

Human overrides MUST be recorded as events.

⸻

45. Recovery Incident

A failed operation that requires coordinated recovery SHOULD create an Incident.

Incident {
    incident_id
    severity
    source_refs[]
    affected_entities[]
    affected_capabilities[]
    failure_signature
    current_state
    recovery_state
    impact
    containment
    recovery
    verification
    human_review
    timeline
    resolution
}

⸻

46. Recovery Timeline

Every incident MUST maintain a timeline.

Example:

12:00:01 ActionStarted
12:00:03 ActionExecuted
12:00:05 VerificationFailed
12:00:06 IncidentCreated
12:00:07 RecoveryStarted
12:00:08 CompensationExecuted
12:00:10 CompensationVerified
12:00:11 IncidentResolved

This becomes part of the audit trail.

⸻

47. Recovery Policies

Policies MAY define:

retry limits
timeout
backoff
compensation order
approval requirements
maximum recovery cost
maximum risk
isolation rules
escalation rules
checkpoint frequency
recovery budgets

Policy precedence follows RFC-0001 and RFC-0010.

⸻

48. Compensation Ordering

For workflows:

T1
T2
T3
T4

If T4 fails:

C3
C2
C1

may be appropriate.

However, strict reverse order MUST NOT be assumed universally.

Dependencies and domain semantics determine compensation order.

Some compensation operations may run in parallel when safe.

⸻

49. Pivot Transactions

A workflow may contain a point of no return.

T1 → T2 → T3 → T4
             ↑
           PIVOT

Before pivot:

Compensation possible

After pivot:

Retry / Forward Recovery / Human Intervention

This concept is important because some external effects cannot truly be undone. (Microsoft Learn)

⸻

50. Recovery Safety Gate

Before executing recovery:

1. Is current state known?
2. Is target state known?
3. Is recovery authorized?
4. Is recovery reversible?
5. Is compensation possible?
6. Could recovery cause additional damage?
7. Could concurrent changes be overwritten?
8. Is simulation available?
9. Is human approval required?
10. Is verification defined?

If critical information is missing:

DO NOT EXECUTE

⸻

51. Recovery Decision Matrix

Failure	Default Strategy
Temporary network failure	Retry
Rate limit	Wait + Retry
Provider unavailable	Failover
Invalid input	Abort
Partial local transaction	Rollback
External committed effect	Compensation
Unknown external state	Verify first
Corrupted capability	Isolate
Invalid plan	Replan
High-risk irreversible failure	Human escalation
Security anomaly	Isolate + Escalate
Resource exhaustion	Reduce scope / Recover resources
Verification contradiction	Stop + Diagnose
Repeated recovery failure	Escalate

⸻

52. Recovery API

The Recovery Engine SHOULD expose:

create_incident()
classify_failure()
diagnose_failure()
reconstruct_state()
generate_recovery_strategies()
evaluate_recovery_strategy()
create_recovery_plan()
create_checkpoint()
restore_checkpoint()
rollback()
compensate()
retry()
resume()
replan()
isolate()
failover()
degrade()
abort()
request_human_intervention()
verify_recovery()
get_recovery_status()
get_incident()
get_timeline()
detect_recovery_loop()
check_recovery_budget()
explain_recovery_decision()

⸻

53. Recovery Events

The system MUST emit events such as:

FailureDetected
FailureClassified
IncidentCreated
RecoveryRequested
RecoveryDiagnosisStarted
RecoveryDiagnosisCompleted
StateReconstructionStarted
StateReconstructed
RecoveryStrategyGenerated
RecoveryStrategyRejected
RecoveryStrategySelected
CheckpointCreated
CheckpointRestored
RollbackStarted
RollbackCompleted
RollbackFailed
CompensationStarted
CompensationCompleted
CompensationFailed
RetryStarted
RetryCompleted
RetryExhausted
ResumeStarted
ResumeCompleted
ReplanningStarted
ReplanningCompleted
IsolationStarted
CapabilityIsolated
CapabilityRestored
FailoverStarted
FailoverCompleted
DegradedModeEntered
DegradedModeExited
HumanInterventionRequested
HumanInterventionReceived
RecoveryVerificationStarted
RecoveryVerified
RecoveryVerificationFailed
RecoveryPartiallyCompleted
RecoveryEscalated
RecoveryAborted
RecoveryUnrecoverable
RecoveryCompleted

⸻

54. Recovery Receipt

Every completed recovery SHOULD produce:

RecoveryReceipt {
    recovery_id
    original_failure
    original_state
    failed_state
    strategy
    actions[]
    evidence[]
    verification
    final_state
    remaining_risk
    unresolved_effects
    human_intervention
    timestamps
    integrity_hash
}

⸻

55. Security Threats

Recovery introduces its own attack surface.

Threats include:

RECOVERY-SEC-01
False failure injection
RECOVERY-SEC-02
Recovery strategy manipulation
RECOVERY-SEC-03
Rollback abuse
RECOVERY-SEC-04
Compensation abuse
RECOVERY-SEC-05
Retry amplification
RECOVERY-SEC-06
Recovery loop attack
RECOVERY-SEC-07
Checkpoint poisoning
RECOVERY-SEC-08
Snapshot tampering
RECOVERY-SEC-09
State rollback attack
RECOVERY-SEC-10
Idempotency-key abuse
RECOVERY-SEC-11
Incident suppression
RECOVERY-SEC-12
Human approval spoofing
RECOVERY-SEC-13
Recovery privilege escalation
RECOVERY-SEC-14
Audit deletion
RECOVERY-SEC-15
False recovery verification

⸻

56. Recovery Must Not Erase History

This is a constitutional-level principle.

Bad:

Failure
 ↓
Rollback
 ↓
Pretend failure never happened

Correct:

Failure
 ↓
Record Failure
 ↓
Recover
 ↓
Verify
 ↓
Record Recovery
 ↓
Continue

Historical truth remains.

⸻

57. Recovery Loop

The complete recovery loop is:

ACTION
  ↓
EXECUTION
  ↓
VERIFICATION
  ↓
FAILURE
  ↓
DIAGNOSIS
  ↓
STATE RECONSTRUCTION
  ↓
RECOVERY STRATEGY
  ↓
SIMULATION
  ↓
AUTHORIZATION
  ↓
RECOVERY ACTION
  ↓
VERIFICATION
  ↓
WORLD UPDATE
  ↓
EXPERIENCE
  ↓
LEARNING

⸻

58. Example: File Deployment

Normal:

Build
 ↓
Test
 ↓
Deploy
 ↓
Verify

Failure:

Deploy
 ↓
Verification Failed

Recovery:

Determine deployed version
        ↓
Compare actual vs expected
        ↓
Create incident
        ↓
Determine rollback availability
        ↓
Simulate rollback
        ↓
Authorize
        ↓
Rollback deployment
        ↓
Health check
        ↓
Verify

If rollback fails:

Isolate deployment
 ↓
Failover
 ↓
Human escalation

⸻

59. Example: Financial Operation

Transfer requested
        ↓
Transfer submitted
        ↓
Network timeout

Veda MUST NOT:

Retry immediately

Instead:

State = UNKNOWN
        ↓
Query transaction status
        ↓
Verify external ledger
        ↓
If completed:
    do not duplicate
If failed:
    retry if authorized
If ambiguous:
    escalate

⸻

60. Example: AI Tool Failure

Model proposes:
Delete 10,000 files

Tool executes:

Delete 2,000
Tool crashes

Recovery:

STOP TOOL
 ↓
ISOLATE CAPABILITY
 ↓
RECONSTRUCT STATE
 ↓
VERIFY deleted files
 ↓
Determine whether restoration exists
 ↓
Restore only verified targets
 ↓
Verify
 ↓
Human review if irreversible

The correct response is not:

"Try the deletion again."

Humanity has already contributed enough stories about computers deleting things twice.

⸻

61. Recovery Metrics

Veda SHOULD measure:

Recovery Success Rate
Mean Time To Recovery
Mean Time To Detect
Compensation Success Rate
Retry Success Rate
False Recovery Rate
Recovery Verification Rate
Unrecoverable Incident Rate
Human Escalation Rate
Recovery Cost
Recovery Risk
Recovery Loop Rate
Prediction Error

⸻

62. Recovery Quality

Recovery quality SHOULD consider:

Safety
Correctness
Completeness
Reversibility
Cost
Latency
Residual Risk
Evidence Quality
Verification Strength
Human Burden

⸻

63. Learning From Recovery

The system SHOULD learn:

Failure Pattern
        ↓
Recovery Outcome
        ↓
Experience
        ↓
Reflection
        ↓
Recovery Knowledge
        ↓
Planner / Router / Decision updates

Examples:

Tool X frequently fails after 10 minutes

may become:

Tool X timeout risk increased

or:

Provider Y has high recovery cost

The learning system MUST preserve provenance and confidence.

⸻

64. Recovery and Evolution

Repeated recovery failures MAY trigger:

Evolution Proposal

Example:

Same tool fails 100 times
        ↓
Experience
        ↓
Reflection
        ↓
Capability redesign proposal

But:

Recovery Engine
≠
Evolution Authority

Architecture changes still pass through RFC-0037 and constitutional governance.

⸻

65. Formal Recovery Model

Let:

Wf = failed world

and:

S = recovery strategy

Then:

Recovery(Wf, S) → W'

The recovery engine seeks:

Safe(W')
∧
Consistent(W')
∧
Authorized(S)
∧
Verified(W')

while minimizing:

Risk
+
Residual Damage
+
Cost
+
Uncertainty

subject to hard constraints.

⸻

66. Recovery Invariants

REC-1

Recovery MUST NOT erase historical failure.

REC-2

Recovery MUST NOT bypass authorization.

REC-3

Recovery MUST NOT assume rollback is always possible.

REC-4

Compensation MUST be distinguished from rollback.

REC-5

Retry MUST be bounded.

REC-6

Recovery MUST be verifiable.

REC-7

Unknown external state MUST remain unknown until verified.

REC-8

Recovery MUST account for concurrent changes.

REC-9

Recovery MUST NOT blindly restore stale snapshots.

REC-10

Recovery actions MUST have provenance.

REC-11

High-risk recovery MUST support escalation.

REC-12

Recovery MUST NOT grant new authority implicitly.

REC-13

Recovery MUST support idempotency where possible.

REC-14

Recovery loops MUST be detectable.

REC-15

Recovery budgets MUST exist.

REC-16

Compensation failure MUST be observable.

REC-17

Partial recovery MUST be represented explicitly.

REC-18

Unrecoverable states MUST be represented explicitly.

REC-19

Recovery MUST distinguish execution success from recovery success.

REC-20

Recovery MUST update the World only according to verification policy.

REC-21

Recovery MUST preserve evidence.

REC-22

Recovery MUST preserve causal history.

REC-23

Recovery strategy selection MUST be auditable.

REC-24

Recovery MUST respect temporal validity.

REC-25

Recovery MUST respect causal dependencies.

REC-26

Recovery SHOULD use simulation for high-risk operations.

REC-27

Recovery SHOULD prefer the least harmful viable strategy.

REC-28

Recovery MUST NOT optimize cost above safety constraints.

REC-29

Human override MUST remain available.

REC-30

No recovery mechanism may claim success without sufficient evidence.

⸻

67. Architecture

                 FAILURE
                    │
                    ▼
             ┌──────────────┐
             │   DIAGNOSIS  │
             └──────┬───────┘
                    │
                    ▼
          ┌────────────────────┐
          │ STATE RECONSTRUCTION│
          └─────────┬──────────┘
                    │
                    ▼
          ┌────────────────────┐
          │ STRATEGY GENERATION│
          └─────────┬──────────┘
                    │
                    ▼
          ┌────────────────────┐
          │ STRATEGY EVALUATION│
          └─────────┬──────────┘
                    │
                    ▼
               SIMULATION
                    │
                    ▼
              AUTHORIZATION
                    │
                    ▼
             RECOVERY ACTION
                    │
                    ▼
               OBSERVATION
                    │
                    ▼
              VERIFICATION
                 /     \
                /       \
          SUCCESS       FAILURE
             │             │
             ▼             ▼
       WORLD UPDATE     ESCALATION
             │
             ▼
         EXPERIENCE

⸻

68. Relationship With Other RFCs

RFC-0020 Planner
      ↓
Normal Plan
RFC-0026 Verification
      ↓
Failure Detection
      ↓
RFC-0027 Recovery
      ↓
Recovery Plan
      ↓
RFC-0024 Simulation
      ↓
RFC-0010 Authorization
      ↓
RFC-0028+ Action Fabric
      ↓
RFC-0026 Verification

Recovery therefore closes the missing half of the execution loop:

PLAN
 ↓
ACT
 ↓
VERIFY
 ↓
FAIL
 ↓
RECOVER
 ↓
VERIFY
 ↓
CONTINUE

⸻

69. Final Principle

Veda must not interpret failure as an exception that disappears when a function returns.

Failure is a first-class state of reality.

The correct architecture is:

Failure
    ↓
Understand
    ↓
Contain
    ↓
Recover
    ↓
Verify
    ↓
Learn

And the deepest rule is:

A system is not truly autonomous because it can perform actions. It becomes robust when it can recognize failure, contain damage, recover safely, verify the recovery, and learn without rewriting history.