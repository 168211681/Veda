RFC-0016 — Intelligence Router

Status: Draft
Version: 0.1.0
Layer: 6 — Intelligence
Module: Intelligence Routing / Model Selection
Depends on: RFC-0013, RFC-0014, RFC-0015
Path: docs/rfc/RFC-0016-intelligence-router.md

⸻

1. Abstract

RFC-0016 defines the Intelligence Router of Veda.

The Intelligence Router determines which Intelligence Provider should perform a given cognitive task.

Veda MAY have access to:

* local language models
* cloud frontier models
* coding models
* vision models
* speech models
* embedding models
* search systems
* deterministic algorithms
* specialist models
* human judgment
* composite intelligence systems

The router MUST select providers based on task requirements rather than provider popularity.

The routing decision MAY consider:

Task
Capability
Quality
Confidence
Risk
Privacy
Latency
Cost
Hardware
Availability
Context Size
Reliability
Energy
Specialization
Previous Performance

The router MUST NOT have authority to bypass Veda’s authorization, capability, or verification systems.

⸻

2. Motivation

A multi-model Veda creates a new problem.

If Veda has:

Model A
Model B
Model C
Model D
Model E

the question becomes:

Which model should perform this task?

A naive architecture might use:

Task → Best Model

This is insufficient.

The best model for one task may be terrible for another.

Example:

Coding
→ Coding Specialist
Private reasoning
→ Local Model
Complex reasoning
→ Frontier Model
Image analysis
→ Vision Model
Simple classification
→ Small Local Model

Therefore Veda needs an explicit routing layer.

⸻

3. Core Principle

The router selects intelligence.

It does NOT select authority.

Router
  ↓
Provider
  ↓
Output
  ↓
Veda Control Systems

The router MUST NOT be able to:

* authorize itself
* expand capability scope
* access unrestricted secrets
* bypass policy
* directly modify the World
* approve its own output
* modify the Constitution

⸻

4. Definitions

4.1 Routing

Routing is the process of selecting one or more intelligence providers for a task.

⸻

4.2 Candidate Provider

A provider that satisfies the minimum requirements of the task.

⸻

4.3 Routing Policy

Rules determining which providers are eligible and how they should be ranked.

⸻

4.4 Routing Decision

A structured decision selecting the provider configuration for a task.

⸻

4.5 Provider Fitness

A measurement of how suitable a provider is for a specific task.

Provider fitness is task-dependent.

⸻

5. Routing Pipeline

Canonical pipeline:

Intelligence Task
       ↓
Task Analysis
       ↓
Requirement Extraction
       ↓
Candidate Discovery
       ↓
Eligibility Filtering
       ↓
Provider Scoring
       ↓
Risk / Policy Check
       ↓
Route Selection
       ↓
Provider Invocation
       ↓
Output Evaluation
       ↓
Success?
   ┌───┴───┐
   ↓       ↓
  YES      NO
   ↓       ↓
Return   Fallback
           ↓
       Re-route

⸻

6. Task Requirements

The router MUST derive requirements from the task.

Possible requirements:

requirements:
  capabilities:
    - reasoning
    - coding
  modalities:
    input:
      - text
    output:
      - structured
  quality:
    minimum: high
  privacy:
    level: private
  latency:
    maximum_ms: 5000
  cost:
    maximum: 0.05
  resources:
    max_ram_gb: 16
  context:
    minimum_tokens: 32000
  reliability:
    minimum: 0.99

⸻

7. Hard Constraints vs Soft Preferences

This distinction is critical.

Hard Constraints

A provider MUST satisfy them.

Examples:

privacy requirement
required modality
minimum context
required capability
maximum risk
authorization restrictions
hardware availability

If a provider fails a hard constraint:

Provider = INELIGIBLE

⸻

Soft Preferences

A provider is ranked higher if it satisfies them.

Examples:

lower cost
lower latency
higher quality
lower energy
better historical performance

⸻

8. Provider Eligibility

Conceptually:

Eligible(P, T) =
Capability
∧ Privacy
∧ Security
∧ Resource
∧ Context
∧ Availability
∧ Policy
∧ Risk

If any mandatory condition fails:

Eligible = FALSE

The router MUST NOT compensate for a failed hard constraint merely because the provider has a high quality score.

⸻

9. Provider Scoring

After filtering, eligible providers MAY be scored.

Conceptual model:

Score(P,T) =
wq × Quality
+ wc × CapabilityFit
+ wr × Reliability
+ wl × Latency
+ wcost × CostEfficiency
+ wp × Privacy
+ wh × HardwareFit
+ ws × Specialization

The exact formula MUST remain configurable.

⸻

10. Risk Adjustment

Raw score is insufficient.

A provider with excellent performance but unacceptable risk MUST NOT automatically win.

Conceptually:

EffectiveScore =
ProviderScore
× RiskAdjustment

For critical tasks, risk may act as a hard constraint rather than a score.

⸻

11. Privacy Routing

Privacy is a first-class routing requirement.

Example:

PRIVATE
   ↓
Local Provider

unless an explicit authorization permits external processing.

For:

SECRET

the default route SHOULD be:

Local / Isolated Provider

⸻

12. Data Classification

The router SHOULD understand:

PUBLIC
INTERNAL
PRIVATE
SENSITIVE
SECRET

Provider policies MAY define:

PUBLIC:
Local + Cloud
PRIVATE:
Local preferred
SENSITIVE:
Local only unless authorized
SECRET:
Restricted isolated providers

⸻

13. Task Classes

Veda SHOULD classify tasks.

Examples:

SIMPLE
ROUTINE
ANALYTICAL
CREATIVE
CODING
REASONING
VISION
AUDIO
RESEARCH
PLANNING
HIGH_RISK
CRITICAL

This classification affects routing.

⸻

14. Complexity Estimation

The router MAY estimate task complexity.

Factors:

context size
reasoning depth
number of constraints
uncertainty
number of dependencies
required precision
tool interactions
time sensitivity
risk

Example:

"Convert 10 USD to THB"
→ SIMPLE
"Design Veda authorization architecture"
→ HIGH COMPLEXITY

⸻

15. Capability Matching

A provider MUST support the required capability.

Example:

Task:
analyze image
Candidate:
text-only LLM
Result:
INELIGIBLE

Another:

Task:
write Swift code
Candidate:
coding model
Result:
ELIGIBLE

⸻

16. Specialist Preference

When a specialist provider has a meaningful advantage, the router SHOULD prefer it.

Examples:

Vision → Vision Model
Coding → Coding Model
Speech → Speech Model
Embedding → Embedding Model

General-purpose models SHOULD NOT automatically replace specialists.

⸻

17. Local vs Cloud Routing

Veda SHOULD support dynamic local/cloud routing.

Example:

Task
 ↓
Privacy Check
 ↓
Can Local Handle?
 ├── YES → Local
 └── NO
      ↓
Cloud Authorization
      ↓
Cloud Provider

This allows Veda to remain functional offline while exploiting stronger external models when appropriate.

⸻

18. Local-First Strategy

For private or routine tasks, Veda SHOULD prefer local intelligence when quality is sufficient.

Advantages:

privacy
offline operation
lower recurring cost
lower network dependency
predictable latency

However:

Local ≠ Automatically Better

The router SHOULD choose local only when it satisfies the task requirements.

⸻

19. Frontier Escalation

A local model MAY attempt a task first.

If confidence or verification quality is insufficient:

Local Model
    ↓
Evaluation
    ↓
Insufficient
    ↓
Escalate
    ↓
Stronger Provider

This creates an intelligence hierarchy.

Example:

Tier 1:
Small Local Model
Tier 2:
Large Local Model
Tier 3:
Specialist Model
Tier 4:
Cloud Frontier Model
Tier 5:
Human Review

Not every task needs Tier 5, because civilization has enough meetings already.

⸻

20. Escalation Conditions

Escalation MAY occur when:

confidence < threshold
verification fails
output schema invalid
reasoning disagreement
task complexity increases
provider unavailable
risk increases
new evidence appears
goal impact increases

Escalation policy MUST be explicit.

⸻

21. Multi-Provider Routing

Some tasks may benefit from multiple providers.

Example:

Provider A
→ Generate solution
Provider B
→ Critique
Provider C
→ Verify

Canonical structure:

Generate
   ↓
Critique
   ↓
Verify
   ↓
Commit

The router MAY construct such pipelines when required.

⸻

22. Ensemble Routing

For high-risk tasks:

Model A
Model B
Model C
   ↓
Independent Outputs
   ↓
Conflict Analysis
   ↓
Evidence / Verification

Consensus MUST NOT automatically equal truth.

Three models repeating the same error are still three models being wrong.

⸻

23. Disagreement Routing

If providers disagree:

Provider A → Result A
Provider B → Result B

