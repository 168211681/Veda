RFC-0014 — Contradiction & Conflict Model

Status: Draft
Version: 0.1.0
Layer: 5 — Truth
Module: Contradiction / Conflict Resolution
Depends on: RFC-0002, RFC-0003, RFC-0004, RFC-0005, RFC-0006, RFC-0007, RFC-0008, RFC-0010, RFC-0012, RFC-0013
Path: docs/rfc/RFC-0014-contradiction-and-conflict-model.md

⸻

1. Abstract

RFC-0014 defines the contradiction and conflict system of Veda.

Veda operates in an environment where information, observations, knowledge, policies, goals, agents, and world states may disagree.

The system MUST NOT assume that disagreement automatically means that one side is wrong.

Veda MUST be able to:

* detect contradictions
* detect broader conflicts
* classify their type
* preserve conflicting information
* evaluate evidence and context
* determine whether a conflict is resolvable
* propose resolution strategies
* request additional evidence
* represent uncertainty
* escalate unresolved conflicts
* prevent unsafe actions caused by unresolved contradictions
* preserve the complete reasoning and resolution history

The primary principle is:

Conflict must be represented before it is resolved.

Resolution MUST NOT be implemented as silent deletion of information.

⸻

2. Problem

Real-world information is rarely perfectly consistent.

Examples:

Source A:
Server is online.
Source B:
Server is offline.

This may represent:

1. one source being wrong
2. different observation times
3. different servers
4. different network locations
5. temporary failure
6. partial availability
7. stale information

Therefore:

A != B

does not immediately imply:

A is false

Veda must reason about the conditions under which the disagreement exists.

⸻

3. Core Definitions

3.1 Contradiction

A contradiction is an incompatibility between two or more claims, knowledge objects, or propositions under the same relevant context.

Example:

Claim A:
server.status = ONLINE
Claim B:
server.status = OFFLINE

under:

same server
same observation context
same time

This is a direct contradiction.

⸻

3.2 Conflict

A conflict is a broader incompatibility between system elements.

Conflict may exist between:

* knowledge
* world state
* goals
* actions
* policies
* authorities
* resources
* agents
* transactions
* schedules
* constraints

Example:

Goal A:
deploy immediately
Policy B:
production deployment requires human approval

This is a policy/goal conflict.

⸻

3.3 Contradiction ≠ Error

A contradiction does not automatically identify the incorrect participant.

Veda MUST preserve:

Claim A
Claim B
Evidence A
Evidence B
Context A
Context B

until sufficient evidence exists to distinguish them.

⸻

3.4 Conflict ≠ Contradiction

Two operations may conflict without representing contradictory knowledge.

Example:

Agent A:
delete file X
Agent B:
modify file X

This is an execution conflict.

⸻

4. Design Principles

CON-PRINCIPLE-1 — Preserve Disagreement

Conflicting information MUST NOT be silently deleted.

⸻

CON-PRINCIPLE-2 — Context Before Resolution

Veda MUST evaluate:

* identity
* scope
* time
* source
* observation conditions
* version
* authority
* dependencies

before declaring a contradiction.

⸻

CON-PRINCIPLE-3 — Unknown Is Valid

When Veda cannot determine the correct state, it MUST be allowed to represent:

UNKNOWN

or:

UNCERTAIN

rather than inventing certainty.

⸻

CON-PRINCIPLE-4 — Resolution Is Evidence-Based

Resolution SHOULD use:

* direct observation
* verified evidence
* freshness
* contextual relevance
* source independence
* integrity
* reproducibility
* verification

rather than arbitrary preference.

⸻

CON-PRINCIPLE-5 — High Impact Requires Escalation

Conflicts affecting:

* safety
* money
* privacy
* security
* irreversible actions
* constitutional rules
* system evolution

MUST have stricter resolution requirements.

⸻

5. Conflict Object

Canonical structure:

conflict_id:
version:
type:
participants:
  - participant_ref
subject:
scope:
context:
claim_refs:
knowledge_refs:
evidence_refs:
world_refs:
goal_refs:
action_refs:
policy_refs:
authorization_refs:
detected_at:
observed_at:
severity:
confidence:
priority:
status:
detection_method:
resolution_strategy:
resolution_result:
human_review:
required:
reviewer:
decision:
dependencies:
created_at:
updated_at:
resolved_at:

⸻

