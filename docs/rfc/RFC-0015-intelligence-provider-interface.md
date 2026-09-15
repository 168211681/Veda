RFC-0015 — Intelligence Provider Interface

Status: Draft
Version: 0.1.0
Layer: 6 — Intelligence
Module: Intelligence Provider Interface
Depends on: RFC-0001, RFC-0002, RFC-0003, RFC-0004, RFC-0005, RFC-0006, RFC-0007, RFC-0008, RFC-0012, RFC-0013, RFC-0014
Path: docs/rfc/RFC-0015-intelligence-provider-interface.md

⸻

1. Abstract

RFC-0015 defines the standard interface between Veda and external or internal intelligence providers.

An Intelligence Provider is any system capable of producing useful cognitive output for Veda.

Examples include:

* local language models
* cloud frontier models
* coding models
* vision models
* speech models
* embedding models
* reasoning models
* planning models
* specialist models
* deterministic algorithms
* search systems
* external AI services
* future Veda-native intelligence systems

Veda MUST treat intelligence providers as replaceable components.

The provider MAY generate:

* interpretations
* hypotheses
* classifications
* plans
* code
* summaries
* predictions
* tool proposals
* explanations
* structured decisions

However:

Intelligence Output ≠ Authority
Intelligence Output ≠ Truth
Intelligence Output ≠ Authorization
Intelligence Output ≠ World State

The provider supplies intelligence.

Veda remains responsible for:

* intent
* authority
* policy
* verification
* execution
* world state
* memory
* audit
* evolution

⸻

2. Motivation

Veda should not depend on a single model.

A single-model architecture creates several problems:

Model failure
      ↓
System failure

and:

Model upgrade
      ↓
Architecture migration

This creates unnecessary coupling.

Instead:

Veda Core
    ↓
Intelligence Interface
    ↓
┌─────────────┬─────────────┬─────────────┬─────────────┐
↓             ↓             ↓             ↓
Local LLM   Cloud LLM     Vision       Coding

The core system remains stable while intelligence providers change.

⸻

3. Core Principle

The canonical relationship is:

Veda
  ↓
Intelligence Provider Interface
  ↓
Provider
  ↓
Model / Algorithm / Service

The provider MUST NOT directly control:

* world state
* permissions
* credentials
* authorization
* irreversible actions

unless those operations are explicitly mediated by Veda’s capability and authorization systems.

⸻

4. Definitions

4.1 Intelligence Provider

An Intelligence Provider is an implementation capable of producing one or more cognitive capabilities through a standardized interface.

⸻

4.2 Model

A Model is an implementation used by a provider.

Examples:

LLM
Vision Model
Speech Model
Embedding Model
Classifier
Planner
Predictor

A provider MAY expose one or more models.

⸻

4.3 Provider

The provider is the integration boundary.

Provider
   ↓
Model
   ↓
Inference

The provider handles:

* input preparation
* model invocation
* output normalization
* errors
* metadata
* resource accounting
* capability reporting

⸻

4.4 Intelligence Task

A structured request for cognitive work.

Examples:

reason
classify
summarize
generate
translate
code
inspect
predict
plan
extract
compare
evaluate

⸻

5. Design Goals

The interface MUST provide:

1. provider independence
2. model independence
3. capability discovery
4. structured inputs
5. structured outputs
6. provenance
7. confidence representation
8. resource reporting
9. failure handling
10. cancellation
11. timeout
12. streaming support
13. deterministic mode where available
14. privacy classification
15. cost tracking
16. observability
17. versioning
18. security isolation
19. evaluation compatibility
20. provider replacement

⸻

6. Non-Goals

RFC-0015 does NOT define:

* which model is best
* how models are trained
* model routing
* long-term memory
* planning architecture
* authorization policy
* tool execution
* autonomous evolution

Those are defined elsewhere.

In particular:

RFC-0015 ≠ Intelligence Router

Routing belongs to RFC-0016.

⸻

7. Provider Object

Canonical representation:

provider_id:
version:
name:
description:
provider_type:
models:
  - model_ref
capabilities:
  - capability
input_modalities:
  - text
  - image
  - audio
  - video
  - structured
output_modalities:
  - text
  - structured
  - code
  - image
  - audio
context_limit:
output_limit:
latency_profile:
cost_profile:
resource_profile:
privacy:
  data_retention:
  external_processing:
  locality:
availability:
health:
supports:
  streaming:
  cancellation:
  deterministic_mode:
  tool_calling:
  structured_output:
  batching:
