RFC-0006: Veda Goal Model

Status: Draft
Version: 0.1.0
Layer: Layer 2 — Human Intent
Module: Goal Management
Depends On:

* RFC-0001 — Veda Constitution
* RFC-0001A — Permission Matrix
* RFC-0002 — World Model
* RFC-0003 — Event Model
* RFC-0004 — State & World Transition
* RFC-0005 — Intent Model

⸻

1. Abstract

RFC-0006 defines the Goal Model of Veda.

A Goal represents a desired future state of the World that Veda can reason about, plan toward, measure, verify, and eventually declare achieved, failed, cancelled, or abandoned.

Intent answers:

“What does the actor want?”

Goal answers:

“What future state would satisfy that intent?”

The fundamental transformation is:

Intent
   ↓
Desired Outcome
   ↓
Goal
   ↓
Success Criteria
   ↓
Planning

A Goal MUST be more precise than the Intent from which it originated.

However, Veda MUST NOT invent success criteria that materially change the user’s intent without explicit authorization.

⸻

2. Motivation

Without a formal Goal Model, autonomous systems tend to confuse:

Task completed

with:

Objective achieved

These are not equivalent.

Example:

Action:
Install backup software.
Execution:
SUCCESS
Goal:
Ensure Veda data can be restored.
Goal status:
NOT VERIFIED

The installation succeeded.

The actual objective may still have failed.

Therefore:

Execution success is not Goal success.

⸻

3. Core Definition

A Goal is a structured representation of a desired future World state together with measurable conditions that determine whether the desired state has been achieved.

Formally:

Goal =
    Desired State
  + Success Criteria
  + Scope
  + Constraints
  + Priority
  + Deadline
  + Dependencies
  + Evidence Requirements
  + Verification Requirements

⸻

4. Goal Object

The canonical Goal object SHOULD contain:

goal_id:
goal_version:
parent_intent:
parent_goal:
actor:
title:
description:
goal_type:
desired_state:
success_criteria:
failure_criteria:
verification_requirements:
scope:
constraints:
preferences:
priority:
urgency:
deadline:
dependencies:
resources:
risk_tolerance:
progress:
confidence:
status:
created_at:
started_at:
completed_at:
expires_at:
assumptions:
evidence:
derived_goals:
supersedes:

⸻

5. Goal Identity

Every Goal MUST have a unique:

goal_id

Example:

goal_01J...

Goal identity MUST remain stable throughout its lifecycle.

Changes to the Goal SHOULD create a new version.

Example:

Goal v1
"Build Veda backup system"
        ↓
Goal v2
"Build Veda backup system with daily automatic backups"

Historical versions MUST remain auditable.

⸻

6. Goal Source

Every Goal MUST reference the Intent that created it.

parent_intent:
  intent_id: intent_01J...
  version: 2

This establishes:

Intent → Goal

traceability.

System-generated Goals MAY originate from:

monitoring
scheduled processes
maintenance policies
failure recovery
other goals

Such Goals MUST identify their origin.

⸻

7. Goal Types

Veda SHOULD support multiple Goal types.

7.1 Informational Goal

Acquire verified information.

Example:

Determine the current disk usage.

⸻

7.2 State Goal

Make the World reach a desired state.

Example:

Veda backup system is configured and operational.

⸻

7.3 Performance Goal

Improve a measurable property.

Example:

Reduce API latency below 500 ms.

⸻

7.4 Quality Goal

Reach a required quality threshold.

Example:

Dataset validation accuracy ≥ 95%.

⸻

7.5 Resource Goal

Acquire or maintain resources.

Example:

Maintain at least 100 GB free storage.

⸻

7.6 Maintenance Goal

Preserve an existing state.

Example:

Keep backup system healthy.

⸻

7.7 Recovery Goal

Restore a desired state after failure.

Example:

Restore Veda service after crash.

⸻

7.8 Learning Goal

Acquire knowledge or capability.

Example:

Understand the architecture of a new API.

⸻

7.9 Development Goal

Create or modify a system.

Example:

Implement RFC-0006-compatible Goal Engine.

⸻

7.10 Monitoring Goal

Maintain awareness of a condition.

Example:

Detect when disk usage exceeds 90%.

⸻

8. Desired State

A Goal SHOULD describe the desired state of the World.

Example:

desired_state:
  entity: veda_backup_system
  properties:
    enabled: true
    schedule: daily
    last_successful_backup: recent
    restore_test: passed

This is stronger than:

"Set up backup."

because the latter describes an activity rather than a verifiable state.

⸻

9. Goal vs Task

A Goal is not necessarily a Task.

Example:

Goal:
Ensure Veda can recover from storage failure.
Tasks:
1. Configure backup
2. Run backup
3. Verify backup
4. Test restore

The Tasks are implementation details.

The Goal is the desired outcome.

This distinction allows the Planner to change implementation without changing the objective.

⸻

10. Success Criteria

Every actionable Goal SHOULD have success criteria.

Example:

success_criteria:
  - type: threshold
    metric: restore_success
    operator: "=="
    value: true
  - type: freshness
    metric: backup_age
    operator: "<"
    value: 24h

Success criteria MUST be measurable whenever practical.

⸻

11. Failure Criteria

Goals SHOULD define conditions under which the Goal is considered failed.

Example:

failure_criteria:
  - backup_corrupted: true
  - restore_test: failed

Failure criteria prevent Veda from falsely declaring success.

⸻

12. Verification Requirements

A Goal MAY require specific evidence before completion.

Example:

verification_requirements:
  - verify_backup_exists
  - verify_backup_integrity
  - verify_restore

A Goal MUST NOT be marked:

ACHIEVED

until required verification succeeds.

⸻

13. Goal Completion

Goal completion follows:

Execution
   ↓
Observation
   ↓
Verification
   ↓
Success Criteria
   ↓
Goal ACHIEVED

Not:

Execution
   ↓
Goal ACHIEVED

⸻

14. Goal Lifecycle

The canonical Goal lifecycle is:

PROPOSED
   ↓
DEFINED
   ↓
VALIDATED
   ↓
READY
   ↓
ACTIVE
   ↓
VERIFYING
   ↓
ACHIEVED

Alternative terminal states:

FAILED
CANCELLED
EXPIRED
ABANDONED
SUPERSEDED
BLOCKED

⸻

15. Goal State Machine

                    ┌───────────┐
                    │ PROPOSED  │
                    └─────┬─────┘
                          ↓
                    ┌───────────┐
                    │  DEFINED  │
                    └─────┬─────┘
                          ↓
                    ┌───────────┐
                    │ VALIDATED │
                    └─────┬─────┘
                          ↓
                    ┌───────────┐
                    │   READY   │
                    └─────┬─────┘
                          ↓
                    ┌───────────┐
                    │   ACTIVE  │
                    └─────┬─────┘
                          ↓
                    ┌───────────┐
                    │ VERIFYING │
                    └─────┬─────┘
                          ↓
                    ┌───────────┐
                    │ ACHIEVED  │
                    └───────────┘
Alternative:
FAILED
CANCELLED
EXPIRED
ABANDONED
SUPERSEDED
BLOCKED

⸻

16. PROPOSED

The Goal has been generated but is not yet fully specified.

Example:

"Improve Veda security."

This may require refinement before planning.

⸻

17. DEFINED

The Goal has:

desired state
scope
success criteria
constraints

sufficiently specified for validation.

⸻

18. VALIDATED

Veda has checked:

World consistency
Actor
Intent relationship
Constraints
Policy
Feasibility
Dependencies
Resources
Risk

⸻

19. READY

The Goal is sufficiently defined and can enter planning.

READY does not imply that execution is authorized.

⸻

20. ACTIVE

The Goal currently has an active Process or Plan.

⸻

21. VERIFYING

Execution has occurred and Veda is checking whether the desired state was actually achieved.

⸻

22. ACHIEVED

All mandatory success criteria and verification requirements have passed.

⸻

23. FAILED

The Goal cannot currently be achieved under the current conditions.

Failure MUST include a reason.

Example:

failure:
  reason: insufficient_storage
  detected_at:
  evidence:

⸻

24. BLOCKED

A Goal is BLOCKED when progress cannot continue because a dependency or external condition is unresolved.

Example:

Goal:
Deploy Veda
Blocked by:
Network unavailable

BLOCKED is different from FAILED.

⸻

25. CANCELLED

The actor or authorized controller intentionally stops the Goal.

⸻

26. EXPIRED