6. Conflict Types

Veda SHOULD support at least the following conflict classes.

6.1 Epistemic Conflict

Conflicting knowledge or claims.

A: temperature = 30°C
B: temperature = 35°C

⸻

6.2 Temporal Conflict

Claims differ because they apply to different times.

10:00 server = ONLINE
12:00 server = OFFLINE

This may not actually be a contradiction.

⸻

6.3 Scope Conflict

Claims apply to different scopes.

Bangkok server = ONLINE
Singapore server = OFFLINE

A naive system may incorrectly treat them as contradictory.

⸻

6.4 Identity Conflict

Two observations may refer to different entities that were incorrectly identified as the same entity.

⸻

6.5 Version Conflict

Two versions of an artifact disagree.

Example:

config v1
config v2

⸻

6.6 Semantic Conflict

Different interpretations of the same statement produce incompatible meanings.

⸻

6.7 Causal Conflict

Two causal explanations predict incompatible outcomes.

⸻

6.8 Policy Conflict

Policies impose incompatible requirements.

Example:

Policy A:
operation allowed.
Policy B:
operation prohibited.

Policy precedence MUST determine whether the conflict can be resolved automatically.

⸻

6.9 Goal Conflict

Two goals cannot simultaneously be satisfied under current constraints.

Goal A:
minimize cost
Goal B:
maximize hardware performance

⸻

6.10 Action Conflict

Two actions cannot safely occur together.

Action A:
delete X
Action B:
modify X

⸻

6.11 Resource Conflict

Multiple processes require the same limited resource.

Process A → GPU
Process B → GPU

when only one execution slot exists.

⸻

6.12 Authority Conflict

Different actors claim incompatible authority over an operation.

⸻

6.13 Concurrency Conflict

Concurrent world transitions modify the same state based on incompatible assumptions.

This MUST integrate with RFC-0004.

⸻

7. Contradiction Detection

The contradiction engine SHOULD evaluate:

Identity
+
Predicate
+
Object
+
Scope
+
Context
+
Time
+
Version
+
Conditions

before declaring direct contradiction.

Canonical comparison:

Claim A
        ↓
Identity Match?
        ↓
Predicate Match?
        ↓
Scope Match?
        ↓
Temporal Overlap?
        ↓
Context Compatibility?
        ↓
Logical Compatibility?
        ↓
Contradiction?

⸻

8. Contradiction Graph

Veda SHOULD maintain a graph representing disagreement.

Example:

Evidence A
     ↓
Claim A
     ↓
Knowledge A
     │
     ├──── CONTRADICTS ────┐
     │                     │
     ↓                     ↓
Goal X                 Knowledge B
                           ↓
                       Claim B
                           ↓
                       Evidence B

This enables impact analysis.

If Knowledge A is invalidated, Veda can identify:

Knowledge
↓
Goal
↓
Plan
↓
Action
↓
World transition

that may be affected.

⸻

9. Conflict Severity

Veda SHOULD classify conflicts.

LOW
MEDIUM
HIGH
CRITICAL

LOW

No meaningful downstream impact.

MEDIUM

May affect reasoning or planning.

HIGH

May affect significant actions or world state.

CRITICAL

May affect:

* safety
* financial operations
* security
* privacy
* constitutional rules
* system integrity
* irreversible actions
* autonomous evolution

Critical conflicts MUST NOT be silently resolved by ordinary inference.

⸻

10. Conflict Lifecycle

Canonical lifecycle:

DETECTED
   ↓
CLASSIFIED
   ↓
EVALUATING
   ↓
RESOLUTION_PROPOSED
   ↓
┌───────────────┬───────────────┬───────────────┐
↓               ↓               ↓
RESOLVED      DEFERRED       ESCALATED

Additional terminal states:

INVALIDATED
ACCEPTED_AS_UNCERTAIN

⸻

11. Resolution Strategies

Veda SHOULD support multiple resolution strategies.

11.1 Reject Weaker Claim

If sufficient evidence establishes that one claim is incorrect:

Claim A → SUPPORTED
Claim B → INVALID

The rejected claim SHOULD remain in history.

⸻

11.2 Narrow Scope

Transform:

X is true

into:

X is true
under Context C

⸻

11.3 Temporal Separation

Transform:

A = ONLINE
B = OFFLINE

into:

10:00 → ONLINE
12:00 → OFFLINE

