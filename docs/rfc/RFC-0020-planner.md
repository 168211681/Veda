RFC-0020: Veda Planner

Status: Draft
Layer: 8 — Attention & Planning
Depends On: RFC-0002, RFC-0004, RFC-0005, RFC-0006, RFC-0007, RFC-0008, RFC-0009, RFC-0010, RFC-0013, RFC-0014, RFC-0017, RFC-0018, RFC-0019
Related: RFC-0021, RFC-0022, RFC-0023, RFC-0024, RFC-0025, RFC-0026, RFC-0027, RFC-0031

⸻

1. Abstract

RFC-0020 defines the Veda Planner.

The Planner transforms:

Goal
+
Current World
+
Constraints
+
Capabilities
+
Resources
+
Risks
+
Knowledge

into:

Plan
→
Steps
→
Dependencies
→
Expected Outcomes
→
Verification
→
Recovery Paths

The Planner does not execute actions.

It determines how a goal could be achieved.

The fundamental abstraction is:

Current World
      ↓
Goal
      ↓
Gap Analysis
      ↓
Candidate Plans
      ↓
Constraint Evaluation
      ↓
Dependency Resolution
      ↓
Risk Analysis
      ↓
Plan Selection
      ↓
Plan
      ↓
Execution / Verification

⸻

2. Motivation

Without an explicit Planner, an AI tends to operate as:

Think
↓
Do something
↓
See what happens
↓
Think again

This works for trivial tasks.

It fails badly when tasks involve:

* dependencies
* multiple steps
* resources
* deadlines
* risk
* irreversible actions
* parallel work
* failures
* alternative strategies
* long-running objectives

Veda therefore requires an explicit planning layer.

⸻

3. Design Goals

The Planner MUST:

1. transform goals into achievable plans
2. represent dependencies
3. represent prerequisites
4. represent resources
5. represent constraints
6. represent expected outcomes
7. represent risks
8. support alternative plans
9. support parallel execution
10. support sequential execution
11. support replanning
12. support partial progress
13. support failure recovery
14. support verification
15. support deadlines
16. support cost estimation
17. support reversibility analysis
18. support simulation
19. preserve provenance
20. remain subordinate to authorization.

⸻

4. Non-Goals

The Planner does NOT:

* grant authority
* execute arbitrary tools
* determine constitutional policy
* replace the Decision Engine
* replace the Brain
* replace Verification
* guarantee outcomes
* modify the world directly
* treat its own predictions as reality

⸻

5. Core Principle

A plan is a hypothesis about how to reach a goal.

Therefore:

Plan ≠ Reality

and:

Expected Outcome ≠ Actual Outcome

A plan must be continuously checked against the real world.

⸻

6. Planning Model

The basic model is:

Goal
 ↓
Desired State
 ↓
Current State
 ↓
State Difference
 ↓
Required Conditions
 ↓
Candidate Actions
 ↓
Dependencies
 ↓
Plan Graph
 ↓
Risk / Cost / Feasibility
 ↓
Candidate Plan
 ↓
Decision

⸻

7. Goal-to-Plan Transformation

Given:

Current World = W₀
Goal = G

the Planner attempts to construct:

Plan = P

such that:

Execute(P, W₀)
→
Wₙ

and:

GoalSatisfied(Wₙ, G) = true

subject to:

Constraints(P) = satisfied

and:

Authority(P) = permitted

⸻

8. Planning Gap

The Planner MUST identify the difference between current and desired state.

Example:

Current:
Veda server has no deployment.
Desired:
Veda server is running version 0.1.

Gap:

build
test
package
provision
deploy
start
verify

This gap becomes the basis for planning.

⸻

9. Plan Object

Conceptual schema:

plan:
  plan_id: string
  version: integer
  goal_ref: string
  world_snapshot_ref: string
  objective:
    description: string
    success_conditions: []
  assumptions: []
  constraints: []
  steps: []
  dependencies: []
  resources: []
  risks: []
  alternatives: []
  expected_outcomes: []
  verification_strategy: []
  recovery_strategy: []
  rollback_strategy: []
  estimated_cost:
    time: number
    compute: number
    money: number
    energy: number
  estimated_probability_of_success: number
  reversibility: string
  status: string
  created_by: string
  created_at: timestamp
  updated_at: timestamp

