RFC-0013: Knowledge Model

Status: Draft
Version: v0.1.0
Layer: Layer 5 — Truth
Module: Knowledge / Claims / Provenance
Depends On: RFC-0001, RFC-0002, RFC-0003, RFC-0004, RFC-0012
Related: RFC-0014, RFC-0017, RFC-0018, RFC-0021, RFC-0022, RFC-0023, RFC-0025, RFC-0026, RFC-0035, RFC-0036

⸻

1. Abstract

RFC-0013 defines the Knowledge Model for Veda.

Knowledge is the structured representation of claims about the World together with their provenance, evidence, scope, temporal validity, confidence, relationships, and verification status.

The fundamental principle is:

Knowledge ≠ Text
Knowledge ≠ Memory
Knowledge ≠ Embedding
Knowledge ≠ Model Parameters
Knowledge ≠ Belief
Knowledge ≠ Truth

Instead:

Evidence
    ↓
Claim
    ↓
Evaluation
    ↓
Knowledge

Knowledge exists to allow Veda to represent not merely:

“What information do I have?”

but:

“What do I currently believe to be true, why, within what scope, based on what evidence, and what would change my conclusion?”

⸻

2. Motivation

A conventional information system often stores:

Document → Text → Search

A language model often stores:

Training Data → Parameters → Generation

Neither structure is sufficient for an autonomous cognitive system.

Veda needs to distinguish:

Book says X
        ↓
Claim X exists
Sensor observed Y
        ↓
Observation Y
Multiple sources support Z
        ↓
Claim Z has stronger support
New evidence contradicts Z
        ↓
Claim Z must be reevaluated

Knowledge therefore becomes a structured, revisable layer between raw evidence and reasoning.

⸻

3. Design Goals

RFC-0013 MUST provide:

1. Structured claims.
2. Knowledge provenance.
3. Evidence references.
4. Scope.
5. Temporal validity.
6. Confidence.
7. Verification state.
8. Knowledge relationships.
9. Dependency tracking.
10. Contradiction support.
11. Versioning.
12. Supersession.
13. Uncertainty.
14. Assumptions.
15. Context.
16. Source attribution.
17. Knowledge composition.
18. Knowledge decomposition.
19. Queryability.
20. Impact analysis.
21. Human correction.
22. Machine learning compatibility.
23. Book/document integration.
24. World Model integration.
25. Decision integration.

⸻

4. Non-Goals

RFC-0013 does not define:

* evidence generation
* general reasoning
* planning
* model architecture
* memory architecture
* policy
* authorization
* execution
* truth as a philosophical absolute

Those concerns belong to other RFCs.

⸻

5. Core Principle

The canonical knowledge chain is:

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
Knowledge

Knowledge should therefore preserve its relationship to evidence.

A knowledge object without provenance is incomplete.

⸻

6. Epistemic Separation

Veda MUST distinguish:

OBSERVED

from:

REPORTED

from:

INFERRED

from:

VERIFIED

from:

BELIEVED

from:

KNOWN

These states must not be collapsed into one generic truth field.

⸻

7. Knowledge Definition

Knowledge is:

A structured, provenance-aware representation of one or more claims that Veda currently accepts as sufficiently supported for a defined purpose and context.

Knowledge is therefore contextual.

Example:

Knowledge:
"Server A is healthy."

may be valid:

at 03:00

but not necessarily:

at 06:00

⸻

8. Knowledge Object

A canonical Knowledge object SHOULD contain:

knowledge_id:
version:
subject:
predicate:
object:
claim_id:
type:
scope:
context:
valid_from:
valid_until:
epistemic_status:
confidence:
evidence_refs:
source_refs:
assumptions:
conditions:
relationships:
dependencies:
derived_from:
supports:
contradicts:
supersedes:
verification:
quality:
status:
created_at:
updated_at:
created_by:

⸻

9. Knowledge as a Claim

The fundamental representation is:

Subject
   +
Predicate
   +
Object

Example:

Subject:
Veda
Predicate:
has_project_name
Object:
"Veda"

Another:

Subject:
server-01
Predicate:
status
Object:
healthy

⸻

10. Claim vs Knowledge

A Claim is a proposition.

Knowledge is a claim plus the epistemic structure required to use it responsibly.

Claim:
"Server A is healthy."
Knowledge:
Claim
+
Evidence
+
Scope
+
Time
+
Confidence
+
Verification
+
Provenance

