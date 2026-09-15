RFC-0024 — Simulation & Counterfactual Engine

Status: Draft
Layer: 9 — Future & Decision
Depends On: RFC-0002, RFC-0003, RFC-0004, RFC-0008, RFC-0012, RFC-0013, RFC-0014, RFC-0015, RFC-0016, RFC-0018, RFC-0020, RFC-0021, RFC-0022, RFC-0023, RFC-0026, RFC-0027
Scope: Core Cognitive Infrastructure

⸻

1. Abstract

RFC-0024 defines the Simulation & Counterfactual Engine of Veda.

The engine allows Veda to test:

* possible actions
* interventions
* plans
* policies
* failures
* alternative decisions
* future scenarios
* counterfactual worlds

before or without modifying the real World.

The fundamental transformation is:

Real World
    ↓
World Snapshot
    ↓
Simulation World
    ↓
Intervention
    ↓
Simulation
    ↓
Predicted World
    ↓
Outcome / Risk / Consequence

The Simulation Engine MUST NOT directly modify the authoritative World.

A simulation is a model of reality.

It is never reality itself.

⸻

2. Motivation

A sufficiently capable autonomous system cannot safely operate using:

Think → Act

Veda requires:

Think
  ↓
Predict
  ↓
Simulate
  ↓
Evaluate
  ↓
Authorize
  ↓
Act
  ↓
Observe
  ↓
Verify

Simulation provides a controlled environment in which Veda can evaluate consequences before committing an action.

Examples:

"If I delete these files, what breaks?"
"If I deploy this code, what services are affected?"
"If I spend this amount of money, what happens to my budget?"
"If I choose Provider A instead of Provider B, how does system performance change?"
"If Veda had chosen another recovery strategy, would the failure have been avoided?"
"If this policy had existed yesterday, would the incident still have occurred?"

These are fundamentally different from ordinary prediction.

Prediction asks:

What is likely to happen?

Counterfactual reasoning asks:

What would have happened under another condition?

Simulation asks:

What happens when we actually execute the modeled intervention inside an isolated world?

⸻

3. Core Principle

The Simulation Engine MUST preserve the separation:

Reality
≠
Observation
≠
Prediction
≠
Simulation
≠
Counterfactual

Therefore:

Simulation Result ≠ Verified Reality

and:

Predicted Outcome ≠ Actual Outcome

The actual World remains authoritative.

⸻

4. Design Goals

RFC-0024 MUST provide:

1. isolated simulation worlds
2. world snapshots
3. deterministic simulation where possible
4. stochastic simulation where necessary
5. intervention modeling
6. counterfactual reasoning
7. branching timelines
8. scenario comparison
9. risk estimation
10. uncertainty propagation
11. simulation provenance
12. reproducibility
13. resource limits
14. simulation fidelity tracking
15. sim-to-real gap detection
16. prediction calibration
17. safe sandbox execution
18. simulation rollback
19. simulation expiration
20. complete auditability

⸻

5. Non-Goals

The Simulation Engine MUST NOT:

* modify authoritative World state
* grant authority
* authorize actions
* bypass policy
* bypass human approval
* assume simulation correctness
* convert predictions into facts
* silently modify causal models
* silently modify Constitution
* execute unrestricted real-world actions
* expose secrets merely because they exist in the real World

Simulation supports decision-making.

It does not possess authority over reality.

⸻

6. Simulation World

A simulation operates on an isolated World representation.

Real World
    │
    └── Snapshot
          │
          ├── Simulation A
          ├── Simulation B
          ├── Simulation C
          └── Counterfactual D

Each simulation MUST have:

simulation_id
world_snapshot_ref
world_version
model_versions
causal_model_ref
temporal_model_ref
scenario_ref
assumptions
constraints
intervention
random_seed
fidelity
resource_budget
status
created_at
expires_at

⸻

7. World Snapshot

A simulation MUST begin from an explicit World snapshot.

Snapshot {
    snapshot_id
    world_version
    timestamp
    entities
    relationships
    states
    active_events
    temporal_context
    causal_context
    knowledge_refs
    evidence_refs
    policy_refs
    capability_context
}

The snapshot MUST be immutable.

If the real World changes while a simulation is running:

Real World ≠ Simulation World

The simulation MUST NOT silently update itself.

It may instead:

invalidate
refresh
branch
rebase

according to policy.

⸻

8. Simulation Levels

Veda SHOULD support multiple simulation fidelity levels.

Level 0 — Static Analysis

No execution.

Example:

Dependency analysis
Schema validation
Permission analysis
Resource estimation

⸻

Level 1 — Dry Run

Execute logic without committing effects.

Action
  ↓
Precondition check
  ↓
Predicted effects

Useful for:

* filesystem operations
* deployment plans
* configuration changes
* database migrations

⸻

Level 2 — Deterministic Sandbox

Execute the operation in an isolated environment.

Properties:

* fixed state
* fixed inputs
* fixed random seed
* deterministic scheduler where possible

Repeated execution SHOULD produce equivalent results.

⸻

Level 3 — Discrete Event Simulation

Model time-dependent events.

t0 → Event A
t1 → Event B
t2 → Event C
t3 → State transition

Useful for:

* queues
* workflows
* resource allocation
* infrastructure
* scheduling

⸻

Level 4 — Agent / Environment Simulation

Multiple agents interact inside the simulated World.

World
 ├── Veda
 ├── User
 ├── Agent A
 ├── Agent B
 └── Environment

Each agent operates according to its own capabilities, policies and objectives.

⸻

Level 5 — Probabilistic Simulation

The same simulation executes multiple times under varying stochastic conditions.

Run 1 → Outcome A
Run 2 → Outcome B
Run 3 → Outcome A
...
Run N → Outcome C

Produces:

P(outcome)
confidence
variance
risk distribution

⸻

Level 6 — High-Fidelity Digital Twin

A synchronized model of a real system.

Examples:

Computer environment
Network
Software architecture
Home infrastructure
Financial system
Personal workflow

Digital twins MUST maintain explicit fidelity and synchronization metadata.

⸻

9. Intervention

An intervention represents a deliberate modification of the simulated World.

Intervention {
    intervention_id
    target
    operation
    value
    start_time
    duration
    actor
    assumptions
    authority_ref
    source
}

Example:

target = deployment.version
operation = SET
value = v2.4.0

The intervention applies only to the simulation unless explicitly authorized for real execution through the normal Action pipeline.

⸻

10. Observation vs Intervention

Veda MUST distinguish:

Observe(X = 1)

from:

Do(X = 1)

Observation means:

"We observed X."

Intervention means:

"We deliberately set X."

This distinction is essential for causal reasoning.

⸻

11. Counterfactual Model

A counterfactual compares:

Actual World

against:

Alternative World

Structure:

          Actual World
              │
              │
        Divergence Point
          /           \
         /             \
 Actual Path       Counterfactual Path
      │                  │
      ↓                  ↓
 Outcome A          Outcome B

Counterfactual object:

Counterfactual {
    counterfactual_id
    baseline_world_ref
    intervention
    divergence_point
    alternative_world_ref
    predicted_outcome
    actual_outcome_ref
    difference
    uncertainty
    assumptions
    causal_model_ref
    confidence
    status
}

⸻

12. Counterfactual Questions

The engine MUST support questions such as:

What if X had happened?
What if X had not happened?
What if Veda had selected Action A?
What if Veda had selected Action B?
What would have happened if the failure had been detected earlier?
Would the goal have been achieved?
Would the incident have occurred?
Which intervention would have produced the best outcome?

⸻

13. Baseline World

Every counterfactual SHOULD define a baseline.

Baseline:
    World W0
    Actual Action A
    Actual Outcome Oa

Alternative:

Counterfactual:
    World W0
    Alternative Action B
    Predicted Outcome Ob

Comparison:

Δ = Ob - Oa

The meaning of Δ MUST be domain-specific.

⸻

14. Simulation Branching

Simulation worlds MAY branch.

W0
├── A
│   ├── A1
│   └── A2
│
├── B
│   ├── B1
│   └── B2
│
└── C

Branches MUST retain:

* parent simulation
* divergence point
* inherited state
* intervention
* model version
* random seed
* provenance

Branches MUST NOT overwrite their parent.

⸻

15. Stochastic Simulation

