RFC-0017 — Memory Model

Status: Draft
Version: 0.1.0
Layer: 7 — Brain & Memory
Module: Memory Architecture
Depends on: RFC-0002, RFC-0003, RFC-0004, RFC-0005, RFC-0006, RFC-0007, RFC-0012, RFC-0013, RFC-0014, RFC-0015, RFC-0016
Path: docs/rfc/RFC-0017-memory-model.md

⸻

1. Abstract

RFC-0017 defines the memory architecture of Veda.

Memory is the system responsible for retaining information from experience, interaction, observation, knowledge, procedures, preferences, failures, and historical state.

Veda MUST NOT treat all retained information as the same kind of memory.

The system MUST distinguish at minimum:

Episodic Memory
Semantic Memory
Procedural Memory
Preference Memory
Policy Memory
Failure Memory
Working Memory
Prospective Memory

Memory MUST preserve provenance and epistemic status.

The fundamental principle is:

Memory records what Veda has encountered or learned. It does not automatically define what is true now.

⸻

2. Problem

A naive AI memory system often looks like:

Text
 ↓
Embedding
 ↓
Vector Database
 ↓
Retrieve

This is insufficient for Veda.

Consider:

User said:
"My server has 16 GB RAM."

This is not equivalent to:

Verified system state:
server has 16 GB RAM.

Likewise:

User preference

is not equivalent to:

System policy

And:

Past failure

is not equivalent to:

Current failure.

Veda therefore requires typed memory.

⸻

3. Core Memory Distinctions

The following distinctions MUST remain explicit:

Event
≠
Experience
≠
Memory
≠
Knowledge
≠
Belief
≠
Truth

More specifically:

Event
    ↓
Experience
    ↓
Memory
    ↓
Knowledge Candidate
    ↓
Evaluation
    ↓
Knowledge

Memory is therefore part of the cognitive system, not the final authority on reality.

⸻

4. Memory Types

4.1 Working Memory

Short-lived information required for the current task.

Examples:

current conversation
current plan
current task context
temporary reasoning state
active tool result

Working memory SHOULD have a short retention period.

⸻

4.2 Episodic Memory

Records specific experiences and events.

Examples:

Veda executed a deployment.
User approved an action.
A server failed at 03:14.
A previous strategy failed.

Episodic memory answers:

What happened?

⸻

4.3 Semantic Memory

Stores generalized knowledge derived from experiences and evidence.

Examples:

Python project uses Poetry.
This repository requires Node 22.
A specific API returns JSON.

Semantic memory answers:

What does Veda currently believe to be known?

Semantic memory MUST remain linked to evidence.

⸻

4.4 Procedural Memory

Stores how to perform tasks.

Examples:

How to deploy a project.
How to run tests.
How to recover a service.
How to perform a specific workflow.

Procedural memory MUST NOT automatically grant capability or authority.

⸻

4.5 Preference Memory

Stores user or system preferences.

Examples:

preferred language
preferred notification style
preferred coding style
preferred working hours

Preference MUST NOT be treated as policy.

⸻

4.6 Policy Memory

Stores policy references and operational rules.

Examples:

production deployment requires approval
private data must remain local

Policy memory is a reference to policy, not authority itself.

Actual enforcement belongs to the policy/authorization system.

⸻

4.7 Failure Memory

Stores failed actions, failed plans, failure causes, and recovery outcomes.

Example:

Action
 ↓
Failure
 ↓
Diagnosis
 ↓
Recovery
 ↓
Lesson

Failure memory exists to prevent repeated mistakes.

⸻

4.8 Prospective Memory

Stores future obligations.

Examples:

check backup tomorrow
renew certificate next month
review project milestone

Prospective memory connects memory to scheduling and future planning.

⸻

5. Memory Object

Canonical structure:

memory_id:
version:
type:
subject:
content:
source_event_refs:
experience_refs:
knowledge_refs:
evidence_refs:
world_refs:
intent_refs:
goal_refs:
process_refs:
action_refs:
context:
scope:
created_at:
observed_at:
valid_from:
valid_until:
importance:
relevance:
confidence:
epistemic_status:
retention_policy:
sensitivity:
access_policy:
status:
created_by:
updated_at:

⸻

6. Memory Lifecycle

Canonical lifecycle:

CAPTURED
   ↓
NORMALIZED
   ↓
CLASSIFIED
   ↓
GROUNDED
   ↓
STORED
   ↓
RETRIEVABLE
   ↓
REVALIDATED
   ↓
ARCHIVED / SUPERSEDED / FORGOTTEN

Memory MUST NOT silently transition from temporary information into trusted knowledge.

⸻

7. Memory Formation

Memory MAY originate from:

user interaction
system events
observations
tool results
external sources
actions
failures
human decisions
verified outcomes
learning
reflection

Every memory SHOULD retain provenance.

⸻

8. Memory Formation Pipeline

Reality
  ↓
Event
  ↓
Observation
  ↓
Experience
  ↓
Memory Candidate
  ↓
Classification
  ↓
Provenance
  ↓
Validation
  ↓
Memory Store

For semantic knowledge:

Memory
  ↓
Generalization
  ↓
Claim
  ↓
Evidence
  ↓
Knowledge

⸻

9. Memory vs Knowledge

A memory may record:

"On Monday, the server returned HTTP 500."

Knowledge may represent:

"The service experienced an HTTP 500 failure on Monday."

But current truth may be:

"The service is currently healthy."

Therefore:

Historical Memory
≠
Current World State

⸻

10. Temporal Validity

Memory SHOULD support:

created_at
observed_at
valid_from
valid_until

Example:

Memory:
server IP = X
valid_from:
2026-01-01
valid_until:
2026-03-01

After expiration, the memory remains historically valid but may no longer describe the current world.

⸻

11. Memory Confidence

Veda SHOULD track:

memory_confidence
source_confidence
evidence_confidence
knowledge_confidence

These MUST NOT be conflated.

Example:

User statement:
high memory confidence
Actual factual correctness:
unknown

The fact that Veda clearly remembers something does not make the thing true.

⸻

12. Importance

Memory importance MAY depend on:

user importance
goal relevance
future utility
risk
frequency
recurrence
historical significance
failure prevention

Important memory SHOULD receive longer retention.

⸻

13. Relevance

Memory retrieval SHOULD consider:

semantic similarity
entity relevance
goal relevance
temporal relevance
causal relevance
task relevance
user relevance
risk relevance

A semantically similar memory is not necessarily the most useful memory.

⸻

14. Memory Retrieval

Canonical retrieval pipeline:

Current Task
    ↓
Context
    ↓
Retrieval Query
    ↓
Candidate Memories
    ↓
Filtering
    ↓
Ranking
    ↓
Conflict Check
    ↓
Context Assembly

Retrieved memory MUST be treated as evidence for reasoning, not unquestionable truth.

⸻

15. Retrieval Ranking

Conceptual score:

MemoryScore =
Relevance
+ Recency
+ Importance
+ GoalFit
+ Confidence
+ CausalRelevance
+ UserRelevance
- Staleness
- ConflictRisk

Weights SHOULD be configurable.

⸻

16. Memory Consolidation

Repeated experiences MAY be consolidated.

Example:

Experience 1
Experience 2
Experience 3
Experience 4
        ↓
Pattern Detection
        ↓
Generalization
        ↓
Knowledge Candidate

Consolidation MUST preserve links to original experiences.

A generalization without provenance is dangerous.

⸻

17. Memory Compression

Memory MAY be compressed through:

summarization
deduplication
aggregation
hierarchical representation
semantic clustering

However:

Compression MUST NOT destroy provenance.

The system SHOULD retain references to original records.

⸻

18. Memory Hierarchy

Veda SHOULD support:

Raw Event
   ↓
Experience
   ↓
Memory
   ↓
Summary
   ↓
Pattern
   ↓
Knowledge
   ↓
Principle

Each higher level SHOULD remain traceable to lower levels.

⸻

19. Memory Graph

Memory SHOULD support graph relationships.

Example:

Experience A
     ↓
Memory A
     ↓
Pattern B
     ↓
Knowledge C
     ↓
Goal D

Relationships MAY include:

caused_by
supports
contradicts
derived_from
relevant_to
preceded
followed
similar_to
supersedes

⸻

20. Memory and World Model

Memory and World MUST remain separate.

World:
what Veda currently believes reality to be
Memory:
what Veda remembers about observations and experiences