Therefore:

Claim ⊂ Knowledge

⸻

11. Knowledge Types

Veda SHOULD support at least:

FACTUAL
OBSERVATIONAL
DEFINITIONAL
PROCEDURAL
CAUSAL
TEMPORAL
SPATIAL
RELATIONAL
STATISTICAL
HISTORICAL
EXPERIENTIAL
PREFERENCE
POLICY
CONSTRAINT
HEURISTIC
MODEL
HYPOTHESIS

⸻

12. Factual Knowledge

Represents claims about the World.

Example:

Thailand is located in Southeast Asia.

The claim should retain supporting evidence.

⸻

13. Observational Knowledge

Derived directly from observations.

Example:

CPU usage on machine A was 83% at 03:10.

Observational knowledge should preserve measurement provenance.

⸻

14. Definitional Knowledge

Defines concepts.

Example:

A Capability is a bounded technical ability.

Definitions may originate from:

* Veda RFCs
* books
* standards
* user definitions
* domain sources

⸻

15. Procedural Knowledge

Represents how to perform something.

Example:

To deploy service X:
1. Build
2. Test
3. Package
4. Deploy
5. Verify

Procedural knowledge should reference evidence or successful experience where appropriate.

⸻

16. Causal Knowledge

Represents relationships involving causality.

Example:

Increasing load
    ↓
increases CPU usage

Causal knowledge requires stronger evidence than simple correlation.

It SHOULD explicitly represent:

cause
effect
conditions
mechanism
confidence
evidence

⸻

17. Temporal Knowledge

Represents facts involving time.

Example:

Project X started on date Y.

Temporal knowledge should integrate with RFC-0021.

⸻

18. Spatial Knowledge

Represents relationships involving location.

Example:

Device A
is located in
Room B

⸻

19. Relational Knowledge

Represents relationships between entities.

Example:

Agent A
belongs_to
Project B

⸻

20. Statistical Knowledge

Represents distributions, probabilities, measurements, or aggregate patterns.

Example:

Build failure rate:
4.2%

Statistical knowledge MUST preserve:

* population
* sample
* time period
* method
* uncertainty

⸻

21. Historical Knowledge

Represents knowledge about past World states.

Example:

On 2026-09-01:
Repository contained RFC-0010.

Historical knowledge should not be overwritten merely because the current state differs.

⸻

22. Experiential Knowledge

Represents knowledge derived from Veda’s own experience.

Example:

Experience:
Running build after typecheck catches fewer errors than running typecheck alone.

This type MUST preserve the underlying experience and evidence.

It must not automatically become universal knowledge.

⸻

23. Preference Knowledge

Represents preferences.

Example:

User prefers concise technical explanations.

Preference knowledge MUST be distinguished from factual knowledge.

A preference can be changed by the user.

⸻

24. Policy Knowledge

Represents system rules.

Example:

High-risk actions require human approval.

Policy knowledge MUST remain linked to its authoritative policy source.

⸻

25. Constraint Knowledge

Represents conditions that restrict possible actions.

Example:

Only modify files inside /workspace/veda/.

Constraint knowledge may originate from:

* policy
* authorization
* capability
* system configuration
* user instruction

⸻

26. Heuristic Knowledge

Represents useful but non-guaranteed rules.

Example:

If a build fails after dependency changes,
run the type checker first.

Heuristics MUST NOT be represented as absolute facts.

⸻

27. Hypothesis

A hypothesis is a candidate explanation not yet sufficiently established.

Example:

Hypothesis:
The service failed because memory pressure caused the process to terminate.

Status:

HYPOTHESIS

It may later become:

SUPPORTED

or:

REFUTED

⸻

28. Knowledge Status

Canonical states:

PROPOSED
CANDIDATE
SUPPORTED
VERIFIED
ACTIVE
DISPUTED
STALE
SUPERSEDED
REVOKED
INVALID
ARCHIVED

⸻

29. Knowledge Lifecycle

Canonical lifecycle:

CAPTURED
   ↓
NORMALIZED
   ↓
GROUNDED
   ↓
EVALUATED
   ↓
CANDIDATE
   ↓
SUPPORTED
   ↓
VERIFIED
   ↓
ACTIVE

Alternative transitions:

DISPUTED
STALE
SUPERSEDED
REVOKED
INVALID
ARCHIVED

