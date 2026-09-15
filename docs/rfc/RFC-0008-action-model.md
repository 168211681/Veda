RFC-0008: Action Model

Status: Draft
Version: 0.1.0
Layer: Layer 3 — Agency
Module: Action Execution
Path: docs/rfc/RFC-0008-action-model.md

⸻

1. Abstract

RFC-0008 defines the Action Model of Veda.

An Action represents a single executable operation that may produce an observable effect on the World, an external system, a resource, another agent, or Veda itself.

The Action Model defines:

* what an Action is;
* how Actions differ from Goals and Processes;
* Action structure and lifecycle;
* execution preconditions;
* capability binding;
* authorization requirements;
* risk classification;
* simulation and preflight validation;
* execution;
* observation;
* verification;
* rollback and compensation;
* idempotency;
* concurrency;
* timeout and retry behavior;
* evidence and auditability;
* failure handling;
* security boundaries.

The central principle is:

An Action is an executable claim about how Veda intends to change or observe reality.

An Action MUST NOT be treated as successful merely because its underlying command completed successfully.

⸻

2. Motivation

Veda operates across multiple abstraction levels.

A user may express:

“จัดการโปรเจกต์ Veda ให้เป็นระเบียบ”

This may become:

Intent
  ↓
Goal
  ↓
Plan
  ↓
Process
  ↓
Action

For example:

Goal:
Organize the Veda repository.
Process:
Repository maintenance workflow.
Action:
Create docs/rfc directory.
Action:
Move RFC document.
Action:
Update README.
Action:
Commit changes.
Action:
Push commit.

The Process describes ongoing work.

The Action describes the concrete operation.

Therefore:

Goal    = desired future state
Plan    = strategy for reaching the goal
Process = active work pursuing the plan
Action  = executable operation
Event   = recorded fact about what happened

These concepts MUST NOT be conflated.

⸻

3. Design Principles

The Action Model follows these principles.

ACT-P1 — Action Is Not Authority

An Action represents an intended operation.

It does not grant permission to execute that operation.

Action ≠ Authorization

⸻

ACT-P2 — Intelligence Is Not Authority

A model may generate an Action.

The model MUST NOT authorize itself.

Model Output
    ↓
Action Proposal
    ↓
Authorization
    ↓
Execution

⸻

ACT-P3 — Every Significant Action Is Traceable

A significant Action MUST be traceable to:

Intent
→ Goal
→ Process
→ Action
→ Authorization
→ Event
→ Verification
→ Outcome

⸻

ACT-P4 — Execution Success Is Not Goal Success

A command can succeed while the desired outcome fails.

Example:

Command:
delete_file()
Execution:
SUCCESS
Actual World:
wrong file deleted

Therefore:

Execution Success ≠ Outcome Success

Verification is mandatory for Actions whose outcome matters.

⸻

ACT-P5 — Actions Must Be Bounded

Every Action SHOULD have explicit boundaries for:

* target;
* operation;
* parameters;
* capability;
* authority;
* scope;
* time;
* resources;
* risk.

⸻

ACT-P6 — Actions Must Be Observable

Veda MUST be able to determine:

* what Action was attempted;
* by whom;
* through which capability;
* against which target;
* with which parameters;
* what happened;
* what evidence exists;
* whether the expected outcome occurred.

⸻

ACT-P7 — Irreversible Actions Require Stronger Control

Actions with irreversible or difficult-to-reverse consequences MUST receive stronger authorization and verification.

⸻

4. Action Definition

An Action is a bounded executable operation that Veda may perform against a defined target.

Formally:

Action =
Actor
+ Executor
+ Capability
+ Target
+ Operation
+ Parameters
+ Preconditions
+ Expected Outcome
+ Authority
+ Verification
+ Recovery

An Action may:

* observe;
* read;
* create;
* modify;
* delete;
* execute;
* communicate;
* transfer;
* configure;
* invoke;
* control;
* transform;
* synchronize;
* request;
* delegate.

⸻

5. Action vs Related Concepts

Concept	Meaning
Intent	What the actor wants
Goal	What future state should become true
Plan	How the goal may be achieved
Process	Active work pursuing the plan
Action	Concrete executable operation
Event	Recorded fact about something that happened
Outcome	Actual resulting state
Verification	Evidence that determines whether the expected result occurred

