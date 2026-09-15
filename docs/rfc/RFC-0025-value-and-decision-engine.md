RFC-0025 — Value & Decision Engine

Status: Draft
Layer: 9 — Future & Decision
Depends On: RFC-0001, RFC-0005, RFC-0006, RFC-0008, RFC-0009, RFC-0010, RFC-0012, RFC-0013, RFC-0014, RFC-0016, RFC-0018, RFC-0019, RFC-0020, RFC-0021, RFC-0022, RFC-0023, RFC-0024, RFC-0026, RFC-0027

⸻

1. Abstract

RFC-0025 defines the Value & Decision Engine of Veda.

Its purpose is to transform:

Goals
+
Values
+
Constraints
+
Authority
+
Evidence
+
Uncertainty
+
Risk
+
Future Scenarios
+
Simulation Results

into:

Decision

The Decision Engine answers:

Given everything Veda currently knows, what option should be selected?

However:

Decision ≠ Authorization

and:

Decision ≠ Execution

The engine may recommend or select an option according to the applicable decision policy, but it MUST NOT grant itself authority to execute the selected option.

⸻

2. Motivation

Without a Decision Engine, Veda can produce:

Plan A
Plan B
Plan C

and simulate all three.

But simulation only tells Veda what may happen.

It does not answer:

Which outcome should Veda prefer?

For example:

Plan A:
    90% success
    high cost
    irreversible
Plan B:
    80% success
    low cost
    reversible
Plan C:
    60% success
    very low cost
    experimental

There is no universally correct answer.

The correct choice depends on:

Values
Goals
Constraints
Risk tolerance
Authority
Resources
Reversibility
Time
Future consequences

Therefore decision-making must be an explicit architectural layer.

⸻

3. Core Principle

Veda MUST NOT use:

Highest probability = best decision

Instead:

Best Decision
=
Values
+
Goals
+
Constraints
+
Authority
+
Risk
+
Expected Outcomes
+
Uncertainty
+
Reversibility
+
Long-term Consequences

subject to constitutional and policy constraints.

⸻

4. Decision Hierarchy

Decision evaluation MUST follow this hierarchy:

1. Constitution
       ↓
2. Hard Safety Constraints
       ↓
3. Authority / Permission
       ↓
4. Explicit User Constraints
       ↓
5. Policies
       ↓
6. Goals
       ↓
7. Values
       ↓
8. Risk Preferences
       ↓
9. Utility / Outcome
       ↓
10. Optimization

Lower layers MUST NOT override higher layers.

Therefore:

High Utility

cannot justify:

Constitution Violation

and:

High Probability of Success

cannot justify:

Unauthorized Action

⸻

5. Decision vs Action

The distinction is mandatory.

Decision:
    "Choose Plan B."
Action:
    "Execute Plan B."

The first is cognitive.

The second changes reality.

Therefore:

Decision
   ↓
Authorization
   ↓
Action
   ↓
Verification

The Decision Engine stops before authorization.

⸻

6. Design Goals

The engine MUST support:

1. option generation input
2. option normalization
3. hard constraint filtering
4. authority checking
5. value evaluation
6. goal alignment
7. risk evaluation
8. uncertainty handling
9. simulation integration
10. scenario comparison
11. expected utility
12. multi-objective optimization
13. reversibility analysis
14. opportunity-cost analysis
15. time sensitivity
16. resource constraints
17. decision confidence
18. ambiguity detection
19. human escalation
20. complete decision trace

⸻

7. Non-Goals

The Decision Engine MUST NOT:

* modify Constitution
* grant authority
* bypass authorization
* execute tools
* alter policy to justify a decision
* treat model confidence as truth
* treat utility as permission
* hide uncertainty
* silently change user values
* fabricate preferences
* convert prediction into fact
* suppress competing options without traceability

⸻

8. Decision Object

Decision {
    decision_id
    version
    subject
    context
    requester
    goal_refs
    intent_refs
    candidate_options
    constraints
    authority_context
    values
    preferences
    evidence_refs
    knowledge_refs
    scenario_refs
    simulation_refs
    risk_assessment
    uncertainty
    evaluation
    selected_option
    rejected_options
    rationale
    confidence
    reversibility
    opportunity_cost
    escalation_requirement
    authorization_requirement
    status
    provenance
    created_at
    expires_at
}