⸻

10. Plan States

DRAFT
   ↓
ANALYZING
   ↓
CANDIDATE
   ↓
EVALUATING
   ↓
SIMULATED
   ↓
PROPOSED
   ↓
APPROVED
   ↓
EXECUTING
   ↓
VERIFYING
   ↓
COMPLETED

Alternative states:

REJECTED
BLOCKED
PAUSED
FAILED
ABORTED
SUPERSEDED
REPLANNING

⸻

11. Plan Step

A plan consists of Steps.

Conceptual schema:

plan_step:
  step_id: string
  plan_ref: string
  objective: string
  action_ref: string
  prerequisites: []
  dependencies: []
  inputs: []
  expected_state_before: {}
  expected_state_after: {}
  expected_outcome: {}
  verification: []
  risk: string
  reversibility: string
  resources: []
  timeout: number
  retry_policy: {}
  fallback_steps: []
  status: string

⸻

12. Step ≠ Action

This distinction is critical.

Plan Step

describes:

What needs to happen.

while:

Action

describes:

A concrete operation that may be executed.

Example:

Plan Step:
"Verify deployment health."
Possible Actions:
- HTTP health request
- process inspection
- log inspection
- synthetic request

The Planner does not need to hard-code one execution method.

⸻

13. Dependencies

Steps MAY depend on other steps.

Example:

A: Write code
 ↓
B: Run tests
 ↓
C: Build
 ↓
D: Deploy
 ↓
E: Verify

Dependency graph:

A → B → C → D → E

A step MUST NOT execute before required dependencies are satisfied.

⸻

14. Parallel Planning

Independent steps MAY execute in parallel.

Example:

       ┌→ A ─┐
Start ─┤     ├→ D
       └→ B ─┘

If A and B have no dependency relationship:

A ∥ B

The Planner SHOULD exploit safe parallelism.

⸻

15. Dependency Types

Initial dependency types:

HARD
SOFT
RESOURCE
TEMPORAL
DATA
AUTHORITY
ENVIRONMENT
VERIFICATION

HARD

Must complete before continuation.

SOFT

Preferred but bypassable.

RESOURCE

Requires a shared resource.

TEMPORAL

Depends on time.

DATA

Requires output from another step.

AUTHORITY

Requires permission.

ENVIRONMENT

Requires world state.

VERIFICATION

Requires verified previous outcome.

⸻

16. Preconditions

Every important step SHOULD define preconditions.

Example:

preconditions:
  - repository_exists
  - branch_is_clean
  - tests_pass
  - deployment_credentials_available

If a precondition fails:

Step cannot proceed.

The Planner SHOULD replan rather than blindly execute.

⸻

17. Postconditions

Steps SHOULD define expected postconditions.

Example:

postconditions:
  - build_artifact_exists
  - artifact_hash_recorded

Postconditions become inputs to verification.

⸻

18. Goal Satisfaction

The Planner MUST define measurable success conditions where possible.

Bad:

"Make Veda better."

Better:

"Deploy Veda API version 0.1,
health endpoint returns HTTP 200,
database migration completes,
and error rate remains below threshold."

Goals should become testable.

⸻

19. Assumptions

Plans may depend on assumptions.

Example:

Assumption:
Server has enough disk space.

Assumptions MUST be explicit.

The Planner SHOULD classify them:

UNVERIFIED
SUPPORTED
VERIFIED
INVALIDATED

Invalidated assumptions SHOULD trigger replanning.

⸻

20. Constraints

Constraints may come from:

Constitution
Policy
User
Goal
Resources
Time
Privacy
Security
Hardware
Budget
Environment

The Planner MUST NOT treat constraints as optional suggestions.

⸻

21. Hard vs Soft Constraints

Hard Constraint

Cannot be violated.

Example:

Never expose private credentials.

