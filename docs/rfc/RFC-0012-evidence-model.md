RFC-0012: Evidence Model

Status: Draft
Version: v0.1.0
Layer: Layer 5 — Truth
Module: Evidence / Provenance / Verification
Depends On: RFC-0001, RFC-0002, RFC-0003, RFC-0004, RFC-0005, RFC-0006, RFC-0007, RFC-0008, RFC-0010, RFC-0011
Related: RFC-0013, RFC-0014, RFC-0017, RFC-0026, RFC-0031, RFC-0035, RFC-0036

⸻

1. Abstract

RFC-0012 defines the Evidence Model for Veda.

Evidence is the structured basis used by Veda to support, challenge, verify, or qualify a claim about the World.

Veda MUST distinguish:

Observation
≠
Evidence
≠
Claim
≠
Belief
≠
Knowledge
≠
Truth

Evidence does not automatically make a claim true.

Instead:

Evidence
    ↓
supports / contradicts
    ↓
Claim
    ↓
confidence / status
    ↓
Knowledge

The purpose of this RFC is to ensure that Veda can answer:

* What do I know?
* Why do I believe it?
* Where did this information come from?
* When was it observed?
* Who produced it?
* Can it be independently verified?
* Is it current?
* Does other evidence contradict it?
* How reliable is the source?
* What assumptions are involved?
* What part is directly observed versus inferred?
* What should happen if the evidence becomes invalid?

Evidence therefore becomes a foundational component of Veda’s truth, knowledge, verification, memory, and learning systems.

⸻

2. Motivation

A conventional AI system often produces:

Question
 ↓
Model
 ↓
Answer

The answer may sound confident even when the underlying information is:

* outdated
* incomplete
* fabricated
* misunderstood
* inferred incorrectly
* based on an unreliable source
* contradicted by newer evidence

Veda requires a different model:

Reality
 ↓
Observation
 ↓
Evidence
 ↓
Claim
 ↓
Evaluation
 ↓
Knowledge / Belief

This allows Veda to maintain epistemic boundaries.

The system must be able to distinguish:

"I observed X."

from:

"Source Y claims X."

from:

"Given X and Y, I infer Z."

These are not equivalent statements.

⸻

3. Design Goals

RFC-0012 MUST provide:

1. Evidence representation.
2. Provenance.
3. Source identity.
4. Evidence type classification.
5. Temporal validity.
6. Reliability metadata.
7. Integrity verification.
8. Claim linkage.
9. Support and contradiction relationships.
10. Evidence aggregation.
11. Evidence freshness.
12. Evidence independence.
13. Evidence quality.
14. Uncertainty representation.
15. Evidence lifecycle.
16. Revocation and invalidation.
17. Auditability.
18. Reproducibility where possible.
19. Chain of provenance.
20. Compatibility with human and machine-generated evidence.

⸻

4. Non-Goals

RFC-0012 does not define:

* general knowledge storage
* full reasoning algorithms
* model architecture
* truth itself
* philosophical certainty
* authorization
* policy
* action execution

Knowledge is defined by RFC-0013.

Contradiction and conflict handling is defined by RFC-0014.

Verification execution is defined by RFC-0026.

⸻

5. Core Principles

5.1 Evidence Is Not Truth

Evidence ≠ Truth

Evidence provides support for a claim.

Even strong evidence may later be shown to be:

* incorrect
* incomplete
* compromised
* misinterpreted
* superseded

⸻

5.2 Source Is Not Claim

Source ≠ Claim

A source may make multiple claims.

Veda MUST represent the relationship explicitly.

⸻

5.3 Observation Is Not Interpretation

Observation ≠ Interpretation

Example:

Observation:
"The temperature sensor returned 39.2°C."
Interpretation:
"The machine is overheating."

The second statement requires additional reasoning.

⸻

5.4 Model Output Is Evidence Only Under Explicit Conditions

A model-generated answer MUST NOT automatically become authoritative evidence.

A model output MAY become evidence about:

what the model said

but not automatically evidence that:

what the model said is true

⸻

5.5 Evidence Requires Provenance

Every durable evidence object SHOULD identify:

Who/what produced it?
When?
From where?
Under what conditions?
How was it obtained?
How was it transformed?

⸻

5.6 Evidence Has Temporal Meaning

Evidence may be valid at one point in time and irrelevant later.

Example:

2026-01-01:
Server status = healthy
2026-09-16:
Server status = unknown

The old evidence remains historically valid.