When uncertainty exists, Veda SHOULD use repeated simulations.

Monte Carlo:
    simulate(World, Action, seed=1)
    simulate(World, Action, seed=2)
    ...
    simulate(World, Action, seed=N)

Output:

mean
median
variance
quantiles
probability_distribution
tail_risk
confidence_interval

The engine MUST record the number of runs.

A probability generated from one deterministic guess MUST NOT be presented as statistical certainty.

⸻

16. Randomness

Every stochastic simulation MUST track:

random_seed
random_source
distribution_versions
sampling_method
run_count

When reproducibility is required:

same World
+
same models
+
same inputs
+
same seed
+
same simulation version
=
reproducible result

⸻

17. Simulation Fidelity

Every simulation MUST declare its fidelity.

Fidelity {
    level
    dimensions
    assumptions
    omitted_variables
    approximations
    validation_refs
    known_limitations
}

Example:

Software deployment simulation:
Filesystem: HIGH
Process behavior: HIGH
Network behavior: MEDIUM
External API behavior: LOW
Human response: UNKNOWN

Veda MUST NOT hide omitted variables.

⸻

18. Sim-to-Real Gap

The engine MUST track:

Predicted World
        ↓
Real World
        ↓
Difference

This produces:

SimToRealGap {
    simulation_ref
    actual_world_ref
    predicted_state
    actual_state
    differences
    magnitude
    causes
    uncertainty
}

Possible causes:

* incorrect causal model
* missing variable
* stale knowledge
* external event
* stochastic variance
* model error
* simulation bug
* incorrect assumption
* environment drift

⸻

19. Simulation Validation

Simulation quality MUST be evaluated independently.

Validation levels:

V0 — No validation
V1 — Structural validation
V2 — Historical validation
V3 — Predictive validation
V4 — Intervention validation
V5 — High-fidelity validation

Higher-impact decisions SHOULD require stronger validation.

⸻

20. Simulation Confidence

Confidence MUST NOT be equivalent to simulation fidelity.

Example:

Simulation confidence: 0.91
Fidelity: LOW

This means:

"The model is internally confident."
NOT:
"The model accurately represents reality."

Both fields MUST remain separate.

⸻

21. Assumption Registry

Every simulation MUST maintain explicit assumptions.

Assumption {
    assumption_id
    statement
    source
    confidence
    impact
    sensitivity
    status
}

Examples:

Assume API remains available.
Assume user responds within 10 minutes.
Assume disk usage remains below 80%.
Assume external price does not change.

Critical assumptions SHOULD be tested through sensitivity analysis.

⸻

22. Sensitivity Analysis

Veda SHOULD determine which assumptions have the greatest effect.

Parameter A → small outcome change
Parameter B → huge outcome change
Parameter C → negligible change

Output:

SensitivityRanking

This allows Veda to identify:

"What do we need to know better before acting?"

⸻

23. Risk Estimation

Simulation SHOULD produce:

risk_probability
impact
expected_loss
worst_case
best_case
tail_risk
uncertainty
reversibility

Risk MUST NOT be reduced to a single number.

For example:

Action A:
    5% catastrophic failure
Action B:
    35% minor failure

A naive average could choose incorrectly.

Decision systems MUST consider severity and distribution.

⸻

24. Outcome Model

Every simulation produces predicted outcomes.

PredictedOutcome {
    outcome_id
    simulation_ref
    target
    expected_state
    probability
    confidence
    uncertainty
    conditions
    verification_method
}

A predicted outcome MUST NOT become World state.

Only actual verified observation can update the authoritative World.

⸻

25. Simulation vs Reality Boundary

The following boundary MUST be enforced:

                 ┌─────────────────────┐
                 │     REAL WORLD      │
                 └──────────┬──────────┘
                            │
                       Snapshot
                            ↓
                 ┌─────────────────────┐
                 │ SIMULATION SANDBOX  │
                 └──────────┬──────────┘
                            │
                       Prediction
                            ↓
                 ┌─────────────────────┐
                 │ DECISION / PLANNER  │
                 └──────────┬──────────┘
                            │
                      Authorization
                            ↓
                 ┌─────────────────────┐
                 │   REAL EXECUTION    │
                 └─────────────────────┘