⸻

11.4 Represent Uncertainty

Instead of forcing a binary result:

A = 60% confidence
B = 40% confidence

Veda MAY represent a probability distribution when the underlying domain supports it.

⸻

11.5 Competing Hypotheses

When insufficient evidence exists:

Hypothesis A
Hypothesis B

may coexist.

⸻

11.6 Request Additional Evidence

Veda may generate an evidence request:

Conflict
↓
Missing Evidence Identified
↓
Evidence Acquisition Plan
↓
Observation
↓
Re-evaluation

⸻

11.7 Human Adjudication

Human review SHOULD be required when:

* stakes are high
* evidence is insufficient
* policies conflict
* authority is disputed
* resolution affects constitutional rules
* irreversible actions are involved

⸻

11.8 Rollback

If a contradiction reveals that a world transition was invalid:

Conflict
↓
Verification Failure
↓
Rollback / Compensation
↓
World Reconciliation

This integrates with RFC-0027.

⸻

11.9 Block Action

When uncertainty is dangerous:

Conflict unresolved
        ↓
Action BLOCKED

This is preferable to guessing.

⸻

12. Evidence Evaluation

Evidence SHOULD be evaluated across multiple dimensions.

Evidence Quality =
Integrity
+ Relevance
+ Freshness
+ Scope Match
+ Context Match
+ Independence
+ Verification
+ Reproducibility

Veda MUST NOT use a simplistic rule such as:

Source A is important
therefore Source A is always correct

Authority of a source and truth of a claim are separate concepts.

⸻

13. Corroboration

Independent evidence SHOULD increase confidence.

Example:

Sensor
   +
System Log
   +
Direct Observation
   ↓
Corroborated State

However, duplicated information originating from the same underlying source SHOULD NOT be counted as independent corroboration.

Example:

Website A
Website B
Website C

if all copied the same original report, they represent one underlying source.

⸻

14. Stale Knowledge

A contradiction may be caused by stale knowledge.

Example:

Knowledge:
package version = 1.2

Current world:

package version = 1.3

Veda SHOULD distinguish:

FALSE

from:

STALE

because the historical claim may have been correct when observed.

⸻

15. World Conflict

World state conflicts MUST be evaluated through the state transition system.

Canonical process:

Current World
     ↓
Proposed Transition
     ↓
Conflict Detection
     ↓
Invariant Check
     ↓
Concurrency Check
     ↓
Authorization Check
     ↓
Verification
     ↓
Commit

A conflicting transition MUST NOT silently overwrite another transition.

⸻

16. Concurrent Actions

When multiple agents modify the same state:

World W
 ├── Agent A → W1
 └── Agent B → W2

Veda MUST detect whether:

W1 + W2

can safely merge.

Possible outcomes:

MERGE
REJECT
REBASE
SEQUENCE
HUMAN_REVIEW

⸻

17. Goal Conflict

When goals conflict, Veda SHOULD evaluate:

Constitution
↓
Safety
↓
Authority
↓
Policy
↓
Priority
↓
Deadline
↓
Value
↓
Cost
↓
Risk
↓
Opportunity Cost

A lower-level goal MUST NOT override a higher-level constraint.

⸻

18. Policy Conflict

Policy conflicts require deterministic precedence.

Example:

Constitution
    ↓
Safety Policy
    ↓
Security Policy
    ↓
System Policy
    ↓
Agent Policy
    ↓
Task Policy

Higher-level policy MUST NOT be silently overridden by lower-level policy.

If two policies exist at the same precedence level and conflict, the system SHOULD escalate unless an explicit tie-break rule exists.

⸻

19. Conflict Resolution Output

A resolution SHOULD produce:

resolution:
  conflict_id:
  decision:
  strategy:
  supporting_evidence:
  rejected_evidence:
  assumptions:
  uncertainty:
  affected_entities:
  affected_knowledge:
  affected_goals:
  affected_actions:
  world_impact:
  rollback_required:
  human_approval:
  confidence:
  explanation:

Resolution MUST itself be auditable.

⸻

20. Unresolved Conflict

An unresolved conflict is a valid system state.

Example:

status: ACCEPTED_AS_UNCERTAIN
known:
  - server may be online
unknown:
  - actual current state
hypotheses:
  - online
  - offline
next_evidence:
  - direct health check