⸻

9. Candidate Option

Each option MUST be represented explicitly.

Option {
    option_id
    description
    action_refs
    plan_refs
    expected_outcomes
    constraints
    resources
    cost
    risk
    reversibility
    time
    dependencies
    assumptions
    simulation_refs
    evidence_refs
    confidence
}

An option MUST NOT be evaluated solely from natural-language descriptions.

⸻

10. Hard Constraints

Hard constraints are non-negotiable.

Examples:

Do not exceed budget.
Do not expose secret data.
Do not violate policy.
Do not delete protected files.
Do not exceed capability scope.
Do not act without required approval.

Formal representation:

Admissible(option)
=
AllHardConstraintsSatisfied(option)

If:

Admissible(option) = FALSE

the option MUST NOT enter normal utility optimization.

It is:

INADMISSIBLE

⸻

11. Soft Constraints

Soft constraints represent preferences.

Examples:

Prefer cheaper.
Prefer faster.
Prefer local model.
Prefer reversible action.
Prefer lower energy consumption.
Prefer privacy.

Soft constraints MAY influence ranking.

They MUST NOT override hard constraints.

⸻

12. Authority Constraint

The Decision Engine MUST evaluate:

Can Veda recommend this?
Can Veda decide this?
Can Veda execute this?
Does this require human approval?

These are separate questions.

Example:

Decision:
    "Delete obsolete files."
Authority:
    Veda may recommend.
Execution authority:
    Requires capability lease.
High-risk deletion:
    Requires human approval.

⸻

13. Decision Rights

Decision rights SHOULD be classified:

OBSERVE_ONLY
RECOMMEND
SELECT
AUTO_EXECUTE
HUMAN_APPROVAL_REQUIRED
HUMAN_ONLY

The level MUST depend on:

Risk
+
Ambiguity
+
Reversibility
+
Authority
+
Policy

This is important because low-risk, low-ambiguity decisions are fundamentally different from high-risk strategic decisions. Current AI governance research likewise emphasizes allocating human/AI decision rights according to risk and ambiguity rather than treating all decisions as equivalent. (MIT CISR)

⸻

14. Decision Classes

Veda SHOULD classify decisions as:

ROUTINE

Low ambiguity, low risk.

Auto decision possible.

CONSEQUENT

Low ambiguity, higher risk.

Structured evaluation + stronger verification.

EXPLORATORY

High ambiguity, lower immediate risk.

Prefer experimentation / simulation.

STRATEGIC

High ambiguity, high consequence.

Human decision normally required.

⸻

15. Values

Values represent what Veda should prefer when multiple admissible options exist.

Examples:

privacy
safety
reliability
efficiency
learning
simplicity
cost reduction
performance
reversibility
user preference
long-term sustainability

Values MUST be explicitly represented.

They MUST NOT exist only inside model weights or hidden prompts.

⸻

16. Value Object

Value {
    value_id
    name
    description
    priority
    scope
    source
    authority
    strength
    conditions
    conflicts_with
    status
    version
}

Example:

Value:
    Privacy
Priority:
    HIGH
Source:
    User Policy
Scope:
    Personal Data
Strength:
    HARD

⸻

17. Value Provenance

Every important value SHOULD identify its origin.

Possible sources:

CONSTITUTION
USER_EXPLICIT
USER_POLICY
SYSTEM_POLICY
LEARNED_PREFERENCE
INFERRED_PREFERENCE
TEMPORARY_CONTEXT

The engine MUST distinguish:

User explicitly said:
    "Never upload private files."

from:

Veda inferred:
    "User probably dislikes cloud storage."

The second MUST have lower authority.

⸻

18. Preference vs Value

A preference is not necessarily a value.

Example:

Preference:
    User likes dark mode.

Value:

Privacy:
    User does not want private information exposed.

Preference may change easily.

Core values SHOULD require stronger evidence or explicit modification.

⸻

19. Value Conflicts

Values may conflict.

Example:

Privacy
vs
Convenience