It does not necessarily remain currently valid.

⸻

6. Evidence Definition

Evidence is:

A recorded, attributable, inspectable representation of information that can be used to support, challenge, verify, or qualify a claim about a World state.

Examples include:

* sensor readings
* documents
* books
* database records
* logs
* API responses
* photographs
* videos
* measurements
* human statements
* experiments
* test results
* source code
* model outputs
* system observations
* execution results
* cryptographic proofs

⸻

7. Evidence Object

A canonical Evidence object SHOULD contain:

evidence_id:
version:
type:
source_ref:
producer_ref:
content_ref:
content_hash:
observed_at:
created_at:
received_at:
expires_at:
world_ref:
context:
provenance:
transformation_chain:
quality:
reliability:
independence:
integrity:
verification:
supports:
contradicts:
confidence:
status:
sensitivity:
access_policy:
parent_evidence:
derived_evidence:
created_by:
updated_at:

⸻

8. Evidence Identity

Every evidence object MUST have a unique identifier.

Example:

evidence-01JVEDA-8H2K

Evidence identity MUST remain stable even if metadata is updated.

⸻

9. Evidence Version

Evidence metadata MAY evolve.

However, the original evidence content SHOULD remain immutable.

Therefore:

Evidence ID
    ↓
Evidence Version
    ↓
Immutable Content

A correction SHOULD create a new version or replacement relationship rather than silently rewriting history.

⸻

10. Evidence Types

Veda SHOULD support at least the following evidence classes.

10.1 Direct Observation

Information directly observed by Veda or a trusted sensor.

Example:

filesystem.exists("/workspace/veda")
→ true

⸻

10.2 Measurement

Quantitative observation.

Example:

CPU temperature = 72.4°C

⸻

10.3 Document

Information extracted from a document.

Examples:

* PDF
* Markdown
* HTML
* text
* database record

⸻

10.4 Human Statement

Information explicitly provided by a human.

Example:

User states:
"The project repository is public."

This is evidence that the user made the statement.

It is not automatically proof that the statement is factually correct.

⸻

10.5 External Source

Information retrieved from an external source.

Examples:

* official documentation
* API
* website
* public database
* scientific paper

⸻

10.6 Execution Result

Evidence generated by an action.

Example:

Action:
run build
Result:
exit_code = 0

This is evidence that the command returned exit code 0.

It does not automatically prove the entire software system is correct.

⸻

10.7 Test Result

Evidence generated by a test.

Example:

test suite:
247 passed
0 failed

⸻

10.8 Model Output

Information generated by an AI model.

Model output MUST have lower epistemic status than independently verified observations unless validated.

⸻

10.9 Derived Evidence

Evidence generated from other evidence through a documented transformation.

Example:

Sensor A = 80°C
Sensor B = 82°C
Derived:
Average = 81°C

⸻

10.10 Cryptographic Evidence

Evidence supported by cryptographic mechanisms.

Examples:

* signatures
* hashes
* certificates
* attestations

Cryptographic integrity proves authenticity/integrity of the data under the relevant trust assumptions.

It does not prove the underlying claim is true.

⸻

11. Source Model

Every evidence object SHOULD reference a source.

Examples:

source:
  type: human
  id: user
source:
  type: sensor
  id: temperature-sensor-01
source:
  type: document
  id: book-123
source:
  type: service
  id: github-api

⸻

12. Producer

The producer is the entity that generated or recorded the evidence.

Examples:

sensor
agent
human
tool
API
model
system
external organization

Source and producer may differ.

Example:

Source:
Government database
Producer:
Veda browser tool

⸻

13. Content Reference

Large evidence objects SHOULD use content references rather than duplicating content throughout the system.

Example:

content_ref:
  storage: evidence-store
  object_id: obj-123
content_hash:
  algorithm: SHA-256
  value: ...

⸻

14. Content Integrity

Evidence SHOULD have an integrity mechanism.

Example:

Content
 ↓
Hash
 ↓
Evidence Record

If the content changes:

Hash(content) ≠ stored_hash

the evidence MUST be considered integrity-invalid.

⸻

15. Observation Time

Evidence SHOULD distinguish multiple timestamps.

observed_at

When the underlying phenomenon occurred.

created_at

When the evidence record was created.

received_at

When Veda received the evidence.

expires_at

When the evidence should no longer be considered current without revalidation.

These timestamps MUST NOT be conflated.

⸻

16. Temporal Validity

Evidence may have different temporal properties:

historical
current
future
interval
recurring
unknown

