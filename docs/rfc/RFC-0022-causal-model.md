RFC-0022: Causal Model

* Status: Draft
* Layer: 9 — Future & Decision
* Depends On: RFC-0002, RFC-0003, RFC-0004, RFC-0012, RFC-0013, RFC-0014, RFC-0021
* Related: RFC-0023, RFC-0024, RFC-0025, RFC-0026, RFC-0035, RFC-0036, RFC-0037
* Scope: Causal representation, causal reasoning, causal inference, intervention, causal prediction, causal explanation

⸻

1. Abstract

RFC-0022 defines the causal model of Veda.

The Temporal Model answers:

WHEN did something happen?

The Causal Model answers:

WHY did something happen?
WHAT caused it?
WHAT would change if we changed something?
WHAT consequences are likely to follow an intervention?

Veda MUST distinguish:

Correlation
Temporal Order
Causation

These are not equivalent.

The core causal chain is:

Cause
  ↓
Mechanism
  ↓
Effect

However, real-world causality may involve:

multiple causes
multiple effects
confounders
mediators
feedback loops
conditional effects
uncertainty
hidden variables
interventions

Therefore Veda requires an explicit causal representation rather than relying only on statistical correlation or language-model inference.

⸻

2. Motivation

A system that cannot reason about causality is limited to describing events.

For example:

Server CPU increased.
Server crashed.

Temporal reasoning can establish:

CPU increase happened before crash.

But this does not prove:

CPU increase caused crash.

Possible explanations include:

CPU increase ← traffic spike ← external event
CPU increase ← runaway process
CPU increase ← memory pressure
Crash ← unrelated hardware failure

Veda must preserve these possibilities.

⸻

3. Core Principle

Veda MUST NOT infer causality solely from:

* temporal order
* correlation
* semantic similarity
* frequency
* model confidence
* textual statements

Causal claims require explicit evidence, mechanism, intervention, controlled observation, or sufficiently strong inference.

⸻

4. Causal Vocabulary

4.1 Cause

A variable, event, condition, action, or state capable of contributing to an effect.

⸻

4.2 Effect

A state, event, outcome, or change influenced by one or more causes.

⸻

4.3 Mechanism

The process through which a cause produces an effect.

Example:

High CPU
   ↓
thermal load
   ↓
hardware protection
   ↓
shutdown

⸻

4.4 Intervention

An intentional modification of a variable or condition.

Conceptually:

do(X = x)

An intervention differs from merely observing:

observe(X = x)

⸻

4.5 Confounder

A variable influencing both the apparent cause and effect.

Example:

Traffic
 ├──→ CPU usage
 └──→ Request latency

CPU usage and latency may correlate because of traffic.

⸻

4.6 Mediator

A variable through which a causal effect passes.

A
 ↓
M
 ↓
B

⸻

4.7 Feedback Loop

A causal cycle in which an effect influences a later state that influences the original variable.

A → B → C → A

Causal cycles MUST be explicitly represented rather than treated as ordinary DAGs.

⸻

5. Causal Model Representation

Veda SHOULD represent causality as a causal graph.

Conceptually:

CausalGraph {
    graph_id
    version
    nodes[]
    edges[]
    assumptions[]
    evidence_refs[]
    scope
    context
    temporal_scope
    confidence
    status
    created_by
    created_at
    updated_at
}

⸻

6. Causal Node

A causal node represents:

* variable
* state
* event
* condition
* action
* process
* outcome

Conceptual:

CausalNode {
    node_id
    type
    subject
    variable
    state
    scope
    temporal_scope
    observability
    controllability
    confidence
    evidence_refs[]
}

⸻

7. Causal Edge

A causal edge represents a causal relationship.

CausalEdge {
    edge_id
    source
    target
    relation
    strength
    confidence
    mechanism_refs[]
    evidence_refs[]
    conditions[]
    temporal_constraints[]
    status
    created_by
    created_at
}

Possible relations:

CAUSES
CONTRIBUTES_TO
INHIBITS
PREVENTS
ENABLES
MEDIATES
MODIFIES
AMPLIFIES
ATTENUATES