Example:

Intent:
"Make the repository clean."
Goal:
Repository contains no unintended changes.
Process:
Repository cleanup.
Action:
Run repository status inspection.
Event:
Inspection completed.
Verification:
Expected repository state confirmed.
Outcome:
Repository is clean.

⸻

6. Action Object

A canonical Action SHOULD contain:

action_id:
version:
actor:
executor:
parent_process:
parent_goal:
parent_intent:
action_type:
capability_ref:
target:
operation:
parameters:
context:
scope:
preconditions:
expected_outcome:
postconditions:
risk:
reversibility:
authorization_ref:
idempotency_key:
transaction_ref:
timeout:
retry_policy:
verification_plan:
rollback_plan:
evidence:
status:
created_at:
authorized_at:
started_at:
completed_at:
verified_at:

⸻

7. Identity

Every Action MUST have a globally unique:

action_id

Example:

act_01JVEDA...

The identifier MUST remain stable throughout the Action lifecycle.

Retries MUST NOT silently create a new logical Action.

Instead:

Action
 ├── Attempt 1
 ├── Attempt 2
 └── Attempt 3

Each attempt MUST be separately observable.

⸻

8. Actor

actor identifies who requested or originated the Action.

Possible actors include:

human
veda
agent
external_system
scheduled_process
trusted_service

Example:

actor:
  type: human
  id: user_primary

The actor is not necessarily the executor.

⸻

9. Executor

executor identifies the component that actually performs the Action.

Example:

Actor:
Veda
Executor:
filesystem-agent

Or:

Actor:
Human
Executor:
Veda

This distinction is necessary for accountability.

⸻

10. Parent References

An Action SHOULD reference its originating context:

parent_intent:
parent_goal:
parent_process:

This provides causal traceability.

Example:

Intent I-001
    ↓
Goal G-001
    ↓
Process P-001
    ↓
Action A-001

An Action without a legitimate parent context MAY exist for:

* system maintenance;
* emergency recovery;
* monitoring;
* infrastructure health;
* explicit human commands.

Such Actions MUST still have a valid authority context.

⸻

11. Action Types

Veda SHOULD support a standardized Action type taxonomy.

11.1 Observe

Observe external or internal state.

Examples:

read sensor
inspect filesystem
check process state
query database
inspect Git status

⸻

11.2 Read

Retrieve information without intentional state mutation.

Examples:

read file
query database
retrieve document
read configuration

⸻

11.3 Create

Create a new resource.

Examples:

create file
create database record
create repository branch
create knowledge object

⸻

11.4 Update

Modify an existing resource.

Examples:

edit file
update database record
modify configuration
update world state

⸻

11.5 Delete

Remove a resource.

Examples:

delete file
remove database record
delete temporary resource

Delete Actions SHOULD have elevated risk classification when recovery is difficult.

⸻

11.6 Execute

Execute a program, command, workflow, or operation.

Examples:

run test
execute build
run script
start service

⸻

11.7 Communicate

Send information to another system or actor.

Examples:

send notification
send email
post message
submit API request

⸻

11.8 Transfer

Move ownership, value, or resources.

Examples:

move file
transfer resource
transfer funds
transfer authorization

Transfer Actions SHOULD receive elevated authorization requirements.

⸻

11.9 Configure

Change configuration or system behavior.

Examples:

change service configuration
modify environment
change system settings

⸻

11.10 Invoke

Call another capability, service, model, or agent.

Examples:

invoke language model
invoke browser capability
invoke external API
invoke specialist agent

⸻

12. Target

Every Action MUST define its target whenever applicable.

Examples:

target:
  type: filesystem
  resource: "/project/file.txt"

or:

target:
  type: github_repository
  resource: "veda"

Targets MUST be resolved before execution.

Ambiguous targets MUST NOT be silently guessed when the consequences are significant.

⸻

13. Operation

operation specifies exactly what the executor is expected to perform.

Example:

operation: filesystem.write

The operation MUST be compatible with the referenced capability.

A capability providing:

filesystem.read

MUST NOT automatically grant:

filesystem.delete

⸻

14. Parameters