Example:

Evidence:
"Server was online at 10:00."
Validity:
2026-09-16T10:00

This does not imply:

Server is online at 14:00.

⸻

17. Evidence Freshness

Evidence freshness SHOULD be represented explicitly.

Example:

freshness:
  observed_at: 10:00
  freshness_window: 5m

After the freshness window:

Evidence = stale

Stale evidence may remain historically valid.

⸻

18. Evidence Status

Canonical status values:

UNVERIFIED
VERIFIED
PARTIALLY_VERIFIED
DISPUTED
SUPERSEDED
REVOKED
INVALID
EXPIRED
CORRUPTED

⸻

19. Evidence Lifecycle

Canonical lifecycle:

CAPTURED
   ↓
INGESTED
   ↓
NORMALIZED
   ↓
INTEGRITY_CHECKED
   ↓
PROVENANCE_ATTACHED
   ↓
EVALUATED
   ↓
VERIFIED / UNVERIFIED
   ↓
ACTIVE

Alternative states:

DISPUTED
SUPERSEDED
REVOKED
INVALID
EXPIRED

⸻

20. Integrity vs Validity

Veda MUST distinguish:

Integrity
=
Has the evidence been altered?
Validity
=
Is the evidence still applicable?
Truth
=
Does the underlying claim correspond to reality?

These are separate properties.

Example:

A five-year-old signed document may have:

Integrity = valid
Validity = expired

⸻

21. Provenance

Provenance describes the history of evidence.

Example:

Book
 ↓
PDF
 ↓
Text extraction
 ↓
Paragraph
 ↓
Claim
 ↓
Evidence

Another example:

Sensor
 ↓
Raw reading
 ↓
Normalization
 ↓
Aggregation
 ↓
Derived evidence
 ↓
Claim

Veda SHOULD preserve this chain.

⸻

22. Transformation Chain

Every derived evidence object SHOULD record transformations.

Example:

transformation_chain:
  - operation: extract_text
    tool: pdf-parser
  - operation: normalize_units
    tool: unit-engine
  - operation: calculate_average
    tool: statistics-engine

This allows reconstruction of how the result was produced.

⸻

23. Evidence Lineage

The relationship can be represented as:

Evidence A
    ↓
Transformation
    ↓
Evidence B
    ↓
Transformation
    ↓
Evidence C

Evidence C MUST NOT hide the existence of its dependencies.

⸻

24. Claims

Evidence exists primarily in relation to claims.

A Claim is a proposition about the World.

Example:

Claim:
"Veda repository exists."

Evidence:

filesystem observation

Relationship:

Evidence
    SUPPORTS
Claim

⸻

25. Evidence Relationships

Veda SHOULD support:

SUPPORTS
CONTRADICTS
CORROBORATES
QUALIFIES
DERIVES
SUPERSEDES
INVALIDATES
REFUTES

⸻

26. Support Strength

Evidence SHOULD include support strength.

Example:

VERY_WEAK
WEAK
MODERATE
STRONG
VERY_STRONG

This is not absolute truth probability.

It represents the assessed contribution of the evidence to a claim.

⸻

27. Reliability

Reliability describes how consistently a source or evidence mechanism has historically produced useful information.

Example:

reliability:
  score: 0.92
  basis:
    - historical_accuracy
    - independent_verification

Reliability SHOULD be evidence-based.

⸻

28. Reliability Is Not Truth

A highly reliable source can still be wrong.

Therefore:

Reliability ≠ Truth

Reliability modifies belief.

It does not replace verification.

⸻

29. Independence

Evidence SHOULD represent independence.

Two sources repeating the same original source are not necessarily independent.

Example:

Source A
 ↓
Article B
 ↓
Article C

B and C may appear to be two sources.

They are actually derived from one source.

Veda SHOULD detect and record such dependencies.

⸻

30. Correlated Evidence

Evidence from correlated sources SHOULD NOT be counted as independent confirmation.

Example:

10 websites
    ↓
all copied
    ↓
1 original source

This should not be interpreted as ten independent confirmations.

⸻

31. Evidence Confidence

Evidence MAY contain a confidence assessment.

Example:

confidence:
  value: 0.87
  basis:
    - direct_observation
    - trusted_sensor
    - independent_confirmation

Confidence MUST include its basis where practical.

⸻

32. Claim Confidence

Claim confidence is distinct from evidence confidence.

Example:

Evidence A confidence = 0.90
Evidence B confidence = 0.80
Claim confidence = derived assessment