Simulation MUST NEVER jump directly into:

Simulation → Real World

There must always be an authorization-controlled Action boundary.

⸻

26. Side Effect Isolation

Simulation environments MUST prevent unintended effects.

Examples:

filesystem sandbox
network sandbox
database clone
container isolation
virtual machine
mock API
fake credentials
synthetic devices

Simulation SHOULD use synthetic credentials whenever possible.

Production credentials MUST NOT automatically become available inside simulations.

⸻

27. Capability Isolation

A simulation MUST NOT inherit unrestricted real capabilities.

For example:

Real capability:
    filesystem.write
Simulation capability:
    sandbox.filesystem.write

Likewise:

network.send

becomes:

simulation.network.mock_send

unless explicitly permitted by policy.

⸻

28. Resource Budgets

Every simulation MUST have limits:

CPU
RAM
GPU
storage
network
execution_time
number_of_branches
number_of_runs
reasoning_iterations

Simulation MUST terminate when its budget is exhausted.

⸻

29. Simulation Lifecycle

REQUESTED
    ↓
VALIDATING
    ↓
SNAPSHOTTING
    ↓
INITIALIZING
    ↓
READY
    ↓
RUNNING
    ↓
PAUSED
    ↓
RUNNING
    ↓
COMPLETED

Alternative terminal states:

FAILED
CANCELLED
EXPIRED
RESOURCE_EXHAUSTED
INVALIDATED

⸻

30. Counterfactual Lifecycle

REQUESTED
    ↓
BASELINE_RESOLVED
    ↓
INTERVENTION_DEFINED
    ↓
WORLD_CLONED
    ↓
SIMULATING
    ↓
OUTCOME_PREDICTED
    ↓
COMPARING
    ↓
EVALUATED

Terminal:

COMPLETED
FAILED
INVALIDATED
INSUFFICIENT_MODEL
INSUFFICIENT_EVIDENCE

⸻

31. Scenario Integration

RFC-0023 generates possible futures.

RFC-0024 tests them.

Future Engine
     ↓
Scenario A
Scenario B
Scenario C
     ↓
Simulation Engine
     ↓
Simulated Outcomes
     ↓
Scenario Evaluation

Therefore:

RFC-0023 = What futures could exist?
RFC-0024 = What happens inside each modeled future?

⸻

32. Planner Integration

Planner:

Goal
 ↓
Candidate Plans

Simulation:

Candidate Plan A
Candidate Plan B
Candidate Plan C
        ↓
Simulation
        ↓
Predicted Outcomes
        ↓
Risk / Cost / Success Probability

The Planner MAY use these results.

The Simulation Engine MUST NOT select or authorize the final plan.

⸻

33. Decision Engine Integration

Decision flow:

Candidates
    ↓
Simulation
    ↓
Risk
    ↓
Expected Outcomes
    ↓
Value Engine
    ↓
Decision

The Decision Engine MUST retain simulation provenance.

⸻

34. Verification Integration

After real execution:

Simulation Prediction
        ↓
Real Execution
        ↓
Observed Outcome
        ↓
Verification
        ↓
Compare

This enables:

Prediction Error

which becomes learning data.

⸻

35. Calibration

Veda SHOULD maintain historical calibration.

Example:

Predicted success:
    80%
Actual success:
    52%

Veda SHOULD recognize:

"This class of simulations is overconfident."

Calibration data MUST NOT silently modify simulation models.

Changes proceed through the Learning/Evolution architecture.

⸻

36. Simulation Provenance

Every result MUST answer:

Which World snapshot?
Which causal model?
Which temporal model?
Which knowledge?
Which evidence?
Which model versions?
Which assumptions?
Which intervention?
Which seed?
Which simulator version?
Which configuration?
Which policy?

This enables complete reconstruction.

⸻

37. Simulation Artifact

A completed simulation SHOULD produce:

SimulationArtifact {
    simulation_id
    input_snapshot
    configuration
    model_versions
    assumptions
    interventions
    execution_trace
    branches
    predicted_states
    outcomes
    risks
    confidence
    fidelity
    validation
    random_seeds
    resource_usage
    provenance
}

Artifacts SHOULD be immutable after completion.

Corrections create new versions.

