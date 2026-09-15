RFC-0049 — Intent Computing Architecture

Status: Draft
Version: 1.0
Layer: Layer 19 — Computing Paradigm
Depends on: RFC-0001 through RFC-0048
Primary Related RFCs: RFC-0005, RFC-0006, RFC-0010, RFC-0018, RFC-0020, RFC-0025, RFC-0026, RFC-0029, RFC-0031, RFC-0032, RFC-0042, RFC-0044, RFC-0048

⸻

1. Abstract

RFC-0049 defines the Intent Computing Architecture, a computing paradigm in which users interact with a computing system primarily through intent rather than explicit application commands.

Traditional computing generally follows:

User
  ↓
Application
  ↓
Interface
  ↓
Command
  ↓
Computer
  ↓
Result

Intent Computing follows:

Human
  ↓
Intent
  ↓
World Model
  ↓
Goal
  ↓
Planning
  ↓
Decision
  ↓
Authorization
  ↓
Capability
  ↓
Action
  ↓
External World
  ↓
Verification
  ↓
World Update
  ↓
Human

The architecture changes the primary abstraction of computing from:

“What command should the user execute?”

to:

“What outcome does the user intend to achieve?”

Intent Computing does not eliminate applications, operating systems, APIs, tools, or interfaces.

Instead, it places them underneath an intent-driven orchestration layer.

⸻

2. Motivation

Modern computing requires humans to translate goals into application-specific commands.

For example:

Goal:
"I need to prepare a report about the current state of my business."

Traditional computing requires the human to determine:

Open browser
→ Search data
→ Open spreadsheet
→ Download files
→ Open editor
→ Write report
→ Save document
→ Email document

The human becomes the orchestration engine.

Intent Computing reverses this relationship.

The user communicates:

"I need a report about the current state of my business."

The system determines:

What information exists?
What is missing?
Which sources are relevant?
Which tools can access them?
What plan achieves the goal?
What actions require permission?
What evidence is required?
How will success be verified?

The computer therefore becomes an active computational environment rather than a collection of disconnected applications.

⸻

3. Scope

RFC-0049 defines the architectural model for:

* Intent representation
* Intent interpretation
* Intent grounding
* Goal derivation
* World-aware computation
* Capability discovery
* Planning
* Decision support
* Authorization
* Action orchestration
* Verification
* Continuous execution
* Human interaction
* World updates
* Auditability
* Explainability
* Multi-agent intent coordination

RFC-0049 does not define:

* a specific LLM,
* a specific UI,
* a specific operating system,
* a specific programming language,
* a specific database,
* a specific agent protocol.

⸻

4. Fundamental Principle

The fundamental principle is:

Computing should optimize for achieving authorized human intent in the real world, rather than merely executing explicit commands.

⸻

5. Traditional Computing Model

Traditional application-centric computing can be represented as:

Human
  ↓
Application
  ↓
UI
  ↓
Command
  ↓
API
  ↓
OS
  ↓
Hardware

This creates application boundaries.

A task that crosses multiple applications becomes the user’s responsibility.

⸻

6. Intent Computing Model

Intent Computing introduces an intent layer above applications and tools.

                    Human
                      │
                      ▼
                   Intent
                      │
                      ▼
                 Goal System
                      │
                      ▼
                 World Model
                      │
                      ▼
                   Planner
                      │
                      ▼
               Decision Engine
                      │
                      ▼
                Authorization
                      │
                      ▼
              Capability Fabric
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       Browser      OS/API      Devices
          │           │           │
          └───────────┼───────────┘
                      ▼
                External World
                      │
                      ▼
                 Verification
                      │
                      ▼
                 World Update

⸻

7. Intent as Primary Input

Intent becomes the primary semantic input to the computing system.

An Intent contains:

intent_id
version
actor
raw_input
normalized_intent
desired_outcome
scope
constraints
preferences
deadline
context
confidence
ambiguity
authority_context
privacy_requirements
risk_tolerance
provenance
status

⸻

8. Intent Is Not a Command

