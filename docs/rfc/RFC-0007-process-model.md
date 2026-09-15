RFC-0007: Veda Process Model

Status: Draft
Version: 0.1.0
Layer: Layer 3 — Agency
Module: Process Runtime
Depends On:

* RFC-0001 — Veda Constitution
* RFC-0001A — Permission Matrix
* RFC-0002 — World Model
* RFC-0003 — Event Model
* RFC-0004 — State & World Transition
* RFC-0005 — Intent Model
* RFC-0006 — Goal Model

⸻

1. Abstract

RFC-0007 defines the Process Model of Veda.

A Process represents active, persistent, observable work performed by Veda or one of its agents in pursuit of a Goal.

The Process Model separates:

Goal
"What should become true?"
Process
"What work is currently being carried out?"
Action
"What operation is being performed?"

A Process is therefore the runtime bridge between planning and execution.

The canonical relationship is:

Intent
   ↓
Goal
   ↓
Plan
   ↓
Process
   ↓
Action
   ↓
Event
   ↓
World Transition

A Process MUST maintain its own execution state, lifecycle, dependencies, progress, context, resources, failures, and recovery state.

⸻

2. Motivation

Without a Process abstraction, Veda would have difficulty representing long-running work.

Examples:

"Train a model for 8 hours."
"Build the Veda runtime."
"Monitor disk usage."
"Research 50 documents."
"Deploy the new system."
"Backup the entire knowledge library."

These are not single Actions.

They are ongoing activities composed of many Actions.

Therefore:

Goal ≠ Process
Process ≠ Action

A Process exists because real work takes time, encounters failures, changes state, and often requires multiple actions.

⸻

3. Core Definition

A Process is a runtime entity representing an active or historical execution context associated with one or more Goals and Plans.

Formally:

Process =
    Process Identity
  + Goal Reference
  + Plan Reference
  + Actor
  + Runtime State
  + Execution Context
  + Current Step
  + Dependencies
  + Resources
  + Actions
  + Events
  + Progress
  + Failure State
  + Recovery State
  + Lifecycle

⸻

4. Process Object

The canonical Process object SHOULD contain:

process_id:
process_version:
parent_goal:
parent_plan:
actor:
executor:
process_type:
status:
current_step:
current_action:
context:
scope:
dependencies:
resources:
progress:
started_at:
updated_at:
paused_at:
completed_at:
failed_at:
expires_at:
retry_policy:
recovery_policy:
failure_state:
checkpoint:
result:
evidence:
child_processes:
parent_process:
priority:
urgency:
lease:
created_at:

⸻

5. Process Identity

Every Process MUST have a unique:

process_id

Example:

process_01J...

Process identity MUST remain stable for the lifetime of that Process.

A retry of the same Process SHOULD NOT automatically create an unrelated Process unless policy requires process replacement.

⸻

6. Process and Goal

A Process MUST reference the Goal it is serving.

parent_goal:
  goal_id: goal_01J...

The Process MUST NOT silently redefine the Goal.

If the desired outcome changes, the Goal Model MUST be updated or superseded.

⸻

7. Process and Plan

A Process MAY reference one active Plan.

parent_plan:
  plan_id: plan_01J...

A Process MAY change Plans if the current Plan becomes invalid.

Example:

Goal
 ↓
Plan A
 ↓
Execution
 ↓
Environment changes
 ↓
Plan A invalid
 ↓
Plan B
 ↓
Same Goal

This is valid.

⸻

8. Process vs Action

A Process may contain many Actions.

Example:

Process:
"Build Veda"
Actions:
1. Read RFC
2. Create file
3. Write code
4. Run tests
5. Fix failure
6. Commit
7. Verify

Each Action remains separately auditable.

⸻

9. Process Types

Veda SHOULD support several Process types.

9.1 Task Process

Finite work with a clear completion condition.

Example:

Implement RFC-0007.

⸻

9.2 Workflow Process

A structured sequence of multiple steps.