⸻

8. Causal Strength

Causal relationships may vary in strength.

Veda SHOULD distinguish:

weak
moderate
strong
deterministic
unknown

Causal strength MUST NOT be confused with confidence.

Example:

strength = strong
confidence = low

means:

If the relationship is real, the effect is strong, but evidence that the relationship exists is weak.

⸻

9. Causal Confidence

Confidence represents belief in the causal claim.

Example:

A → B
confidence = 0.82

This does not mean:

P(B) = 0.82

Nor does it mean:

82% of B is caused by A

The semantics MUST remain explicit.

⸻

10. Causal Evidence

Causal claims SHOULD reference evidence.

Evidence may include:

direct observation
controlled experiment
intervention
natural experiment
longitudinal observation
mechanistic evidence
statistical analysis
expert report
simulation
model inference
correlation

Evidence quality MUST be tracked.

⸻

11. Evidence Hierarchy

As a general heuristic:

Controlled intervention
        ↓
Strong natural experiment
        ↓
Repeated longitudinal evidence
        ↓
Mechanistic evidence
        ↓
High-quality observational evidence
        ↓
Statistical association
        ↓
Expert report
        ↓
Model inference
        ↓
Pure correlation

This is not an absolute ranking.

Context determines evidence quality.

⸻

12. Temporal Requirement

A causal relationship normally requires temporal compatibility.

If:

B occurs before A

then:

A → B

is generally impossible under ordinary causal semantics.

However, temporal precedence alone does not establish causation.

Therefore:

Temporal Precedence
      ≠
Causation

⸻

13. Correlation vs Causation

Veda MUST explicitly represent correlation separately from causation.

Example:

A correlates with B

does not automatically become:

A causes B

The knowledge model SHOULD preserve:

RELATION = CORRELATES_WITH

until stronger causal evidence exists.

⸻

14. Confounding

Veda SHOULD actively search for potential confounders.

Example:

A → B

may actually be:

      C
     / \
    ↓   ↓
    A   B

The system SHOULD maintain alternative causal hypotheses.

⸻

15. Alternative Causal Hypotheses

When causality is uncertain, Veda SHOULD maintain multiple hypotheses.

Example:

H1:
A causes B
H2:
C causes A and B
H3:
B causes A
H4:
A and C jointly cause B

These hypotheses MUST NOT be silently collapsed into one conclusion.

⸻

16. Causal Status

Causal claims SHOULD have states:

HYPOTHESIZED
PROPOSED
SUPPORTED
LIKELY
ESTABLISHED
DISPUTED
REJECTED
UNKNOWN
SUPERSEDED

ESTABLISHED should be reserved for sufficiently strong evidence.

⸻

17. Causal Lifecycle

OBSERVED
   ↓
ASSOCIATION_DETECTED
   ↓
CAUSAL_HYPOTHESIS
   ↓
EVIDENCE_EVALUATION
   ↓
INTERVENTION / ANALYSIS
   ↓
CAUSAL_ASSESSMENT
   ↓
SUPPORTED / DISPUTED / REJECTED
   ↓
VERIFIED

Not every relationship reaches VERIFIED.

⸻

18. Intervention

The most important distinction in causal reasoning is:

Observation

versus:

Intervention

Observation:

observe(X = 10)

Intervention:

do(X = 10)

An intervention changes the system intentionally.

Veda MUST represent this distinction.

⸻

19. Intervention Object

Intervention {
    intervention_id
    target
    previous_state
    target_state
    actor
    authority
    preconditions
    expected_effects
    causal_hypothesis_refs[]
    risk
    reversibility
    authorization
    action_ref
    verification_strategy
}

An intervention remains subject to:

* RFC-0009
* RFC-0010
* RFC-0011
* RFC-0008
* RFC-0026

⸻

20. Intervention vs Action

Not every action is a causal intervention.

Example:

Reading CPU usage

is observation.

Killing a process to reduce CPU

is an intervention.

An action becomes a causal intervention when its purpose includes changing a causal variable or testing a causal hypothesis.