The architecture MUST distinguish:

Intent
Goal
Plan
Action
Command

For example:

Intent:
"Make my development environment faster."
Goal:
Reduce development workflow latency.
Plan:
Analyze system → identify bottlenecks → propose changes → test → apply.
Action:
Change configuration X.
Command:
execute configuration operation Y.

These are different abstraction levels.

⸻

9. Intent Interpretation

Intent interpretation transforms human communication into a structured representation.

Human Input
    ↓
Language / Voice / Gesture / Interface
    ↓
Intent Extraction
    ↓
Intent Normalization
    ↓
Context Resolution
    ↓
Ambiguity Detection
    ↓
Intent Object

Interpretation MUST preserve uncertainty.

The system MUST NOT silently convert uncertain interpretation into certainty.

⸻

10. Intent Grounding

An intent must be grounded against the World Model.

Example:

User:
"Clean up my computer."

The system must determine:

Which computer?
What counts as cleanup?
What files?
What applications?
What risks?
What should never be deleted?
What is reversible?

Grounding therefore combines:

Intent
+
World
+
Memory
+
Knowledge
+
Policies
+
User Preferences

⸻

11. Ambiguous Intent

When intent is ambiguous, Veda may:

1. resolve from context,
2. produce multiple interpretations,
3. simulate consequences,
4. ask for clarification,
5. perform only safe reversible actions,
6. defer execution.

The choice MUST depend on risk.

⸻

12. Intent Confidence

Intent confidence represents confidence in interpretation.

It MUST NOT be confused with:

* truth,
* authorization,
* success probability,
* trust,
* user approval.

Example:

Intent confidence:
0.91
Authorization:
NOT GRANTED
Outcome confidence:
UNKNOWN

High intent confidence does not authorize action.

⸻

13. Intent Lifecycle

CAPTURED
   ↓
NORMALIZED
   ↓
GROUNDED
   ↓
DISAMBIGUATED
   ↓
VALIDATED
   ↓
GOAL_DERIVED
   ↓
PLANNED
   ↓
EXECUTED
   ↓
VERIFIED
   ↓
COMPLETED

Alternative states:

AMBIGUOUS
DEFERRED
CANCELLED
REJECTED
EXPIRED
SUPERSEDED
BLOCKED

⸻

14. Intent → Goal

Intent describes desired direction.

Goal describes a measurable desired state.

Example:

Intent:
"I want my website to load faster."
Goal:
Reduce median page load time from current baseline
to target threshold under defined test conditions.

RFC-0006 governs the goal representation.

⸻

15. Intent → World

Intent Computing requires a World Model.

Without a World Model, the system cannot reliably determine:

* current state,
* relevant entities,
* dependencies,
* available capabilities,
* constraints,
* history,
* expected consequences.

Therefore:

Intent Computing
        ↓
requires
        ↓
World Representation

⸻

16. World Is Not Reality

The World Model remains a representation.

Reality
   ↓
Observation
   ↓
Evidence
   ↓
World Model

Veda MUST NOT treat its World Model as identical to reality.

⸻

17. Contextual Computing

Intent must be interpreted in context.

Context may include:

Current World
Active Goal
Current Process
Previous Interaction
Memory
Knowledge
Time
Location
Device
Available Resources
User Preferences
Policies
Security State
Privacy State

Context MUST be scoped.

The system MUST NOT include irrelevant private data merely because it is available.

⸻

18. Context Budget

Intent Computing SHOULD support context budgets.

A task may specify:

Maximum Context Size
Maximum Sensitive Data
Maximum Retrieval Cost
Maximum Reasoning Time
Maximum External Data

The system SHOULD prefer relevant context over maximal context.

⸻

19. Intent Hierarchy

Intents may form a hierarchy:

Life Intent
    ↓
Strategic Intent
    ↓
Project Intent
    ↓
Task Intent
    ↓
Immediate Intent

Example:

Strategic:
Build sustainable software income.
Project:
Build Veda.
Task:
Implement Marketplace.
Immediate:
Create RFC-0048.