The claim confidence MUST NOT simply equal the average of evidence scores.

The evidence relationship, independence, quality, recency, and contradiction all matter.

⸻

33. Evidence Aggregation

Veda MAY combine multiple evidence items.

Example:

Evidence A ─┐
Evidence B ─┼→ Claim Evaluation
Evidence C ─┘

The aggregation method MUST be recorded.

Possible methods:

* rule-based
* weighted evidence
* Bayesian
* probabilistic
* logical
* human judgment
* model-assisted reasoning

⸻

34. No Hidden Evidence Aggregation

If evidence aggregation materially changes a decision, the system SHOULD preserve:

input evidence
+
method
+
parameters
+
result

This enables replay and audit.

⸻

35. Contradictory Evidence

Example:

Evidence A:
Server = online
Evidence B:
Server = offline

Veda MUST NOT silently select one.

The system SHOULD represent:

Claim
  ↑
  ├── SUPPORTS ← Evidence A
  └── CONTRADICTS ← Evidence B

Conflict resolution is handled by RFC-0014.

⸻

36. Evidence Supersession

New evidence may replace the operational relevance of old evidence.

Example:

Evidence A:
Version = 1.0
Evidence B:
Version = 2.0

Evidence B may supersede A for current-state reasoning.

A remains historically preserved.

⸻

37. Evidence Revocation

Evidence MAY be revoked.

Reasons include:

* source withdrawal
* discovered fabrication
* corrupted content
* invalid measurement
* compromised sensor
* invalid provenance
* legal removal
* incorrect extraction

Revocation MUST NOT erase historical audit records.

⸻

38. Evidence Expiration

Some evidence naturally expires.

Examples:

weather observation
system health
stock price
network status
temporary configuration

Expiration means:

Do not assume current validity without revalidation.

It does not mean:

The historical observation never happened.

⸻

39. Evidence Quality

Quality MAY include:

quality:
  completeness:
  precision:
  resolution:
  consistency:
  freshness:
  provenance_quality:
  extraction_quality:

Quality assessment SHOULD be explainable.

⸻

40. Source Trust

Source trust MAY influence evidence evaluation.

However:

Trust ≠ Evidence

A trusted source produces potentially stronger evidence.

It does not automatically make every statement true.

⸻

41. Model-Generated Evidence

When a model generates information:

Model Output

Veda SHOULD record:

producer:
  type: model
  model_id:
  model_version:
prompt_context:
context_hash:
generation_parameters:
timestamp:

The output is evidence of what the model generated.

It becomes evidence about the external World only after appropriate validation.

⸻

42. Human-Generated Evidence

Human statements SHOULD be preserved accurately.

Example:

Human statement:
"The server was restarted at 09:00."

Veda records:

Evidence:
Human made this statement.

If the claim is important, Veda SHOULD seek independent evidence.

⸻

43. Sensor Evidence

Sensors SHOULD record:

* sensor identity
* calibration state
* measurement unit
* measurement uncertainty
* timestamp
* environment
* sampling method

Example:

type: measurement
sensor:
  id: temp-01
value:
  72.4
unit:
  celsius
uncertainty:
  ±0.5

⸻

44. Execution Evidence

Execution evidence SHOULD include:

action_id
process_id
executor
command/operation reference
start_time
end_time
exit_status
output_reference
side_effect_observations
verification_result

Execution success is not automatically outcome success.

⸻

45. Verification Evidence

Verification MAY produce evidence.

Example:

Action:
Create file
Verification:
File exists
Hash matches expected content

Result:

Verification Evidence

This can support the claim:

"The requested file was created correctly."

⸻

46. External Source Evidence

For external sources, Veda SHOULD preserve:

source identity
location/reference
retrieval timestamp
content hash
retrieval method
relevant excerpt location
license information
transformation history

This is particularly important for Veda’s future book and knowledge library.

⸻

47. Book Evidence

A book SHOULD produce provenance such as:

Book
 ↓
Edition
 ↓
Chapter
 ↓
Section
 ↓
Page
 ↓
Paragraph
 ↓
Claim
 ↓
Evidence

This allows Veda to answer:

Where in the book did this knowledge come from?

rather than merely producing a mysterious vector-database hallucination from somewhere in the digital basement.

⸻

48. Evidence and Memory

Memory SHOULD reference evidence.

Example:

Memory
 ↓
Claim
 ↓
Evidence

A memory without provenance SHOULD be treated as lower-confidence information.