⸻

30. Knowledge Confidence

Knowledge MAY include confidence.

Example:

confidence:
  value: 0.93
  basis:
    - direct_observation
    - independent_verification
    - recent_evidence

Confidence is an epistemic estimate.

It is not a universal truth probability.

⸻

31. Confidence Is Not Certainty

The system MUST distinguish:

0.99 confidence

from:

VERIFIED

Verification means a defined verification condition passed.

Confidence describes belief strength.

⸻

32. Evidence Support

Every important knowledge object SHOULD reference evidence.

Example:

evidence_refs:
  - evidence-001
  - evidence-007
  - evidence-019

The system should be able to answer:

Why does Veda believe this?

with actual provenance.

⸻

33. Source References

Knowledge SHOULD preserve source references.

Example:

source_refs:
  - book-123
  - document-456
  - api-response-789

A source reference is not equivalent to evidence.

The evidence layer provides the specific supporting observation or content.

⸻

34. Knowledge Scope

Knowledge MUST define scope where applicable.

Example:

Claim:
"System A is reliable."
Scope:
System A
Version 2.4
Under workload < 50%

Without scope, Veda may incorrectly generalize.

⸻

35. Context

Knowledge may depend on context.

Example:

Knowledge:
"Strategy A works."
Context:
Linux
Python 3.13
CPU architecture X
Dataset Y

Outside the context, applicability may be unknown.

⸻

36. Conditions

Knowledge MAY contain conditions.

Example:

If:
CPU > 90%
Then:
Performance degradation becomes likely.

Conditional knowledge should not be treated as unconditional.

⸻

37. Assumptions

Knowledge MAY depend on assumptions.

Example:

assumptions:
  - network_is_available
  - clock_is_synchronized
  - sensor_is_calibrated

If an assumption becomes false, dependent knowledge SHOULD be reevaluated.

⸻

38. Knowledge Dependencies

Knowledge may depend on other knowledge.

Example:

Knowledge A:
Python package X supports feature Y.
Knowledge B:
Project uses package X.
Therefore:
Knowledge C:
Project can use feature Y.

Knowledge C should preserve dependencies on A and B.

⸻

39. Knowledge Graph

Veda SHOULD represent knowledge as a graph.

Entity
  |
  +── Relationship ──→ Entity
  |
  +── Attribute ─────→ Value
  |
  +── Claim ─────────→ Evidence

This graph integrates with the World Model.

⸻

40. World Model vs Knowledge Model

These are related but distinct.

World Model

Represents:

What is currently believed to exist
and how entities relate.

Knowledge Model

Represents:

Why Veda believes a claim,
where it came from,
how certain it is,
and under what conditions it applies.

Therefore:

World Model
    ← grounded by →
Knowledge
    ← supported by →
Evidence

⸻

41. Knowledge and World State

Example:

World:
server-01.status = healthy

Supporting knowledge:

Knowledge:
server-01.status = healthy
Evidence:
health-check-001
Observed:
03:00
Confidence:
0.96

If new evidence says:

server-01.status = failed

the World Model must be reevaluated.

⸻

42. Knowledge Versioning

Knowledge SHOULD be versioned.

Example:

Knowledge K1 v1:
Service A uses 2GB RAM.
Knowledge K1 v2:
Service A uses 3GB RAM under workload X.

Old knowledge should remain historically accessible.

⸻

43. Knowledge Supersession

New knowledge may supersede old knowledge.

Example:

K1:
Version 1.0 is current.
K2:
Version 2.0 is current.

K2 may supersede K1.

K1 remains historically valid.

⸻

44. Knowledge Revocation

Knowledge may be explicitly revoked when:

* source is fraudulent
* evidence is invalid
* extraction was incorrect
* reasoning was flawed
* underlying assumptions are false

Revocation MUST preserve the historical record.

⸻

45. Knowledge Staleness

Knowledge becomes stale when its supporting evidence is no longer current.

Example:

Knowledge:
Server is healthy.
Last evidence:
3 hours ago.
Required freshness:
5 minutes.

The knowledge becomes:

STALE

not necessarily:

FALSE

⸻

46. Knowledge Expiration

Some knowledge has an explicit validity interval.

Example:

valid_from: 2026-09-16T03:00
valid_until: 2026-09-16T04:00

After the interval:

ACTIVE → EXPIRED / STALE

depending on semantics.

⸻

47. Knowledge Contradiction

Veda MUST support multiple incompatible knowledge claims.

Example:

K1:
Server = online
K2:
Server = offline

Both may temporarily exist.

The contradiction system defined in RFC-0014 determines resolution.

⸻

48. No Silent Overwrite

When new knowledge conflicts with old knowledge, Veda MUST NOT simply replace the old record.

Instead:

Old Knowledge
      ↓
Conflict
      ↓
New Knowledge
      ↓
Resolution

This preserves epistemic history.

⸻

49. Knowledge Corroboration

Multiple independent evidence sources may strengthen a knowledge claim.

Example:

Sensor
+
Log
+
External Monitor
        ↓
Corroboration
        ↓
Claim

The system MUST account for source independence.

⸻

50. Knowledge Derivation

Knowledge may be derived from other knowledge.

Example:

K1:
A > B
K2:
B > C
Derived:
A > C

Derived knowledge MUST preserve its derivation chain.

⸻

51. Deductive Knowledge

For logically derived knowledge:

Premises
   ↓
Rule
   ↓
Conclusion

Veda SHOULD record:

premises
rule
derivation_engine
result

⸻

52. Probabilistic Knowledge

For probabilistic conclusions:

Evidence
   ↓
Model
   ↓
Probability
   ↓
Claim

The model and assumptions SHOULD be recorded.

⸻

53. Causal Knowledge

Causal knowledge SHOULD NOT be inferred merely from correlation.

Example:

A and B occur together

does not automatically imply:

A causes B

Causal claims SHOULD require stronger evidence or explicit assumptions.

⸻

54. Knowledge Quality

Knowledge quality MAY include:

quality:
  evidence_quality:
  provenance_quality:
  freshness:
  completeness:
  consistency:
  verification_strength:
  source_independence:

⸻

55. Knowledge Authority

Authority of a source and confidence in a claim are distinct.

Example:

Official source:
high source authority
Claim:
possibly outdated

Therefore:

Source Authority ≠ Claim Truth

⸻

56. Knowledge Query

Veda SHOULD support queries such as:

What does Veda currently know about X?
Why does Veda believe X?
What evidence supports X?
What contradicts X?
When was X last verified?
Under what conditions is X true?
What assumptions does X depend on?
What decisions depend on X?

⸻

57. Knowledge Retrieval

Retrieval SHOULD support multiple strategies:

semantic retrieval
keyword retrieval
graph traversal
temporal retrieval
entity retrieval
relationship retrieval
evidence retrieval
causal retrieval

Vector search alone MUST NOT be treated as the complete knowledge system.

⸻

58. Embeddings

Embeddings MAY be used for retrieval.

However:

Embedding ≠ Knowledge

An embedding represents a vectorized representation useful for similarity.

It does not inherently preserve:

* truth
* provenance
* authority
* temporal validity
* contradiction
* causality
* confidence

These must remain explicit.

⸻

59. Knowledge Storage

A conceptual architecture:

Knowledge System
      |
      +── Claim Store
      |
      +── Entity Store
      |
      +── Relationship Graph
      |
      +── Evidence Links
      |
      +── Provenance Graph
      |
      +── Temporal Index
      |
      +── Semantic Index
      |
      +── Confidence Index
      |
      +── Dependency Graph

⸻

60. Book Knowledge

Veda’s book library SHOULD represent:

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

Example:

Book:
"The Art of War"
Passage:
specific passage
Claim:
concept extracted from passage
Evidence:
exact passage reference
Knowledge:
structured interpretation

The interpretation MUST be distinguished from what the book literally states.

⸻

61. Document Knowledge

The same architecture applies to:

* PDFs
* websites
* manuals
* source code
* research papers
* notes
* RFCs
* logs

⸻

62. Knowledge Extraction

A document ingestion pipeline SHOULD be:

Document
   ↓
Parse
   ↓
Segment
   ↓
Extract Claims
   ↓
Attach Provenance
   ↓
Evaluate
   ↓
Store Knowledge

Extraction MUST preserve the original source.

⸻

63. Knowledge Normalization

Different sources may express the same concept differently.

Example:

"CPU utilization"
"CPU usage"
"processor utilization"

Veda MAY normalize these into a common concept while preserving original terminology.

⸻

64. Concept Identity