Parameters contain the concrete inputs required for execution.

Example:

parameters:
  path: "/veda/docs/rfc/RFC-0008-action-model.md"
  encoding: "utf-8"

Parameters MUST be validated before execution.

Sensitive parameters SHOULD be redacted from ordinary logs while remaining available to authorized audit systems when necessary.

⸻

15. Scope

Every Action SHOULD define an explicit scope.

Examples:

scope:
  filesystem:
    root: "/veda"

or:

scope:
  repositories:
    allowed:
      - "veda"

An Action MUST NOT silently expand its scope during execution.

Scope expansion requires a new authorization decision.

⸻

16. Preconditions

Preconditions define what MUST be true before execution.

Example:

Repository exists.
File exists.
No conflicting write is active.
Capability is available.
Authorization is valid.
Target matches expected identity.

Execution MUST NOT begin if mandatory preconditions fail.

⸻

17. Expected Outcome

Every meaningful Action SHOULD define an expected outcome.

Example:

expected_outcome:
  type: file_created
  target: "/veda/test.txt"

This allows verification to distinguish:

"command completed"

from:

"desired effect actually occurred"

⸻

18. Postconditions

Postconditions define the expected resulting state.

Example:

File exists.
File contains expected content.
Permissions remain within policy.
No unrelated files changed.

Postconditions are evaluated by the Verification Engine.

⸻

19. Capability Binding

An Action MUST reference the capability required to execute it.

capability_ref:
  capability_id: filesystem.write

The Action MUST NOT directly bypass the Capability Fabric.

Execution path:

Action
  ↓
Capability Registry
  ↓
Capability
  ↓
Executor
  ↓
External World

This provides a single enforcement point.

⸻

20. Authorization Binding

An Action MUST reference the authorization decision that permits execution.

authorization_ref:
  authorization_id: auth_...

Authorization MUST be evaluated independently from Action generation.

The following MUST NOT be valid:

AI generated Action
→ AI declares itself authorized
→ execute

Instead:

AI generated Action
→ Policy Engine
→ Authorization Decision
→ Capability Check
→ Execution

⸻

21. Risk Classification

Every Action SHOULD have a risk classification.

Suggested levels:

R0 — Observation
R1 — Low-impact reversible
R2 — Moderate-impact reversible
R3 — High-impact
R4 — Irreversible or sensitive
R5 — Critical

Examples:

Action	Risk
Read public file	R0
Create temporary file	R1
Modify project file	R2
Push repository change	R3
Delete important data	R4
Critical external transfer	R5

Risk classification MUST consider actual consequences rather than merely the command name.

⸻

22. Reversibility

Actions SHOULD declare whether they are:

reversible
partially_reversible
compensatable
irreversible
unknown

Example:

Create file
→ reversible
Database update
→ potentially reversible
External message
→ compensatable but not fully reversible
Permanent deletion
→ potentially irreversible

Unknown reversibility MUST NOT be treated as safe reversibility.

⸻

23. Idempotency

Actions with external side effects SHOULD support idempotency.

Example:

idempotency_key: idem_12345

If the same Action is retried after an uncertain network failure, Veda MUST determine whether the original operation already occurred before performing it again.

This prevents:

request
→ timeout
→ retry
→ duplicate effect

⸻

24. Transaction Reference

Where supported, Actions SHOULD belong to a transaction.

transaction_ref:
  transaction_id: tx_001

A transaction MAY contain:

Action A
Action B
Action C

The transaction defines consistency requirements.

Not every external Action can be truly atomic.

Therefore Veda MUST distinguish:

atomic rollback

from:

compensating action

⸻

25. Atomicity

Where an Action can be executed atomically, the executor SHOULD guarantee:

all effect
OR
no effect

Where atomicity is impossible, the Action MUST declare that limitation.

Example:

External API call

may succeed externally while Veda loses network connectivity before receiving the response.

This creates:

Unknown Outcome

rather than automatically:

Failure

⸻

26. Action State Machine

The canonical Action lifecycle is:

PROPOSED
    ↓
PRECHECK
    ↓
AUTHORIZATION_PENDING
    ↓
AUTHORIZED
    ↓
SIMULATING
    ↓
APPROVED
    ↓