Example:

Build → Test → Deploy → Verify

⸻

9.3 Monitoring Process

Long-running observation.

Example:

Monitor disk usage.

⸻

9.4 Service Process

A persistent runtime service.

Example:

Veda Event Bus.

⸻

9.5 Research Process

Collect, evaluate, synthesize, and verify information.

Example:

Research local AI runtimes.

⸻

9.6 Learning Process

Acquire knowledge or improve a model/skill.

Example:

Train a specialized classifier.

⸻

9.7 Recovery Process

Restore desired system state after failure.

Example:

Recover Veda after storage failure.

⸻

9.8 Scheduled Process

Triggered according to a temporal policy.

Example:

Daily backup.

⸻

10. Process Lifecycle

The canonical lifecycle is:

CREATED
   ↓
INITIALIZING
   ↓
READY
   ↓
RUNNING
   ↓
VERIFYING
   ↓
COMPLETED

Alternative states:

PAUSED
BLOCKED
WAITING
FAILED
CANCELLED
EXPIRED
ABANDONED
RECOVERING

⸻

11. Process State Machine

                  ┌─────────┐
                  │ CREATED │
                  └────┬────┘
                       ↓
               ┌──────────────┐
               │ INITIALIZING │
               └──────┬───────┘
                      ↓
                ┌──────────┐
                │  READY   │
                └────┬─────┘
                     ↓
                ┌──────────┐
          ┌────►│ RUNNING  │◄────┐
          │     └────┬─────┘     │
          │          │            │
          │          ▼            │
          │    ┌───────────┐      │
          │    │ VERIFYING │      │
          │    └─────┬─────┘      │
          │          │            │
          │          ▼            │
          │    ┌────────────┐     │
          │    │ COMPLETED  │     │
          │    └────────────┘     │
          │                       │
     ┌────┴────┐             ┌────┴─────┐
     │  PAUSED │             │ RECOVERY │
     └────┬────┘             └────┬─────┘
          │                       │
          └───────────────────────┘
Other terminal states:
FAILED
CANCELLED
EXPIRED
ABANDONED
Temporary state:
BLOCKED
WAITING

⸻

12. CREATED

The Process object exists but execution has not started.

⸻

13. INITIALIZING

Veda prepares:

context
resources
dependencies
capabilities
workspace
checkpoints
authorization references

⸻

14. READY

All required prerequisites are satisfied.

The Process may begin execution.

READY does not mean the Process has permission to perform every possible Action.

Each Action remains subject to authorization.

⸻

15. RUNNING

The Process is actively performing or coordinating work.

⸻

16. WAITING

The Process is waiting for an external condition.

Examples:

network response
timer
human approval
external job
file lock
resource availability

Waiting MUST NOT be interpreted as failure.

⸻

17. BLOCKED

A Process cannot continue because a required dependency is unavailable.

Example:

Build process
    ↓
Blocked by missing compiler

⸻

18. PAUSED

Execution has intentionally stopped while preserving runtime state.

The Process SHOULD be resumable.

⸻

19. VERIFYING

Execution has produced an intermediate or final result and verification is occurring.

⸻

20. COMPLETED

The Process completed its assigned execution steps.

IMPORTANT:

Process COMPLETED

does NOT necessarily mean:

Goal ACHIEVED

The Goal must still be evaluated independently.

⸻

21. FAILED

The Process encountered a failure that prevented continuation or successful completion.

Failure MUST include:

failure type
failure reason
evidence
last known state
recoverability

⸻

22. RECOVERING

The Process is executing recovery procedures.

Possible recovery:

retry
rollback
restore checkpoint
change plan
replace resource
request approval
spawn recovery process

⸻

23. CANCELLED

Execution was intentionally terminated by an authorized actor or policy.

⸻

24. EXPIRED

The Process exceeded its allowed execution lifetime.

⸻

25. ABANDONED

The Process is no longer actively pursued and no automatic continuation is expected.