⸻

20. Intent Persistence

Some intents are temporary.

Others are persistent.

Types:

EPHEMERAL
SESSION
TASK
PROJECT
STRATEGIC
LONG_TERM
RECURRING
CONDITIONAL

Persistent intent MUST have explicit lifecycle and cancellation semantics.

⸻

21. Intent Conflict

Multiple intents may conflict.

Example:

Intent A:
Finish quickly.
Intent B:
Minimize cost.
Intent C:
Maximize quality.
Intent D:
Never send private data externally.

Intent conflict is evaluated through RFC-0014 and RFC-0025.

No hidden priority should be invented.

⸻

22. Intent and User Values

User values may constrain intent interpretation.

However:

User Value
    ≠
Authorization

Values influence decisions.

Authorization determines permitted actions.

⸻

23. Intent and Constitution

The Veda Constitution remains superior to user-level optimization.

Therefore:

Intent
  ↓
Goal
  ↓
Plan
  ↓
Decision
  ↓
Constitution / Safety / Authority

Intent cannot override constitutional constraints.

⸻

24. Intent-to-Action Compilation

Intent Computing may be viewed as compilation.

Human Intent
     ↓
Semantic Representation
     ↓
Goal
     ↓
Plan
     ↓
Action Graph
     ↓
Capability Calls
     ↓
External Operations

This resembles compilation from a high-level language into executable operations.

The difference is that the world is dynamic.

Therefore compilation must be continuously adaptive.

⸻

25. Closed-Loop Computation

Intent Computing MUST NOT be purely open-loop.

The system should operate:

Intent
 ↓
Plan
 ↓
Action
 ↓
Observe
 ↓
Verify
 ↓
Update World
 ↓
Replan
 ↓
Action

This creates a feedback loop.

⸻

26. Dynamic Replanning

Plans may become invalid when:

* world state changes,
* dependencies change,
* resources disappear,
* tools fail,
* assumptions become false,
* new evidence appears,
* user intent changes.

Veda MUST be able to invalidate and regenerate plans.

⸻

27. Intent Persistence Across Failures

A failed action does not necessarily invalidate the intent.

Example:

Intent:
Deploy application.
Action:
Deploy using Provider A.
Result:
Provider A unavailable.
System:
Try Provider B.

The system preserves the intent while changing the plan.

⸻

28. Intent vs Plan

Intent answers:

What do we want?

Plan answers:

How might we achieve it?

Decision answers:

Which available approach should be selected?

Authorization answers:

Is this action permitted?

Execution answers:

What happened when we attempted it?

Verification answers:

Did the intended outcome actually occur?

⸻

29. Intent vs AI Model

An AI model is an intelligence provider.

It is not the Intent System.

Intent
  ↓
Veda Cognitive Architecture
  ↓
Intelligence Router
  ↓
Model

Different models may interpret, reason, plan, critique, or verify portions of the task.

The Intent remains owned by the Veda system.

⸻

30. Model Replacement

Intent Computing MUST remain model-independent.

A model replacement should not change:

* identity,
* authority,
* World Model semantics,
* Constitution,
* audit semantics,
* authorization model.

This permits:

Model A
Model B
Model C
Local Model
Cloud Model
Specialist Model

to be interchangeable according to RFC-0015 and RFC-0016.

⸻

31. Intent and Tools

Tools become capabilities used to achieve intent.

Traditional:

User → Tool

Intent Computing:

User
 ↓
Intent
 ↓
Goal
 ↓
Plan
 ↓
Capability Selection
 ↓
Tool

The tool is therefore an implementation mechanism, not the primary user abstraction.

⸻

32. Intent and Applications

Applications become capability providers.

For example:

Browser
    → web interaction capability
Editor
    → document editing capability
Terminal
    → system execution capability
GitHub
    → repository capability
Database
    → data capability

The user need not know which application performs the operation.

However, the system MUST preserve traceability.

⸻

33. Application Independence

Intent Computing SHOULD reduce application lock-in.