Soft Constraint

May be violated only when policy permits.

Example:

Prefer local model over cloud model.

The Planner SHOULD distinguish these explicitly.

⸻

22. Resource Planning

Plans SHOULD estimate:

CPU
RAM
GPU
VRAM
storage
network
time
money
energy
human attention

Example:

resources:
  ram: 8GB
  gpu_vram: 6GB
  estimated_time: 15m
  cloud_cost: 0.04

⸻

23. Resource Conflicts

Two steps may require the same limited resource.

Example:

Step A → GPU 8GB
Step B → GPU 8GB
Available → GPU 10GB

The Planner MUST detect that:

A ∥ B

may be impossible.

Possible solutions:

A → B

or:

reduce resource requirements

or:

use alternative provider

⸻

24. Risk Model

Every significant plan SHOULD identify risks.

Conceptual structure:

risk:
  risk_id: string
  description: string
  probability: number
  impact: number
  severity: number
  affected_steps: []
  mitigation: []
  contingency: []
  rollback: []

⸻

25. Risk Severity

Conceptually:

Risk Severity =
Probability × Impact

High-risk plans SHOULD receive stronger evaluation.

⸻

26. Reversibility

The Planner MUST consider whether actions can be reversed.

Levels:

FULLY_REVERSIBLE
MOSTLY_REVERSIBLE
PARTIALLY_REVERSIBLE
DIFFICULT_TO_REVERSE
IRREVERSIBLE

Example:

Create temporary file
→ FULLY_REVERSIBLE
Deploy service
→ MOSTLY_REVERSIBLE
Delete production database
→ IRREVERSIBLE

Irreversible plans require stronger controls.

⸻

27. Recovery Strategy

Every significant plan SHOULD define what happens when a step fails.

Example:

Deploy
 ↓
FAIL
 ↓
Collect logs
 ↓
Rollback
 ↓
Verify rollback
 ↓
Diagnose
 ↓
Replan

Failure MUST NOT automatically imply retrying the exact same operation.

⸻

28. Retry Policy

Retry behavior MUST be explicit.

Possible policies:

NO_RETRY
FIXED_RETRY
EXPONENTIAL_BACKOFF
ALTERNATIVE_STRATEGY
PROVIDER_SWITCH
HUMAN_ESCALATION

Retry MUST consider idempotency.

⸻

29. Idempotency

Plans SHOULD identify whether steps are safe to repeat.

Example:

Read file
→ idempotent
Create unique database
→ potentially non-idempotent
Delete database
→ dangerous to retry

The Planner MUST NOT blindly retry non-idempotent operations.

⸻

30. Alternative Plans

The Planner SHOULD generate multiple candidate strategies for complex tasks.

Example:

Plan A:
Local deployment
Plan B:
Cloud deployment
Plan C:
Hybrid deployment

Each plan may have:

time
risk
quality
privacy
resource requirements
probability of success

The Decision Engine later selects among them.

⸻

31. Planner vs Decision Engine

This distinction is fundamental.

Planner:

"What ways can achieve the goal?"

Decision Engine:

"Which candidate is preferable under the rules?"

Therefore:

Planner → Candidates
Decision Engine → Selection

The Planner MUST NOT silently become the final decision authority.

⸻

32. Plan Quality

Plans MAY be evaluated using:

success probability
time
cost
risk
quality
resource usage
privacy
reversibility
complexity
human effort
maintenance burden

A plan that is technically possible is not necessarily a good plan.

⸻

33. Plan Dominance

If:

Plan A

is:

* no more expensive
* no slower
* no riskier
* equal or better quality
* equal or better privacy

than:

Plan B

then A may dominate B.

Dominated plans MAY be removed before decision-making.

⸻

34. Planning Heuristics

The Planner MAY use heuristics such as:

prefer verified capabilities
prefer reversible actions
prefer local resources when privacy matters
prefer simpler plans
prefer fewer dependencies
prefer known successful strategies
prefer lower operational risk
prefer plans with measurable verification

These are heuristics, not constitutional rules.

⸻