⸻

26. Current Step

A Process SHOULD maintain its current logical step.

Example:

current_step:
  id: step_04
  name: verify_backup

The current step is informational runtime state.

The authoritative history remains the Event Store.

⸻

27. Process History

A Process MUST NOT rely exclusively on mutable runtime state.

Important transitions MUST generate Events.

Example:

ProcessCreated
ProcessStarted
StepStarted
ActionStarted
ActionCompleted
StepCompleted
ProcessPaused
ProcessResumed
ProcessFailed
ProcessRecovered
ProcessCompleted

This allows reconstruction of the Process lifecycle.

⸻

28. Event-Sourced Process State

The runtime Process state MAY be represented as a projection of Events.

Conceptually:

Events
  ↓
Process Projection
  ↓
Current Process State

The Event Store remains the historical source of truth according to RFC-0003.

⸻

29. Process Context

A Process SHOULD have an execution context.

Example:

context:
  project: veda
  workspace: /projects/veda
  environment: development
  branch: main

Context MUST be explicit enough for the Process to be resumed safely.

⸻

30. Process Scope

Process scope MUST define the boundaries within which work may occur.

Example:

scope:
  filesystem:
    - /projects/veda
  repositories:
    - github.com/168211681/Veda

A Process MUST NOT silently expand scope.

⸻

31. Resource Management

A Process MAY reserve or consume:

CPU
RAM
GPU
storage
network
API quota
money
time
human attention
external services

Resources SHOULD be tracked.

⸻

32. Resource Exhaustion

If required resources become unavailable:

RUNNING
   ↓
RESOURCE_FAILURE
   ↓
BLOCKED / RECOVERING / FAILED

The choice depends on policy.

⸻

33. Dependencies

A Process MAY depend on:

other Processes
Goals
resources
external systems
human approval
network
tools
data

Dependencies MUST be explicit where possible.

⸻

34. Process Dependency Graph

Example:

P1: Build
 ↓
P2: Test
 ↓
P3: Deploy
 ↓
P4: Verify

P3 MUST NOT begin if P2 has a mandatory failed dependency.

⸻

35. Parent and Child Processes

A Process MAY spawn child Processes.

Example:

P0: Build Veda
 ├── P1: Build backend
 ├── P2: Build frontend
 ├── P3: Run tests
 └── P4: Documentation

Child Processes MUST remain traceable to their parent.

⸻

36. Parallel Processes

Independent child Processes MAY execute concurrently.

Example:

             P0
          /  |  \
         /   |   \
       P1   P2   P3

The parent Process SHOULD define synchronization conditions.

⸻

37. Process Synchronization

Synchronization conditions MAY include:

ALL
ANY
QUORUM
SEQUENCE
THRESHOLD
CONDITION

Example:

Deploy may begin only when:
P1 = completed
P2 = completed
P3 = verified

⸻

38. Process Checkpoint

Long-running Processes SHOULD support checkpoints.

Example:

checkpoint:
  checkpoint_id: cp_004
  step: dataset_validation
  state_hash:
  created_at:

A checkpoint allows recovery without restarting the entire Process.

⸻

39. Checkpoint Integrity

Checkpoints MUST contain enough information to determine:

what happened
what state was reached
what remains
what resources were acquired
what assumptions were active

Checkpoint metadata SHOULD be integrity-protected.

⸻

40. Resume Semantics

A paused or recovered Process MUST NOT blindly continue.

Before resuming:

checkpoint
    ↓
World state check
    ↓
Dependency check
    ↓
Authorization check
    ↓
Resource check
    ↓
Resume

If the World has changed materially, Veda SHOULD replan or restart from a safe checkpoint.

⸻

41. Idempotency

Process Actions SHOULD be idempotent where possible.

Example:

create directory

can safely be retried if it already exists.

Non-idempotent operations MUST use stronger transaction or compensation mechanisms.

⸻

42. Retry Policy

A Process MAY define:

retry_policy:
  max_attempts: 3
  backoff: exponential
  retryable_failures:
    - timeout
    - network_error

Retry MUST NOT be infinite by default.

⸻

43. Retry Safety

Before retrying, Veda MUST determine:

Did the original Action actually execute?
Did the external system change?
Is the Action idempotent?
Would repeating it cause harm?

This is essential for operations such as:

payments
deletions
deployments
messages
API mutations

⸻

44. Process Recovery

Recovery SHOULD follow:

Failure
  ↓
Diagnose
  ↓
Determine State
  ↓
Assess Reversibility
  ↓
Choose Recovery Strategy
  ↓
Authorize
  ↓
Execute Recovery
  ↓
Verify

⸻

45. Recovery Strategies

Supported strategies SHOULD include:

RETRY
RESUME
ROLLBACK
COMPENSATE
REPLAN
RESTART
ABORT
HUMAN_REVIEW

⸻

46. Process Failure Classification

Failures SHOULD be classified as:

INPUT_FAILURE
AUTHORIZATION_FAILURE
CAPABILITY_FAILURE
RESOURCE_FAILURE
DEPENDENCY_FAILURE
NETWORK_FAILURE
TOOL_FAILURE
MODEL_FAILURE
WORLD_STATE_CONFLICT
VERIFICATION_FAILURE
TIMEOUT
UNKNOWN_FAILURE

⸻

47. Human Intervention

A Process MAY require human intervention.

Example:

Process:
Deploy production system
Condition:
Security policy requires human approval

State:

WAITING_FOR_HUMAN

The approval becomes an auditable Event.

⸻

48. Process Lease

Long-running Processes MAY use a runtime lease.

Example:

lease:
  owner: agent_veda
  expires_at:
  scope:

The lease prevents stale Processes from continuing indefinitely.

A Process with an expired lease MUST stop or renew through authorized policy.

⸻

49. Process Ownership

Every Process MUST have:

actor
executor
owner

where applicable.

These fields have different meanings.

Actor

Who requested the work.

Executor

Which agent/system is performing it.

Owner

Which authority is responsible for the Process.

These MUST NOT be conflated.

⸻

50. Process Priority

Processes inherit priority from their Goals unless explicitly overridden by policy.

Possible:

LOW
NORMAL
HIGH
CRITICAL

Priority affects scheduling.

It MUST NOT override safety or authorization.

⸻

51. Process Preemption

A Process MAY be preempted by:

higher-priority process
safety event
resource emergency
human command
system shutdown
security event

Preemption SHOULD preserve resumable state where possible.

⸻

52. Process Starvation

The Scheduler SHOULD prevent low-priority Processes from being permanently starved by high-priority workloads.

Possible mechanisms:

fair scheduling
quotas
time slices
aging
resource budgets

⸻

53. Process Cancellation

Cancellation MUST be explicit and auditable.

Example:

ProcessCancelled

The system SHOULD evaluate whether active Actions require:

rollback
compensation
cleanup

⸻

54. Process Timeout

Processes MAY have execution deadlines.

Example:

expires_at: 2026-09-17T00:00:00+07:00

Timeout behavior SHOULD be predefined.

⸻

55. Process Result

A Process MAY produce a result:

result:
  status:
  outputs:
  artifacts:
  evidence:

Results MUST be distinguishable from raw execution logs.

⸻

56. Process Artifacts

A Process MAY produce:

files
commits
datasets
models
reports
configuration
database changes
messages
external resources

Artifacts SHOULD be registered in the World Model where appropriate.

⸻

57. Process Verification

Process completion SHOULD be verified against Process-level criteria.

Example:

Process:
Build software.
Process completion:
Build command exited successfully.
Goal verification:
Application actually works.

Again:

Process success ≠ Goal success

⸻

58. Process Progress

Progress SHOULD be based on observable execution state.

Example:

progress:
  completed_steps: 7
  total_steps: 10

Progress is an estimate of execution progress.