The Goal reached its deadline without satisfying completion criteria.

⸻

27. ABANDONED

The Goal is intentionally no longer pursued without being completed.

⸻

28. SUPERSEDED

A newer Goal replaces the current Goal.

Example:

Goal v1:
Use local model A.
Goal v2:
Use local model B.

The original Goal remains historically visible.

⸻

29. Goal Hierarchy

Goals MAY form a hierarchy:

Life Objective
    ↓
Project Goal
    ↓
Milestone
    ↓
Task Goal
    ↓
Action

For Veda:

Build Personal AI
        ↓
Build Veda Core
        ↓
Build World Model
        ↓
Implement Event Store
        ↓
Implement Transition Engine

⸻

30. Parent and Child Goals

A parent Goal MAY contain child Goals.

Example:

Parent:
Build reliable Veda storage
Children:
├── Configure storage
├── Implement backup
├── Implement restore
├── Test failure recovery
└── Monitor storage

The parent MUST define how child completion contributes to parent completion.

⸻

31. Goal Dependency

Goals MAY depend on other Goals.

Example:

Goal A:
Implement Event Store
Goal B:
Implement World Transition Engine

Dependency:

B depends_on A

B SHOULD NOT become READY until A reaches an acceptable state.

⸻

32. Dependency Types

Supported dependency types SHOULD include:

BLOCKS
REQUIRES
ENABLES
SOFT_DEPENDS
CONFLICTS

Example:

Goal A BLOCKS Goal B

means B cannot proceed without A.

⸻

33. Goal Graph

The Goal system SHOULD represent dependencies as a directed graph.

G1
├── G2
│   ├── G4
│   └── G5
└── G3

The graph MUST prevent cycles where the dependency semantics prohibit them.

Example invalid graph:

G1 → G2 → G3 → G1

⸻

34. Constraints

Goals inherit applicable constraints from their parent Intent.

Example:

Intent:
Build system without cloud services.
Goal:
Deploy local model.
Constraint:
cloud=false

A child Goal MUST NOT weaken inherited hard constraints.

⸻

35. Constraint Propagation

Constraints propagate:

Intent
   ↓
Goal
   ↓
Plan
   ↓
Task
   ↓
Action

A lower layer MAY add stricter constraints.

It MUST NOT remove a mandatory constraint without explicit authority.

⸻

36. Goal Priority

Priority MAY be:

LOW
NORMAL
HIGH
CRITICAL

Priority affects scheduling.

Priority MUST NOT override:

constitutional constraints
security constraints
authorization requirements
safety requirements

⸻

37. Goal Urgency

Urgency represents how quickly the Goal should be addressed.

Example:

urgency:
  level: HIGH
  deadline: 2026-09-16T18:00:00+07:00

Urgency is distinct from priority.

A Goal can be:

High priority + low urgency

or:

Low priority + high urgency

⸻

38. Deadline

A Goal MAY have:

deadline

After the deadline:

ACTIVE → EXPIRED

unless policy explicitly permits continuation.

⸻

39. Recurring Goals

Some Goals recur.

Example:

Maintain daily backup.

This SHOULD be represented as a Goal Policy or recurring Goal specification rather than creating an unrelated Goal every time.

Example:

recurrence:
  schedule: "daily"
  timezone: "Asia/Bangkok"

Each execution cycle SHOULD remain separately traceable.

⸻

40. Conditional Goals

Example:

If disk usage exceeds 90%, restore free space below 80%.

Representation:

activation_condition:
  metric: disk_usage
  operator: ">="
  value: 90%
success_condition:
  metric: disk_usage
  operator: "<"
  value: 80%

⸻

41. Goal Scope

Every Goal SHOULD define its scope.

Example:

scope:
  world: veda_local
  entities:
    - storage_main
  projects:
    - veda_core

Scope prevents accidental expansion.

⸻

42. Scope Creep Protection

A Goal MUST NOT silently expand beyond its defined scope.

Example:

Goal:
Clean Veda project temporary files.

MUST NOT become:

Clean all temporary files on the computer.

unless explicitly authorized.

⸻

43. Goal Resources

A Goal MAY define required resources.

Examples:

CPU
RAM
storage
network
money
time
models
tools
human approval

Example:

resources:
  max_budget: 3000 THB
  required_storage: 100GB

⸻