35. Hierarchical Planning

Large goals SHOULD be decomposed hierarchically.

Example:

Goal:
Build Veda
Project:
Core Runtime
Milestone:
World Model
Task:
Implement entity store
Action:
Create database schema

Relationship:

Life Goal
 ↓
Project
 ↓
Milestone
 ↓
Task
 ↓
Action

RFC-0006 defines the Goal hierarchy.

⸻

36. Recursive Planning

A plan step MAY itself contain a sub-plan.

Example:

Deploy Veda

may expand into:

Provision server
  ↓
Configure runtime
  ↓
Deploy service
  ↓
Verify health

This allows hierarchical decomposition.

⸻

37. Planning Depth

The Planner SHOULD avoid unnecessary decomposition.

A task should be expanded only when:

uncertainty
complexity
risk
dependency
resource allocation
verification

requires it.

Over-planning wastes computation.

Under-planning causes execution chaos.

⸻

38. Planning Horizon

Plans MAY have:

short horizon
medium horizon
long horizon

Long-horizon plans SHOULD use checkpoints.

Example:

Phase 1
 ↓
Verify
 ↓
Phase 2
 ↓
Verify
 ↓
Phase 3

The Planner SHOULD NOT assume the distant future remains identical to the current world.

⸻

39. Dynamic Replanning

A plan MUST be considered provisional.

If:

Expected World
≠
Observed World

then:

Replanning MAY be required.

Triggers include:

unexpected outcome
new evidence
goal change
resource change
policy change
dependency failure
risk increase
world state change

⸻

40. Replanning Loop

Plan
 ↓
Execute Step
 ↓
Observe
 ↓
Verify
 ↓
Compare Expected vs Actual
 ↓
┌───────────────┐
│               │
MATCH        MISMATCH
│               │
↓               ↓
Continue      Diagnose
                ↓
             Replan

This is essential for operating in the real world.

⸻

41. Closed-Loop Planning

Veda SHOULD use:

Plan
 ↓
Act
 ↓
Observe
 ↓
Verify
 ↓
Update World
 ↓
Replan

rather than:

Plan once
 ↓
Execute blindly

⸻

42. Planning Under Uncertainty

Plans SHOULD represent uncertainty.

Example:

Step:
Deploy service
Probability:
0.85
Expected:
service healthy
Alternative:
rollback

The Planner SHOULD support branches.

⸻

43. Conditional Plans

Example:

IF tests_pass
    deploy
ELSE
    diagnose_tests

This becomes a conditional plan graph.

⸻

44. Branching Plans

             Start
               |
          Run verification
          /             \
      PASS               FAIL
       |                  |
    Deploy             Diagnose
       |                  |
   Verify              Replan

Branch conditions MUST be explicit.

⸻

45. Temporal Planning

Some plans depend on time.

Example:

Wait until backup completes

or:

Execute deployment during maintenance window

Temporal semantics are expanded by RFC-0021.

⸻

46. Causal Planning

The Planner SHOULD distinguish:

Action that changes state

from:

Action that merely correlates with success

Causal reasoning will be defined more deeply in RFC-0022.

⸻

47. Simulation Integration

High-risk plans SHOULD be simulated before execution.

Plan
 ↓
Simulation
 ↓
Predicted Outcome
 ↓
Risk
 ↓
Decision

RFC-0024 defines the simulation layer.

⸻

48. Verification Strategy

Every significant plan SHOULD define:

What proves success?
What proves failure?
What must be observed?
What evidence is required?

Example:

verification:
  - health_endpoint == 200
  - error_rate < threshold
  - database_connection == healthy
  - deployment_version == expected

⸻

49. Plan Provenance

Every plan SHOULD record:

goal
world snapshot
knowledge
evidence
assumptions
planner version
reasoning source
provider models
constraints
policy version

This allows later auditing.

⸻

50. Plan Versioning

Plans MUST be versioned.

Example:

Plan P-100 v1
 ↓
world changed
 ↓
Plan P-100 v2

Previous versions MUST remain traceable.

⸻

51. Plan Invalidation