It is not evidence of Goal achievement.

⸻

59. Process Drift

A Process MUST be monitored for divergence from its parent Goal.

Example:

Goal:
Improve Veda security.
Process:
Rewrite dashboard animations.

The Process has drifted.

Veda SHOULD detect low alignment.

⸻

60. Process Alignment

Veda MAY calculate:

process_goal_alignment

based on:

current step
current action
goal criteria
scope
dependencies

Low alignment SHOULD trigger:

replanning
pause
human review

depending on risk.

⸻

61. Dynamic Replanning

A Process SHOULD support replanning.

Example:

Goal
 ↓
Plan A
 ↓
Process
 ↓
World changes
 ↓
Plan A invalid
 ↓
Plan B
 ↓
Continue Process

The Process identity SHOULD remain stable when appropriate.

⸻

62. Process Concurrency

Multiple Processes MAY operate on the same World.

Example:

P1: Backup
P2: Update database
P3: Monitor storage

Concurrency control MUST rely on:

RFC-0004
state versioning
transactions
conflict detection
authorization

⸻

63. Process Conflict

If two Processes attempt incompatible changes:

P1 → modify file A
P2 → delete file A

the system MUST detect the conflict.

Resolution MAY be:

serialize
merge
reject
pause
human review

⸻

64. Process Isolation

Processes SHOULD have isolated execution contexts where appropriate.

Examples:

container
sandbox
temporary workspace
virtual environment
restricted filesystem

Isolation SHOULD be proportional to risk.

⸻

65. Process Security Boundary

A Process MUST NOT automatically inherit unlimited capabilities.

The Process receives only:

authorized capabilities
+
appropriate scope
+
valid lease

Capabilities are defined by RFC-0009.

Authorization is defined by RFC-0010.

⸻

66. Process and Agent

An Agent MAY execute a Process.

However:

Agent ≠ Process

An Agent is an actor/executor with capabilities.

A Process is the work being performed.

One Agent MAY execute many Processes.

One Process MAY be transferred between authorized Agents.

⸻

67. Process Handoff

A Process MAY be handed from one Agent to another.

Example:

Agent A
   ↓
Process
   ↓
Agent B

The handoff MUST preserve:

Process identity
Goal
Plan
state
checkpoint
authorization
audit trail

⸻

68. Process Pause

Pause MUST preserve sufficient runtime state to resume safely.

Pause causes MAY include:

human request
resource shortage
safety condition
waiting dependency
system maintenance
policy

⸻

69. Process Resume

Resume requires:

valid Process state
valid authorization
valid capability
valid context
valid World assumptions

If any critical assumption changed, the Process SHOULD enter:

REVALIDATION

before continuing.

⸻

70. Process State Reconciliation

After crash or restart:

Stored Process State
       ↓
Event History
       ↓
Current World
       ↓
Reconcile
       ↓
Recover or Halt

Veda MUST NOT blindly trust stale in-memory state.

⸻

71. Process Crash Recovery

On system restart, Veda SHOULD identify Processes in:

RUNNING
VERIFYING
RECOVERING

and determine whether they were interrupted.

Each Process SHOULD transition to an appropriate recovery state.

⸻

72. Zombie Process Prevention

A Process MUST NOT remain indefinitely marked RUNNING when its executor no longer exists.

The runtime SHOULD use:

heartbeat
lease
executor identity
timeout
event reconciliation

to detect orphaned Processes.

⸻

73. Process Observability

Every active Process SHOULD expose:

status
current step
current action
progress
resource usage
dependencies
last event
last heartbeat
failure state

This enables Veda and humans to understand what the system is actually doing.

⸻

74. Human Interface

The user SHOULD be able to inspect:

What is Veda doing?
Why is it doing it?
Which Goal does it serve?
Which Process is active?
What Action is running?
What resources are being used?
What is blocked?
What failed?
What approval is required?

This is a core requirement for trustworthy autonomy.