This is particularly important for:

* user preferences
* system state
* learned procedures
* past failures
* historical events

⸻

49. Evidence and Knowledge

Knowledge SHOULD reference supporting evidence.

Example:

Knowledge
  ↓
Claim
  ├── Evidence A
  ├── Evidence B
  └── Evidence C

Knowledge without provenance SHOULD be considered incomplete.

⸻

50. Evidence and Learning

Learning systems MUST preserve evidence lineage.

Example:

Experience
 ↓
Reflection
 ↓
Lesson
 ↓
Learning Update

The lesson SHOULD reference the evidence that produced it.

This prevents Veda from learning an unsupported assumption and later treating that assumption as fact.

⸻

51. Evidence and World Model

World state updates SHOULD reference evidence.

Example:

World Entity:
server-01
State:
status = healthy
Evidence:
health-check-2026-09-16

If evidence becomes invalid, the World Model SHOULD be able to identify affected state.

⸻

52. Evidence and World Transitions

RFC-0004 World Transition SHOULD accept evidence references.

Conceptually:

Event
 ↓
Evidence
 ↓
State Transition
 ↓
Verification
 ↓
Commit

The transition SHOULD be traceable to the evidence that justified it.

⸻

53. Evidence and Authorization

Evidence MAY support authorization decisions.

Example:

Evidence:
User explicitly approved operation X.
Authorization:
ALLOW operation X.

The approval evidence MUST be tied to:

* identity
* action
* timestamp
* scope
* authorization context

⸻

54. Evidence and Leases

RFC-0011 leases SHOULD preserve evidence supporting issuance.

Example:

Lease
 ↓
Authorization
 ↓
Approval Evidence

If the authorization basis is invalidated, dependent leases MAY need revocation.

⸻

55. Evidence Quality Pipeline

A canonical evidence ingestion pipeline is:

Raw Source
    ↓
Capture
    ↓
Identity
    ↓
Integrity Check
    ↓
Provenance
    ↓
Normalization
    ↓
Quality Assessment
    ↓
Freshness Assessment
    ↓
Reliability Assessment
    ↓
Independence Analysis
    ↓
Verification
    ↓
Evidence Store

⸻

56. Evidence Quarantine

Suspicious evidence SHOULD enter quarantine.

Example:

Captured
   ↓
Suspicious
   ↓
QUARANTINED
   ↓
Review / Verification

Quarantined evidence MUST NOT automatically influence high-impact decisions.

⸻

57. Evidence Access Control

Evidence may contain sensitive information.

Evidence SHOULD support:

classification
access policy
owner
scope
retention
redaction

Examples:

PUBLIC
INTERNAL
PRIVATE
SENSITIVE
SECRET

⸻

58. Evidence Retention

Retention policies SHOULD distinguish:

content retention
metadata retention
audit retention
legal retention

Deleting evidence content MUST NOT necessarily delete the fact that evidence once existed.

⸻

59. Evidence Deletion

If evidence must be removed, Veda SHOULD preserve a tombstone where legally and technically appropriate.

Example:

evidence_id: evidence-123
status: REVOKED
content: unavailable
reason: retention_policy

This preserves lineage without retaining restricted content.

⸻

60. Evidence Reproducibility

Evidence SHOULD be reproducible when possible.

For generated evidence:

input
+
tool version
+
parameters
+
environment
=
reproducible result

If reproducibility is impossible, the evidence SHOULD state why.

⸻

61. Evidence Determinism

Deterministic evidence generation SHOULD record enough metadata for replay.

Nondeterministic processes SHOULD record:

* random seed where available
* model version
* tool version
* environment
* input references

⸻

62. Evidence Hash Chain

High-integrity systems MAY maintain a chain:

Evidence A
 ↓ hash
Evidence B
 ↓ hash
Evidence C

This can provide tamper-evident history.

RFC-0031 will define the broader audit/event architecture.

⸻

63. Evidence Graph

Veda SHOULD represent evidence relationships as a graph.

        Evidence A
             |
          SUPPORTS
             ↓
           Claim
          /     \
 SUPPORTS       CONTRADICTS
     ↓               ↓
Evidence B       Evidence C

This enables:

* provenance traversal
* contradiction discovery
* confidence analysis
* knowledge auditing
* evidence impact analysis

⸻

64. Evidence Impact

When evidence changes status, Veda SHOULD identify dependent objects.

Example:

Evidence A
   ↓
Claim B
   ↓
Knowledge C
   ↓
Decision D