authentication:
credential_ref:
created_at:
updated_at:
status:

⸻

8. Provider Types

Veda SHOULD support:

LOCAL_MODEL
CLOUD_MODEL
HYBRID_MODEL
SPECIALIST_MODEL
ALGORITHM
SEARCH_ENGINE
RETRIEVAL_ENGINE
SIMULATOR
EXTERNAL_SERVICE
HUMAN_PROVIDER
COMPOSITE_PROVIDER

A human can technically act as an intelligence provider for specific tasks.

Example:

Veda → Human Approval Provider

This is useful when the system needs judgment rather than computation.

⸻

9. Intelligence Task

Canonical task structure:

task_id:
version:
type:
request:
context:
intent_ref:
goal_ref:
process_ref:
input_refs:
knowledge_refs:
evidence_refs:
world_refs:
constraints:
requirements:
desired_output:
output_schema:
risk_level:
privacy_level:
deadline:
timeout:
determinism_required:
created_at:
expires_at:

⸻

10. Task Types

Minimum supported task types:

ANALYZE
REASON
CLASSIFY
EXTRACT
SUMMARIZE
GENERATE
TRANSFORM
TRANSLATE
COMPARE
PREDICT
PLAN
CODE
DEBUG
REVIEW
VERIFY
INTERPRET
SIMULATE
SEARCH
RETRIEVE

Providers MAY support additional types.

⸻

11. Request Contract

Every provider invocation SHOULD receive a normalized request.

Example:

request:
  task_id: task_123
  task_type: ANALYZE
  input:
    content_ref: object_456
  context:
    world_ref: world_1
    knowledge_refs:
      - knowledge_12
      - knowledge_19
  constraints:
    max_tokens: 4000
    timeout_ms: 30000
  output:
    format: structured
    schema_ref: analysis_schema_v1
  safety:
    risk_level: medium
  provenance:
    required: true

The provider MUST NOT need to understand the entire internal architecture of Veda.

The interface is the abstraction boundary.

⸻

12. Response Contract

Canonical response:

response:
  task_id:
  provider_id:
  model_id:
  provider_version:
  model_version:
  status:
  output:
    content:
    structured_data:
  confidence:
  reasoning_metadata:
  citations:
  evidence_refs:
  usage:
    input_tokens:
    output_tokens:
    latency_ms:
  resources:
    cpu:
    memory:
    gpu:
    energy:
  cost:
  privacy:
  warnings:
  errors:
  created_at:
  completed_at:

⸻

13. Output Status

Provider responses SHOULD use:

COMPLETED
PARTIAL
FAILED
TIMEOUT
CANCELLED
REJECTED
UNAVAILABLE
RATE_LIMITED
INVALID_REQUEST
RESOURCE_EXHAUSTED

⸻

14. Confidence

Providers MAY report confidence.

However:

Provider Confidence ≠ Truth Probability

Confidence MUST be treated as metadata, not authority.

Veda SHOULD distinguish:

model_confidence
evidence_confidence
knowledge_confidence
decision_confidence

These represent different concepts.

⸻

15. Provenance

Every provider output SHOULD be traceable to:

provider
model
version
task
input
context
timestamp
configuration

Example:

provenance:
  provider_id: local_provider
  model_id: model_x
  model_version: 1.2.0
  task_id: task_123
  input_hash: sha256:...
  configuration_hash: sha256:...
  created_at:

This is essential for reproducibility and debugging.

⸻

16. Model Output Is Not Automatically Knowledge

The following pipeline MUST be preserved:

Model Output
     ↓
Evaluation
     ↓
Evidence / Source Analysis
     ↓
Claim Extraction
     ↓
Knowledge Candidate
     ↓
Verification
     ↓
Knowledge

A model response MUST NOT automatically enter trusted Knowledge.

⸻

17. Model Output Is Not Automatically Action

Likewise:

Model
 ↓
Proposal
 ↓
Intent / Goal / Plan
 ↓
Action
 ↓
Authorization
 ↓
Capability
 ↓
Execution
 ↓
Verification

A provider MUST NOT bypass this chain.

⸻

18. Context Contract

Veda SHOULD send context through structured references rather than dumping the entire world into a model prompt.

Context SHOULD include only relevant information.

Possible context:

World State
Intent
Goal
Process
Current Task
Relevant Knowledge
Evidence
Constraints
Policies
Previous Results