the router SHOULD invoke RFC-0014.

Disagreement
     ↓
Contradiction Analysis
     ↓
Evidence Evaluation
     ↓
Resolution / Escalation

The router MUST NOT arbitrarily choose one merely because it was invoked first.

⸻

24. Historical Performance

The router SHOULD maintain provider performance history.

Metrics MAY include:

accuracy
verification success
task completion
latency
failure rate
cost
resource usage
human correction rate
hallucination rate
schema compliance

Performance SHOULD be task-specific.

Example:

Model A:
excellent coding
poor factual research
Model B:
excellent research
average coding

The router SHOULD learn this distinction.

⸻

25. Context Fit

Provider selection MUST consider context capacity.

Example:

Required context:
100k tokens
Provider A:
16k
Provider B:
128k

Provider A is ineligible unless Veda can safely transform the context.

Possible transformations:

retrieval
summarization
hierarchical context
chunking
context compression

⸻

26. Hardware-Aware Routing

For local providers, routing SHOULD consider:

RAM
VRAM
CPU
GPU
storage
thermal state
power
current workload

Example:

Model A:
requires 24 GB RAM
Available:
16 GB
Result:
INELIGIBLE

This prevents the wonderfully human strategy of attempting impossible computation and then blaming the computer.

⸻

27. Resource Contention

Multiple tasks may compete for hardware.

Example:

Task A → GPU
Task B → GPU
Task C → GPU

The router SHOULD consider:

priority
deadline
resource availability
preemption
queue
expected duration

⸻

28. Cost-Aware Routing

Cloud providers SHOULD expose cost estimates.

Example:

Provider A:
$0.001
Provider B:
$0.05
Provider C:
$0.30

The router SHOULD NOT spend $0.30 to answer a task that can be safely completed for $0.001.

For high-impact tasks, quality may justify additional cost.

⸻

29. Latency-Aware Routing

For real-time tasks:

latency

may be a hard requirement.

For background research:

quality

may be more important.

The routing policy MUST be task-sensitive.

⸻

30. Reliability-Aware Routing

A provider with:

95% success

may be unsuitable for a critical task.

A provider with:

99.99% success

may be preferable even if slightly slower.

Reliability SHOULD be evaluated using historical evidence.

⸻

31. Provider Availability

The router MUST consider current health.

HEALTHY
DEGRADED
UNAVAILABLE

Unavailable providers MUST NOT receive new tasks unless the operation is specifically designed for recovery or probing.

⸻

32. Fallback

Every important task SHOULD define fallback behavior.

Example:

Primary:
Local Model
Fallback:
Larger Local Model
Fallback 2:
Cloud Model
Fallback 3:
Human Review

Fallback MUST respect privacy and authorization.

⸻

33. Retry

Retry SHOULD NOT blindly repeat the same request.

The router SHOULD distinguish:

transient failure
persistent failure
invalid request
provider failure
model failure
resource exhaustion

Retry policy MUST depend on failure type.

⸻

34. Provider Blacklisting

A provider MAY be temporarily excluded when:

repeated failures
security issue
quality degradation
invalid outputs
privacy violation
resource instability

Blacklisting MUST be auditable.

⸻

35. Provider Recovery

A disabled provider MAY return through:

DISABLED
   ↓
HEALTH CHECK
   ↓
VALIDATION
   ↓
AVAILABLE

Recovery MUST NOT automatically restore previous trust assumptions if the provider changed materially.

⸻

36. Routing Decision Object

Canonical structure:

routing_decision:
  routing_id:
  task_id:
  selected_providers:
    - provider_id:
      model_id:
      role:
  rejected_providers:
    - provider_id:
      reason:
  requirements:
  constraints:
  score:
  risk:
  policy_refs:
  authorization_refs:
  fallback_plan:
  estimated:
    latency:
    cost:
    resources:
  confidence:
  created_at:
  expires_at:

⸻

37. Routing Roles

Multi-provider tasks MAY assign roles:

GENERATOR
REASONER
CRITIC
VERIFIER
RESEARCHER
EXTRACTOR
PLANNER
CODER
SIMULATOR
JUDGE

No role automatically grants authority.

⸻

38. Routing Policy

Routing policy SHOULD support:

hard constraints
soft preferences
provider priorities
privacy rules
cost limits
latency limits
quality thresholds
risk thresholds
fallback rules
escalation rules

Policies MUST be versioned.

⸻

39. Policy Example