Veda SHOULD continue operating only within the subset of actions safe under the uncertainty.

⸻

21. Impact Propagation

When conflict affects an existing knowledge object:

Evidence Conflict
       ↓
Knowledge Conflict
       ↓
World Model Impact
       ↓
Goal Impact
       ↓
Plan Impact
       ↓
Action Impact

Veda SHOULD identify:

affected_goals
affected_processes
affected_actions
affected_world_entities
affected_decisions

before continuing execution.

⸻

22. Action Safety

If an unresolved contradiction affects an action’s critical precondition:

Action
   ↓
Critical Precondition
   ↓
Conflict
   ↓
UNRESOLVED

the action SHOULD transition to:

BLOCKED

or:

AUTHORIZATION_PENDING

depending on the nature of the conflict.

Veda MUST NOT treat uncertainty as permission.

⸻

23. Conflict Memory

Veda SHOULD remember previous conflicts.

Memory SHOULD contain:

Conflict
↓
Cause
↓
Resolution
↓
Evidence
↓
Outcome
↓
Lesson

This enables the system to recognize recurring conflict patterns.

Example:

Repeated API discrepancy
        ↓
Historical pattern detected
        ↓
Known source of stale data
        ↓
Future conflict resolved faster

This memory MUST remain distinguishable from current truth.

Historical resolution is evidence about how conflicts behaved previously, not proof that the current conflict has the same cause.

⸻

24. Human Intervention

Human intervention MUST be represented as an explicit event.

Example:

ConflictDetected
HumanReviewRequested
HumanDecisionReceived
ResolutionApplied

The system MUST record:

who
what
when
why
under which authority

Human intervention MUST NOT modify historical evidence silently.

⸻

25. Security

Conflict systems can themselves become attack surfaces.

Potential attacks include:

* evidence poisoning
* false contradiction injection
* source impersonation
* fabricated corroboration
* stale-data exploitation
* conflict flooding
* denial-of-resolution
* authority spoofing
* malicious policy conflicts
* adversarial ambiguity
* selective evidence suppression

Veda MUST protect:

Evidence Integrity
Provenance
Identity
Authorization
Auditability
Resolution History

⸻

26. Conflict Flood Protection

An attacker or malfunctioning system may generate thousands of artificial conflicts.

Veda SHOULD support:

deduplication
aggregation
rate limiting
priority queues
correlation
root-cause grouping

Example:

10,000 contradictory observations
          ↓
       grouped
          ↓
1 underlying infrastructure incident

⸻

27. Determinism

Given the same:

World State
Evidence
Claims
Policies
Resolution Rules

the conflict engine SHOULD produce the same resolution proposal.

Any nondeterministic model-assisted decision MUST record:

model
version
prompt/context reference
input evidence
output
confidence
decision influence

⸻

28. Model-Assisted Conflict Resolution

AI models MAY assist with:

* semantic comparison
* contradiction detection
* hypothesis generation
* evidence ranking
* explanation
* resolution proposal

However:

Model Output ≠ Truth
Model Output ≠ Authority
Model Confidence ≠ Evidence

Final resolution MUST still follow Veda’s policy, evidence, authority, and verification systems.

⸻

29. Events

The following events SHOULD be supported:

ConflictDetected
ConflictClassified
ConflictEvaluationStarted
ConflictEvidenceAdded
ConflictEvidenceRejected
ConflictResolutionProposed
ConflictResolutionAccepted
ConflictResolutionRejected
ConflictDeferred
ConflictEscalated
ConflictInvalidated
ConflictAcceptedAsUncertain
ConflictResolved
ConflictImpactDetected
ConflictActionBlocked
ConflictWorldReconciliationStarted
ConflictWorldReconciled

All events MUST be compatible with RFC-0003.

⸻

30. Integration

RFC-0014 integrates with:

RFC-0002  World Model
RFC-0003  Event Model
RFC-0004  State & World Transition
RFC-0005  Intent Model
RFC-0006  Goal Model
RFC-0007  Process Model
RFC-0008  Action Model
RFC-0010  Authorization & Policy
RFC-0012  Evidence Model
RFC-0013  Knowledge Model
RFC-0020  Planner
RFC-0023  Future & Scenario Engine
RFC-0025  Value & Decision Engine
RFC-0026  Verification Engine
RFC-0027  Rollback & Recovery
RFC-0031  Event / Audit / Trace Fabric
RFC-0032  Veda Chronicle