⸻

38. Replay

A simulation SHOULD be replayable.

Artifact
   +
same inputs
   +
same configuration
   ↓
Replay

Replay MUST identify divergence.

Expected trajectory
        vs
Replay trajectory

If divergence occurs:

ReplayMismatch

MUST be generated.

⸻

39. Determinism

Where deterministic simulation is possible:

Deterministic simulation SHOULD be preferred.

Where stochastic behavior is required:

Explicit randomness MUST be preferred over hidden randomness.

⸻

40. Branch Management

To prevent scenario explosion:

max_depth
max_branches
max_runs
pruning_policy
dominance_policy
probability_threshold
resource_budget

Branches with negligible probability or dominated outcomes MAY be pruned.

Pruning MUST be auditable.

⸻

41. Dominance

If:

Scenario A

is worse than:

Scenario B

across all relevant dimensions under the same assumptions, A MAY be marked:

DOMINATED

It MUST NOT be deleted.

⸻

42. Unknowns

Simulation MUST support:

UNKNOWN

instead of forcing a prediction.

Example:

External human response:
    UNKNOWN

This is preferable to:

Human response:
    73.4%

when no defensible model exists.

⸻

43. Human Behavior

Human behavior is especially difficult to simulate.

Veda SHOULD distinguish:

known behavioral model
historical pattern
probabilistic estimate
LLM-generated hypothesis
unknown

LLM-generated human behavior MUST NOT automatically be treated as empirical evidence.

⸻

44. Model Composition

A simulation MAY combine:

deterministic rules
+
causal model
+
statistical model
+
physical model
+
agent model
+
LLM reasoning

Each component MUST declare its role.

For example:

Physics model → environment
Causal model → intervention propagation
LLM → agent decision
Statistical model → uncertainty

No component automatically becomes authoritative merely because it is sophisticated.

⸻

45. Simulation Security

Threats include:

SIM-SEC-1

Sandbox escape.

SIM-SEC-2

Real credential leakage.

SIM-SEC-3

Network side effects.

SIM-SEC-4

Simulation resource exhaustion.

SIM-SEC-5

Malicious model injection.

SIM-SEC-6

False confidence.

SIM-SEC-7

Poisoned causal model.

SIM-SEC-8

Hidden simulation assumptions.

SIM-SEC-9

Prediction laundering.

Meaning:

"Simulation says X"

being incorrectly transformed into:

"X is true."

SIM-SEC-10

Authorization bypass.

⸻

46. Prediction Laundering Prevention

Veda MUST preserve epistemic status:

REAL
OBSERVED
VERIFIED
PREDICTED
SIMULATED
COUNTERFACTUAL
HYPOTHESIZED
UNKNOWN

A simulated result MUST remain:

SIMULATED

until independently verified.

⸻

47. Audit Events

The engine MUST emit events including:

SimulationRequested
SimulationValidated
WorldSnapshotCreated
SimulationCreated
SandboxInitialized
InterventionDefined
InterventionApplied
SimulationStarted
SimulationPaused
SimulationResumed
SimulationBranched
SimulationCompleted
SimulationFailed
SimulationCancelled
SimulationExpired
CounterfactualCreated
CounterfactualCompared
OutcomePredicted
RiskEstimated
FidelityEvaluated
SensitivityCalculated
ReplayStarted
ReplayMismatchDetected
PredictionVerified
PredictionFailed
SimToRealGapDetected
SimulationInvalidated
SimulationPruned

⸻

48. API

The Simulation Engine SHOULD expose:

create_simulation()
validate_simulation()
snapshot_world()
clone_world()
initialize_sandbox()
apply_intervention()
remove_intervention()
run()
pause()
resume()
cancel()
branch()
fork()
simulate_scenario()
simulate_plan()
simulate_action()
create_counterfactual()
evaluate_counterfactual()
compare_outcomes()
estimate_risk()
estimate_probability()
calculate_sensitivity()
evaluate_fidelity()
validate_model()
replay()
compare_replay()
get_result()
get_artifact()
get_trace()
destroy_sandbox()
get_active_simulations()
get_resource_usage()

⸻

49. Simulation Request

Example:

SimulationRequest {
    request_id
    world_snapshot_ref
    scenario_ref
    action_ref
    plan_ref
    intervention
    simulation_level
    temporal_horizon
    assumptions
    constraints
    fidelity_requirements
    run_count
    random_seed
    resource_budget
    timeout
    output_requirements
    validation_requirements
    requester
}

⸻

50. Simulation Result

SimulationResult {
    simulation_id
    status
    final_state
    trajectory
    predicted_events
    predicted_outcomes
    risks
    opportunities
    uncertainty
    confidence
    fidelity
    assumptions
    sensitivity
    resource_usage
    provenance
    verification_plan
}

⸻

51. Failure Handling

If simulation fails:

FAIL
 ↓
DIAGNOSE
 ↓
CLASSIFY

Possible causes:

MODEL_FAILURE
RESOURCE_FAILURE
SANDBOX_FAILURE
INPUT_FAILURE
CONSTRAINT_FAILURE
UNKNOWN_DEPENDENCY
TIMEOUT
SECURITY_VIOLATION

Recovery:

retry
reduce fidelity
reduce scope
change simulator
change provider
request missing information
abort
escalate

A failed simulation MUST NOT be interpreted as evidence that the real action failed.

⸻

52. Simulation Expiration

A simulation becomes stale when:

World changed significantly
Model changed
Knowledge changed
Policy changed
Causal model changed
Temporal assumptions expired
External dependency changed

Status:

STALE

A stale simulation MUST NOT automatically authorize an action.

⸻

53. Real-Time Simulation

For rapidly changing environments:

World(t)
   ↓
Snapshot
   ↓
Simulation
   ↓
Prediction

If:

World(t+1)

changes materially before execution:

Simulation → STALE

The system SHOULD re-simulate.

⸻

54. Irreversible Actions

High-impact or irreversible actions SHOULD require simulation when technically possible.

Examples:

delete critical data
deploy destructive migration
change security configuration
transfer significant funds
modify identity/permissions
destroy infrastructure
publish irreversible external content

If simulation is impossible:

Decision Engine
+
Risk Engine
+
Authorization
+
Human approval

MAY still be required.

⸻

55. Simulation Gate

For high-risk actions:

Action
 ↓
Simulation Available?
 ├── YES
 │    ↓
 │  Simulate
 │    ↓
 │  Risk Acceptable?
 │    ├── YES → Authorization
 │    └── NO  → Block / Review
 │
 └── NO
      ↓
   Escalate

⸻

56. Relationship With RFC-0022

RFC-0022 provides:

Causal Model

RFC-0024 consumes it.

Causal Model
      ↓
Intervention
      ↓
Simulation
      ↓
Counterfactual Outcome

Simulation MUST NOT silently invent causal relationships.

If required relationships are missing:

INSUFFICIENT_CAUSAL_MODEL

MUST be returned.

⸻

57. Relationship With RFC-0023

RFC-0023:

Generate possible futures

RFC-0024:

Execute modeled futures

Together:

Current World
      ↓
Future Engine
      ↓
Possible Futures
      ↓
Simulation Engine
      ↓
Simulated Futures
      ↓
Decision

⸻

58. Relationship With RFC-0026

RFC-0026 determines whether actual outcomes satisfy expected outcomes.

Simulation:
    "Expected X"

Actual:

Reality:
    "Observed Y"

Verification:

X ≠ Y

Therefore:

Prediction Error

is generated.

⸻

59. Learning Loop

Simulation results become learning data.

Simulation
    ↓
Prediction
    ↓
Real Action
    ↓
Observed Outcome
    ↓
Prediction Error
    ↓
Experience
    ↓
Reflection
    ↓
Learning

This creates a self-improving prediction system.

⸻

60. Constitutional Constraints

Simulation MUST obey RFC-0001.

Specifically:

Simulation ≠ Authority
Simulation ≠ Authorization
Simulation ≠ Reality
Prediction ≠ Truth

No simulation result may override:

Constitution
Policy
Human authority
Safety constraints
Capability restrictions

⸻

61. Invariants

SIM-1

Simulation MUST operate on an isolated World.

SIM-2

Simulation MUST NOT directly mutate authoritative World state.

SIM-3

Every simulation MUST reference a World snapshot.