If Evidence A becomes INVALID:

Evidence A → INVALID
        ↓
Claim B → REEVALUATE
        ↓
Knowledge C → REEVALUATE
        ↓
Decision D → IMPACT CHECK

This is critical for a continuously evolving system.

⸻

65. Evidence Dependency Graph

The system SHOULD maintain dependency relationships:

Evidence
 ↓
Claim
 ↓
Knowledge
 ↓
Goal
 ↓
Decision
 ↓
Action

This enables Veda to determine:

What decisions were based on this evidence?

⸻

66. Evidence Freshness Re-evaluation

Veda SHOULD periodically re-evaluate time-sensitive evidence.

Example:

Evidence:
"Server healthy"
TTL:
5 minutes

After TTL:

Status:
STALE

The system may trigger:

Observation
 ↓
New Evidence
 ↓
Re-evaluation

⸻

67. Evidence Confidence Decay

For some evidence types, confidence SHOULD decay over time.

Conceptually:

Confidence(t) = f(initial_confidence, age, volatility)

The exact mathematical function is implementation-specific.

Highly volatile domains SHOULD decay faster than stable historical facts.

⸻

68. Evidence Independence Graph

Veda SHOULD represent source dependencies.

Example:

Source A
 ├── Article B
 └── Article C

B and C are not independent.

This prevents false confidence from duplicated information.

⸻

69. Evidence Contradiction Detection

Evidence MAY contradict existing knowledge.

Example:

Current Knowledge:
Server = online
New Evidence:
Server = offline

The system SHOULD trigger:

Contradiction Detection
       ↓
Evidence Evaluation
       ↓
World Reconciliation

RFC-0014 will define the conflict model.

⸻

70. Evidence Confidence Levels

Veda MAY use qualitative levels:

UNKNOWN
VERY_LOW
LOW
MEDIUM
HIGH
VERY_HIGH
VERIFIED

The system SHOULD distinguish:

HIGH confidence

from:

VERIFIED

High confidence is probabilistic.

Verified means a defined verification condition has passed.

⸻

71. Verification Is Contextual

Evidence is verified relative to a verification criterion.

Example:

Claim:
"File exists."
Verification:
filesystem.exists(file)

Result:

VERIFIED

But:

Claim:
"File is safe."

requires a different verification criterion.

Therefore:

Verified for X
≠
Verified for everything

⸻

72. Evidence of Absence

Absence requires special treatment.

Example:

No file found.

This may mean:

file does not exist

or:

search failed

or:

permission denied

Veda MUST distinguish:

OBSERVED_ABSENCE
SEARCH_INCONCLUSIVE
ACCESS_LIMITED
UNKNOWN

⸻

73. Negative Evidence

Evidence can support a negative claim.

Example:

Repeated health checks failed.

This may support:

Server likely unavailable.

Negative evidence SHOULD preserve the observation method and coverage.

⸻

74. Evidence Completeness

Evidence MAY be incomplete.

Example:

A database query returned 100 records.

This does not necessarily mean:

The database contains only 100 records.

unless the query guarantees completeness.

Veda SHOULD record completeness assumptions.

⸻

75. Evidence Scope

Evidence MUST have scope.

Example:

Evidence:
CPU = 80%
Scope:
server-01
2026-09-16T03:00

It MUST NOT automatically be generalized to:

all servers
all time
all workloads

⸻

76. Evidence Generalization

When evidence is generalized:

Specific Evidence
 ↓
General Claim

the transformation MUST be explicit.

Example:

Observed:
Machine A failed under load.
Invalid generalization:
All machines fail under load.

Veda SHOULD avoid unsupported generalization.

⸻

77. Evidence Confidence and Decisions

A decision MAY require a minimum evidence threshold.

Example:

decision_policy:
  minimum_confidence: 0.90
  require_independent_sources: 2

If the threshold is not met:

DEFER

or:

REQUEST_MORE_EVIDENCE

rather than fabricating certainty.

⸻

78. Evidence Requests

Veda SHOULD be able to identify missing evidence.

Example:

Goal:
Determine whether server is healthy.
Known:
CPU = 40%
Missing:
memory health
disk health
network health
service status

Veda can generate:

Evidence Request

rather than guessing.

⸻

79. Evidence Acquisition

Evidence acquisition itself is an Action.

Therefore:

Evidence Need
 ↓
Goal
 ↓
Plan
 ↓
Action
 ↓
Capability
 ↓
Authorization
 ↓
Lease
 ↓