⸻

21. Counterfactual Reasoning

Causal models enable questions such as:

What would have happened if A had not occurred?
What would happen if A changed?
What if B were increased?
Would the failure still occur?

Counterfactual execution belongs primarily to RFC-0024.

RFC-0022 defines the causal relationships required to support it.

⸻

22. Causal Prediction

Given:

A → B

Veda may predict:

If A changes,
B is likely to change.

The prediction MUST include:

causal_model_version
conditions
confidence
temporal_scope
assumptions
evidence

⸻

23. Conditions

Causal relationships may only apply under certain conditions.

Example:

A → B

may only be valid when:

C = true

Therefore causal edges SHOULD support conditions.

Conceptually:

A
 ↓
B
condition:
C = true

⸻

24. Nonlinearity

Causal effects may not be linear.

Example:

A increases
↓
small effect
A increases further
↓
large effect

Veda SHOULD allow causal relationships to specify:

thresholds
ranges
nonlinear functions
interaction effects

⸻

25. Interaction Effects

Two causes may jointly produce an effect.

A → B
C → B

does not necessarily imply:

A + C = independent effects

There may be:

A × C → B

Therefore interaction effects SHOULD be representable.

⸻

26. Necessary Causes

A cause may be necessary but not sufficient.

A required for B

but:

A alone does not guarantee B

The model SHOULD distinguish:

necessary
sufficient
necessary_and_sufficient
contributory

⸻

27. Preventive Causes

Causal reasoning must also represent prevention.

Example:

Firewall rule
   ↓
blocks malicious request

The absence of the attack is itself relevant.

Supported relation:

PREVENTS

⸻

28. Enabling Conditions

Some variables enable other causes.

Example:

Internet access
   ↓
allows
API request

This does not mean:

Internet access directly causes API request.

The relation may be:

ENABLES

⸻

29. Causal Mechanisms

Where possible, Veda SHOULD represent mechanisms.

Example:

High load
   ↓
CPU saturation
   ↓
request queue growth
   ↓
timeout
   ↓
service failure

A mechanism provides stronger explanatory value than:

High load correlates with failure.

⸻

30. Causal Chains

Veda SHOULD support causal paths.

Example:

A → B → C → D

It should be able to answer:

What caused D?

Possible result:

Direct cause:
C
Upstream causes:
B
A

The system MUST distinguish direct from indirect causes.

⸻

31. Root Cause

Root cause analysis MUST be treated carefully.

A system SHOULD NOT automatically label the earliest known cause as the root cause.

Example:

Configuration error
   ↓
memory leak
   ↓
CPU saturation
   ↓
service crash

Possible root causes include:

configuration error
deployment process
missing validation
organizational failure

depending on the analysis scope.

Therefore:

root_cause_scope

must be explicit.

⸻

32. Causal Responsibility

Causal contribution MUST NOT automatically imply moral, legal, or policy responsibility.

Example:

A contributed to B

does not mean:

A is responsible for B

Responsibility is a separate concept.

⸻

33. Causal Attribution

Veda SHOULD distinguish:

causal attribution
responsibility
blame

The Causal Model concerns the first.

⸻

34. Causal Scope

A causal relationship MUST specify scope where necessary.

Example:

A causes B

may only apply to:

machine X
environment Y
software version Z
temperature range T

Scope MUST be preserved.

⸻

35. Temporal Scope

Causal relationships may change over time.

Example:

Software version 1:
A → B
Software version 2:
A → C

Therefore causal claims MUST support temporal validity.

⸻

36. Causal Drift

Real systems evolve.

A previously valid causal relationship may weaken or disappear.

Veda SHOULD detect:

causal drift
mechanism change
environment change
system change
model mismatch

Old causal knowledge MUST NOT be treated as universally permanent.

⸻

37. Causal Conflict

Two causal models may disagree.

Example:

Model A:
A → B
Model B:
C → B

This is not necessarily a contradiction.

Both may be true.

RFC-0014 SHOULD manage competing causal hypotheses.

⸻

38. Causal Cycles

Some systems contain feedback.