or:

Cost
vs
Reliability

or:

Speed
vs
Safety

The engine MUST NOT silently resolve such conflicts.

It MUST produce:

ValueConflict

and apply the applicable priority or escalate.

⸻

20. Goal Alignment

An option should be evaluated against the active goal hierarchy.

Life Goal
    ↓
Project
    ↓
Milestone
    ↓
Task
    ↓
Action

The engine SHOULD calculate:

GoalAlignment(option)

An option that achieves a local task while damaging the parent goal SHOULD receive a penalty or rejection.

⸻

21. Short-Term vs Long-Term

Veda MUST distinguish:

Immediate Utility

from:

Long-Term Utility

Example:

Action A:
    immediate gain = high
    long-term damage = high
Action B:
    immediate gain = moderate
    long-term benefit = high

A purely short-term optimizer may choose A.

Veda SHOULD evaluate both.

⸻

22. Opportunity Cost

Every meaningful decision SHOULD consider:

"What are we giving up by choosing this?"

For option A:

OpportunityCost(A)
=
BestFeasibleAlternative - A

The exact mathematical representation MAY vary by domain.

⸻

23. Reversibility

Reversibility MUST influence decision risk.

Classification:

REVERSIBLE
PARTIALLY_REVERSIBLE
DIFFICULT_TO_REVERSE
IRREVERSIBLE

Example:

Changing UI theme:
    reversible
Deleting database:
    potentially irreversible

Higher irreversibility SHOULD increase required decision rigor.

⸻

24. Risk

Risk MUST include more than probability.

Risk =
Probability
×
Impact
×
Exposure
×
Irreversibility

The exact formulation MAY be domain-specific.

The engine MUST preserve the underlying dimensions.

⸻

25. Tail Risk

Average outcome is insufficient.

Veda MUST consider:

Best Case
Expected Case
Worst Case
Tail Risk

Example:

Option A:
    Expected value = +100
    Catastrophic tail = -100,000
Option B:
    Expected value = +70
    Worst case = -100

A naive expected-value optimizer may choose A.

Veda SHOULD recognize the tail risk.

⸻

26. Uncertainty

Decision uncertainty SHOULD be represented explicitly.

Types:

DATA_UNCERTAINTY
MODEL_UNCERTAINTY
CAUSAL_UNCERTAINTY
TEMPORAL_UNCERTAINTY
WORLD_STATE_UNCERTAINTY
USER_INTENT_UNCERTAINTY
OUTCOME_UNCERTAINTY
AUTHORITY_UNCERTAINTY

⸻

27. Unknown User Intent

If the optimal decision depends heavily on an unresolved user preference:

Decision Status:
    NEEDS_CLARIFICATION

Veda MUST NOT fabricate the preference.

For example:

"Should I optimize for speed or privacy?"

If no existing policy resolves this:

Human clarification required.

⸻

28. Decision Under Uncertainty

The engine MAY use:

Expected Utility
Expected Regret
Risk-adjusted Utility
Minimax
Maximin
Robust Optimization
Bayesian Decision Theory
Multi-objective Optimization

The chosen method MUST be recorded.

⸻

29. Utility Function

A conceptual utility model:

U(option)
=
GoalValue
+
ValueAlignment
+
ExpectedBenefit
-
Cost
-
RiskPenalty
-
OpportunityCost
-
IrreversibilityPenalty

This is not a universal equation.

Different domains MAY define different utility functions.

The selected utility model MUST be explicit.

⸻

30. Hard Constraint First

The evaluation sequence SHOULD be:

Candidates
    ↓
Hard Constraints
    ↓
Authority
    ↓
Safety
    ↓
Feasibility
    ↓
Simulation
    ↓
Risk
    ↓
Values
    ↓
Goals
    ↓
Utility
    ↓
Decision

This prevents an attractive outcome from making an illegal or unauthorized option appear valid.

⸻

31. Multi-Objective Decision

Some decisions have several objectives.

Example:

Minimize:
    cost
    latency
    energy
Maximize:
    reliability
    privacy
    performance

The engine SHOULD support:

Pareto Frontier

rather than forcing every objective into one number prematurely.