⸻

31. Formal Model

Let:

C = {c1, c2, ..., cn}

be a set of claims.

The contradiction engine evaluates:

Conflict(C) =
f(
  identity,
  predicate,
  object,
  scope,
  context,
  time,
  conditions,
  evidence
)

A resolution is:

R = Resolve(C, E, P, A, W)

where:

C = Claims
E = Evidence
P = Policies
A = Authority
W = World Context

The result MAY be:

RESOLVED
DEFERRED
ESCALATED
UNCERTAIN
INVALIDATED

It MUST NOT be assumed that every conflict has a deterministic resolution.

⸻

32. Invariants

CONFLICT-1

Conflicting information MUST NOT be silently deleted.

CONFLICT-2

Contradiction detection MUST consider context.

CONFLICT-3

Temporal differences MUST be evaluated before declaring contradiction.

CONFLICT-4

Scope differences MUST be evaluated before declaring contradiction.

CONFLICT-5

Identity mismatch MUST be considered.

CONFLICT-6

Evidence MUST remain traceable.

CONFLICT-7

Resolution MUST be auditable.

CONFLICT-8

Unknown MUST remain a valid state.

CONFLICT-9

Uncertainty MUST NOT automatically become truth.

CONFLICT-10

Uncertainty MUST NOT automatically become permission.

CONFLICT-11

Model output MUST NOT become authority.

CONFLICT-12

High-risk conflicts MUST require stricter resolution.

CONFLICT-13

Critical unresolved conflicts MUST block affected unsafe actions.

CONFLICT-14

Policy precedence MUST be deterministic.

CONFLICT-15

Lower-level policy MUST NOT override higher-level policy.

CONFLICT-16

Concurrent state changes MUST NOT silently overwrite one another.

CONFLICT-17

Conflict resolution MUST preserve historical state.

CONFLICT-18

Rejected claims MUST remain recoverable through provenance.

CONFLICT-19

Corroboration MUST consider source independence.

CONFLICT-20

Stale information MUST be distinguishable from false information.

CONFLICT-21

Resolution MUST identify affected downstream objects where possible.

CONFLICT-22

Human decisions MUST be auditable.

CONFLICT-23

Conflict resolution MUST respect authorization.

CONFLICT-24

Conflict handling MUST NOT create privilege escalation.

CONFLICT-25

Conflict detection MUST be protected against evidence poisoning.

CONFLICT-26

Conflict floods MUST be bounded.

CONFLICT-27

Conflict state MUST be versioned.

CONFLICT-28

Resolution proposals MUST be reproducible where practical.

CONFLICT-29

Conflict resolution MUST NOT modify immutable historical evidence.

CONFLICT-30

No system component may declare a conflict resolved solely because doing so is convenient for execution.

⸻

33. Canonical Conflict Pipeline

Observation
    ↓
Evidence
    ↓
Claim
    ↓
Knowledge
    ↓
Contradiction Detection
    ↓
Conflict Classification
    ↓
Context Analysis
    ↓
Evidence Evaluation
    ↓
Impact Analysis
    ↓
Resolution Proposal
    ↓
Policy / Authority Check
    ↓
┌──────────────────────────┐
│                          │
↓                          ↓
Resolve                 Escalate
│                          │
↓                          ↓
Verify                  Human / New Evidence
│                          │
└──────────────┬───────────┘
               ↓
        Resolution Commit
               ↓
        World / Knowledge
        Reconciliation
               ↓
             Audit

⸻

34. Final Principle

Veda MUST NOT be designed as a system that always has an answer.

It must be designed as a system that knows the difference between:

TRUE
FALSE
UNKNOWN
UNCERTAIN
STALE
CONTRADICTORY
CONFLICTING
UNVERIFIED

The objective is not to eliminate disagreement.

The objective is to make disagreement:

visible
structured
traceable
evaluatable
resolvable
or safely unresolved

The fundamental chain is:

Reality
  ↓
Observation
  ↓
Evidence
  ↓
Claim
  ↓
Knowledge
  ↓
Contradiction Detection
  ↓
Conflict Analysis
  ↓
Resolution / Uncertainty
  ↓
Verification
  ↓
World / Knowledge Update
  ↓
Audit

And the core rule is:

Veda must never confuse the absence of contradiction with the presence of truth.