Observation
 ↓
Evidence

This integrates evidence generation into the main Veda architecture.

⸻

80. Evidence Provenance and Audit

Every durable evidence object SHOULD be linked to the Event/Audit fabric.

Minimum relationship:

Evidence
 ↔
Event
 ↔
Actor
 ↔
Action
 ↔
Process
 ↔
World

⸻

81. Evidence Privacy

Evidence may contain:

* personal information
* credentials
* private documents
* private communications
* system secrets

The Evidence Store MUST enforce access controls.

Evidence MUST NOT be automatically exposed to all models or agents.

⸻

82. Evidence Redaction

Redaction SHOULD preserve provenance.

Example:

Original Evidence
      ↓
Sensitive Field Redacted
      ↓
Derived Evidence

The system SHOULD record:

redaction method
redaction authority
timestamp
reason

⸻

83. Evidence Licensing

External evidence may have licensing restrictions.

For documents and books, Veda SHOULD preserve:

source
license
access rights
usage restrictions

This becomes important for the future Veda knowledge library and training/data refinery.

⸻

84. Evidence and Books

The planned Veda book library SHOULD use evidence-first ingestion.

Canonical structure:

Book
 ↓
Edition
 ↓
Chapter
 ↓
Section
 ↓
Passage
 ↓
Claim
 ↓
Evidence
 ↓
Knowledge

This allows Veda to distinguish:

"The book states X."

from:

"X is objectively true."

That distinction is essential.

⸻

85. Evidence and Training Data

If evidence becomes training material:

Evidence
 ↓
Quality Check
 ↓
License Check
 ↓
Deduplication
 ↓
Dataset

The dataset SHOULD retain provenance back to the original evidence.

This supports future:

* SFT
* LoRA
* evaluation
* retrieval
* distillation

without losing source lineage.

⸻

86. Evidence and Self-Learning

Veda MUST NOT treat every successful interaction as truth.

Instead:

Experience
 ↓
Observation
 ↓
Evidence
 ↓
Evaluation
 ↓
Lesson
 ↓
Learning Proposal

This reduces accidental self-reinforcement.

⸻

87. Evidence Failure Modes

The system SHOULD detect:

missing provenance
unknown source
stale evidence
duplicate evidence
correlated evidence
tampered evidence
contradictory evidence
incomplete evidence
low-quality extraction
invalid measurement
unverified model output
expired evidence
revoked evidence

⸻

88. Evidence Store

A conceptual storage architecture:

Evidence Registry
        |
        +── Evidence Objects
        |
        +── Source Registry
        |
        +── Provenance Graph
        |
        +── Claim Index
        |
        +── Integrity Index
        |
        +── Freshness Index
        |
        +── Dependency Graph
        |
        +── Verification Records

⸻

89. Evidence Query Interface

Veda SHOULD support queries such as:

What evidence supports Claim X?
What source produced this evidence?
When was this observed?
Is this evidence still current?
What evidence contradicts this claim?
What decisions depend on this evidence?
What knowledge was derived from this book?

⸻

90. Example Evidence Query

query:
  claim_id: claim-server-health
requirements:
  minimum_status: VERIFIED
  freshness: 5m
  independent_sources: 2

Result:

result:
  status: insufficient
reason:
  independent_sources: 1
  required: 2

Veda should then seek additional evidence rather than pretending the requirement was met.

⸻

91. Evidence Object Example

evidence_id: evidence-health-001
version: 1
type: measurement
source_ref:
  type: sensor
  id: server-health-monitor
producer_ref:
  type: tool
  id: health-checker
content:
  cpu_usage: 31.2
  memory_usage: 48.1
  disk_health: healthy
observed_at: 2026-09-16T03:00:00+07:00
created_at: 2026-09-16T03:00:02+07:00
quality:
  completeness: 0.91
reliability:
  score: 0.95
integrity:
  hash: "..."
verification:
  status: VERIFIED
supports:
  - claim-server-healthy
status: ACTIVE

⸻

92. Example: Model Output

evidence_id: evidence-model-001
type: model_output
producer_ref:
  type: model
  id: reasoning-model-x
content_ref:
  object_id: output-123
created_at: 2026-09-16T03:05:00+07:00
status: UNVERIFIED

This evidence establishes:

The model produced this output.

It does not automatically establish:

The output is true.

⸻

93. Example: Independent Verification

Model Output
     ↓
Claim
     ↓
External Documentation
     ↓
Verification
     ↓
Claim Confidence ↑