44. Feasibility

Before a Goal becomes READY, Veda SHOULD estimate feasibility.

Possible results:

FEASIBLE
PROBABLY_FEASIBLE
UNCERTAIN
INFEASIBLE
BLOCKED

A Goal SHOULD NOT be treated as feasible simply because a model claims it is.

Feasibility SHOULD use:

World state
Resources
Capabilities
Dependencies
Constraints
Historical performance

⸻

45. Goal Confidence

Veda MAY assign confidence to the interpretation of a Goal.

Example:

confidence:
  intent_alignment: 0.96
  success_criteria: 0.91
  feasibility: 0.72

Confidence MUST NOT be confused with completion.

⸻

46. Goal Progress

Goal progress SHOULD be represented using observable evidence.

Example:

progress:
  completed_children: 4
  total_children: 5
  estimated_percent: 80

Estimated progress MUST NOT be presented as verified completion.

⸻

47. Progress Is Not Completion

The following are different:

Progress = 90%

and:

Goal = ACHIEVED

A Goal can remain incomplete at 99% if its final success criterion fails.

⸻

48. Goal Evidence

Goals SHOULD specify required evidence.

Example:

evidence_requirements:
  - test_result
  - checksum
  - system_observation
  - user_confirmation

Evidence MUST be traceable to its source.

⸻

49. Verification

Goal verification SHOULD follow:

Expected State
      ↓
Observe World
      ↓
Collect Evidence
      ↓
Evaluate Criteria
      ↓
Determine Outcome

The Verification Engine defined by RFC-0026 performs formal verification.

⸻

50. Goal Failure Semantics

A Goal failure MUST distinguish:

execution failure
verification failure
resource failure
dependency failure
constraint conflict
external failure
unknown failure

Example:

Backup command succeeded.
Restore test failed.

This is:

execution_success = true
goal_success = false

⸻

51. Partial Achievement

A Goal MAY support partial achievement.

Example:

Goal:
Migrate 1000 records.
Result:
850 records migrated.

Representation:

status: PARTIALLY_ACHIEVED
progress:
  completed: 850
  target: 1000

Partial achievement MUST NOT be treated as full achievement.

⸻

52. Goal Replanning

When the World changes significantly:

Goal
 ↓
World changes
 ↓
Original plan invalid

Veda MAY replan.

The Goal itself SHOULD remain stable unless its desired outcome has changed.

This preserves:

Goal stability
Plan flexibility

⸻

53. Goal Drift

Goal drift occurs when the execution process gradually changes the objective.

Example:

Original:
Make Veda faster.
Drift:
Change architecture.
Then:
Rewrite UI.
Then:
Build unrelated features.

Veda SHOULD detect divergence between:

Current Work

and:

Goal

⸻

54. Goal Alignment Score

Veda MAY calculate:

alignment =
current_action_relevance
vs
goal_requirements

Low alignment SHOULD trigger:

warning
replanning
human review

depending on risk.

⸻

55. Goal Conflict

Goals MAY conflict.

Example:

Goal A:
Minimize cost.
Goal B:
Maximize performance.

Veda SHOULD identify conflicts rather than optimizing both blindly.

Resolution SHOULD follow:

Constitution
→ Hard Constraints
→ Authorization
→ Goal Priority
→ User Preferences
→ Optimization

⸻

56. Goal Utility

A Goal MAY define optimization criteria.

Example:

optimization:
  objectives:
    - minimize_cost
    - maximize_reliability
    - minimize_latency
  weights:
    cost: 0.3
    reliability: 0.5
    latency: 0.2

Weights MUST NOT override hard constraints.

⸻

57. Goal and Human Approval

Some Goals SHOULD require explicit human approval before planning or execution.

Example:

Goal:
Transfer money.
approval_required:
true

The existence of an accepted Goal does not remove the requirement.

⸻

58. Goal Delegation

A user may delegate a Goal to Veda.

Example:

"ทำให้ระบบนี้พร้อม production"

Veda MUST determine:

What does production-ready mean?
What is in scope?
What tests are required?
What deployment authority exists?
What risks require approval?

Undefined success criteria MUST NOT be silently invented when consequences are significant.

⸻

59. Goal Cancellation

Cancellation MUST create an Event.

Example:

GoalCancelled

The system MUST determine what active Processes or Actions depend on that Goal.

