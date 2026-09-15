RFC-0023: Future & Scenario Engine

* Status: Draft
* Layer: 9 — Future & Decision
* Depends On: RFC-0002, RFC-0003, RFC-0004, RFC-0006, RFC-0013, RFC-0014, RFC-0016, RFC-0017, RFC-0018, RFC-0020, RFC-0021, RFC-0022
* Related: RFC-0024, RFC-0025, RFC-0026, RFC-0033, RFC-0034, RFC-0035, RFC-0036, RFC-0037
* Scope: Future-state generation, scenario modeling, uncertainty, forecasting, branching futures, horizon management, scenario comparison

⸻

1. Abstract

RFC-0023 defines the Future & Scenario Engine of Veda.

The engine transforms:

Current World
+
Temporal Model
+
Causal Model
+
Goals
+
Constraints
+
Knowledge
+
Uncertainty

into:

Possible Future States

The system MUST NOT represent the future as a single certain prediction.

Instead, it SHOULD construct multiple possible scenarios:

Current World
      ↓
Possible Futures
 ┌────┼────┐
 ↓    ↓    ↓
F1   F2   F3

Each scenario contains:

* assumptions
* causal conditions
* temporal horizon
* predicted events
* expected states
* uncertainty
* confidence
* risks
* opportunities
* dependencies
* evidence
* divergence points
* scenario probability where meaningful

The purpose is not merely to predict.

The purpose is to help Veda answer:

What could happen?
Why could it happen?
What would cause the futures to diverge?
What are the risks?
What opportunities exist?
What should we monitor?
What can we do now to influence the future?

⸻

2. Motivation

A traditional software system often assumes:

Current State
    ↓
Next State

Real environments are rarely deterministic.

Example:

Current:
Server is under heavy load.

Possible futures:

F1:
Load decreases → system remains healthy.
F2:
Load remains high → latency increases.
F3:
Load increases → service crashes.
F4:
Scaling succeeds → system stabilizes.
F5:
Scaling fails → outage occurs.

Veda needs to represent all meaningful possibilities rather than selecting one arbitrarily.

⸻

3. Core Principle

The future is not a fact.

A future representation MUST be distinguishable from:

Observed Reality
Verified Knowledge
Historical Memory
Current World State

Conceptually:

Past:
Evidence
Present:
State
Future:
Scenario

⸻

4. Design Goals

The Future Engine MUST support:

1. Multiple possible futures
2. Scenario branching
3. Temporal horizons
4. Causal propagation
5. Uncertainty
6. Assumptions
7. Conditions
8. Probabilistic outcomes where justified
9. Non-probabilistic scenarios
10. Best-case scenarios
11. Worst-case scenarios
12. Baseline scenarios
13. Intervention scenarios
14. Monitoring conditions
15. Divergence points
16. Scenario comparison
17. Scenario expiration
18. Recalculation
19. Prediction verification
20. Historical evaluation of predictions

⸻

5. Non-Goals

RFC-0023 does not define:

* final action selection
* authorization
* real-world execution
* full counterfactual simulation
* causal inference methodology
* final value optimization

Those belong to:

RFC-0022
RFC-0024
RFC-0025

respectively.

⸻

6. Future Representation

A future is represented as a scenario.

Conceptually:

Scenario {
    scenario_id
    version
    parent_scenario_id
    initial_world_ref
    temporal_horizon
    assumptions[]
    conditions[]
    causal_model_ref
    knowledge_refs[]
    evidence_refs[]
    predicted_events[]
    predicted_states[]
    divergence_points[]
    dependencies[]
    uncertainty
    confidence
    probability
    probability_status
    risks[]
    opportunities[]
    monitoring_conditions[]
    expected_outcome
    status
    created_at
    expires_at
}

⸻

7. Scenario

A scenario is a coherent possible future trajectory.

Example:

Scenario A
Current:
CPU = 80%
Assumption:
Traffic remains stable.
Future:
CPU gradually decreases.
Outcome:
System remains operational.

Another scenario:

Scenario B
Current:
CPU = 80%
Assumption:
Traffic increases.
Future:
CPU reaches saturation.
Outcome:
Service degradation.

Both may be valid scenarios.

⸻

8. Scenario Initial State

Every scenario MUST reference the world state from which it begins.