This is the preferred path for important facts.

⸻

94. Example: Contradiction

Evidence A
"Version = 1.0"
Evidence B
"Version = 2.0"

Both remain stored.

The system creates:

Claim Conflict

rather than deleting A.

RFC-0014 determines resolution.

⸻

95. Example: Evidence Invalidation

Initial:

Evidence A
status = VERIFIED

Later:

Source announces measurement error.

Veda performs:

Evidence A
   ↓
REVOKED
   ↓
Dependent Claims
   ↓
REEVALUATE
   ↓
Dependent Knowledge
   ↓
REEVALUATE

⸻

96. Evidence Dependency Impact

The system SHOULD answer:

"If this evidence is invalid,
what else becomes uncertain?"

This requires dependency tracking across:

Evidence
Claims
Knowledge
Memory
Goals
Decisions
Actions
World State

This is one of the reasons evidence is a first-class object rather than merely metadata attached to text.

⸻

97. Security Invariants

EVID-1

Every durable evidence object MUST have a unique identifier.

EVID-2

Evidence provenance SHOULD be preserved.

EVID-3

Evidence content SHOULD be integrity-protected.

EVID-4

Evidence MUST distinguish observation time from record creation time where relevant.

EVID-5

Evidence MUST NOT automatically be treated as truth.

EVID-6

Model output MUST NOT automatically become verified external-world evidence.

EVID-7

Evidence scope MUST be represented.

EVID-8

Evidence freshness MUST be representable.

EVID-9

Evidence invalidation MUST be traceable.

EVID-10

Evidence revocation MUST NOT silently erase historical provenance.

EVID-11

Contradictory evidence MUST remain representable.

EVID-12

Correlated evidence MUST NOT automatically count as independent confirmation.

EVID-13

Derived evidence MUST preserve transformation lineage.

EVID-14

Evidence-dependent claims MUST be identifiable.

EVID-15

Evidence-dependent decisions SHOULD be identifiable.

EVID-16

Evidence access MUST respect security and privacy policies.

EVID-17

Sensitive evidence MUST NOT be exposed to unauthorized agents or models.

EVID-18

Evidence deletion MUST preserve necessary audit lineage.

EVID-19

Evidence quality assessments SHOULD be explainable.

EVID-20

Confidence MUST NOT be represented as certainty unless a defined verification condition has passed.

EVID-21

Evidence freshness MUST NOT silently extend its validity.

EVID-22

Historical validity MUST be distinguished from current validity.

EVID-23

Evidence scope MUST NOT be silently generalized.

EVID-24

Evidence transformations MUST be traceable where materially relevant.

EVID-25

Evidence used for high-impact decisions SHOULD meet defined verification requirements.

EVID-26

Evidence acquisition MUST itself be traceable.

EVID-27

Evidence provenance MUST survive knowledge extraction where practical.

EVID-28

Training data derived from evidence SHOULD retain provenance.

EVID-29

Evidence invalidation SHOULD trigger impact analysis.

EVID-30

Veda MUST preserve the distinction between:

Observed
Reported
Inferred
Verified
Believed
Known

⸻

98. Canonical Epistemic Chain

The canonical Veda truth pipeline is:

Reality
   ↓
Observation
   ↓
Evidence
   ↓
Claim
   ↓
Evaluation
   ↓
Verification
   ↓
Knowledge
   ↓
Belief / Confidence
   ↓
Decision

The reverse dependency chain must also be traceable:

Decision
   ↓
Knowledge
   ↓
Claim
   ↓
Evidence
   ↓
Source
   ↓
Reality / Observation

⸻

99. Final Principle

Veda must never confuse:

"I have information"

with:

"I know the truth."

Instead:

I observed X
    ↓
I have evidence for X
    ↓
Evidence has provenance
    ↓
Evidence has quality
    ↓
Evidence may support or contradict a claim
    ↓
The claim is evaluated
    ↓
The claim may become knowledge
    ↓
Knowledge retains its evidence
    ↓
New evidence can challenge it
    ↓
Veda can revise its belief

The fundamental rule is:

No Provenance
    → Weak Evidence
No Evidence
    → No Strong Claim
Contradictory Evidence
    → No Silent Certainty
Expired Evidence
    → No Assumed Current Truth
Model Output
    → Not Automatically Truth
Verified Claim
    → Knowledge With Traceable Basis

Veda therefore treats evidence as a first-class object, not an afterthought attached to a generated answer.

⸻

End of RFC-0012