EXECUTING
    ↓
OBSERVING
    ↓
VERIFYING
    ↓
COMMITTED

Alternative states:

REJECTED
BLOCKED
CANCELLED
EXPIRED
FAILED
ROLLING_BACK
ROLLED_BACK
COMPENSATING
COMPENSATED
UNKNOWN_OUTCOME

⸻

27. State Semantics

PROPOSED

Action has been generated but is not executable.

⸻

PRECHECK

Veda validates:

* schema;
* target;
* capability;
* parameters;
* preconditions;
* scope;
* resource availability;
* policy requirements.

⸻

AUTHORIZATION_PENDING

Authorization is required but has not yet been granted.

⸻

AUTHORIZED

A valid authorization decision exists.

Authorization MUST still be valid at execution time.

⸻

SIMULATING

The Action is evaluated against the current World or an isolated simulation environment.

⸻

APPROVED

The Action passed required simulation and control checks.

⸻

EXECUTING

The executor is performing the Action.

⸻

OBSERVING

Veda collects execution results and relevant external state.

⸻

VERIFYING

The Verification Engine determines whether expected postconditions occurred.

⸻

COMMITTED

The Action’s verified result has been accepted into the World.

⸻

28. Failure State

Failure MUST NOT be represented by a single generic state when more information exists.

Possible categories:

VALIDATION_FAILURE
AUTHORIZATION_FAILURE
CAPABILITY_FAILURE
PRECONDITION_FAILURE
EXECUTION_FAILURE
TIMEOUT
RESOURCE_FAILURE
CONCURRENCY_CONFLICT
VERIFICATION_FAILURE
ROLLBACK_FAILURE
UNKNOWN_OUTCOME

Example:

Action executes successfully
↓
Expected file not found
↓
VERIFICATION_FAILURE

⸻

29. Unknown Outcome

An Action MUST support an UNKNOWN_OUTCOME state.

This occurs when Veda cannot determine whether the external effect happened.

Example:

External request sent
↓
Network connection lost
↓
No response received

Veda MUST NOT blindly retry potentially non-idempotent Actions.

Instead:

UNKNOWN_OUTCOME
→ reconcile external state
→ determine actual outcome
→ continue / compensate / retry

⸻

30. Simulation

High-risk Actions SHOULD support simulation before execution.

Action
  ↓
Simulation
  ↓
Predicted World
  ↓
Risk Analysis
  ↓
Authorization / Approval
  ↓
Execution

Simulation MUST NOT be treated as proof that reality will behave identically.

It is a prediction.

Therefore:

Simulation ≠ Reality

⸻

31. Dry Run

Where supported, Veda SHOULD execute a dry-run mode.

Example:

What files would be changed?
What database records would be affected?
What resources would be consumed?

Dry-run results SHOULD be attached to the Action evidence.

⸻

32. Concurrency

Multiple Actions may operate on the same resource.

Example:

Action A → edit file
Action B → edit same file

Veda MUST detect or prevent unsafe concurrent modification.

Possible strategies:

optimistic concurrency
locking
leases
version checks
conflict detection
serialization
human review

⸻

33. Version Preconditions

An Action MAY require a target version.

Example:

preconditions:
  target_version: 42

If the actual target version is:

43

the Action MUST be considered stale.

Veda MUST NOT blindly apply stale Actions to changed state.

⸻

34. Timeout

Actions SHOULD define a timeout.

timeout:
  duration: 30s

Timeout behavior MUST be explicitly defined.

A timeout does not necessarily mean the external Action failed.

It may produce:

UNKNOWN_OUTCOME

when external execution cannot be determined.

⸻

35. Retry Policy

Retries MUST be policy-driven.

A retry policy SHOULD define:

maximum_attempts
backoff
retryable_errors
non_retryable_errors
idempotency_requirement

Unsafe retries MUST be prohibited.

Example:

Read operation
→ usually safe to retry
Non-idempotent external transfer
→ retry requires reconciliation

⸻

36. Rollback

If an Action is reversible, Veda SHOULD execute rollback when verification fails and policy permits it.

Action
 ↓
Execution
 ↓
Verification Failure
 ↓
Rollback
 ↓
Verification

Rollback itself is an Action and MUST be authorized.