initial_world_ref

This prevents scenarios from silently starting from inconsistent states.

The initial world snapshot MUST be versioned.

⸻

9. Scenario Horizon

Every scenario MUST define a temporal horizon.

Examples:

Immediate:
0 → 5 minutes
Short:
5 minutes → 24 hours
Medium:
1 day → 30 days
Long:
1 month → 1 year
Strategic:
1 year+

These categories are configurable.

⸻

10. Scenario Time

The Future Engine MUST use RFC-0021.

Each predicted state or event SHOULD contain:

predicted_time
time_window
temporal_uncertainty

Example:

Service failure:
expected:
14:00
window:
13:30 → 15:00

⸻

11. Scenario Branching

Scenarios branch when meaningful uncertainty exists.

Current World
      |
      +---- F1
      |
      +---- F2
      |
      +---- F3

A branch SHOULD have an explicit divergence condition.

Example:

Traffic > threshold

causes:

Scenario A → Scenario B

⸻

12. Divergence Point

A divergence point represents a condition at which possible futures separate.

DivergencePoint {
    divergence_id
    condition
    parent_state
    branches[]
    expected_time
    uncertainty
    evidence_refs[]
}

Example:

If backup completes before 02:00:
    Scenario A
If backup fails:
    Scenario B

⸻

13. Scenario Tree

The Future Engine MAY represent futures as a tree.

Current
   |
   +-- A
   |    |
   |    +-- A1
   |    +-- A2
   |
   +-- B
        |
        +-- B1
        +-- B2

Scenario trees MUST be bounded.

Unbounded branching is computationally unsafe.

⸻

14. Scenario Graph

For complex systems, futures MAY be represented as a directed graph rather than a tree.

This allows shared future states.

A ──→ C
 \
  ──→ B ──→ C

The engine SHOULD avoid duplicating equivalent states unnecessarily.

⸻

15. Scenario Types

Veda SHOULD support at least:

BASELINE
BEST_CASE
WORST_CASE
EXPECTED
HIGH_RISK
LOW_RISK
OPTIMISTIC
PESSIMISTIC
INTERVENTION
NO_INTERVENTION
RECOVERY
FAILURE
ADVERSARIAL
EXPLORATORY

These are scenario categories, not guaranteed outcomes.

⸻

16. Baseline Scenario

The baseline represents the future under current assumptions and without major intervention.

Conceptually:

Current World
+
Current Trends
+
No Significant Change

It provides a reference point.

⸻

17. Best-Case Scenario

Represents a favorable future under explicit assumptions.

It MUST NOT be presented as likely merely because it is desirable.

⸻

18. Worst-Case Scenario

Represents a plausible adverse trajectory.

It MUST remain evidence-based.

The engine MUST avoid catastrophic fantasy generation without supporting conditions.

⸻

19. Expected Scenario

Represents the scenario currently judged most plausible under the available model.

It MUST retain uncertainty.

expected ≠ certain

⸻

20. Intervention Scenario

Represents a future after a hypothetical or planned intervention.

Example:

No scaling:
→ outage risk 30%
Scale infrastructure:
→ outage risk 8%

The intervention scenario must reference:

intervention
causal assumptions
constraints
expected effects

⸻

21. No-Intervention Scenario

A useful counterfactual baseline:

What happens if we do nothing?

This scenario is especially important for decision analysis.

⸻

22. Assumptions

Every scenario SHOULD explicitly declare assumptions.

Examples:

Traffic remains stable.
No hardware failure occurs.
User does not change the goal.
Network remains available.
Model remains operational.

Each assumption SHOULD have:

assumption_id
statement
confidence
evidence
impact
status

⸻

23. Assumption Sensitivity

The engine SHOULD identify which assumptions strongly influence scenario outcomes.

Example:

Assumption:
Traffic remains stable.
Impact:
HIGH

If the assumption changes, the scenario may become invalid.

⸻

24. Conditions

Scenarios MAY contain conditional branches.

Example:

IF
CPU > 90%
THEN
service degradation likely

Conditions SHOULD reference the causal model where possible.

⸻

25. Uncertainty

Uncertainty is first-class.

Scenario uncertainty may arise from:

unknown state
unknown causes
incomplete evidence
environmental variation
model limitations
future actions
external agents
measurement error