A task should be expressible as:

"Create the report."

rather than:

"Open Application X,
click menu Y,
select option Z..."

⸻

34. Interface Independence

The same intent SHOULD be expressible through:

Text
Voice
Gesture
Phone
Desktop
Wearable
API
Automation
Physical Interface

The semantic intent should remain independent from the interface.

⸻

35. Human Interface

The interface becomes a portal into the Intent System.

It should expose:

What Veda understood
What it plans to do
What it needs
What it is doing
What happened
What remains uncertain

⸻

36. Intent Preview

Before consequential execution, Veda SHOULD present an intent preview.

Example:

Intent:
Clean temporary development files.
Interpretation:
Remove cache and build artifacts.
Scope:
Project directory only.
Excluded:
Source code
Git history
Environment secrets
Estimated reclaimed storage:
12.4 GB
Risk:
Low
Rollback:
Partial

This is substantially more useful than asking the user to decipher a wall of implementation details.

⸻

37. Intent Negotiation

When ambiguity materially affects outcome, Veda may negotiate intent.

Example:

User:
"Make it cheaper."
Veda:
Possible interpretations:
A. Reduce infrastructure cost.
B. Reduce API cost.
C. Reduce storage cost.
D. Reduce total operating cost.
Current evidence suggests A and B.

Negotiation MUST remain faithful to available evidence.

⸻

38. Intent Refinement

Intent may become more precise over time.

Initial:
"Build an AI assistant."
Refined:
"Build a private personal AI system."
Refined:
"Build a local-first personal AI with cloud escalation."
Refined:
"Build Veda under the defined Constitution."

Intent lineage MUST preserve these changes.

⸻

39. Intent Versioning

Every persistent intent SHOULD be versioned.

Intent v1
   ↓
Intent v2
   ↓
Intent v3

Previous interpretations remain auditable.

⸻

40. Intent Cancellation

Users MUST be able to cancel persistent intent.

Cancellation MUST propagate to:

* planning,
* queued actions,
* background processes,
* scheduled operations,
* delegated agents,

subject to the limits of external systems.

⸻

41. Intent Expiration

Some intents have deadlines.

Example:

"Remind me to renew this before Friday."

The intent should carry temporal constraints through RFC-0021.

Expired intent MUST NOT silently execute.

⸻

42. Conditional Intent

Intent may depend on future conditions.

Example:

"If the backup fails, notify me and retry once."

This becomes:

Condition
   ↓
Trigger
   ↓
Intent
   ↓
Goal
   ↓
Plan

⸻

43. Recurring Intent

Recurring intent may be represented as:

Every day:
Review system health.
Every week:
Summarize project progress.
When:
Storage > 90%
Then:
Initiate cleanup analysis.

Recurring intent MUST remain bounded by authorization and policy.

⸻

44. Intent Monitoring

Persistent intents require monitoring.

The system should track:

Intent State
Goal Progress
Plan State
World Changes
Deadlines
Blocked Dependencies
Risk Changes
Relevant Events

RFC-0019 determines attention allocation.

⸻

45. Intent Priority

Priority MAY be influenced by:

Urgency
Importance
Goal Alignment
Risk
Deadline
User Explicit Priority
Information Value
Resource Cost

Intent priority MUST NOT override hard safety or authorization constraints.

⸻

46. Multiple Intents

Veda may maintain multiple active intents.

Example:

Intent A:
Finish software project.
Intent B:
Monitor infrastructure.
Intent C:
Research new model.
Intent D:
Maintain backups.

The Attention Engine determines which requires immediate cognitive resources.

⸻

47. Intent Scheduling

Intent execution may be scheduled according to:

* deadline,
* resource availability,
* dependencies,
* risk,
* user-defined windows,
* external events.

Scheduling MUST integrate with RFC-0021.

⸻

48. Intent Dependencies

Intents may depend on other intents.

Intent A
    ↓
Intent B
    ↓
Intent C

Dependencies may be:

DATA
TEMPORAL
RESOURCE
AUTHORITY
KNOWLEDGE
WORLD_STATE

⸻

49. Intent Delegation

An intent may be delegated to another agent.

Human
 ↓
Veda
 ↓
Agent A
 ↓
Agent B

Delegation MUST preserve:

* identity,
* authority boundaries,
* provenance,
* scope,
* expiration,
* accountability.

RFC-0044 and RFC-0046 govern multi-agent delegation.

⸻

50. Intent Decomposition

Complex intent may be decomposed:

Intent:
Launch product.
Goals:
 ├── Product
 ├── Infrastructure
 ├── Documentation
 ├── Marketing
 ├── Distribution
 └── Monitoring

Each goal may become an independent process.

⸻

51. Intent Composition

Multiple compatible intents may be combined.

Example:

Intent A:
Back up project.
Intent B:
Analyze project size.
Intent C:
Clean temporary files.

If operations overlap safely, Veda may compose them into one plan.

Composition MUST preserve the original intent lineage.

⸻

52. Intent Conflict Resolution

If intents conflict:

Intent A → delete old files
Intent B → preserve all project history

Veda MUST NOT silently choose one.

Conflict resolution follows:

Constitution
 ↓
Safety
 ↓
Authority
 ↓
Explicit User Constraints
 ↓
Goal Priority
 ↓
Decision Engine
 ↓
Human Escalation if unresolved

⸻

53. Intent and Simulation

High-risk intent MAY be simulated before execution.

Intent
 ↓
Plan
 ↓
Simulation
 ↓
Predicted Outcome
 ↓
Risk
 ↓
Decision

Simulation results are predictions, not facts.

⸻

54. Intent and Verification

Intent completion MUST NOT be inferred solely from action execution.

Example:

Intent:
Send report.
Action:
Email API returned success.
Verification:
Recipient server accepted message.
Outcome:
UNKNOWN whether recipient read it.

Therefore:

Execution Success
    ≠
Intent Success

⸻

55. Intent Completion

Intent completion should be based on explicit success criteria.

Intent
 ↓
Goal
 ↓
Success Conditions
 ↓
Observed State
 ↓
Verification
 ↓
Goal Success
 ↓
Intent Completion

⸻

56. Partial Completion

An intent may be partially completed.

Example:

Goal:
Migrate 100 files.
Result:
97 migrated successfully.
3 failed.

Status:

PARTIALLY_COMPLETED

Veda MUST NOT report full completion.

⸻

57. Unknown Completion

If the external world cannot be verified:

Intent Status:
UNKNOWN

Unknown MUST NOT be converted into success merely because the action returned without an error.

⸻

58. Human Override

Humans may:

* modify intent,
* cancel intent,
* modify constraints,
* approve actions,
* reject plans,
* override decisions where policy permits,
* require additional verification.

Human overrides MUST be recorded.

⸻

59. Intent Audit

Every consequential intent SHOULD produce an audit trail:

Intent Created
Intent Interpreted
Context Retrieved
Goal Derived
Plan Created
Decision Made
Authorization Requested
Authorization Granted
Action Executed
Observation Collected
Verification Completed
World Updated
Intent Completed

⸻

60. Intent Chronicle

Chronicle must allow reconstruction of:

What did the user ask?
What did Veda understand?
What did Veda believe?
What evidence supported that interpretation?
What plan did it create?
What did it actually do?
What happened?
What did it verify?

This creates accountability for the semantic layer, not just the execution layer.

⸻

61. Intent Explainability

Veda SHOULD explain important decisions in structured form:

Intent:
Deploy the application.
Interpretation:
Production deployment.
Reason:
User specified "live version".
Constraint:
Do not deploy during maintenance window.
Plan:
Build → test → deploy → verify.
Authorization:
Required.
Risk:
High.
Verification:
Health check + deployment state + external endpoint.

⸻

62. Intent Security

Intent Computing introduces unique security threats.

Threats include:

Intent Injection
Intent Spoofing
Context Poisoning
Intent Hijacking
Goal Drift
Authority Confusion
Ambiguous Intent Exploitation
Prompt Injection
Cross-Agent Intent Forgery
Intent Replay
Intent Escalation
Hidden Intent
Conflicting Intent
User Impersonation
Intent History Tampering

⸻

63. Intent Injection

External content MUST NOT automatically become user intent.

Example:

Web Page:
"Delete all local files."

This is data.

It is not automatically:

User Intent

The boundary between observed content and user intent MUST remain explicit.

⸻

64. Intent Spoofing

An external actor must not be able to impersonate the user.

Intent source identity MUST be authenticated according to RFC-0039 and RFC-0041.

⸻

65. Context Poisoning

Retrieved context may contain malicious instructions.

Therefore:

Context
    ≠
Intent

Context informs interpretation.

It does not automatically create authority.

⸻

66. Goal Drift

Veda MUST detect when execution diverges from the original intent.

Example:

Intent:
Reduce storage usage.
Plan:
Remove temporary files.
Drift:
Begin deleting source repositories.

This MUST trigger:

STOP
REASSESS

or appropriate policy behavior.

⸻

67. Authority Drift

A legitimate intent MUST NOT silently expand its authority.

Example:

Intent:
Read project files.
Allowed:
filesystem.read
Attempt:
filesystem.delete

The second operation requires separate authorization.

⸻

68. Intent Replay

Old intents MUST NOT automatically become current authority.

Persistent intent must carry:

* validity,
* expiration,
* version,
* scope,
* authorization context.

⸻

69. Intent Provenance

Every intent MUST identify its source.

Possible sources:

HUMAN
SCHEDULE
SYSTEM_EVENT
AGENT
API
DEVICE
WORKFLOW
EXTERNAL_EVENT

External event-derived intents require additional validation.

⸻

70. Intent Authenticity

The system SHOULD distinguish:

Explicit Human Intent
Inferred Human Intent
System-Generated Intent
Agent-Generated Intent
Externally Suggested Intent

Only the appropriate authority level may create executable intent.

⸻

71. Suggested Intent

Veda may suggest:

"You appear to want to clean temporary files."

But a suggestion is not automatically an authorized command.

⸻

72. Autonomous Intent

Veda may create internal intents for:

* maintenance,
* diagnostics,
* monitoring,
* recovery,
* verification,
* scheduled tasks.

Such intents remain subordinate to policy and authority.

⸻

73. Self-Generated Intent

Veda may generate internal goals such as:

Improve system reliability.
Reduce resource consumption.
Investigate repeated failure.

However:

Self-Generated Intent
    ≠
Constitutional Authority

Self-generated intent cannot modify constitutional constraints or grant itself privileges.

⸻

74. Intent Economics

Intent Computing SHOULD consider resource costs.

Each intent may have:

CPU Budget
GPU Budget
Memory Budget
Network Budget
API Budget
Money Budget
Time Budget
Energy Budget
Attention Budget

This integrates with RFC-0033 and RFC-0034.

⸻

75. Intent Value

Intent may have measurable value:

Expected Benefit
Expected Cost
Risk
Time
Resource Consumption
Opportunity Cost

RFC-0025 performs formal decision evaluation.

⸻

76. Intent Competition

When multiple intents compete for limited resources, Veda SHOULD use:

Priority
Deadline
Expected Value
Risk
Resource Cost
Dependency
User Priority

The system MUST preserve fairness and prevent starvation.

⸻

77. Intent and Attention

Attention determines which intents deserve cognitive processing.

World Event
   ↓
Attention
   ↓
Relevant Intent
   ↓
Brain

Not every intent requires continuous reasoning.

⸻

78. Intent and Memory

Memory may preserve:

Past Intent
Intent Outcome
User Preference
Previous Interpretation
Successful Strategy
Failed Strategy

However, memory does not override current explicit intent.

⸻

79. Intent and Knowledge

Knowledge helps determine:

What does this intent mean?
What constraints apply?
What actions exist?
What outcomes are possible?

Knowledge does not become authority.