⸻

32. Pareto Frontier

If:

Option A

is better than B in every relevant dimension:

A dominates B

B MAY be eliminated.

If neither dominates:

A:
    cheaper
B:
    safer

both remain candidates.

This preserves genuine tradeoffs.

⸻

33. Decision Thresholds

Veda MAY use thresholds:

minimum_success_probability
maximum_risk
maximum_cost
minimum_confidence
minimum_goal_alignment
maximum_irreversibility

Example:

IF risk > threshold
THEN human approval required.

Thresholds MUST come from policy/value/authority configuration, not arbitrary model output.

⸻

34. Confidence

Decision confidence represents confidence in the decision process.

It MUST NOT mean:

"This outcome will definitely happen."

Instead:

DecisionConfidence =
confidence that current evidence and models support this selection

⸻

35. Confidence Components

Confidence SHOULD consider:

Evidence quality
World-state completeness
Model reliability
Simulation fidelity
Causal certainty
Option comparability
User preference certainty
Prediction calibration

⸻

36. Confidence vs Risk

High confidence does not mean low risk.

Example:

Confidence:
    95%
Risk:
    catastrophic

Veda MUST still escalate if policy requires.

⸻

37. Simulation Integration

RFC-0024 provides:

Predicted Outcomes
Risk
Uncertainty
Sensitivity

Decision Engine consumes them.

Option A
    ↓
Simulation
    ↓
Risk/Outcome
Option B
    ↓
Simulation
    ↓
Risk/Outcome
Comparison
    ↓
Decision

⸻

38. Scenario Integration

RFC-0023 provides multiple future scenarios.

Decision Engine SHOULD evaluate:

How does this option perform across scenarios?

Example:

Option A:
    Great in baseline
    terrible in worst case
Option B:
    Good across all scenarios

The engine MAY prefer B for robustness.

⸻

39. Robustness

A robust decision is one that remains acceptable across reasonable uncertainty.

Robustness(option)
=
Performance across plausible worlds

This is distinct from maximizing one predicted future.

⸻

40. Counterfactual Integration

RFC-0024 MAY provide:

What if we choose A?
What if we choose B?
What if we do nothing?

The Decision Engine SHOULD compare:

A
B
DO_NOTHING
DEFER

Doing nothing is itself an option.

⸻

41. Status Quo

Veda MUST explicitly model:

NO_ACTION

because:

Not acting

also changes the future.

The status quo SHOULD be evaluated like any other option.

⸻

42. Decision Delay

Sometimes:

Act now

is worse than:

Wait

if waiting provides valuable information.

Therefore the engine SHOULD support:

ValueOfInformation

and:

ValueOfWaiting

⸻

43. Value of Information

Veda SHOULD ask:

"What information would most improve this decision?"

Example:

Current uncertainty:
    40%
One additional test:
    reduces uncertainty to 10%

If the test cost is low compared to the decision risk:

Test first.

This prevents Veda from acting merely because it can.

⸻

44. Exploration vs Exploitation

Decision Engine MAY classify options:

EXPLOIT
EXPLORE
DEFER
ABORT

Exploration can be valuable when:

uncertainty is high
learning value is high
risk is low

⸻

45. Learning Value

An action may have value beyond immediate outcome.

TotalValue =
ImmediateValue
+
InformationValue
+
LearningValue

Example:

Small safe experiment

may be preferable to:

Large uncertain deployment

because it improves Veda’s future knowledge.

⸻

46. Resource Constraints

Decision evaluation MUST consider:

CPU
RAM
GPU
storage
money
time
network
human attention
model quota
energy

Human attention is a resource.

Veda SHOULD avoid asking humans to approve trivial decisions.

⸻

47. Human Attention Budget

Human escalation should be treated as costly.

HumanApprovalCost

may include:

time
delay
cognitive load
interruptions
context switching

However:

Human attention cost

MUST NOT override mandatory human approval.

⸻

48. Decision Escalation

The engine SHOULD escalate when:

risk is high
ambiguity is high
authority is unclear
values conflict
user intent is unclear
simulation is insufficient
evidence is contradictory
decision is irreversible
confidence is low