A plan MUST be invalidated when critical assumptions become false.

Example:

Plan assumes:
server available
Server destroyed
→ Plan invalid

The system MUST NOT continue blindly.

⸻

52. Human Constraints

User constraints MAY become plan constraints.

Example:

User:
"Do not use cloud services."

Planner:

Cloud provider candidates removed.

The Planner MUST preserve constraint provenance.

⸻

53. Human Approval Points

A plan SHOULD identify approval boundaries.

Example:

Step 1:
Build
Step 2:
Test
Step 3:
Deploy
[Human approval required]
Step 4:
Verify

Approval MUST be represented as part of the plan.

⸻

54. Authorization Boundary

The Planner MUST NOT interpret:

Plan contains action

as:

Action is authorized

Correct chain:

Plan
 ↓
Decision
 ↓
Authorization
 ↓
Action

⸻

55. Plan and Capability

The Planner MAY inspect available capabilities to determine feasibility.

Example:

Need:
Deploy Docker container
Capabilities:
Docker = available
Filesystem = available
Network = restricted

Planner may conclude:

Deployment plan feasible with network approval.

It does not grant that approval.

⸻

56. Plan and Resources

The Planner SHOULD cooperate with resource management.

If resources are insufficient:

Plan may:
reduce concurrency
choose another provider
defer work
split phases
request resources

⸻

57. Plan and Brain

The relationship is:

Brain
 ↓
Understand Goal
 ↓
Planner
 ↓
Candidate Plans
 ↓
Brain
 ↓
Evaluate / Critique

Brain provides cognition.

Planner provides structured plans.

⸻

58. Plan and Attention

Attention determines:

Which goal/problem deserves planning now?

Planner determines:

How can that goal be achieved?

⸻

59. Plan and Verification

The Planner defines expected outcomes.

Verification determines whether reality matches them.

Expected:
Service healthy
Observed:
Service unhealthy
→ Verification failure
→ Replanning

⸻

60. Plan Execution Interface

Conceptual interface:

create_plan(goal)
analyze_gap(plan)
decompose(plan)
generate_candidates(plan)
evaluate_feasibility(plan)
identify_dependencies(plan)
estimate_resources(plan)
estimate_risk(plan)
simulate(plan)
propose(plan)
pause(plan)
resume(plan)
replan(plan, world_delta)
invalidate(plan)
get_plan(plan_id)
get_plan_version(plan_id, version)
get_execution_state(plan_id)

⸻

61. Planning Events

The following events SHOULD exist:

PlanningRequested
PlanningStarted
GoalAnalyzed
GapIdentified
CandidatePlanCreated
CandidatePlanRejected
DependencyDiscovered
ResourceRequirementCalculated
RiskIdentified
PlanSimulated
PlanEvaluated
PlanSelected
PlanProposed
PlanApproved
PlanStarted
PlanPaused
PlanResumed
PlanStepStarted
PlanStepCompleted
PlanStepFailed
PlanVerificationFailed
PlanInvalidated
ReplanningStarted
PlanRevised
PlanCompleted
PlanAborted

These integrate with the audit system.

⸻

62. Example: Building Veda

Goal:

Deploy Veda Core v0.1.

Current state:

Code exists.
Tests incomplete.
Server available.
Docker available.
Database configured.

Planner creates:

1. Run static analysis
2. Run unit tests
3. Fix failures
4. Build container
5. Scan container
6. Push image
7. Deploy
8. Verify health
9. Run integration tests
10. Record deployment

Dependencies:

1 → 2
2 → 3
3 → 4
4 → 5
5 → 6
6 → 7
7 → 8
8 → 9
9 → 10

If step 2 fails:

Plan does not continue to deployment.

Instead:

Diagnose
→ Fix
→ Re-test
→ Continue

⸻

63. Example: Parallel Planning

Goal:

Prepare release.

Independent tasks:

A: Update documentation
B: Run security scan
C: Build release notes
D: Run tests

A, B, C, and D may run concurrently.

Then:

A
B
C
D
 ↓
Release approval