Concepts SHOULD have stable identifiers.

Example:

concept.cpu.utilization

This allows multiple sources to reference the same concept.

⸻

65. Ontology

Veda MAY maintain domain ontologies.

An ontology defines:

entities
concepts
relationships
properties
constraints

Example:

Computer
 ├── hasCPU
 ├── hasMemory
 ├── runsProcess
 └── connectedToNetwork

⸻

66. Knowledge Composition

Complex knowledge can be composed from smaller units.

Example:

Entity Knowledge
+
Relationship Knowledge
+
Temporal Knowledge
+
Causal Knowledge
        ↓
Composite Knowledge

Each dependency remains traceable.

⸻

67. Knowledge Decomposition

A complex statement SHOULD be decomposable.

Example:

"Server A is healthy and ready for deployment."

may become:

K1:
Server A is reachable.
K2:
CPU is healthy.
K3:
Memory is healthy.
K4:
Required service is running.
K5:
Deployment prerequisites are satisfied.

This makes reasoning and verification more precise.

⸻

68. Knowledge Uncertainty

Veda SHOULD explicitly represent unknowns.

Example:

Known:
Server A exists.
Unknown:
Current CPU temperature.

The system MUST NOT fill unknown fields with assumptions unless explicitly marked.

⸻

69. Unknown Is a Valid State

The knowledge system MUST support:

UNKNOWN

as a legitimate epistemic state.

This is essential.

A system that cannot represent “I don’t know” will eventually represent “I made something up.”

⸻

70. Assumption Registry

Assumptions SHOULD be first-class objects.

Example:

Assumption A1:
Network connection remains available.

Knowledge depending on A1 references it.

If A1 becomes invalid:

A1 invalid
 ↓
Dependent knowledge
 ↓
Reevaluation

⸻

71. Knowledge Impact Graph

Veda SHOULD be able to traverse:

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

This allows questions such as:

If this fact is wrong, what actions might Veda have taken incorrectly?

This is essential for autonomous systems.

⸻

72. Knowledge and Planning

Planner decisions MAY reference knowledge.

Example:

Goal:
Deploy application.
Knowledge:
Current server has enough memory.
Planner:
Select server A.

If the knowledge becomes stale:

Planner
 ↓
Knowledge validation
 ↓
Replan

⸻

73. Knowledge and Simulation

Simulation SHOULD reference the knowledge used to construct assumptions.

Example:

Simulation:
Expected CPU usage = 70%
Knowledge:
Historical workload data
Evidence:
Previous runs

This makes predictions auditable.

⸻

74. Knowledge and Decision

Decision Engine SHOULD receive:

knowledge
confidence
evidence
constraints
uncertainty

A decision based on weak knowledge SHOULD carry higher uncertainty.

⸻

75. Knowledge and Learning

Learning should update knowledge through controlled processes.

Canonical path:

Experience
   ↓
Reflection
   ↓
Candidate Lesson
   ↓
Evidence Evaluation
   ↓
Knowledge Proposal
   ↓
Validation
   ↓
Knowledge Update

Learning MUST NOT directly overwrite trusted knowledge without validation.

⸻

76. Human Correction

Humans MAY correct knowledge.

A correction SHOULD produce:

Correction Event
      ↓
New Evidence
      ↓
Knowledge Reevaluation

The system SHOULD preserve the previous state.

⸻

77. Knowledge Governance

High-impact knowledge SHOULD require stronger governance.

Examples:

Low impact:
formatting preference
Medium impact:
technical configuration
High impact:
security policy
Critical:
constitutional rule

The required verification level SHOULD increase with impact.

⸻

78. Knowledge Security

Knowledge access MUST respect:

* identity
* authorization
* privacy
* classification
* tenancy
* agent scope

Not every agent should see every knowledge object.

⸻

79. Knowledge Poisoning

Veda MUST consider knowledge poisoning.

Attack pattern:

False Evidence
     ↓
False Claim
     ↓
False Knowledge
     ↓
Bad Decision
     ↓
Bad Action

Defenses include:

* provenance
* source trust
* independent verification
* anomaly detection
* contradiction detection
* human review
* confidence limits

⸻

80. Self-Generated Knowledge

Veda may create knowledge from its own experience.

This knowledge MUST be marked as self-derived.

Example:

derived_from:
  - experience-001
  - action-result-009