An Action MUST NOT gain unlimited authority merely because it is attempting recovery.

⸻

37. Compensation

When rollback is impossible, Veda MAY execute a compensating Action.

Example:

Action A:
Create external resource
Compensation:
Request external resource deletion

Compensation does not guarantee perfect restoration.

The resulting state MUST be verified.

⸻

38. Cancellation

An Action MAY be cancelled before execution.

Once execution begins, cancellation depends on executor capabilities.

States:

Cancellable
Non-cancellable
Cancellation requested
Cancellation confirmed
Cancellation failed

Veda MUST NOT claim an Action was cancelled merely because a cancellation request was sent.

⸻

39. Resource Management

Actions SHOULD declare expected resources:

CPU
RAM
storage
network
tokens
time
money
external quotas

The planner and process manager MAY use this information for scheduling.

Resource limits MUST be enforced independently from model intent.

⸻

40. Security Boundary

The Action Model is a critical security boundary.

Every Action MUST be evaluated against:

Identity
Capability
Scope
Authorization
Policy
Risk
Target
Parameters
Context

An Action MUST NOT:

* escalate its own privileges;
* modify its own authorization;
* expand its scope;
* bypass policy;
* disable auditing;
* delete evidence of its own execution;
* modify constitutional rules;
* grant itself new capabilities.

⸻

41. Self-Modification

Actions that modify Veda itself require additional controls.

Examples:

modify policy
modify authorization system
modify Action executor
modify audit system
modify Constitution
modify core security

These Actions SHOULD require:

high-risk classification
simulation
independent verification
human authorization
immutable audit trail
rollback strategy

Core constitutional changes MUST NOT be autonomously authorized by Veda.

⸻

42. Sensitive Actions

Sensitive Actions MAY include:

financial operations
identity changes
credential operations
private data access
external communications
system administration
security configuration
irreversible deletion

These SHOULD require stronger authorization.

The exact policy is defined by RFC-0010 Authorization & Policy.

⸻

43. Evidence

Every meaningful Action SHOULD generate evidence.

Possible evidence:

command output
exit code
file hash
database version
API response
screenshot
sensor reading
system state
verification result
external confirmation

Evidence SHOULD include provenance.

Example:

evidence:
  source: filesystem
  timestamp: ...
  hash: ...
  collector: ...

⸻

44. Auditability

The following MUST be auditable:

Action creation
Action modification
Authorization decision
Execution start
Execution attempts
Execution result
Verification
Rollback
Compensation
Final outcome

Audit records MUST NOT depend solely on the Action executor itself.

Otherwise a compromised executor could simply report:

"Everything went perfectly."

and reality would remain unimpressed.

⸻

45. Verification

Verification MUST evaluate actual state against expected state.

Example:

Expected:
file exists
Observed:
file exists
Result:
PASS

More complex verification may evaluate:

expected_state
vs
observed_state

Verification SHOULD produce:

PASS
PARTIAL
FAIL
UNKNOWN

⸻

46. Action Result

An Action Result SHOULD contain:

action_id:
execution_status:
verification_status:
actual_outcome:
evidence:
side_effects:
errors:
resource_usage:
rollback_status:
final_state:
timestamp:

The result describes what happened.

It does not automatically determine whether the parent Goal was achieved.

⸻

47. Action vs Goal Outcome

Example:

Goal:
Deploy application successfully.
Action:
Run deployment command.
Action result:
Command exited with code 0.
Verification:
Deployment health check failed.
Therefore:
Action execution = successful
Action outcome = failed
Goal = not achieved

This distinction is mandatory.

⸻

48. Action Events

The Action Model SHOULD emit events including:

ActionProposed
ActionValidated
ActionPrecheckStarted
ActionPrecheckCompleted
ActionAuthorizationRequested
ActionAuthorized
ActionAuthorizationRejected
ActionSimulationStarted
ActionSimulationCompleted
ActionApproved
ActionStarted
ActionAttemptStarted
ActionAttemptCompleted
ActionObserved
ActionVerificationStarted
ActionVerificationCompleted
ActionCommitted
ActionFailed
ActionCancelled
ActionExpired
ActionRollbackStarted
ActionRollbackCompleted
ActionCompensationStarted
ActionCompensationCompleted
ActionOutcomeUnknown