⸻

75. Process Events

The Process subsystem SHOULD emit:

ProcessCreated
ProcessInitialized
ProcessReady
ProcessStarted
ProcessStepStarted
ProcessStepCompleted
ProcessWaiting
ProcessBlocked
ProcessPaused
ProcessResumed
ProcessActionStarted
ProcessActionCompleted
ProcessVerificationStarted
ProcessVerificationCompleted
ProcessFailed
ProcessRecoveryStarted
ProcessRecoveryCompleted
ProcessReplanned
ProcessHandoff
ProcessCancelled
ProcessExpired
ProcessCompleted

All significant transitions MUST integrate with RFC-0003.

⸻

76. Process Audit

For every consequential Process, Veda SHOULD be able to answer:

Which Goal created this Process?
Which Plan was used?
Which Agent executed it?
Which Actions occurred?
Which capabilities were used?
Which authorization permitted them?
What World changes occurred?
What failed?
What recovery occurred?
Why did the Process stop?

⸻

77. Process Security Requirements

The Process Runtime MUST:

1. Maintain process identity.
2. Maintain actor identity.
3. Preserve Goal linkage.
4. Preserve Plan linkage.
5. Respect capability boundaries.
6. Respect authorization.
7. Maintain execution history.
8. Support safe pause/resume.
9. Detect stale processes.
10. Prevent unauthorized scope expansion.
11. Support failure recovery.
12. Preserve auditability.
13. Prevent silent objective drift.
14. Prevent stale execution after authorization expiry.

⸻

78. Privacy Requirements

Process contexts may contain sensitive information.

The runtime SHOULD:

minimize exposed context
isolate secrets
restrict logs
classify artifacts
control provider access

Secrets MUST NOT be written into ordinary logs when avoidable.

⸻

79. Performance Requirements

The Process Runtime SHOULD be lightweight.

Long-running Processes SHOULD use:

event-driven updates
checkpoints
heartbeats
incremental state

rather than continuously recomputing the entire World.

⸻

80. Determinism

Process behavior SHOULD be reproducible from:

Process definition
Plan
Event history
World state
Policy
Capability state
Model versions
external observations

External nondeterminism MUST be recorded as observed input.

⸻

81. Process Schema Versioning

Every Process MUST contain:

schema_version: "0.1"
process_version: 1

Historical Process records MUST remain interpretable.

⸻

82. Process Invariants

PROC-1

Every Process MUST have a unique identity.

PROC-2

Every user-derived Process MUST trace to a Goal.

PROC-3

A Process MUST NOT redefine its parent Goal without an explicit Goal change.

PROC-4

Process completion MUST NOT imply Goal achievement.

PROC-5

Every consequential Action executed by a Process MUST be auditable.

PROC-6

A Process MUST NOT execute outside its authorized scope.

PROC-7

A Process MUST NOT automatically inherit unlimited capabilities.

PROC-8

A paused Process MUST preserve sufficient state for safe resumption.

PROC-9

A resumed Process MUST revalidate critical assumptions.

PROC-10

A failed Process MUST preserve failure information.

PROC-11

Retries MUST respect idempotency and safety.

PROC-12

Expired authorization MUST prevent further consequential execution.

PROC-13

Zombie Processes MUST be detectable.

PROC-14

Concurrent Processes MUST be subject to World consistency rules.

PROC-15

Process state MUST remain reconstructible from authoritative history where required.

PROC-16

Process scope MUST NOT silently expand.

PROC-17

Process recovery MUST remain auditable.

PROC-18

An Agent MUST remain distinct from the Process it executes.

PROC-19

A Process MUST remain distinct from the Actions it performs.

PROC-20

Process progress MUST NOT be represented as verified Goal achievement.

⸻

83. Relationship With Previous RFCs

RFC-0005
Intent
   ↓
RFC-0006
Goal
   ↓
RFC-0007
Process

The semantic progression is:

Intent
"What do I want?"
Goal
"What state should exist?"
Process
"What work is currently being performed?"

⸻

84. Relationship With Future RFCs

RFC-0007 provides the runtime foundation for:

RFC-0008 — Action Model
RFC-0009 — Capability Model
RFC-0010 — Authorization & Policy
RFC-0011 — Capability Lease & Token
RFC-0019 — Attention Engine
RFC-0020 — Planner
RFC-0026 — Verification Engine
RFC-0027 — Rollback & Recovery
RFC-0028 — Tool & Capability Registry
RFC-0031 — Event/Audit/Trace Fabric
RFC-0033 — Veda Self Model
RFC-0034 — Self-Diagnostics
RFC-0035 — Experience Model
RFC-0037 — Evolution Engine

⸻

85. Canonical Execution Model

The Veda runtime now becomes:

Human
  ↓
Intent
  ↓
Goal
  ↓
Plan
  ↓
Process
  ↓
Action
  ↓
Authorization
  ↓
Execution
  ↓
Event
  ↓
World Transition
  ↓
Verification
  ↓
Process Outcome
  ↓
Goal Outcome

⸻

86. Example: Veda Building Veda

User Intent:

"สร้างระบบ World Model ให้ Veda"

Goal:

World Model implementation exists,
passes required tests,
supports entities, relationships, states,
events, evidence, and temporal state.

Plan:

1. Define schema
2. Implement storage
3. Implement query layer
4. Implement projections
5. Implement tests
6. Verify

Process:

process_id: process_world_model_001
status: RUNNING
current_step: implement_query_layer

Actions:

A1 create files
A2 write code
A3 run tests
A4 inspect failure
A5 modify code
A6 rerun tests

Events:

ActionStarted
FileCreated
CodeWritten
TestStarted
TestFailed
CodeModified
TestStarted
TestPassed

Verification:

World Model test suite passes.
Required invariants pass.

Process:

COMPLETED

Goal:

ACHIEVED

The distinction remains intact throughout the entire lifecycle.

⸻

87. Long-Running Process Example

Consider:

"Train a model overnight."

Goal:

Produce a model satisfying benchmark threshold X.

Process:

Initialize environment
      ↓
Load dataset
      ↓
Validate dataset
      ↓
Start training
      ↓
Monitor resources
      ↓
Checkpoint
      ↓
Evaluate
      ↓
If insufficient:
    resume/retrain
      ↓
Final verification

If training exits successfully but benchmark fails:

Process:
COMPLETED
Goal:
FAILED / NOT ACHIEVED

This is correct behavior.

⸻

88. Runtime Principle

The Process layer exists to make Veda’s behavior observable.

Veda should never merely say:

“I’m working on it.”

It should be able to represent:

Goal:
Build Veda World Model
Process:
process_042
Status:
RUNNING
Current Step:
Implement transition projection
Current Action:
Running integration tests
Progress:
62%
Blocked:
No
Required Approval:
No
Last Event:
TestSuiteCompleted
Next Expected Step:
Verify World State

This makes autonomy inspectable instead of magical.

⸻

89. Final Principle

The Process Model defines the difference between an objective and ongoing work.

The canonical hierarchy is:

Intent
  ↓
Goal
  ↓
Plan
  ↓
Process
  ↓
Action
  ↓
Event
  ↓
World

The central invariants are:

PROCESS ≠ GOAL
PROCESS ≠ ACTION
PROCESS COMPLETED ≠ GOAL ACHIEVED
PROCESS AUTHORITY ≠ UNLIMITED AUTHORITY

Veda is therefore able to execute long-running, interruptible, recoverable, auditable work while preserving the original human objective.

⸻

90. Status

Draft v0.1.0

Next RFC:

RFC-0008 — Action Model

RFC-0008 will define the atomic execution unit of Veda: exactly what it means for Veda to perform an operation against the real world, including preconditions, parameters, expected outcomes, authorization, risk, idempotency, verification, rollback, and auditability.