policy:
  task_type: coding
  hard:
    privacy: private
    max_latency_ms: 10000
  preferred:
    local: true
    specialist: true
  quality:
    minimum: 0.80
  fallback:
    - larger_local
    - cloud_coding
    - human_review

⸻

40. Routing Simulation

The router SHOULD support dry-run routing.

Example:

Task
 ↓
Candidate Discovery
 ↓
Scoring
 ↓
Selected Provider

without actually invoking the provider.

This allows Veda to compare routes before execution.

⸻

41. Routing Explainability

Every routing decision SHOULD be explainable.

Example:

Selected:
Local Coding Model
Reasons:
- supports required coding capability
- private task
- sufficient context
- available RAM
- lower latency
- lower cost
Rejected:
Cloud Model
Reason:
- external processing prohibited by current policy

This is important for debugging and governance.

⸻

42. Routing Audit

Every routing decision SHOULD produce an event.

Example:

RoutingRequested
CandidatesEvaluated
ProviderSelected
ProviderRejected
RoutingEscalated
RoutingFallbackActivated
RoutingCompleted

The event MUST include sufficient provenance to reconstruct the decision.

⸻

43. Learning From Routing

Veda SHOULD learn provider performance over time.

Pipeline:

Routing Decision
      ↓
Execution
      ↓
Verification
      ↓
Outcome
      ↓
Performance Record
      ↓
Routing Knowledge

This creates an empirical provider profile.

The router MUST NOT update critical routing policy solely from unverified model feedback.

⸻

44. Cold Start

New providers have no historical performance.

The router SHOULD initially use:

certification
benchmark
declared capabilities
known architecture
sandbox tests

rather than pretending the provider has a proven history.

⸻

45. Provider Specialization Discovery

Veda MAY discover that a provider is unexpectedly strong at a particular task.

Example:

Provider X
Unknown specialization
Observed:
high verification success in SQL tasks

The router may update:

Provider X
→ SQL specialization

only after sufficient evidence.

⸻

46. Routing Feedback Loop

Canonical learning loop:

Task
 ↓
Route
 ↓
Provider
 ↓
Output
 ↓
Verification
 ↓
Outcome
 ↓
Performance
 ↓
Router Update

This allows routing quality to improve without modifying the core architecture.

⸻

47. Safety Boundary

The router MUST NOT:

* authorize actions
* grant capabilities
* modify policies
* modify Constitution
* bypass human approval
* access restricted secrets
* commit unverified World changes

Its responsibility is:

SELECT INTELLIGENCE

not:

CONTROL REALITY

⸻

48. Security Threats

Potential threats include:

* malicious provider ranking
* provider self-promotion
* poisoned performance metrics
* routing manipulation
* cost exploitation
* privacy leakage
* model impersonation
* fake benchmark results
* feedback poisoning
* provider denial-of-service
* adversarial task classification

Routing decisions MUST therefore be auditable.

⸻

49. Anti-Gaming Rules

Providers MUST NOT be able to directly modify their own ranking.

Performance updates SHOULD be based on:

observed outcomes
verification
independent evaluation

rather than provider claims alone.

⸻

50. Human Override

Humans MAY override routing for:

debugging
privacy
testing
emergency
cost control
quality control
development

Human override MUST be logged.

⸻

51. Deterministic Routing

For reproducibility-sensitive tasks, routing SHOULD support deterministic policy evaluation.

Given:

same task
same provider registry
same policy
same system state

the router SHOULD produce the same route.

If dynamic conditions change:

hardware
availability
cost
network
provider health

the routing result may legitimately differ.

⸻

52. Routing Events

The following events SHOULD be supported:

RoutingRequested
TaskRequirementsDerived
ProviderCandidateDiscovered
ProviderCandidateRejected
ProviderScored
ProviderSelected
RoutingApproved
RoutingStarted
RoutingFallbackActivated
RoutingEscalated
ProviderDisagreementDetected
RoutingCompleted
RoutingFailed
RoutingCancelled
RoutingOverridden
RoutingPolicyChanged
ProviderRankingUpdated

⸻

53. Formal Model

Let:

T = Intelligence Task
P = Set of Providers
C = Constraints
R = Requirements
W = Current World
Pol = Routing Policies

Candidate set:

Candidates(T) =
{ p ∈ P | Eligible(p,T,C,Pol,W) }

Provider score:

Score(p,T) =
f(
 capability_fit,
 quality,
 reliability,
 latency,
 cost,
 privacy,
 resource_fit,
 specialization,
 risk
)