The engine MUST preserve the source of uncertainty.

⸻

26. Probability

Probability MAY be attached to scenarios when justified.

Example:

Scenario A:
probability = 0.60
Scenario B:
probability = 0.30
Scenario C:
probability = 0.10

However, the engine MUST NOT fabricate precise probabilities from weak evidence.

It may instead use:

LIKELY
POSSIBLE
UNLIKELY
UNKNOWN

when numerical probability is unjustified.

⸻

27. Probability Status

Every probability SHOULD declare its basis.

ProbabilityStatus:
EMPIRICAL
MODEL_BASED
STATISTICAL
SIMULATED
EXPERT_ESTIMATE
HEURISTIC
UNKNOWN

This prevents false mathematical authority.

⸻

28. Scenario Confidence

Confidence measures confidence in the scenario model.

It is distinct from:

probability

Example:

Probability:
0.70
Confidence:
LOW

means the scenario may be estimated as 70% under the model, but the model itself is unreliable.

⸻

29. Scenario Evidence

Every important scenario SHOULD reference:

knowledge
evidence
causal relationships
historical patterns
current observations

This allows later verification.

⸻

30. Scenario Provenance

The engine MUST preserve:

scenario creator
model version
knowledge version
causal model version
world snapshot
assumptions
timestamp
configuration

A future prediction without provenance is not useful for learning.

⸻

31. Scenario Prediction

Predictions SHOULD use explicit objects.

Prediction {
    prediction_id
    scenario_ref
    target
    predicted_state
    predicted_event
    time_window
    probability
    confidence
    assumptions[]
    evidence_refs[]
    generated_at
    expires_at
    status
}

⸻

32. Prediction Lifecycle

GENERATED
   ↓
ACTIVE
   ↓
MONITORED
   ↓
VERIFIED

Alternative outcomes:

EXPIRED
INVALIDATED
FAILED
PARTIALLY_CONFIRMED
SUPERSEDED

⸻

33. Prediction Verification

When the prediction horizon passes:

Prediction
    ↓
Observe Reality
    ↓
Compare
    ↓
Verified / Failed

The result SHOULD feed:

RFC-0026 Verification
RFC-0035 Experience
RFC-0036 Learning
RFC-0037 Evolution

⸻

34. Prediction Error

The engine SHOULD record:

predicted_state
actual_state
prediction_error
time_error
confidence
scenario
model_version

This allows Veda to learn where its future model is wrong.

⸻

35. Future State

A future state is a predicted world state.

Conceptually:

FutureState {
    state_id
    scenario_ref
    timestamp
    state_delta
    confidence
    uncertainty
    evidence_refs[]
}

The state MUST NOT be written into the actual World Model as though it already occurred.

⸻

36. Predicted Event

A predicted event represents a future event.

PredictedEvent {
    prediction_id
    event_type
    expected_time
    probability
    confidence
    conditions
    causes
    consequences
}

Predicted events remain predictions until observed.

⸻

37. Future World vs Actual World

Veda MUST maintain a strict boundary:

Actual World

versus:

Predicted World

Conceptually:

ActualWorld(t)
FutureScenario(t+n)

A scenario MUST NOT mutate actual world state.

⸻

38. Scenario Comparison

Veda SHOULD compare scenarios across:

expected outcome
risk
cost
time
reversibility
uncertainty
resource usage
goal satisfaction
failure probability
opportunity

The comparison itself does not choose the final action.

That belongs to RFC-0025.

⸻

39. Scenario Dominance

A scenario MAY dominate another under defined criteria.

Example:

Scenario A:
same benefit
lower risk
lower cost
faster completion

A may dominate B.

Dominance MUST use explicit criteria.

⸻

40. Opportunity Analysis

Future analysis SHOULD identify opportunities.

Example:

Current state:
unused GPU capacity
Possible future:
run batch training during idle period

Opportunity representation:

Opportunity {
    target
    window
    expected_benefit
    required_action
    risk
    confidence
}

⸻

41. Risk Analysis

Each scenario SHOULD contain risks.

Risk {
    risk_id
    event
    probability
    impact
    severity
    mitigation
    detection_signal
}

Risk probability MUST remain distinct from certainty.

⸻

42. Early Warning Signals