⸻

80. Intent and Causality

Intent execution may require causal reasoning.

Example:

Intent:
Reduce server cost.
Possible action:
Reduce server count.
Causal question:
Will this reduce cost without violating availability requirements?

RFC-0022 and RFC-0024 govern this analysis.

⸻

81. Intent and Future

The Future Engine can evaluate:

If we pursue this intent,
what future states become possible?

This allows intent-aware scenario analysis.

⸻

82. Intent and Multi-Agent Systems

Multiple agents may collaborate around one intent.

Example:

Human Intent
      ↓
Veda
 ┌────┼────┐
 ▼    ▼    ▼
Research  Coding  Verification
 Agent     Agent      Agent
 └────┼────┘
      ▼
 Shared World

The intent remains governed by the originating authority.

⸻

83. Intent Delegation Chain

Every delegation SHOULD preserve:

Original Intent
    ↓
Delegator
    ↓
Delegated Scope
    ↓
Delegate
    ↓
Actions
    ↓
Outcome

No delegate may silently expand the original scope.

⸻

84. Intent Federation

Across independent Veda Worlds:

Intent
 ↓
Federation
 ↓
Remote Agent
 ↓
Remote World

Remote agents MUST NOT automatically inherit local authority.

RFC-0046 governs cross-world negotiation.

⸻

85. Intent Marketplace

RFC-0048 may provide packages capable of handling specific intent classes.

Example:

Intent:
Analyze financial documents.
Marketplace:
Find specialized finance-analysis skill.

The package becomes an implementation capability.

It does not own the intent.

⸻

86. Intent Protocol

NCP may carry structured intent context:

intent_id
intent_version
intent_type
desired_outcome
constraints
context_refs
goal_refs
authority_refs
privacy_requirements
uncertainty
provenance

NCP transports context.

It does not authorize actions.

⸻

87. Intent Computing API

A reference API SHOULD include:

create_intent()
get_intent()
update_intent()
cancel_intent()
expire_intent()
interpret_intent()
normalize_intent()
ground_intent()
detect_ambiguity()
resolve_intent()
derive_goal()
validate_intent()
prioritize_intent()
decompose_intent()
compose_intent()
delegate_intent()
simulate_intent()
plan_intent()
execute_intent()
verify_intent()
get_intent_progress()
get_intent_trace()
pause_intent()
resume_intent()
explain_intent()
get_intent_history()

⸻

88. Intent Events

The system SHOULD emit:

IntentCaptured
IntentAuthenticated
IntentInterpreted
IntentNormalized
IntentGrounded
IntentAmbiguityDetected
IntentClarificationRequested
IntentClarified
IntentValidated
GoalDerived
IntentPlanned
IntentDecisionRequested
IntentAuthorizationRequested
IntentAuthorized
IntentExecutionStarted
IntentProgressUpdated
IntentBlocked
IntentReplanned
IntentVerificationStarted
IntentPartiallyCompleted
IntentCompleted
IntentFailed
IntentUnknown
IntentCancelled
IntentExpired
IntentSuperseded
IntentDelegated
IntentRecalled
IntentDriftDetected
IntentConflictDetected
IntentSecurityViolation

⸻

89. Intent State Machine

CAPTURED
   ↓
AUTHENTICATED
   ↓
INTERPRETED
   ↓
GROUNDED
   ↓
VALIDATED
   ↓
GOAL_DERIVED
   ↓
PLANNED
   ↓
AUTHORIZED
   ↓
EXECUTING
   ↓
VERIFYING
   ↓
COMPLETED

Alternative states:

AMBIGUOUS
BLOCKED
DEFERRED
PAUSED
FAILED
UNKNOWN
CANCELLED
EXPIRED
SUPERSEDED

⸻

90. Reference Execution Loop

The complete Intent Computing loop is:

                  ┌──────────────┐
                  │    Human     │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │    Intent    │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │     Goal     │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │  World Model │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │    Planner   │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │   Decision   │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │ Authorization│
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │  Capability  │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │    Action    │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │External World│
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │ Verification │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │ World Update │
                  └──────┬───────┘
                         │
                         └──────────────┐
                                        ▼
                                   Replanning