The provider SHOULD NOT automatically receive:

* unrelated private memory
* secrets
* credentials
* unrestricted world state
* unrelated user data

unless explicitly authorized.

⸻

19. Context Budget

The interface SHOULD expose:

context_limit
context_used
context_remaining

The system SHOULD support:

context compression
context summarization
retrieval
relevance filtering
hierarchical context

This allows small models to operate efficiently.

⸻

20. Privacy Boundary

Every intelligence request SHOULD carry a privacy classification.

Example:

PUBLIC
INTERNAL
PRIVATE
SENSITIVE
SECRET

Provider eligibility MUST respect the classification.

Example:

SECRET
   ↓
Local Provider Only

unless explicit authorization permits external processing.

⸻

21. Resource Contract

Provider metadata SHOULD expose:

CPU requirement
RAM requirement
GPU requirement
VRAM requirement
latency
energy
network
storage
cost

This enables Veda to choose appropriate providers later.

⸻

22. Cancellation

Long-running tasks MUST support cancellation where technically possible.

Canonical sequence:

RUNNING
   ↓
CANCEL_REQUESTED
   ↓
CANCELLED

Provider MUST attempt to stop execution.

If cancellation cannot be guaranteed, the response MUST state that explicitly.

⸻

23. Timeout

Every invocation SHOULD have a timeout.

Possible outcomes:

COMPLETED
TIMEOUT
PARTIAL
UNKNOWN

A timeout MUST NOT automatically be interpreted as execution failure if the provider may have continued processing externally.

This distinction is critical for external APIs.

⸻

24. Idempotency

Provider requests SHOULD support idempotency keys.

idempotency_key:

This prevents accidental duplicate operations at the provider layer.

For purely inferential operations, duplicate execution may be acceptable.

For side-effecting providers, idempotency is mandatory where technically applicable.

⸻

25. Streaming

Providers MAY support streaming.

Example:

REQUESTED
   ↓
STARTED
   ↓
STREAMING
   ↓
COMPLETED

Partial output MUST NOT automatically become committed Knowledge or Action.

⸻

26. Structured Output

Veda SHOULD prefer structured output for system-critical operations.

Example:

{
  "decision": "REVIEW_REQUIRED",
  "confidence": 0.82,
  "evidence_refs": ["ev_12"],
  "risks": ["unknown_state"],
  "next_step": "request_observation"
}

Free-form text MAY be used for human-facing communication.

⸻

27. Tool Calling

A provider MAY propose tool calls.

However:

Model
 ↓
Tool Proposal
 ↓
Action Model
 ↓
Authorization
 ↓
Capability
 ↓
Execution

The model MUST NOT directly own the tool.

Tool execution remains under Veda’s capability architecture.

⸻

28. Error Model

Provider errors SHOULD be normalized.

Categories:

AUTHENTICATION_ERROR
AUTHORIZATION_ERROR
INVALID_INPUT
MODEL_ERROR
PROVIDER_ERROR
NETWORK_ERROR
TIMEOUT
RATE_LIMIT
RESOURCE_LIMIT
CONTEXT_LIMIT
SAFETY_REJECTION
OUTPUT_INVALID
SERVICE_UNAVAILABLE
UNKNOWN_ERROR

The system SHOULD preserve provider-specific error information for diagnosis.

⸻

29. Health Model

Providers SHOULD expose health state:

UNKNOWN
HEALTHY
DEGRADED
UNAVAILABLE
FAILED
DISABLED

Health checks MAY include:

connectivity
latency
model loading
memory
GPU
authentication
quota
test inference
output validity

⸻

30. Provider Registration

Provider registration SHOULD follow:

DISCOVERED
    ↓
REGISTERED
    ↓
VALIDATING
    ↓
CERTIFIED
    ↓
AVAILABLE

Possible states:

DEGRADED
DISABLED
REVOKED

A provider MUST NOT be used before passing required validation.

⸻

31. Provider Capability Declaration

Providers MUST declare what they can actually do.

Example:

capabilities:
  - text_generation
  - structured_output
  - reasoning
  - coding

A provider MUST NOT advertise unsupported capabilities.

Capability declarations SHOULD be benchmarked rather than blindly trusted.

⸻

32. Provider Certification

Veda MAY test providers before allowing production use.

Tests SHOULD include:

correctness
latency
schema compliance
failure behavior
security
privacy
resource usage
reproducibility
tool-call safety
context handling

Certification result:

PASS
CONDITIONAL
FAIL

⸻

33. Benchmark Profile

Each provider SHOULD maintain a benchmark profile.

Example:

benchmark:
  reasoning:
  coding:
  extraction:
  planning:
  vision:
  latency:
  reliability:
  memory:
  cost:
  privacy:

The benchmark SHOULD be task-specific.

A model being excellent at coding does not imply that it is excellent at every other task.

Humanity has repeatedly learned this lesson and repeatedly ignored it.

Veda should not.

⸻

34. Provider Versioning

Provider versions MUST be explicit.

provider_version
model_version
interface_version
configuration_version

Changing any of these MAY change output behavior.

Critical decisions SHOULD record all relevant versions.

⸻

35. Deterministic Mode

Where supported, a provider SHOULD expose deterministic execution.

Possible controls:

seed
temperature
sampling
model version
system configuration

Determinism MUST NOT be assumed merely because a provider accepts a seed.

⸻

36. Reproducibility

Veda SHOULD be able to reconstruct:

What model produced this?
With what input?
With what context?
Using which version?
Under which configuration?
At what time?

For reproducibility-sensitive tasks, Veda SHOULD retain sufficient provenance.

⸻

37. Human Provider

Human judgment MAY be represented through the same conceptual interface.

Example:

Intelligence Task
       ↓
Human Review
       ↓
Structured Response

This allows Veda to treat human judgment as a controlled cognitive resource without pretending that human judgment is infallible.

⸻

38. Composite Provider

A provider MAY internally combine multiple intelligence systems.

Example:

Composite Provider
 ├── local model
 ├── vision model
 ├── retrieval engine
 └── deterministic analyzer

The composite provider MUST preserve provenance of each contributing component.

⸻

39. Provider Isolation

A provider MUST operate within its authorized resource boundary.

The provider MUST NOT automatically receive:

* unrestricted filesystem access
* arbitrary network access
* credentials
* authorization authority
* unrestricted memory
* direct world mutation

unless explicitly mediated through the relevant capability system.

⸻

40. Security

Potential threats include:

* malicious model
* compromised provider
* prompt injection
* context poisoning
* output manipulation
* data exfiltration
* model supply-chain compromise
* malicious tool proposal
* hidden side effects
* provider impersonation

Veda SHOULD assume that an intelligence provider can fail or become compromised.

⸻

41. Trust

Provider trust MUST remain separate from authority.

Trust
  ≠
Permission

A highly trusted provider still cannot perform an unauthorized operation.

Likewise, a low-trust provider MAY be allowed to perform harmless isolated tasks.

⸻

42. Secrets

Secrets MUST NOT be embedded into ordinary model context unless explicitly required and authorized.

Where possible:

Model
 ↓
Reference
 ↓
Capability Layer
 ↓
Secret Retrieval
 ↓
Tool Execution

rather than:

Model receives raw secret

⸻

43. Provider Failure

If a provider fails:

Provider Failure
      ↓
Task Failure

does NOT necessarily mean:

Goal Failure

Veda SHOULD be able to:

retry
fallback
replan
switch provider
request human assistance
defer
abort

This will later be coordinated by RFC-0016.

⸻

44. Provider Replacement

Replacing a provider SHOULD NOT require modifying:

* World Model
* Intent Model
* Goal Model
* Process Model
* Action Model
* Authorization
* Knowledge Model
* Audit system

Only the provider adapter and relevant registration metadata should need modification.

This is one of the primary architectural goals of RFC-0015.

⸻

45. Provider Events

The following events SHOULD be supported:

ProviderDiscovered
ProviderRegistered
ProviderValidated
ProviderCertified
ProviderEnabled
ProviderDisabled
ProviderDegraded
ProviderRevoked
IntelligenceTaskCreated
IntelligenceTaskStarted
IntelligenceTaskStreaming
IntelligenceTaskCompleted
IntelligenceTaskFailed
IntelligenceTaskTimedOut
IntelligenceTaskCancelled
ProviderHealthChanged
ProviderBenchmarkCompleted
ProviderVersionChanged

All events MUST integrate with RFC-0003 and RFC-0031.

⸻

46. Formal Interface

Conceptual interface:

interface IntelligenceProvider {
    describe() -> ProviderDescriptor
    capabilities() -> CapabilityDescriptor[]
    health() -> HealthStatus
    invoke(request) -> IntelligenceResponse
    cancel(task_id) -> CancellationResult
    benchmark(request) -> BenchmarkResult
}