Scenarios SHOULD define observable signals that indicate which branch is becoming more likely.

Example:

Scenario:
service failure
Early signals:
CPU > 90%
memory > 85%
error rate > 5%

These signals can be connected to the Attention Engine.

⸻

43. Scenario Monitoring

The engine SHOULD continuously compare:

Predicted trajectory
vs
Observed trajectory

If divergence becomes significant:

Scenario becomes stale

and should be recalculated.

⸻

44. Scenario Staleness

A scenario becomes stale when:

world state changes
causal model changes
critical assumption changes
new evidence arrives
time horizon changes
goal changes
constraints change

Stale scenarios MUST NOT be silently used as current predictions.

⸻

45. Scenario Invalidation

A scenario may become invalid.

Example:

Assumption:
Server has backup network.
Reality:
Backup network does not exist.

The scenario SHOULD transition:

ACTIVE
 ↓
INVALIDATED

The reason MUST be recorded.

⸻

46. Recalculation

When significant changes occur:

World Change
   ↓
Scenario Impact Analysis
   ↓
Invalidate / Update
   ↓
Generate New Scenarios

Veda SHOULD avoid recomputing every scenario unnecessarily.

⸻

47. Scenario Caching

Scenario computation may be expensive.

The engine MAY cache scenarios.

Cache validity MUST depend on:

world version
causal model version
knowledge version
goal version
constraint version
temporal horizon

If these inputs change materially, cached scenarios may become invalid.

⸻

48. Scenario Branch Pruning

To control complexity, the engine MAY prune branches.

Pruning criteria may include:

negligible probability
dominated scenario
invalid assumptions
insufficient relevance
resource limits
horizon exceeded
duplicate state

Pruning MUST be auditable.

⸻

49. Scenario Explosion

Scenario branching can grow exponentially.

Example:

3 branches
→ 9
→ 27
→ 81
→ 243
→ ...

Therefore the engine MUST enforce:

maximum_depth
maximum_branches
maximum_compute
maximum_time
minimum_relevance

⸻

50. Scenario Horizon

Long-range predictions SHOULD have lower confidence unless supported by strong structural knowledge.

The engine SHOULD track:

forecast_horizon
model_horizon
confidence_decay

Longer horizon does not automatically mean lower confidence, but the system must explicitly model uncertainty growth where appropriate.

⸻

51. External Agents

Future states may depend on external actors.

Examples:

user
other people
companies
attackers
services
markets
weather
hardware

Veda cannot assume full control over them.

The model SHOULD represent:

controlled
partially_controlled
uncontrolled
unknown

variables.

⸻

52. Controlled Variables

A variable is controlled when Veda can directly modify it through authorized capabilities.

Example:

local process priority

⸻

53. Uncontrolled Variables

Example:

internet outage
external service failure
another person's decision

These should remain uncertain.

⸻

54. Partially Controlled Variables

Example:

cloud API availability

Veda may influence the probability but cannot guarantee the outcome.

⸻

55. Goal-Aware Future Modeling

Scenarios MAY be generated relative to a goal.

Example:

Goal:
Complete project by Friday.

The engine can analyze:

Scenario A:
on time
Scenario B:
1 day late
Scenario C:
blocked by dependency

Goal satisfaction remains evaluated separately.

⸻

56. Future and Attention

The Future Engine SHOULD notify the Attention Engine when:

important event approaching
deadline approaching
high-risk scenario increasing
scenario divergence detected
prediction fails
new opportunity appears

⸻

57. Future and Planner

Planner can use scenarios to create adaptive plans.

Scenario A
    ↓
Plan A
Scenario B
    ↓
Plan B

This enables conditional planning.

⸻

58. Future and Decision

Decision Engine receives scenario outcomes.

Example:

Action A:
Scenario outcomes:
good / medium / catastrophic
Action B:
Scenario outcomes:
medium / medium / low-risk

RFC-0025 evaluates which option best satisfies the objective under the value system.

⸻

59. Future and Simulation

RFC-0024 may simulate individual scenarios in greater detail.

Future Engine
    ↓
Scenario
    ↓
Simulation
    ↓
Predicted trajectory

The Future Engine creates possible futures.

Simulation tests their dynamics.

⸻

60. Scenario API

Conceptual interface:

FutureEngine {
    create_scenario()
    generate_scenarios()
    generate_baseline()
    generate_best_case()
    generate_worst_case()
    generate_intervention_scenario()
    identify_divergence_points()
    propagate_causal_effects()
    estimate_probability()
    assess_confidence()
    compare_scenarios()
    identify_risks()
    identify_opportunities()
    identify_early_signals()
    monitor_scenario()
    invalidate_scenario()
    refresh_scenario()
    prune_scenarios()
    verify_prediction()
    get_active_scenarios()
    get_future_horizon()
    explain_scenario()
}

⸻

61. Future Events

The following events SHOULD be supported:

ScenarioGenerationRequested
ScenarioGenerationStarted
ScenarioCreated
ScenarioBranched
DivergencePointDetected
ScenarioEvaluated
ScenarioRanked
ScenarioPruned
ScenarioInvalidated
ScenarioStale
ScenarioRefreshed
PredictionGenerated
PredictionMonitored
PredictionConfirmed
PredictionPartiallyConfirmed
PredictionFailed
PredictionExpired
PredictionSuperseded
EarlyWarningDetected
ScenarioRiskChanged
ScenarioOpportunityDetected
FutureModelUpdated
ForecastHorizonChanged

⸻

62. Failure Handling

Possible failures:

InsufficientEvidence
UnknownInitialState
CausalModelUnavailable
ScenarioExplosion
ResourceLimit
ModelConflict
TemporalConflict
InvalidAssumption
StaleWorld
PredictionInstability

The engine SHOULD respond by:

reduce horizon
reduce branches
request evidence
use simpler model
defer
escalate
mark uncertainty
abort

It MUST NOT invent missing information merely to complete a scenario.

⸻

63. Security Considerations

Future modeling introduces risks:

1. Adversarial scenario injection
2. Prediction poisoning
3. Manipulated assumptions
4. Probability manipulation
5. Scenario flooding
6. False urgency
7. Strategic deception
8. Long-horizon resource exhaustion
9. Prediction leakage
10. Autonomous action based on unverified forecasts

Forecasts MUST remain subordinate to:

Policy
Authorization
Verification
Human oversight

where required.

⸻

64. Audit Requirements

Every scenario SHOULD record:

scenario_id
world_snapshot
world_version
goal_ref
causal_model_version
knowledge_version
assumptions
constraints
horizon
model/provider
generation_time
probability_basis
confidence
pruning decisions
invalidation reason
verification outcome

This enables Veda to later answer:

"What did I predict?"
"Why did I predict it?"
"What assumptions did I use?"
"Was it correct?"
"Where was the model wrong?"

⸻

65. Learning From Prediction

Prediction outcomes SHOULD become Experience.

Prediction
    ↓
Reality
    ↓
Prediction Error
    ↓
Experience
    ↓
Reflection
    ↓
Learning

This connects:

RFC-0023
     ↓
RFC-0035
     ↓
RFC-0036
     ↓
RFC-0037

⸻

66. Scenario Metrics

The system SHOULD track:

scenario_generation_latency
scenario_count
branch_count
pruning_rate
prediction_accuracy
prediction_calibration
forecast_error
scenario_staleness
scenario_invalidation_rate
false_positive_rate
false_negative_rate
horizon_accuracy
risk_prediction_accuracy

Calibration is particularly important.

A system that says “90%” constantly and is correct only 55% of the time is not sophisticated. It is merely confident.

⸻

67. Scenario Calibration

If Veda repeatedly produces:

90% confidence

but succeeds only:

50%

the system MUST detect poor calibration.

Calibration metrics SHOULD influence:

model confidence
provider routing
future predictions
decision risk

⸻

68. Scenario Explainability

Veda SHOULD be able to explain:

Why was this scenario generated?
Which assumptions matter?
Which causal relationships drive it?
What evidence supports it?
What could cause it to fail?
What signals indicate the scenario is becoming likely?
What alternative scenarios exist?

Explanations MUST reference actual scenario structure.

They must not be fabricated narratives.

⸻

69. Human Review

High-impact future scenarios SHOULD support human review.

Examples:

financial loss
data destruction
security incident
major infrastructure change
irreversible action
high-risk external action

Human review does not turn the forecast into truth.