Cancellation of a Goal SHOULD trigger appropriate cancellation or compensation policies.

⸻

60. Goal Recovery

After failure, Veda MAY:

retry
replan
rollback
create recovery goal
request human intervention
abort

The chosen response MUST depend on:

risk
reversibility
failure type
authority
policy

⸻

61. Goal Memory

Completed Goals MAY become Experience.

Example:

Goal
 ↓
Outcome
 ↓
Experience
 ↓
Reflection
 ↓
Learning

RFC-0035 and RFC-0036 define these later stages.

⸻

62. Goal Events

The Goal Engine SHOULD emit Events for important lifecycle transitions:

GoalCreated
GoalDefined
GoalValidated
GoalReady
GoalActivated
GoalBlocked
GoalProgressUpdated
GoalVerificationStarted
GoalAchieved
GoalPartiallyAchieved
GoalFailed
GoalCancelled
GoalExpired
GoalAbandoned
GoalSuperseded
GoalReplanned

These Events MUST integrate with RFC-0003.

⸻

63. Goal Auditability

For every consequential Goal, Veda SHOULD be able to answer:

Which Intent created it?
Who requested it?
What state was desired?
What constraints applied?
What success criteria existed?
What assumptions were made?
What Plans were generated?
What Actions were executed?
What evidence was collected?
Why was it declared successful or failed?

⸻

64. Security Requirements

The Goal Engine MUST:

1. Preserve parent Intent.
2. Preserve hard constraints.
3. Prevent unauthorized scope expansion.
4. Prevent silent objective modification.
5. Track goal versions.
6. Track actor identity.
7. Require verification before declaring success.
8. Respect authorization boundaries.
9. Preserve failure evidence.
10. Prevent lower-level actions from redefining the Goal.

⸻

65. Privacy Requirements

Goals MAY contain sensitive information.

The system SHOULD apply:

classification
access control
retention policy
encryption where required
provider restrictions

External intelligence providers SHOULD receive only the Goal context necessary for their task.

⸻

66. Performance Requirements

Goal evaluation SHOULD be inexpensive for simple Goals.

Complex Goals MAY invoke:

reasoning models
planning engines
simulation
world queries
historical analysis

Veda SHOULD avoid using expensive reasoning for trivial state checks.

⸻

67. Determinism

Given equivalent:

Intent
World State
Constraints
Policy
Goal Schema

Veda SHOULD produce semantically equivalent Goals.

⸻

68. Goal Schema Versioning

Goal objects MUST include:

schema_version: "0.1"
goal_version: 1

Changes to the Goal schema MUST NOT invalidate historical Goal records.

⸻

69. Goal Invariants

GOAL-1

Every Goal MUST have a unique identity.

GOAL-2

Every user-derived Goal MUST reference its source Intent.

GOAL-3

A Goal MUST define a desired outcome or desired state.

GOAL-4

Action completion MUST NOT automatically imply Goal completion.

GOAL-5

Mandatory success criteria MUST be verified before ACHIEVED.

GOAL-6

Hard constraints MUST propagate downstream.

GOAL-7

Goal scope MUST NOT silently expand.

GOAL-8

Goal modifications MUST be versioned.

GOAL-9

Goal failure MUST preserve a reason and available evidence.

GOAL-10

BLOCKED MUST remain distinguishable from FAILED.

GOAL-11

Partial achievement MUST remain distinguishable from full achievement.

GOAL-12

Cancellation MUST be auditable.

GOAL-13

Expired Goals MUST NOT silently execute.

GOAL-14

A Goal MUST NOT grant authority by itself.

GOAL-15

A Goal MUST remain distinguishable from a Plan.

GOAL-16

A Plan MAY change while the Goal remains stable.

GOAL-17

A Goal MUST NOT be declared successful based solely on model-generated claims.

GOAL-18

Goal success MUST correspond to observable World state or explicitly accepted evidence.

⸻

70. Relationship With Previous RFCs

RFC-0001
Constitution
       ↓
RFC-0002
World Model
       ↓
RFC-0003
Event Model
       ↓
RFC-0004
World Transition
       ↓
RFC-0005
Intent Model
       ↓
RFC-0006
Goal Model

The transformation is:

Human Input
     ↓
Intent
     ↓
Desired Outcome
     ↓