⸻

91. Core Invariants

ICA-1

Intent MUST be distinguishable from command.

ICA-2

Intent MUST be distinguishable from goal.

ICA-3

Intent MUST be distinguishable from plan.

ICA-4

Intent MUST be distinguishable from authorization.

ICA-5

Intent MUST be distinguishable from action.

ICA-6

Intent MUST be distinguishable from outcome.

ICA-7

Intent interpretation MUST preserve uncertainty.

ICA-8

External content MUST NOT automatically become user intent.

ICA-9

Intent MUST have provenance.

ICA-10

High-risk ambiguous intent MUST NOT silently become execution.

ICA-11

Intent MUST be grounded against relevant world context.

ICA-12

World Model MUST NOT be treated as reality.

ICA-13

Intent MUST NOT bypass authorization.

ICA-14

Intent MUST NOT bypass capability controls.

ICA-15

Intent MUST NOT bypass verification.

ICA-16

Persistent intent MUST support cancellation.

ICA-17

Persistent intent MUST support expiration where applicable.

ICA-18

Intent changes MUST be versioned.

ICA-19

Intent delegation MUST preserve authority boundaries.

ICA-20

Delegated agents MUST NOT expand intent scope.

ICA-21

Intent execution SHOULD be closed-loop.

ICA-22

Failed actions MUST NOT automatically imply failed intent.

ICA-23

Successful execution MUST NOT automatically imply successful intent.

ICA-24

Unknown outcomes MUST remain UNKNOWN.

ICA-25

Intent drift MUST be detectable.

ICA-26

Intent history MUST be auditable.

ICA-27

Human override MUST remain available according to policy.

ICA-28

Model replacement MUST NOT alter intent authority semantics.

ICA-29

Intent orchestration MUST remain independent from any particular application.

ICA-30

The system MUST optimize for authorized outcomes, not merely command execution.

⸻

92. Architectural Transformation

Traditional computing:

Application-Centric

becomes:

Intent-Centric

Traditional:

User
 ↓
App
 ↓
Command

Intent Computing:

Human
 ↓
Intent
 ↓
World
 ↓
Goal
 ↓
Plan
 ↓
Capability
 ↓
Action
 ↓
Reality

The application becomes an implementation detail beneath the semantic computing layer.

⸻

93. Relationship to Existing Systems

Intent-driven computing is not an entirely new academic idea. Cognitive architectures and intent-based systems have previously separated high-level goals or intents from lower-level execution. For example, intent-based networking uses a knowledge base, reasoning engine, and agent architecture to translate higher-level intent into operational actions. (ericsson.com)

Veda’s architectural distinction is that Intent Computing is connected explicitly to:

World Model
Evidence
Memory
Planning
Causality
Simulation
Decision
Authorization
Capability Leases
External World Interface
Verification
Chronicle
Evolution

The intent therefore becomes part of an auditable end-to-end computation model.

⸻

94. Fundamental Boundary

Intent Computing does not mean:

“AI can do whatever the user vaguely wants.”

It means:

“The computer can reason about what the user is trying to achieve while preserving explicit boundaries between interpretation, authority, execution, and reality.”

That distinction is fundamental.

⸻

95. Final Principle

The traditional computer asks:

“Which command should I execute?”

Intent Computing asks:

“What authorized outcome is the human trying to achieve, what is the current state of the world, what actions could achieve it, what are their consequences, and how will we verify what actually happened?”

Therefore:

Intent
    ↓
Understanding
    ↓
World
    ↓
Goal
    ↓
Plan
    ↓
Decision
    ↓
Authorization
    ↓
Action
    ↓
Reality
    ↓
Verification
    ↓
Learning

Intent becomes the semantic entry point to computing.

The command is no longer the center.

The application is no longer the center.

The interface is no longer the center.

The authorized human intent becomes the center, while the system remains accountable to reality.