origin:
  type: veda_experience

Self-generated knowledge MUST NOT automatically become authoritative.

⸻

81. Knowledge Revalidation

Knowledge SHOULD be revalidated when:

* supporting evidence expires
* source changes
* assumptions change
* World state changes
* contradiction appears
* policy changes
* new stronger evidence arrives

⸻

82. Knowledge Garbage Collection

Unused or obsolete knowledge MAY be archived.

However:

Archive ≠ Delete History

Historical knowledge should remain recoverable where appropriate.

⸻

83. Knowledge API

A conceptual API MAY expose:

create_claim()
get_claim()
update_claim()
evaluate_claim()
attach_evidence()
get_evidence()
find_support()
find_contradictions()
get_provenance()
get_dependencies()
invalidate_knowledge()
supersede_knowledge()
verify_knowledge()
query_knowledge()

⸻

84. Example Knowledge Object

knowledge_id: knowledge-server-healthy-001
version: 3
subject:
  type: server
  id: server-01
predicate:
  status
object:
  healthy
type:
  observational
scope:
  server_id: server-01
valid_from:
  2026-09-16T03:00:00+07:00
valid_until:
  2026-09-16T03:05:00+07:00
epistemic_status:
  VERIFIED
confidence:
  value: 0.97
evidence_refs:
  - evidence-health-001
  - evidence-health-002
assumptions:
  - monitoring_system_operational
verification:
  method: health_check
status:
  ACTIVE

⸻

85. Example: Procedural Knowledge

knowledge_id: knowledge-build-process-001
type:
  procedural
subject:
  veda-project
predicate:
  build_procedure
object:
  - typecheck
  - build
  - test
  - verify
evidence_refs:
  - experience-build-001
  - action-result-004
confidence:
  value: 0.91
epistemic_status:
  SUPPORTED

⸻

86. Example: Hypothesis

knowledge_id: hypothesis-memory-failure-001
type:
  hypothesis
subject:
  service-01
predicate:
  failure_cause
object:
  memory_pressure
status:
  CANDIDATE
confidence:
  value: 0.63
evidence_refs:
  - log-001
  - metric-002

⸻

87. Example: Knowledge Conflict

Knowledge A:
server-01.status = healthy
Knowledge B:
server-01.status = degraded
Relationship:
CONTRADICTS

The system retains both until resolution.

⸻

88. Knowledge Resolution

Resolution may use:

source quality
evidence strength
freshness
scope
independence
verification
temporal ordering
context
human judgment

RFC-0014 defines the conflict-resolution mechanism.

⸻

89. Knowledge Audit

The system SHOULD support:

Who created this knowledge?
When?
From what evidence?
Which model produced it?
Which human approved it?
What assumptions existed?
What decisions used it?
Was it later contradicted?
Why was it superseded?

⸻

90. Knowledge Reproducibility

A knowledge conclusion SHOULD be reconstructible where possible.

Given:

Evidence
+
Rules
+
Models
+
Parameters
+
Context

Veda SHOULD be able to reconstruct the reasoning path.

⸻

91. Knowledge Determinism

For deterministic derivations:

same evidence
+
same rules
+
same context
=
same knowledge result

For nondeterministic model-assisted derivations, the system SHOULD preserve:

* model identity
* model version
* prompt/context reference
* generation metadata
* evidence
* resulting claim

⸻

92. Knowledge and Model Providers

Models are knowledge consumers and producers.

A model MAY:

retrieve knowledge
evaluate evidence
propose claims
derive hypotheses

But models MUST NOT silently modify authoritative knowledge.

Knowledge updates should pass through the Knowledge system.

⸻

93. Knowledge and Multiple Models

Different models may produce different interpretations.

Example:

Model A → Claim X
Model B → Claim Y

These become candidate claims.

The system SHOULD compare:

evidence
reasoning
assumptions
confidence

rather than choosing based solely on model identity.

⸻

94. Knowledge Consensus

Veda MAY use multiple intelligence providers to evaluate important claims.

Example:

Evidence
   ↓
Model A
Model B
Model C
   ↓
Independent Evaluation
   ↓
Knowledge Assessment

Model agreement alone is not sufficient evidence.

⸻

95. Knowledge and NCP

Future NCP messages SHOULD support transferring:

knowledge references
claim references
evidence references
confidence
scope
temporal validity
provenance