Goal
     ↓
Desired World State
     ↓
Success Criteria

⸻

71. Relationship With Future RFCs

RFC-0006 provides the objective layer for:

RFC-0007 — Process Model
RFC-0008 — Action Model
RFC-0010 — Authorization & Policy
RFC-0019 — Attention Engine
RFC-0020 — Planner
RFC-0021 — Temporal Model
RFC-0023 — Future & Scenario Engine
RFC-0025 — Value & Decision Engine
RFC-0026 — Verification Engine
RFC-0027 — Rollback & Recovery
RFC-0035 — Experience Model
RFC-0037 — Evolution Engine

The critical dependency is:

Goal
 ↓
Planner
 ↓
Process
 ↓
Action

⸻

72. Conceptual Data Flow

                 HUMAN
                   │
                   ▼
                INTENT
                   │
                   ▼
          ┌─────────────────┐
          │   Goal Engine   │
          └────────┬────────┘
                   │
        ┌──────────┼───────────┐
        ▼          ▼           ▼
   World State   Constraints   Policy
        │          │           │
        └──────────┼───────────┘
                   ▼
             GOAL DEFINITION
                   │
                   ▼
           SUCCESS CRITERIA
                   │
                   ▼
             FEASIBILITY
                   │
                   ▼
              GOAL READY
                   │
                   ▼
                PLANNER

⸻

73. Goal Completion Architecture

                GOAL
                  │
                  ▼
                PLAN
                  │
                  ▼
                ACTION
                  │
                  ▼
              EXECUTION
                  │
                  ▼
              OBSERVATION
                  │
                  ▼
             VERIFICATION
                  │
          ┌───────┴────────┐
          ▼                ▼
       SUCCESS           FAILURE
          │                │
          ▼                ▼
      ACHIEVED        Recovery / Retry

⸻

74. Fundamental Separation

Veda MUST maintain the following distinction:

Intent
"What do I want?"
Goal
"What future state satisfies it?"
Plan
"How can I achieve it?"
Process
"What sequence is currently being executed?"
Action
"What operation should occur?"
Event
"What happened?"
Verification
"Did the desired outcome actually happen?"

This separation allows Veda to change its strategy without losing the original objective.

⸻

75. Example: Building Veda

Suppose the user says:

"อยากให้ Veda ควบคุมคอมได้"

Intent:

Enable Veda to control a computer.

Goal:

goal:
  desired_state:
    veda_computer_control:
      enabled: true
  success_criteria:
    - veda_can_observe_desktop
    - veda_can_execute_authorized_actions
    - actions_are_logged
    - actions_require_appropriate_authorization
    - verification_is_available

Possible child Goals:

G1 — Desktop Observation
G2 — Input Control
G3 — Tool Registry
G4 — Authorization Layer
G5 — Action Audit
G6 — Verification
G7 — Recovery

Notice the difference:

The user did not explicitly request seven subsystems.

The Intent describes the desired capability.

The Goal Engine can derive the required structure while preserving traceability to the original Intent.

⸻

76. Core Architectural Principle

Veda MUST optimize for:

Goal Stability
+
Plan Flexibility
+
Action Accountability
+
Outcome Verification

not:

User Request
→ Fixed Procedure

This allows Veda to adapt when reality changes.

⸻

77. Final Principle

The Goal Model converts human desire into an objectively testable destination.

The canonical Veda chain becomes:

Human
  ↓
Input
  ↓
Intent
  ↓
Goal
  ↓
Desired World State
  ↓
Success Criteria
  ↓
Plan
  ↓
Authorization
  ↓
Action
  ↓
Event
  ↓
World Transition
  ↓
Verification
  ↓
Goal Outcome

The central invariant is:

GOAL ≠ TASK
GOAL ≠ PLAN
GOAL ≠ ACTION
GOAL ACHIEVED ≠ ACTION SUCCEEDED

A Goal is achieved only when the desired World state is actually established and sufficiently verified.

⸻

78. Status

Draft v0.1.0

Next RFC:

RFC-0007 — Process Model

RFC-0007 will define the runtime representation of work in progress: how a Goal becomes an active Process, how processes pause/resume/fail/recover, how they relate to Plans and Actions, and how Veda maintains execution state without confusing “currently doing something” with “the objective has been achieved.”