The World Model MAY be updated using memory-derived evidence.

But memory MUST NOT directly overwrite current World state without validation.

⸻

21. Memory and Event Sourcing

Events remain the historical source.

Event
 ↓
Experience
 ↓
Memory

Memory is therefore a derived cognitive representation.

The event history SHOULD remain independently auditable.

⸻

22. Working Memory

Working memory MAY contain:

active intent
active goal
active process
current plan
current action
recent tool outputs
temporary hypotheses
current reasoning context

Working memory MAY be discarded after task completion unless promoted.

⸻

23. Memory Promotion

Information MAY be promoted:

Working Memory
      ↓
Episodic Memory
      ↓
Semantic Knowledge

Promotion SHOULD require explicit criteria.

Examples:

repeated occurrence
high importance
verified fact
user explicitly requests retention
future utility
failure prevention

⸻

24. Memory Demotion

Memory MAY be demoted when:

stale
incorrect
superseded
low relevance
temporary
duplicated
invalidated

Demotion MUST NOT erase historical provenance.

⸻

25. Forgetting

Veda MUST support controlled forgetting.

Forgetting means:

not available for ordinary retrieval

It does NOT necessarily mean:

physically deleted

Deletion MUST follow:

* retention policy
* privacy policy
* legal requirements
* user instructions
* security policy

⸻

26. User-Controlled Memory

Users SHOULD be able to:

view memory
correct memory
delete memory
promote memory
demote memory
mark memory private
restrict memory
export memory

A user correction MUST generate an auditable event.

⸻

27. Sensitive Memory

Memory MAY contain sensitive information.

Each memory SHOULD have:

sensitivity
access_policy
retention_policy
encryption_status

Sensitive memories MUST NOT automatically enter external intelligence provider context.

⸻

28. Memory Access Control

Memory access SHOULD follow:

Identity
 ↓
Authorization
 ↓
Memory Policy
 ↓
Scope
 ↓
Retrieval

An agent should only retrieve memories within its authorized scope.

⸻

29. Multi-Agent Memory

Veda may have multiple agents.

Memory visibility SHOULD support:

PRIVATE
AGENT
PROJECT
TEAM
WORLD
SYSTEM

An agent’s private memory MUST NOT automatically become global memory.

⸻

30. Shared Memory

Shared memory SHOULD require explicit publication.

Example:

Agent A
  ↓
Private Memory
  ↓
Validated
  ↓
Shared Memory

Shared memory MUST retain:

publisher
provenance
scope
confidence
version

⸻

31. Memory Conflict

When memories conflict:

Memory A
   vs
Memory B

Veda MUST invoke RFC-0014 rather than silently selecting one.

Possible result:

A valid at T1
B valid at T2

or:

A uncertain
B better supported

⸻

32. Memory Staleness

Each memory SHOULD have a staleness model.

Staleness may depend on domain.

Example:

weather:
minutes
software state:
hours/days
user preference:
months/years
historical fact:
potentially permanent

Staleness MUST be domain-specific rather than universally time-based.

⸻

33. Memory Validation

Memory MAY be revalidated when:

retrieved for high-risk task
old
contradicted
high-impact
used to justify action

Example:

Memory:
server exists
Before destructive action:
→ verify current World

Historical memory alone is insufficient justification for high-risk actions.

⸻

34. Memory Decay

Veda MAY use a relevance decay function.

Conceptually:

EffectiveMemoryValue =
Importance
× Relevance
× Confidence
× Freshness

Decay MUST NOT modify historical truth.

It modifies retrieval priority.

⸻

35. Memory Provenance

Every important memory SHOULD answer:

Where did this come from?
When was it observed?
Who created it?
Which event caused it?
Which evidence supports it?
Has it been verified?
Has it been contradicted?

⸻

36. Memory Security Threats

Potential attacks:

* memory poisoning
* false memory injection
* prompt-induced memory corruption
* unauthorized retrieval
* cross-agent leakage
* stale memory exploitation
* malicious memory promotion
* provenance forgery
* selective deletion
* memory flooding

Veda MUST treat memory as a security-sensitive subsystem.

⸻

37. Memory Poisoning Protection

Memory SHOULD NOT be promoted solely because:

a model said it

or:

the information appeared frequently

Promotion SHOULD consider:

provenance
verification
source independence
user authority
evidence
repetition
context

⸻

38. Memory Audit

Memory operations SHOULD generate events:

MemoryCaptured
MemoryClassified
MemoryStored
MemoryRetrieved
MemoryPromoted
MemoryDemoted
MemoryCorrected
MemoryInvalidated
MemorySuperseded
MemoryArchived
MemoryDeleted
MemoryShared
MemoryAccessDenied

⸻

39. Memory and Learning

Memory is a foundation for learning.

Canonical pipeline:

Experience
 ↓
Memory
 ↓
Reflection
 ↓
Lesson
 ↓
Knowledge / Skill
 ↓
Evaluation
 ↓
Learning

This will later integrate with RFC-0035 and RFC-0036.

⸻

40. Memory and Self Model

Veda’s Self Model may use memory to represent:

past actions
past decisions
past failures
capabilities
preferences
limitations
performance
identity history

However, Self Model MUST remain distinguishable from raw memory.

⸻

41. Memory and Planning

Planner may query memory for:

previous solutions
known failures
resource history
user preferences
successful procedures
historical constraints

Example:

New Task
 ↓
Retrieve Similar Experiences
 ↓
Avoid Known Failure
 ↓
Generate Better Plan

⸻

42. Memory and Prediction

Historical memory MAY support prediction.

Example:

Past:
service frequently fails after update
Current:
new update detected
Prediction:
elevated failure risk

Prediction MUST remain probabilistic unless verified.

⸻

43. Memory and Identity

Memory SHOULD contribute to continuity of Veda’s identity.

But:

Identity ≠ Memory

Identity is defined by RFC-0039.

Memory is one source of historical continuity.

⸻

44. Memory Storage Architecture

Veda SHOULD support multiple storage layers:

┌──────────────────────────┐
│ Working Memory           │
├──────────────────────────┤
│ Episodic Store           │
├──────────────────────────┤
│ Semantic Knowledge Store │
├──────────────────────────┤
│ Procedural Store         │
├──────────────────────────┤
│ Preference Store         │
├──────────────────────────┤
│ Failure Store            │
├──────────────────────────┤
│ Archive                  │
└──────────────────────────┘

Physical implementation MAY use:

SQL
Document Store
Graph Store
Vector Store
Object Store
Append-Only Event Log

The logical model MUST remain independent of physical storage.

⸻

45. Vector Database

Embeddings MAY be used for retrieval.

However:

Embedding ≠ Memory

A vector index is an optimization for finding related content.

It MUST NOT become the canonical source of memory truth.

Canonical memory records MUST remain structured and addressable.

⸻

46. Hybrid Retrieval

Veda SHOULD support:

semantic search
keyword search
graph traversal
temporal filtering
entity filtering
scope filtering
metadata filtering

A hybrid retrieval engine is preferred over vector similarity alone.

⸻

47. Memory Snapshot

Veda MAY create snapshots for:

backup
migration
testing
simulation
recovery
debugging

Snapshots MUST preserve version information.

⸻

48. Memory Versioning

Memory SHOULD support:

version
supersedes
corrects
invalidates
derived_from

Example:

Memory v1
   ↓
Correction
   ↓
Memory v2

Historical v1 SHOULD remain recoverable unless deletion policy requires removal.

⸻

49. Memory Export

Veda SHOULD support export of memory into a portable format.

Export SHOULD include:

memory records
relationships
provenance
versions
timestamps
access metadata

This reduces vendor lock-in.

⸻

50. Memory Import

Imported memory MUST be treated as untrusted until validated.

Pipeline:

Import
 ↓
Integrity Check
 ↓
Schema Validation
 ↓
Provenance Validation
 ↓
Conflict Detection
 ↓
Trust Evaluation
 ↓
Promotion

⸻

51. Memory Metrics

Veda SHOULD monitor:

memory_count
retrieval_latency
retrieval_precision
retrieval_recall
stale_memory_rate
conflict_rate
promotion_rate
correction_rate
deletion_rate
poisoning_detection
storage_usage

⸻

52. Memory Quality

Memory quality SHOULD be evaluated through:

accuracy
relevance
freshness
provenance
retrievability
consistency
security

More memory does not automatically mean better memory.

⸻

53. Memory Budget