rather than sending only raw text.

⸻

96. Knowledge and World Delta

World Delta SHOULD reference knowledge changes where appropriate.

Example:

World Delta:
server.status → healthy
Grounding:
knowledge-server-health-001
Evidence:
health-check-001

⸻

97. Knowledge Security Invariants

KNOW-1

Every durable knowledge object MUST have a unique identifier.

KNOW-2

Important knowledge SHOULD preserve provenance.

KNOW-3

Knowledge MUST distinguish claims from evidence.

KNOW-4

Knowledge MUST distinguish confidence from certainty.

KNOW-5

Knowledge MUST support unknown states.

KNOW-6

Knowledge MUST support contradiction.

KNOW-7

Knowledge MUST support temporal validity.

KNOW-8

Knowledge MUST support scope.

KNOW-9

Knowledge MUST preserve historical versions.

KNOW-10

Knowledge MUST NOT silently overwrite contradictory knowledge.

KNOW-11

Derived knowledge MUST preserve derivation lineage.

KNOW-12

Knowledge MUST be able to reference supporting evidence.

KNOW-13

Invalid evidence MUST be able to trigger knowledge reevaluation.

KNOW-14

Stale knowledge MUST be distinguishable from false knowledge.

KNOW-15

Superseded knowledge MUST remain historically traceable.

KNOW-16

Model-generated knowledge MUST be distinguishable from directly observed knowledge.

KNOW-17

Self-generated knowledge MUST be explicitly marked.

KNOW-18

Knowledge scope MUST NOT be silently generalized.

KNOW-19

Assumptions SHOULD be explicitly represented.

KNOW-20

Conditional knowledge MUST preserve its conditions.

KNOW-21

Causal claims SHOULD require stronger grounding than simple correlation.

KNOW-22

Knowledge used for high-impact decisions SHOULD meet defined verification requirements.

KNOW-23

Knowledge access MUST respect authorization.

KNOW-24

Sensitive knowledge MUST NOT be exposed to unauthorized agents.

KNOW-25

Knowledge corrections MUST preserve history.

KNOW-26

Knowledge dependencies SHOULD be traceable.

KNOW-27

Knowledge impact SHOULD be analyzable.

KNOW-28

Embeddings MUST NOT be treated as authoritative knowledge.

KNOW-29

Retrieval similarity MUST NOT be treated as truth.

KNOW-30

Veda MUST preserve the distinction between:

Observed
Reported
Inferred
Verified
Believed
Known
Unknown

⸻

98. Canonical Knowledge Pipeline

The canonical Veda knowledge pipeline is:

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
Knowledge Candidate
   ↓
Verification
   ↓
Active Knowledge
   ↓
Reasoning
   ↓
Decision

With feedback:

New Evidence
   ↓
Knowledge Reevaluation
   ↓
Update / Contradiction / Supersession

⸻

99. Knowledge as a Living System

Knowledge in Veda is not a static database.

It behaves more like:

Knowledge
    ↓
supported by Evidence
    ↓
used by Reasoning
    ↓
used by Decisions
    ↓
produces Actions
    ↓
Actions produce new Evidence
    ↓
Knowledge is reevaluated

Therefore:

World
 ↕
Evidence
 ↕
Knowledge
 ↕
Reasoning
 ↕
Decision
 ↕
Action

The system continuously updates its understanding of the World.

⸻

100. Final Principle

Veda should never represent knowledge as:

"Some text exists in the database."

Instead:

This claim exists
    ↓
It has a defined scope
    ↓
It has a temporal context
    ↓
It has supporting evidence
    ↓
The evidence has provenance
    ↓
The claim has a confidence level
    ↓
Its assumptions are known
    ↓
Its contradictions are known
    ↓
Its dependencies are known
    ↓
Its downstream decisions are traceable

The fundamental model is:

Evidence
    ↓
Claim
    ↓
Knowledge
    ↓
Reasoning
    ↓
Decision

And the fundamental epistemic rule is:

No provenance
    → weak knowledge
No evidence
    → unsupported claim
Unknown
    → remain unknown
Contradiction
    → preserve and resolve
New evidence
    → reevaluate
Knowledge
    → always remains traceable to its basis

Veda therefore does not merely store what it knows.

It stores why it believes what it knows, when that belief applies, what could invalidate it, and what consequences depend on it.

⸻

End of RFC-0013