It provides governance over decisions based on the forecast.

⸻

70. Future World Isolation

Predicted states MUST be isolated from actual world state.

Conceptually:

Actual World Store
        |
        +---- Future Scenario Store

A scenario MUST NOT directly mutate:

actual world
memory
knowledge
identity
permissions

unless its results are explicitly verified and committed through the appropriate systems.

⸻

71. Scenario Versioning

Scenario changes MUST create versions.

Scenario v1
   ↓ new evidence
Scenario v2

The previous version remains auditable.

⸻

72. Scenario Reproducibility

Given equivalent:

world snapshot
knowledge version
causal model
configuration
provider version
parameters

Veda SHOULD be able to reproduce or explain why a scenario was generated.

⸻

73. Scenario Governance

The Future Engine MUST NOT:

* authorize itself
* execute interventions
* modify Constitution
* modify policy
* treat predictions as facts
* silently delete scenarios
* hide uncertainty
* fabricate probabilities
* bypass verification

Its responsibility is:

Generate
Represent
Compare
Monitor
Verify
Learn

not:

Command
Authorize
Execute

⸻

74. Invariants

FUTURE-1

A future scenario MUST be distinguishable from actual world state.

FUTURE-2

Every scenario MUST reference an initial world state.

FUTURE-3

Every scenario MUST have a temporal horizon.

FUTURE-4

Future predictions MUST contain uncertainty information.

FUTURE-5

Predictions MUST NOT automatically become facts.

FUTURE-6

Scenario assumptions MUST be explicit.

FUTURE-7

Scenario conditions MUST be explicit.

FUTURE-8

Scenario provenance MUST be preserved.

FUTURE-9

Scenario versions MUST be auditable.

FUTURE-10

Scenario branching MUST have explicit divergence conditions.

FUTURE-11

Scenario explosion MUST be bounded.

FUTURE-12

Scenario pruning MUST be auditable.

FUTURE-13

Probability MUST NOT be fabricated when evidence is insufficient.

FUTURE-14

Probability MUST be distinguishable from confidence.

FUTURE-15

Probability basis MUST be identifiable.

FUTURE-16

Long-horizon predictions MUST represent appropriate uncertainty.

FUTURE-17

Uncontrolled external variables MUST remain uncertain.

FUTURE-18

Controlled and uncontrolled variables MUST be distinguishable.

FUTURE-19

Stale scenarios MUST NOT silently remain active.

FUTURE-20

Invalidated scenarios MUST preserve their invalidation reason.

FUTURE-21

Prediction outcomes MUST be verifiable.

FUTURE-22

Prediction errors MUST be recordable.

FUTURE-23

Future states MUST NOT mutate actual world state.

FUTURE-24

Scenario computation MUST respect resource limits.

FUTURE-25

Causal assumptions MUST reference the causal model where applicable.

FUTURE-26

Temporal assumptions MUST reference the Temporal Model where applicable.

FUTURE-27

Scenario comparison MUST remain distinct from final decision selection.

FUTURE-28

Human review MUST be supported for high-impact scenarios.

FUTURE-29

Future predictions MUST remain subordinate to authorization and policy.

FUTURE-30

Veda MUST represent the future as a set of possible states, not as a single unquestionable prediction.

⸻

75. Relationship With Veda Cognitive Loop

RFC-0023 creates the future-modeling stage:

Reality
   ↓
World Model
   ↓
Temporal Model
   ↓
Causal Model
   ↓
Future & Scenario Engine
   ↓
Simulation
   ↓
Decision
   ↓
Plan
   ↓
Action
   ↓
Verification
   ↓
Reality

This forms the foundation for closed-loop agency.

⸻

76. Final Principle

Veda should not ask:

"What will happen?"

as though the universe owes it a single answer.

It should ask:

"What futures are possible?"
"What assumptions separate them?"
"What causes the branches?"
"Which future is becoming more likely?"
"What can we do to influence the outcome?"
"What should we monitor?"
"What happens if our assumptions are wrong?"

The Future & Scenario Engine therefore transforms Veda from a system that merely understands the present into a system capable of reasoning over possible futures while preserving uncertainty.

The World Model describes where Veda is.
The Temporal Model describes when.
The Causal Model describes why.
The Future Engine describes where the world could go.