Events become part of the Veda Chronicle defined by RFC-0031.

⸻

49. Event-Sourced Action State

Action state SHOULD be reconstructible from its event history.

Conceptually:

ActionState(t)
=
Reduce(ActionEvents[0..t])

This allows:

* deterministic replay;
* debugging;
* audit;
* recovery;
* historical analysis;
* failure diagnosis.

⸻

50. Immutability

After execution begins, the semantic definition of an Action MUST NOT be silently modified.

If a meaningful change is required:

Action A
    ↓
Superseded
    ↓
Action B

The relationship MUST be recorded.

This prevents history from being rewritten after the fact.

⸻

51. Action Attempts

One logical Action MAY have multiple execution attempts.

Action A
 ├── Attempt 1 → timeout
 ├── Attempt 2 → transient error
 └── Attempt 3 → success

Each attempt MUST contain:

attempt_id
start_time
executor
parameters_hash
result
evidence
error

⸻

52. Executor Isolation

Where practical, Action executors SHOULD operate with least privilege.

Example:

filesystem executor
→ only project directory
database executor
→ only permitted database
network executor
→ only approved destinations

The executor MUST NOT receive broader privileges merely because the parent Action was generated by a highly capable model.

⸻

53. External World Actions

Actions against external systems SHOULD account for:

latency
partial failure
rate limits
network failure
authentication expiry
remote state changes
unknown outcomes
external side effects

External systems are not guaranteed to behave like deterministic software components.

Veda MUST treat them as partially observable environments.

⸻

54. Human Approval

Certain Actions MAY require explicit human approval.

Example:

Action
→ risk assessment
→ approval request
→ human decision
→ authorization event
→ execution

The approval MUST include sufficient information to understand:

what will happen
where
why
with what authority
risk
expected effect
rollback options

Human approval MUST be auditable.

⸻

55. Approval Expiration

Authorization SHOULD have an expiration time.

An expired authorization MUST NOT be reused automatically.

Example:

Authorization:
valid until 12:00
Current time:
12:03
Result:
INVALID

A new authorization decision is required.

⸻

56. Capability Revocation

If the referenced capability is revoked after authorization but before execution:

Action
→ capability revoked
→ execution blocked

Authorization alone MUST NOT override capability revocation.

⸻

57. Action Ordering

Actions with dependencies MUST preserve dependency order.

Example:

A: create database
B: write schema
C: insert data

Required:

A → B → C

Veda MUST NOT reorder Actions merely for performance when doing so violates dependency constraints.

⸻

58. Parallel Execution

Independent Actions MAY execute concurrently.

Example:

A → inspect file
B → inspect database
C → inspect system metrics

If:

A ⟂ B
A ⟂ C
B ⟂ C

they may run in parallel.

If they share conflicting state:

A ↔ B

the scheduler MUST enforce appropriate synchronization.

⸻

59. Action Dependencies

An Action MAY declare:

depends_on:
  - action_001
  - action_002

Dependency completion MUST be verified before execution when required.

⸻

60. Action Priority

Actions MAY have priority.

Example:

critical recovery
security event
user request
maintenance
background optimization

Priority MUST NOT override authorization or safety policy.

A low-priority authorized Action cannot be made safe merely by waiting.

A high-priority Action cannot become authorized merely by being urgent.

⸻

61. Emergency Actions

Veda MAY define emergency recovery Actions.

However:

Emergency ≠ unlimited authority

Emergency policies MUST be predefined and auditable.

Emergency Actions MUST still produce an audit trail.

⸻

62. Action Handoff

An Action MAY be handed from one executor to another.

Example:

Agent A
  ↓
handoff
  ↓
Agent B

The handoff MUST preserve:

action_id
authorization
scope
parameters
risk
verification requirements
audit history

The new executor MUST NOT inherit authority beyond the original Action.

⸻

63. Action Delegation

Delegating an Action does not automatically delegate the entire authority of the parent actor.

Example:

Veda
→ delegates filesystem inspection
→ Agent A

Agent A receives only:

filesystem.read
scope=/veda

not:

full system control

⸻

64. Action Composition

Complex operations MAY be represented as Action graphs.