Example:

A → B
B → C
C → A

Veda MUST support causal cycles.

For cyclic systems, the model SHOULD identify:

cycle_id
nodes
direction
feedback_type
stability
conditions

⸻

39. Feedback Systems

Feedback may be:

positive
negative
reinforcing
balancing
delayed

The Temporal Model MUST be used to represent delay.

Example:

A
↓
B
↓ after delay
A

⸻

40. Causal Delay

Effects may occur after a delay.

Cause:
10:00
Effect:
10:15

The causal relationship SHOULD support:

delay
minimum_delay
maximum_delay
expected_delay

⸻

41. Causal Uncertainty

Causal uncertainty MUST be explicit.

Example:

A → B
confidence = 0.65

Possible explanation:

Evidence incomplete
Confounders unresolved
Mechanism plausible
Intervention unavailable

The uncertainty MUST not be hidden behind fluent language.

⸻

42. Causal Evidence Update

When new evidence arrives:

Existing causal hypothesis
        ↓
New evidence
        ↓
Re-evaluation
        ↓
Confidence update

The previous state MUST remain auditable.

⸻

43. Causal Learning

Verified outcomes MAY improve causal models.

Example:

Hypothesis:
A causes B
Intervention:
change A
Observed:
B changes consistently
Result:
causal confidence increases

Learning must preserve:

evidence
intervention
context
outcome
model version

⸻

44. Causal Experimentation

Veda MAY propose experiments to distinguish hypotheses.

Example:

H1:
A → B
H2:
C → B

Experiment:

change A
hold C constant
observe B

The experiment itself requires:

* planning
* authorization
* safety checks
* execution
* verification

The Causal Engine does not bypass these systems.

⸻

45. Experiment Object

CausalExperiment {
    experiment_id
    hypotheses[]
    variables
    intervention
    controls
    expected_outcomes
    risks
    authorization
    observation_plan
    verification_plan
    results
    conclusions
    causal_model_version
}

⸻

46. Simulation

Causal models may be used by simulation.

Causal Model
     ↓
Simulation
     ↓
Possible Outcomes

Simulation belongs to RFC-0024.

Simulation results MUST NOT automatically become factual knowledge.

⸻

47. Causal Model and Planner

Planner may use causal knowledge to choose effective steps.

Example:

Goal:
Reduce CPU load.
Causal model:
Process X → CPU load.
Plan:
Reduce Process X workload.

But Planner must consider alternative causes.

⸻

48. Causal Model and Decision Engine

Decision Engine may compare interventions.

Example:

Intervention A:
High effectiveness
High risk
Intervention B:
Moderate effectiveness
Low risk

The Causal Model provides expected effects.

RFC-0025 determines value and trade-offs.

⸻

49. Causal Model and Verification

After an intervention:

Intervention
   ↓
Observed outcome
   ↓
Compare expected vs actual
   ↓
Update causal confidence

Verification belongs to RFC-0026.

⸻

50. Causal Observability

Veda SHOULD record:

causal hypothesis count
supported causal relationships
disputed relationships
intervention count
intervention success rate
prediction accuracy
causal drift
failed causal assumptions
confounding detections

These metrics can feed Self-Diagnostics.

⸻

51. Causal API

Conceptual interface:

CausalEngine {
    create_model()
    add_node()
    add_edge()
    remove_edge()
    evaluate_relationship()
    detect_confounders()
    generate_hypotheses()
    compare_hypotheses()
    identify_mechanism()
    identify_direct_causes()
    identify_upstream_causes()
    identify_downstream_effects()
    estimate_effect()
    propose_intervention()
    evaluate_intervention()
    analyze_counterfactual()
    detect_feedback()
    detect_causal_drift()
    update_model()
    explain_cause()
    get_model_version()
}

⸻

52. Causal Events

The following events SHOULD be supported:

CausalRelationDetected
CausalHypothesisCreated
CausalHypothesisUpdated
CausalHypothesisSupported
CausalHypothesisDisputed
CausalHypothesisRejected
ConfounderDetected
MediatorDetected
CausalMechanismProposed
CausalMechanismVerified
CausalInterventionProposed
CausalInterventionAuthorized
CausalInterventionExecuted
CausalInterventionVerified
CausalEffectObserved
CausalPredictionGenerated
CausalPredictionVerified
CausalPredictionFailed
CausalModelUpdated
CausalModelSuperseded
CausalDriftDetected
CausalConflictDetected
CausalExperimentCreated
CausalExperimentCompleted

⸻

53. Security Considerations

Incorrect causal reasoning can produce dangerous actions.

Threats include:

False causal attribution
Causal model poisoning
Adversarial evidence
Confounded observations
Manipulated experiments
Model hallucination
Feedback exploitation
Intervention abuse
Causal confidence inflation

High-impact causal claims SHOULD require stronger verification.

⸻

54. Causal Safety

Veda MUST NOT perform a real-world intervention merely because:

"The model predicts it will work."

Before execution:

Causal Hypothesis
      ↓
Risk Analysis
      ↓
Simulation
      ↓
Authorization
      ↓
Intervention
      ↓
Verification

must be respected.

⸻

55. Causal Invariants

CAUSAL-1

Temporal precedence MUST NOT be treated as proof of causality.

CAUSAL-2

Correlation MUST NOT automatically become causation.

CAUSAL-3

Causal claims MUST have provenance.

CAUSAL-4

Causal claims SHOULD reference evidence.

CAUSAL-5

Causal uncertainty MUST be representable.

CAUSAL-6

Alternative causal hypotheses MUST be representable.

CAUSAL-7

Confounders MUST be representable.

CAUSAL-8

Mediators MUST be representable.

CAUSAL-9

Causal mechanisms MUST be representable.

CAUSAL-10

Causal relationships MUST support temporal scope.

CAUSAL-11

Causal relationships MUST support contextual scope.

CAUSAL-12

Causal strength MUST be distinct from confidence.

CAUSAL-13

Direct causes MUST be distinguishable from indirect causes.

CAUSAL-14

Necessary causes MUST be distinguishable from sufficient causes.

CAUSAL-15

Preventive relationships MUST be representable.

CAUSAL-16

Enabling relationships MUST be representable.

CAUSAL-17

Causal delay MUST be representable.

CAUSAL-18

Feedback loops MUST be representable.

CAUSAL-19

Causal cycles MUST be representable.

CAUSAL-20

Causal drift MUST be detectable.

CAUSAL-21

Intervention MUST be distinguished from observation.

CAUSAL-22

Interventions MUST remain subject to authorization.

CAUSAL-23

Causal predictions MUST NOT become facts automatically.

CAUSAL-24

Simulation results MUST NOT automatically become verified knowledge.

CAUSAL-25

Historical causal models MUST remain auditable.

CAUSAL-26

Causal model updates MUST preserve provenance.

CAUSAL-27

Causal disagreement MUST be preserved rather than silently overwritten.

CAUSAL-28

High-impact causal claims SHOULD require stronger verification.

CAUSAL-29

Causal responsibility MUST NOT be inferred from causal contribution alone.

CAUSAL-30

Veda MUST distinguish what it observed from what it believes caused the observation.

⸻

56. Relationship With Future Architecture

The Causal Model forms the bridge:

World
  ↓
Temporal Model
  ↓
Causal Model
  ↓
Future Scenarios
  ↓
Simulation
  ↓
Value / Decision
  ↓
Action
  ↓
Verification
  ↓
Learning

This is one of the most important cognitive loops in Veda.

⸻

57. Final Principle

Veda must not merely remember that:

A happened.
Then B happened.

It must be able to represent:

A happened.
B happened afterward.
Evidence suggests A contributed to B.
The mechanism may be X.
The relationship applies under conditions Y.
Confidence is Z.
Alternative explanation C remains possible.
An intervention could test the hypothesis.
The intervention has risks.
The result can update the causal model.

The Causal Model therefore transforms Veda from a system that merely observes sequences into a system capable of reasoning about why the world changes.

Temporal order tells Veda when.
Causality gives Veda a model of why.