⸻

49. Escalation Levels

LEVEL 0
Auto-select
LEVEL 1
Notify
LEVEL 2
Request confirmation
LEVEL 3
Human approval
LEVEL 4
Human decision
LEVEL 5
Constitutional / exceptional review

⸻

50. Decision Trace

Every meaningful decision MUST be reconstructable.

Trace:

Input
 ↓
World State
 ↓
Goals
 ↓
Constraints
 ↓
Authority
 ↓
Candidate Options
 ↓
Evidence
 ↓
Simulation
 ↓
Risk
 ↓
Values
 ↓
Utility
 ↓
Rejected Options
 ↓
Selected Option
 ↓
Escalation
 ↓
Decision

⸻

51. Explanation

Veda SHOULD be able to answer:

Why did you choose this?
Why did you reject option B?
What constraint eliminated option C?
Which value mattered most?
What uncertainty remains?
What simulation influenced the decision?
What would change your decision?

The explanation MUST reference actual decision data.

It MUST NOT be fabricated post-hoc narrative.

⸻

52. Decision Sensitivity

Veda SHOULD determine:

Which assumption would change the decision?

Example:

Current:
    Choose A
If cost increases > 20%:
    Choose B
If reliability drops > 5%:
    Choose C

This produces a:

Decision Boundary

⸻

53. Decision Stability

A decision is stable if small changes do not change the selected option.

Input perturbation
      ↓
Decision unchanged

A decision is unstable if:

tiny input change
      ↓
different decision

Unstable decisions SHOULD receive additional scrutiny.

⸻

54. Decision Expiration

Decisions may become stale.

Examples:

World changed
Goal changed
Policy changed
Value changed
Evidence changed
Simulation expired
Resource changed
Deadline changed

Decision status becomes:

STALE

It MUST NOT automatically remain valid forever.

⸻

55. Decision Lifecycle

REQUESTED
    ↓
CONTEXT_RESOLVED
    ↓
CANDIDATES_COLLECTED
    ↓
CONSTRAINT_CHECKED
    ↓
AUTHORITY_CHECKED
    ↓
EVALUATING
    ↓
SIMULATED
    ↓
RISK_ASSESSED
    ↓
VALUE_EVALUATED
    ↓
COMPARING
    ↓
SELECTED
    ↓
ESCALATION_CHECK
    ↓
DECIDED

Alternative states:

NEEDS_CLARIFICATION
BLOCKED
DEFERRED
ESCALATED
REJECTED
EXPIRED
SUPERSEDED
CANCELLED

⸻

56. Decision and Authorization Boundary

The final architecture MUST be:

Value Engine
      ↓
Decision
      ↓
Authorization Engine
      ↓
Capability Check
      ↓
Action

The Decision Engine MUST NOT call execution tools directly.

This separation is critical because autonomous AI architecture increasingly treats decision rights and execution rights as separate governance concerns. (MIT CISR)

⸻

57. Decision Override

A human MAY override a decision.

Decision:
    A
Human:
    Choose B

The override MUST generate an event.

DecisionOverridden

Veda SHOULD record:

reason
actor
timestamp
original decision
new decision

Human override becomes valuable learning data.

⸻

58. Learning From Overrides

Repeated overrides may reveal:

wrong value weights
wrong risk model
wrong user preference
missing constraint
poor simulation
bad world model

But Veda MUST NOT automatically change core values from one override.

Learning follows:

Experience
 ↓
Reflection
 ↓
Learning Proposal
 ↓
Evaluation
 ↓
Authorization

through RFC-0035 onward.

⸻

59. Decision Events

The engine MUST emit:

DecisionRequested
ContextResolved
CandidatesCollected
CandidateRejected
ConstraintApplied
AuthorityEvaluated
RiskEvaluated
ValueEvaluated
GoalAlignmentCalculated
SimulationReferenced
ScenarioCompared
UtilityCalculated
ParetoFrontierGenerated
DecisionSensitivityCalculated
DecisionStabilityCalculated
ValueConflictDetected
DecisionAmbiguityDetected
DecisionSelected
DecisionDeferred
DecisionEscalated
DecisionApproved
DecisionRejected
DecisionOverridden
DecisionExpired
DecisionSuperseded
DecisionCompleted