The Planner should exploit safe parallelism.

⸻

64. Example: Failure

Plan:

Deploy
 ↓
Verify

Observed:

HTTP 500

Planner:

Expected ≠ Actual

Then:

Stop
 ↓
Collect evidence
 ↓
Diagnose
 ↓
Determine rollback
 ↓
Rollback if authorized
 ↓
Verify
 ↓
Replan

⸻

65. Example: Irreversible Operation

Goal:

Remove obsolete production data.

Planner identifies:

Impact = High
Reversibility = Low
Risk = High

Plan includes:

1. Identify records
2. Verify scope
3. Create backup
4. Verify backup
5. Generate deletion proposal
6. Require authorization
7. Execute deletion
8. Verify deletion
9. Verify backup integrity

This is preferable to:

DELETE FROM database;

followed by a prayer to the software gods.

⸻

66. Plan Optimization

The Planner MAY optimize:

time
cost
risk
resource usage
quality
privacy
reliability
human effort

Optimization MUST remain constrained by hard policies.

Example:

Cheaper cloud provider

must not be selected if:

Privacy Policy = Local Only

⸻

67. Planning Under Resource Scarcity

When resources are limited, the Planner SHOULD rank plans by:

goal value
resource efficiency
risk
deadline
success probability

It MAY:

defer low-value tasks
reduce parallelism
use smaller models
split tasks
use deterministic tools
request additional resources

⸻

68. Planning Under Uncertainty

When important information is missing, the Planner SHOULD create information-gathering steps.

Example:

Unknown:
Which dependency caused failure?

Plan:

1. Inspect lockfile
2. Inspect build logs
3. Compare versions
4. Test candidate dependency
5. Determine cause
6. Continue deployment plan

The Planner does not need to pretend certainty where none exists.

⸻

69. Information-Gathering Plans

A plan may have the purpose of reducing uncertainty rather than directly achieving the final goal.

Goal:
Determine root cause.

This is a valid plan.

The result may be:

Knowledge gained

which enables a later plan.

⸻

70. Planning and Learning

Completed plans provide experience.

Plan
 ↓
Execution
 ↓
Outcome
 ↓
Reflection
 ↓
Learning
 ↓
Future Planning

The system SHOULD track:

predicted duration
actual duration
predicted cost
actual cost
predicted risk
actual risk
predicted success probability
actual outcome

This allows future planning to improve.

⸻

71. Plan Performance Metrics

Metrics MAY include:

plan_success_rate
step_success_rate
replanning_rate
average_plan_duration
prediction_error
resource_prediction_error
risk_prediction_error
rollback_rate
verification_failure_rate
human_intervention_rate

⸻

72. Planner Security

Threats include:

Malicious Plan Injection

External content attempts to insert actions.

Constraint Bypass

Planner attempts to reinterpret hard constraints.

Goal Hijacking

A plan gradually changes the original goal.

Risk Hiding

Planner understates risk to obtain approval.

Dependency Manipulation

Fake dependencies alter plan order.

Resource Exhaustion

Planner generates enormous plans.

Infinite Replanning

World changes trigger endless plan regeneration.

⸻

73. Goal Drift Protection

The Planner MUST preserve the original goal reference.

Every major plan step SHOULD be traceable to:

Goal
 ↓
Subgoal
 ↓
Plan Step

If a step cannot be justified against the goal or required constraints, it SHOULD be flagged.

⸻

74. Replanning Limits

Replanning MUST be bounded.

Example:

replanning:
  max_iterations: 5
  max_time: 10m
  max_cost: 0.20

If exceeded:

ESCALATE

or:

ABORT

according to policy.

⸻

75. Plan Compression

Large plans MAY be represented hierarchically.

Instead of storing:

10,000 individual steps

Veda MAY store:

Release Veda
 ├── Build
 ├── Test
 ├── Security
 ├── Deploy
 └── Verify

with expandable subplans.

⸻

76. Plan Checkpoints

Long plans SHOULD define checkpoints.

Checkpoint 1:
Build verified
Checkpoint 2:
Deployment verified
Checkpoint 3:
Integration verified