Action A
   ↓
Action B ──→ Action C
   ↓
Action D

However, the composition MUST preserve individual Action identity and auditability.

A compound Action MUST NOT become an excuse to hide untracked sub-actions.

⸻

65. Action Batching

Independent low-risk Actions MAY be batched for efficiency.

Example:

read file A
read file B
read file C

The batch MUST preserve:

* individual target identity;
* individual outcomes;
* error isolation;
* auditability.

⸻

66. Action Policy Evaluation

Before execution, Veda SHOULD evaluate:

Identity
↓
Capability
↓
Scope
↓
Authorization
↓
Policy
↓
Risk
↓
Preconditions
↓
Simulation
↓
Execution

The Action executor MUST NOT skip this pipeline.

⸻

67. Action Security Invariants

The following invariants MUST hold.

ACT-1

Every Action MUST have a unique Action ID.

ACT-2

An Action MUST NOT authorize itself.

ACT-3

Action generation MUST NOT grant authority.

ACT-4

Every executable Action MUST reference a valid capability.

ACT-5

Every privileged Action MUST have valid authorization.

ACT-6

Action scope MUST be explicit or safely bounded.

ACT-7

An Action MUST NOT expand its own scope.

ACT-8

Execution success MUST NOT automatically imply outcome success.

ACT-9

Meaningful Actions MUST be verifiable.

ACT-10

Unknown external outcomes MUST NOT be treated as confirmed failure.

ACT-11

Unsafe retries MUST be prohibited.

ACT-12

Irreversible Actions MUST receive stronger controls.

ACT-13

Action execution MUST be auditable.

ACT-14

Execution MUST NOT bypass the Capability Fabric.

ACT-15

Authorization MUST be evaluated independently from intelligence generation.

ACT-16

Expired authorization MUST NOT be reused.

ACT-17

Revoked capabilities MUST block dependent Actions.

ACT-18

Action history MUST NOT be silently rewritten.

ACT-19

Rollback and compensation Actions MUST themselves be authorized.

ACT-20

An Action MUST NOT modify the Constitution without the required governance process.

ACT-21

A child Action MUST NOT inherit broader authority than its parent authorization permits.

ACT-22

Action parameters MUST be validated before execution.

ACT-23

Stale Actions MUST NOT blindly modify changed state.

ACT-24

Execution state MUST be reconstructible from authoritative events or equivalent durable records.

ACT-25

Veda MUST distinguish execution result, verified outcome, and parent Goal outcome.

⸻

68. Reference Execution Pipeline

The canonical execution pipeline is:

Intent
   ↓
Goal
   ↓
Plan
   ↓
Process
   ↓
Action Proposal
   ↓
Target Resolution
   ↓
Parameter Validation
   ↓
Capability Binding
   ↓
Precondition Check
   ↓
Risk Assessment
   ↓
Authorization
   ↓
Simulation / Dry Run
   ↓
Approval
   ↓
Execution
   ↓
Observation
   ↓
Verification
   ↓
Commit
   ↓
World Transition
   ↓
Goal Evaluation

⸻

69. Failure Pipeline

When verification fails:

EXECUTING
    ↓
OBSERVING
    ↓
VERIFYING
    ↓
FAIL
    ↓
DIAGNOSE
    ↓
┌───────────────┬───────────────┐
│               │               │
ROLLBACK     COMPENSATE       RETRY
│               │               │
└───────────────┴───────────────┘
                ↓
             VERIFY
                ↓
        COMMIT / ABORT

If outcome cannot be determined:

VERIFY
  ↓
UNKNOWN
  ↓
RECONCILIATION
  ↓
ACTUAL OUTCOME

⸻

70. Example: Filesystem Action

action_id: act_001
action_type: create
actor: veda
executor: filesystem_executor
capability_ref: filesystem.write
target:
  type: file
  path: /veda/test.txt
operation: filesystem.create
parameters:
  content: "hello"
scope:
  root: /veda
preconditions:
  - parent_directory_exists
expected_outcome:
  file_exists: true
risk:
  level: R1
  reversibility: reversible
verification_plan:
  - verify_file_exists
  - verify_content_hash
status: PROPOSED

Execution:

PROPOSED
→ PRECHECK
→ AUTHORIZED
→ EXECUTING
→ OBSERVING
→ VERIFYING
→ COMMITTED

⸻

71. Example: Git Repository Action

A repository update may contain:

Action:
git.commit

But a successful commit does not prove:

repository is correct

Verification may include:

commit exists
working tree state matches expectation
tests pass
no unintended files changed

A subsequent push is a separate Action:

Action A:
git.commit
Action B:
git.push

This separation allows independent authorization and rollback policy.

⸻

72. Example: Model Invocation

Invoking an AI model is also an Action.

Action:
invoke_model

Parameters may include:

model
input context
temperature
token budget
tools
timeout
privacy class

The model’s response is:

Action Result

not:

Authority

A model response proposing:

delete_file("/important")

is still only a proposal until the normal Action authorization pipeline permits it.

⸻

73. Example: External Communication

Action:
send_message

Expected outcome:

message accepted by destination

Verification may require:

provider confirmation
message ID
delivery state

The Action MUST NOT claim delivery merely because the local API call returned successfully.

⸻

74. Example: High-Risk Operation

A high-risk Action may follow:

Proposal
 ↓
Risk Analysis
 ↓
Simulation
 ↓
Human Approval
 ↓
Short-lived Capability Lease
 ↓
Execution
 ↓
Independent Verification
 ↓
Commit

This architecture ensures that increasing intelligence does not automatically increase uncontrolled authority.

⸻

75. Relationship With Other RFCs

RFC-0008 depends on:

RFC-0001 Constitution
RFC-0002 World Model
RFC-0003 Event Model
RFC-0004 State & World Transition
RFC-0005 Intent Model
RFC-0006 Goal Model
RFC-0007 Process Model

RFC-0008 will be consumed by:

RFC-0009 Capability Model
RFC-0010 Authorization & Policy
RFC-0011 Capability Lease & Token
RFC-0026 Verification Engine
RFC-0027 Rollback & Recovery
RFC-0028 Tool & Capability Registry
RFC-0029 External World Interface
RFC-0031 Event/Audit/Trace Fabric

⸻

76. Architectural Position

The Action Model sits at the boundary between cognition and execution.

┌─────────────────────────────┐
│ Intelligence                │
├─────────────────────────────┤
│ Intent                      │
├─────────────────────────────┤
│ Goal                        │
├─────────────────────────────┤
│ Plan                        │
├─────────────────────────────┤
│ Process                     │
├─────────────────────────────┤
│ ACTION MODEL                │
├─────────────────────────────┤
│ Capability / Authorization  │
├─────────────────────────────┤
│ Executor                    │
├─────────────────────────────┤
│ External World              │
└─────────────────────────────┘

The Action Model therefore becomes one of the most important control boundaries in Veda.

⸻

77. Core Principle

The complete Veda agency chain is:

INTELLIGENCE
    ↓
INTENT
    ↓
GOAL
    ↓
PLAN
    ↓
PROCESS
    ↓
ACTION
    ↓
AUTHORIZATION
    ↓
CAPABILITY
    ↓
EXECUTION
    ↓
OBSERVATION
    ↓
VERIFICATION
    ↓
WORLD TRANSITION
    ↓
OUTCOME

The most important separation is:

Thinking
≠
Wanting
≠
Planning
≠
Executing
≠
Authorizing
≠
Verifying

Veda MUST preserve these boundaries.

⸻

78. Final Invariants

The Action Model exists to enforce one fundamental rule:

Veda may be intelligent enough to decide what should happen, but intelligence alone must never be sufficient to make it happen.

An Action is the controlled bridge between Veda’s internal reasoning and the external World.

Therefore:

No Action without Identity.
No Privileged Action without Authorization.
No Execution without Capability.
No Significant Action without Traceability.
No Claimed Success without Verification.
No Unsafe Retry.
No Silent Scope Expansion.
No Self-Authorization.
No Untracked Side Effect.

⸻

79. Status

RFC-0008 Status: Draft v0.1.0

This RFC defines the conceptual and structural contract for Actions.

Implementation-specific executor interfaces, capability schemas, authorization policies, verification protocols, and external integration mechanisms are defined by subsequent RFCs.