⸻

60. API

The engine SHOULD expose:

create_decision()
collect_options()
normalize_options()
evaluate_constraints()
evaluate_authority()
evaluate_values()
evaluate_goals()
evaluate_risk()
evaluate_uncertainty()
run_simulations()
compare_scenarios()
calculate_utility()
calculate_expected_value()
calculate_expected_regret()
calculate_pareto_frontier()
calculate_opportunity_cost()
calculate_reversibility()
calculate_value_of_information()
calculate_value_of_waiting()
calculate_sensitivity()
calculate_stability()
select_option()
request_clarification()
escalate()
override_decision()
explain_decision()
get_decision()
get_decision_trace()
invalidate_decision()

⸻

61. Decision Request

DecisionRequest {
    decision_id
    subject
    intent_ref
    goal_refs
    world_snapshot_ref
    candidate_options
    constraints
    authority_context
    value_context
    policy_refs
    evidence_refs
    scenario_refs
    simulation_refs
    deadline
    resource_budget
    required_confidence
    escalation_policy
}

⸻

62. Decision Result

DecisionResult {
    decision_id
    status
    selected_option
    rejected_options
    constraint_results
    authority_result
    risk_assessment
    value_assessment
    goal_alignment
    utility_scores
    pareto_frontier
    uncertainty
    confidence
    sensitivity
    stability
    opportunity_cost
    escalation
    rationale
    provenance
    expires_at
}

⸻

63. Security Threats

DEC-SEC-1

Utility manipulation.

DEC-SEC-2

Value poisoning.

DEC-SEC-3

Preference hallucination.

DEC-SEC-4

Constraint bypass.

DEC-SEC-5

Risk underestimation.

DEC-SEC-6

Confidence inflation.

DEC-SEC-7

Simulation result manipulation.

DEC-SEC-8

Decision trace tampering.

DEC-SEC-9

Goal hijacking.

DEC-SEC-10

Reward hacking.

DEC-SEC-11

Opportunity-cost blindness.

DEC-SEC-12

Human escalation abuse.

DEC-SEC-13

Decision laundering.

Decision laundering means:

Model recommends X
       ↓
Decision Engine accepts X
       ↓
System claims:
"Veda decided X objectively."

when the actual decision was driven by hidden or unauthorized assumptions.

The engine MUST prevent this.

⸻

64. Decision Integrity

A valid decision requires:

Valid World Context
+
Valid Goal
+
Valid Authority
+
Valid Constraints
+
Valid Options
+
Valid Evidence
+
Valid Evaluation

If any required component is invalid:

Decision = INVALID

or:

Decision = NEEDS_REVIEW

⸻

65. Constitutional Boundary

The Decision Engine MUST NOT decide:

What the Constitution should be.

It may only operate under:

Constitution

Likewise it MUST NOT decide:

What authority it deserves.

Authority is defined by RFC-0009 through RFC-0011 and governance policy.

⸻

66. Example

User goal:

Build Veda cheaply.

Candidate options:

A:
Buy expensive GPU server.
B:
Use used workstation + cloud escalation.
C:
Use phone only.

Evaluation:

A
Cost: HIGH
Capability: HIGH
Risk: MEDIUM
B
Cost: LOW
Capability: HIGH
Privacy: MEDIUM
Complexity: MEDIUM
C
Cost: VERY LOW
Capability: LOW

Simulation:

A → fastest development
B → best cost/capability ratio
C → severe compute limitations

Values:

Cost efficiency: HIGH
Capability: HIGH
Privacy: HIGH

Decision:

B

But:

Decision B
≠
Purchase equipment

The latter still requires:

Authorization
→ Action

⸻

67. Example: Destructive Action

Goal:

Free disk space.

Options:

A:
Delete old files.
B:
Archive files.
C:
Buy additional storage.

Simulation:

A:
    15% chance of deleting needed data
B:
    1% chance of recovery failure
C:
    almost zero data-loss risk

Even if A is cheapest:

A

may be rejected because:

Irreversibility
+
Risk
+
Value of data

dominate cost.