At each checkpoint:

Expected World
vs
Actual World

is compared.

⸻

77. Plan Snapshot

The Planner SHOULD maintain snapshots containing:

world_snapshot
goal_version
plan_version
constraints
assumptions
resource_state
risk_state
execution_state

This enables reliable resumption and auditing.

⸻

78. Planner Invariants

PLAN-1

A plan MUST reference a goal.

PLAN-2

A plan MUST NOT be treated as reality.

PLAN-3

Expected outcomes MUST be distinguishable from actual outcomes.

PLAN-4

Plan steps MUST preserve dependencies.

PLAN-5

Critical preconditions MUST be explicit.

PLAN-6

Important postconditions SHOULD be explicit.

PLAN-7

Plans MUST respect hard constraints.

PLAN-8

Plans MUST NOT grant authority.

PLAN-9

Plans MUST NOT directly bypass authorization.

PLAN-10

Plans SHOULD represent risks.

PLAN-11

Plans SHOULD represent reversibility.

PLAN-12

Plans SHOULD represent recovery paths.

PLAN-13

Non-idempotent actions MUST NOT be blindly retried.

PLAN-14

Plans SHOULD support alternative strategies.

PLAN-15

Plans SHOULD support safe parallelism.

PLAN-16

Resource conflicts MUST be detected.

PLAN-17

Invalidated assumptions MUST trigger re-evaluation.

PLAN-18

Plans MUST be versioned.

PLAN-19

Plan revisions MUST remain traceable.

PLAN-20

Plan execution MUST be closed-loop.

PLAN-21

Observed world state MUST override stale plan assumptions.

PLAN-22

Verification failure MUST be capable of triggering replanning.

PLAN-23

Replanning MUST be bounded.

PLAN-24

Long-running plans SHOULD use checkpoints.

PLAN-25

Plans SHOULD expose expected success conditions.

PLAN-26

Plans SHOULD expose verification strategies.

PLAN-27

Plans SHOULD preserve provenance.

PLAN-28

Goal drift MUST be detectable.

PLAN-29

Planning MUST NOT become autonomous authority.

PLAN-30

Human approval requirements MUST be represented explicitly.

⸻

79. Relationship to the Veda Architecture

The resulting chain is:

┌───────────────┐
│     WORLD     │
└───────┬───────┘
        ↓
     EVENTS
        ↓
┌───────────────┐
│   ATTENTION   │
└───────┬───────┘
        ↓
┌───────────────┐
│     BRAIN     │
└───────┬───────┘
        ↓
     GOAL
        ↓
┌───────────────┐
│    PLANNER    │
└───────┬───────┘
        ↓
 CANDIDATE PLANS
        ↓
┌───────────────┐
│    DECISION   │
└───────┬───────┘
        ↓
 AUTHORIZATION
        ↓
     ACTION
        ↓
  VERIFICATION
        ↓
      WORLD

⸻

80. Fundamental Distinctions

Veda MUST preserve:

Goal ≠ Plan
Plan ≠ Action
Plan Step ≠ Action
Plan ≠ Decision
Decision ≠ Authorization
Authorization ≠ Execution
Expected Outcome ≠ Actual Outcome
Prediction ≠ Reality
Plan Success ≠ Goal Success

These distinctions prevent the Planner from becoming an uncontrolled autonomous execution engine.

⸻

81. Final Principle

The Veda Planner transforms goals into explicit, constrained, testable, risk-aware plans while remaining subordinate to decision, authorization, execution, and verification.

The complete planning loop is:

GOAL
  ↓
CURRENT WORLD
  ↓
GAP
  ↓
PLAN
  ↓
DEPENDENCIES
  ↓
RESOURCES
  ↓
RISKS
  ↓
SIMULATION
  ↓
DECISION
  ↓
AUTHORIZATION
  ↓
ACTION
  ↓
OBSERVATION
  ↓
VERIFICATION
  ↓
ACTUAL WORLD
  ↓
REPLAN

A plan is never allowed to outrank reality.