Selected provider:

p* = argmax Score(p,T)

subject to:

Eligible(p*,T,C,Pol,W) = TRUE

For multi-provider tasks:

P* = SelectSet(T)

where the selected set satisfies task requirements and routing policy.

⸻

54. Invariants

ROUTE-1

The router MUST select only eligible providers.

ROUTE-2

Hard constraints MUST NOT be overridden by soft preferences.

ROUTE-3

Routing MUST respect privacy policy.

ROUTE-4

Routing MUST respect authorization boundaries.

ROUTE-5

The router MUST NOT grant authority.

ROUTE-6

The router MUST NOT grant capabilities.

ROUTE-7

Provider output MUST pass through appropriate Veda control systems.

ROUTE-8

Routing decisions MUST be auditable.

ROUTE-9

Provider selection MUST be traceable.

ROUTE-10

Provider health MUST influence eligibility where relevant.

ROUTE-11

Historical performance MUST be distinguished from provider claims.

ROUTE-12

Routing metrics MUST NOT be silently manipulated by providers.

ROUTE-13

Provider disagreement MUST be representable.

ROUTE-14

Provider disagreement MUST NOT automatically determine truth.

ROUTE-15

High-risk tasks MUST use stricter routing requirements.

ROUTE-16

Critical tasks MAY require multi-provider verification.

ROUTE-17

Privacy restrictions MUST override optimization preferences.

ROUTE-18

Unavailable providers MUST NOT receive ordinary new tasks.

ROUTE-19

Fallback MUST respect the same security and privacy constraints as primary routing.

ROUTE-20

Retry MUST be failure-aware.

ROUTE-21

Provider replacement MUST NOT require modification of Veda Core.

ROUTE-22

Routing policy MUST be versioned.

ROUTE-23

Routing decisions MUST record relevant provider versions.

ROUTE-24

Routing MUST account for resource constraints.

ROUTE-25

Routing MUST account for context constraints.

ROUTE-26

Routing MUST NOT assume provider confidence equals truth.

ROUTE-27

Routing feedback MUST be grounded in verified outcomes.

ROUTE-28

New providers MUST NOT automatically receive maximum trust.

ROUTE-29

Human routing overrides MUST be auditable.

ROUTE-30

No routing decision may bypass Veda’s authority, verification, or safety architecture.

⸻

55. Canonical Architecture

                    ┌──────────────────────┐
                    │    Intelligence      │
                    │        Task          │
                    └──────────┬───────────┘
                               ↓
                    ┌──────────────────────┐
                    │   Task Analyzer      │
                    └──────────┬───────────┘
                               ↓
                    ┌──────────────────────┐
                    │ Requirement Extractor│
                    └──────────┬───────────┘
                               ↓
                    ┌──────────────────────┐
                    │ Candidate Registry   │
                    └──────────┬───────────┘
                               ↓
                    ┌──────────────────────┐
                    │ Eligibility Filter   │
                    └──────────┬───────────┘
                               ↓
                    ┌──────────────────────┐
                    │ Provider Scorer      │
                    └──────────┬───────────┘
                               ↓
                    ┌──────────────────────┐
                    │ Policy / Risk Check  │
                    └──────────┬───────────┘
                               ↓
                    ┌──────────────────────┐
                    │ Route Selector       │
                    └──────────┬───────────┘
                               ↓
              ┌────────────────┴────────────────┐
              ↓                                 ↓
        Local Provider                    Cloud Provider
              ↓                                 ↓
        Specialist Model                 Frontier Model
              └────────────────┬────────────────┘
                               ↓
                         Output Evaluation
                               ↓
                    ┌──────────┴───────────┐
                    ↓                      ↓
                 Success                Failure
                    ↓                      ↓
                  Return              Fallback /
                                      Escalation

⸻

56. Final Principle

The Intelligence Router exists to answer:

Who is best suited to think about this problem right now?

It does not answer:

Who is allowed to act?

That remains the responsibility of the Authority and Action systems.

The canonical chain is:

Task
 ↓
Requirements
 ↓
Candidate Providers
 ↓
Eligibility
 ↓
Scoring
 ↓
Risk / Policy
 ↓
Route
 ↓
Intelligence Provider
 ↓
Output
 ↓
Verification
 ↓
Knowledge / Plan / Proposal
 ↓
Authorization
 ↓
Action
 ↓
World

The core rule is:

Veda chooses intelligence dynamically, but intelligence never chooses its own authority.