SIM-4

Every simulation MUST identify model versions.

SIM-5

Every simulation MUST identify assumptions.

SIM-6

Every intervention MUST be explicit.

SIM-7

Observation MUST remain distinct from intervention.

SIM-8

Simulation result MUST remain distinct from reality.

SIM-9

Prediction MUST remain distinct from verification.

SIM-10

Counterfactuals MUST identify their baseline.

SIM-11

Counterfactuals MUST identify their divergence point.

SIM-12

Branches MUST preserve parent provenance.

SIM-13

Randomness MUST be explicit.

SIM-14

Stochastic simulations MUST record seeds and run counts.

SIM-15

Simulation fidelity MUST be explicit.

SIM-16

Simulation confidence MUST NOT equal real-world correctness.

SIM-17

Unknown variables MUST be representable.

SIM-18

Missing causal structure MUST be reportable.

SIM-19

Simulation MUST NOT grant capabilities.

SIM-20

Simulation MUST NOT grant authorization.

SIM-21

Simulation MUST NOT bypass human approval.

SIM-22

Simulation environments MUST enforce resource limits.

SIM-23

Simulation environments MUST isolate production credentials.

SIM-24

Simulation side effects MUST be prevented or explicitly controlled.

SIM-25

Every completed simulation MUST have provenance.

SIM-26

Simulation results MUST be reproducible when deterministic mode is used.

SIM-27

Simulation branches MUST be independently traceable.

SIM-28

Stale simulations MUST NOT be treated as current evidence.

SIM-29

Real outcomes MUST be used to evaluate prediction accuracy.

SIM-30

Simulation MUST NEVER be treated as reality.

⸻

62. Core Data Flow

The complete flow is:

REAL WORLD
    │
    ▼
WORLD SNAPSHOT
    │
    ├──────────────┐
    ▼              ▼
TEMPORAL MODEL   CAUSAL MODEL
    │              │
    └──────┬───────┘
           ▼
     SIMULATION WORLD
           │
           ▼
      INTERVENTION
           │
           ▼
       SIMULATOR
           │
      ┌────┴────┐
      ▼         ▼
   Branch A   Branch B
      │         │
      ▼         ▼
 Outcome A   Outcome B
      │         │
      └────┬────┘
           ▼
      COMPARISON
           │
           ▼
       RISK/VALUE
           │
           ▼
        DECISION
           │
           ▼
      AUTHORIZATION
           │
           ▼
       REAL ACTION
           │
           ▼
        REALITY
           │
           ▼
       VERIFICATION
           │
           ▼
   SIM-TO-REAL GAP
           │
           ▼
        LEARNING

⸻

63. Example

Suppose Veda wants to deploy a new software version.

Planner produces:

Plan A:
    Deploy v2

Simulation creates:

World Snapshot:
    production = v1

Intervention:

production.version = v2

Simulation predicts:

API latency +8%
error rate +1%
database load +15%

Alternative:

Plan B:
    staged deployment

Simulation predicts:

latency +3%
error rate +0.2%
database load +5%

Decision Engine can now compare:

Plan A
Plan B

But neither simulation result changes production.

Only after:

Decision
→ Authorization
→ Action

does reality change.

⸻

64. Why This Matters for Veda

Without simulation:

Veda:
"ฉันคิดว่าน่าจะปลอดภัย"

With simulation:

Veda:
"จาก World snapshot W1842
และ causal model C71
ฉันจำลอง 10,000 trajectories
ผลลัพธ์:
82% success
15% degraded
3% critical failure
critical failure มีสาเหตุหลักจาก database load
ดังนั้นฉันแนะนำ staged deployment

That is a fundamentally different class of system.

It is no longer merely generating an answer.

It is constructing a model of a world, manipulating that model, comparing possible outcomes, then deciding whether reality should be changed.

⸻

65. Final Principle

If an action can be safely tested in a model, test it before testing it on reality.

But:

A simulation is still a model. It is not reality.

Therefore Veda MUST always preserve:

MODEL
   ≠
WORLD

and:

PREDICTION
   ≠
FACT

and:

SIMULATION
   ≠
AUTHORITY

This boundary is what prevents Veda from confusing its imagination with the world.