The exact implementation language is intentionally unspecified.

⸻

47. Provider Adapter

Veda SHOULD use adapters.

Veda
  ↓
Provider Interface
  ↓
Adapter
  ↓
External API / Local Runtime
  ↓
Model

Examples:

LocalLLMAdapter
CloudLLMAdapter
VisionAdapter
EmbeddingAdapter
SpeechAdapter
CodingModelAdapter
SearchAdapter

This prevents provider-specific APIs from contaminating Veda Core.

⸻

48. Interface Boundary

The provider interface MUST be narrow.

The provider should receive:

Task
Relevant Context
Constraints
Output Schema

and return:

Output
Confidence Metadata
Provenance
Usage
Errors

It should NOT directly manipulate:

World
Goals
Authorization
Capabilities
Audit History
Constitution

⸻

49. Relationship With RFC-0016

RFC-0015 defines:

HOW Veda talks to intelligence.

RFC-0016 will define:

WHICH intelligence Veda should use.

Therefore:

RFC-0015
Provider Interface
RFC-0016
Intelligence Router

The router will consider:

task
capability
quality
confidence
latency
cost
privacy
hardware
availability
risk
model specialization

and select an appropriate provider.

⸻

50. Invariants

INTEL-1

Every provider MUST have a unique identity.

INTEL-2

Every production provider MUST declare supported capabilities.

INTEL-3

Provider output MUST include provenance.

INTEL-4

Model output MUST NOT automatically become truth.

INTEL-5

Model output MUST NOT automatically become authorization.

INTEL-6

Model output MUST NOT directly mutate the World.

INTEL-7

Provider access MUST respect privacy classification.

INTEL-8

Provider access MUST respect capability boundaries.

INTEL-9

Provider failure MUST be distinguishable from goal failure.

INTEL-10

Provider versions MUST be traceable.

INTEL-11

Critical outputs MUST preserve sufficient provenance for audit.

INTEL-12

Provider confidence MUST NOT be treated as objective truth.

INTEL-13

Provider trust MUST NOT imply authority.

INTEL-14

Providers MUST NOT receive unrestricted secrets by default.

INTEL-15

Tool proposals MUST pass through Action and Authorization systems.

INTEL-16

Provider replacement MUST NOT require redesign of Veda Core.

INTEL-17

Provider-specific protocols MUST remain behind adapters.

INTEL-18

Unsupported capabilities MUST NOT be advertised.

INTEL-19

Critical provider failures MUST be observable.

INTEL-20

Provider state MUST be auditable.

INTEL-21

Context sent to providers MUST be bounded by relevance and authorization.

INTEL-22

Provider responses MUST be validated against expected schemas.

INTEL-23

Partial output MUST NOT automatically be treated as complete output.

INTEL-24

Timeout MUST NOT automatically imply external execution was terminated.

INTEL-25

Determinism MUST NOT be assumed without evidence.

INTEL-26

Provider-generated claims MUST pass through the Knowledge/Evidence pipeline when entering trusted knowledge.

INTEL-27

Provider-generated actions MUST pass through the Action/Authorization pipeline.

INTEL-28

Provider isolation MUST prevent unauthorized world mutation.

INTEL-29

Critical provider decisions MUST retain model and configuration provenance.

INTEL-30

No intelligence provider may become the constitutional authority of Veda.

⸻

51. Canonical Intelligence Pipeline

Intent
  ↓
Goal
  ↓
Process
  ↓
Intelligence Task
  ↓
Intelligence Provider Interface
  ↓
Provider
  ↓
Model / Algorithm
  ↓
Output
  ↓
Validation
  ↓
Evidence / Knowledge / Plan / Proposal
  ↓
Veda Control Systems
  ↓
Authorization
  ↓
Action
  ↓
Verification
  ↓
World

⸻

52. Final Principle

Veda does not need one perfect brain.

It needs an architecture capable of using many imperfect brains safely.

The fundamental separation is:

Veda Core
    │
    ├── World
    ├── Intent
    ├── Goals
    ├── Processes
    ├── Actions
    ├── Authority
    ├── Knowledge
    ├── Memory
    └── Audit
            │
            ↓
   Intelligence Interface
            │
      ┌─────┼─────┐
      ↓     ↓     ↓
    Local  Cloud  Specialist
    Model  Model    Model

The core rule is:

Intelligence is a replaceable service of Veda, not the owner of Veda.