Veda SHOULD maintain budgets for:

RAM
disk
vector index
graph
working context
provider context

Memory allocation SHOULD prioritize information according to:

importance
relevance
risk
future utility

⸻

54. Memory Consolidation Job

A background process MAY periodically perform:

deduplication
staleness detection
conflict detection
summarization
knowledge extraction
archive
index rebuilding
integrity checks

This process MUST NOT silently alter high-value historical records.

⸻

55. Memory Recovery

If a memory store becomes corrupted:

Detect
 ↓
Isolate
 ↓
Restore Snapshot
 ↓
Replay Events
 ↓
Verify Integrity
 ↓
Rebuild Index
 ↓
Resume

Canonical event history remains the recovery source where available.

⸻

56. Formal Model

Let:

M = set of memories
E = event history
W = world state
K = knowledge

Memory formation:

M_new = f(E, W, K, Experience)

Retrieval:

Retrieve(q) =
Rank(
 Filter(
   Search(M,q)
 )
)

Knowledge promotion:

K_candidate =
Generalize(M)

followed by:

Evidence
+
Evaluation
+
Verification

before trusted knowledge activation.

⸻

57. Invariants

MEM-1

Every persistent memory MUST have a unique identity.

MEM-2

Memory MUST retain provenance.

MEM-3

Memory MUST have a type.

MEM-4

Memory MUST have an epistemic status.

MEM-5

Memory MUST NOT automatically become truth.

MEM-6

Memory MUST NOT automatically become authorization.

MEM-7

Memory MUST NOT directly mutate World state.

MEM-8

Historical memory MUST remain distinguishable from current state.

MEM-9

Stale memory MUST be distinguishable from invalid memory.

MEM-10

Conflicting memories MUST be preserved until resolved or classified.

MEM-11

Conflict resolution MUST use RFC-0014.

MEM-12

Memory retrieval MUST respect access policy.

MEM-13

Sensitive memory MUST respect privacy boundaries.

MEM-14

Agent-private memory MUST NOT automatically become shared memory.

MEM-15

Shared memory MUST preserve publisher provenance.

MEM-16

Memory promotion MUST have defined criteria.

MEM-17

Memory demotion MUST preserve historical provenance.

MEM-18

Memory deletion MUST respect retention and privacy policy.

MEM-19

Embeddings MUST NOT be the canonical memory representation.

MEM-20

Memory compression MUST preserve provenance.

MEM-21

Memory correction MUST be auditable.

MEM-22

Memory import MUST be treated as untrusted until validated.

MEM-23

High-risk decisions MUST NOT rely solely on stale memory.

MEM-24

Memory retrieval MUST be bounded by relevance and authorization.

MEM-25

Memory poisoning MUST be detectable and containable.

MEM-26

Memory versions MUST be traceable.

MEM-27

Memory stores MUST support integrity verification.

MEM-28

Memory corruption MUST have a recovery strategy.

MEM-29

Memory quality MUST be measurable.

MEM-30

No memory mechanism may silently rewrite historical reality.

⸻

58. Canonical Memory Architecture

                         REALITY
                            │
                            ↓
                         EVENTS
                            │
                            ↓
                       EXPERIENCE
                            │
                            ↓
                    MEMORY FORMATION
                            │
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
      EPISODIC          PROCEDURAL       PREFERENCE
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ↓
                       CONSOLIDATION
                            ↓
                     SEMANTIC MEMORY
                            ↓
                         CLAIMS
                            ↓
                        EVIDENCE
                            ↓
                        KNOWLEDGE
                            │
                            ↓
                    WORLD / PLANNING
                            │
                            ↓
                         ACTION
                            │
                            ↓
                       NEW EVENTS
                            │
                            └──────────→ MEMORY

⸻

59. Final Principle

Veda’s memory is not a pile of text.

It is a structured historical and cognitive system.

The fundamental distinction is:

Event
    ↓
Experience
    ↓
Memory
    ↓
Knowledge
    ↓
Decision
    ↓
Action
    ↓
Outcome
    ↓
New Experience

Memory gives Veda continuity.

Knowledge gives Veda structured understanding.

The World Model gives Veda a representation of current reality.

Verification determines what actually happened.

Therefore:

Veda must remember the past without confusing the past with the present.