⸻

68. Example: Uncertainty

Suppose Veda needs to choose:

A or B

but user preference is unknown.

Instead of inventing:

User prefers A.

Veda evaluates:

Decision sensitivity:
    HIGH
Preference uncertainty:
    HIGH

Result:

NEEDS_CLARIFICATION

This is a valid decision outcome.

Not knowing is not a software bug.

⸻

69. Full Decision Loop

WORLD
  ↓
INTENT
  ↓
GOAL
  ↓
OPTIONS
  ↓
CONSTRAINTS
  ↓
AUTHORITY
  ↓
SCENARIOS
  ↓
SIMULATION
  ↓
RISK
  ↓
VALUES
  ↓
UTILITY
  ↓
DECISION
  ↓
ESCALATION
  ↓
AUTHORIZATION
  ↓
ACTION
  ↓
VERIFICATION
  ↓
REALITY

⸻

70. Invariants

DEC-1

Decision MUST be separate from Action.

DEC-2

Decision MUST NOT grant authority.

DEC-3

Decision MUST respect Constitution.

DEC-4

Hard constraints MUST be evaluated before optimization.

DEC-5

Unauthorized options MUST NOT become executable merely because utility is high.

DEC-6

Values MUST be explicit.

DEC-7

Value provenance MUST be preserved.

DEC-8

Inferred preferences MUST remain distinguishable from explicit preferences.

DEC-9

Unknown preferences MUST NOT be fabricated.

DEC-10

Goals MUST be represented explicitly.

DEC-11

Risk MUST include impact, not probability alone.

DEC-12

Tail risk MUST be representable.

DEC-13

Uncertainty MUST be explicit.

DEC-14

Simulation results MUST retain provenance.

DEC-15

Prediction MUST remain distinct from fact.

DEC-16

Reversibility MUST influence decision rigor.

DEC-17

Opportunity cost SHOULD be considered.

DEC-18

No-action MUST be a valid candidate option.

DEC-19

Waiting MAY be a valid candidate option.

DEC-20

Value of information SHOULD be representable.

DEC-21

Multi-objective decisions MUST support tradeoffs.

DEC-22

Dominated options MAY be pruned but MUST remain auditable.

DEC-23

Decision confidence MUST NOT equal outcome certainty.

DEC-24

Decision sensitivity SHOULD be measurable.

DEC-25

Decision stability SHOULD be measurable.

DEC-26

High-risk ambiguous decisions SHOULD escalate.

DEC-27

Human overrides MUST be auditable.

DEC-28

Decisions MUST expire when their assumptions become invalid.

DEC-29

Decision traces MUST be reconstructable.

DEC-30

The Decision Engine MUST never become its own authority.

⸻

71. Architectural Position

The completed Layer 9 becomes:

RFC-0021
Temporal Model
      ↓
RFC-0022
Causal Model
      ↓
RFC-0023
Future & Scenario Engine
      ↓
RFC-0024
Simulation & Counterfactual Engine
      ↓
RFC-0025
Value & Decision Engine

Together they form:

             WORLD
               │
        ┌──────┴──────┐
        ▼             ▼
      TIME          CAUSE
        │             │
        └──────┬──────┘
               ▼
          FUTURE SPACE
               │
               ▼
          SIMULATION
               │
               ▼
       POSSIBLE OUTCOMES
               │
               ▼
        VALUE + RISK
               │
               ▼
           DECISION
               │
               ▼
        AUTHORIZATION
               │
               ▼
            ACTION

⸻

72. Final Principle

A capable intelligence predicts what may happen.

A decision system determines what should be preferred under constraints.

An authority system determines what may actually be done.

Veda MUST keep these three separate:

Prediction
    ≠
Decision
    ≠
Authority

The Decision Engine therefore does not make Veda “free to do anything.”

It makes Veda capable of explaining:

"What choices exist?"
"Which are admissible?"
"What are their consequences?"
"What do we value?"
"What are we risking?"
"What are we giving up?"
"How certain are we?"
"Why is this option preferable?"
"Who must approve it?"

That is the boundary between an AI that merely produces answers and an AI architecture capable of making accountable decisions.