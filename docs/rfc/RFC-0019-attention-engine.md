RFC-0019: Veda Attention Engine

Status: Draft
Layer: 8 — Attention & Planning
Depends On: RFC-0002, RFC-0003, RFC-0005, RFC-0006, RFC-0007, RFC-0010, RFC-0013, RFC-0014, RFC-0017, RFC-0018
Related: RFC-0020, RFC-0021, RFC-0023, RFC-0025, RFC-0026, RFC-0031

⸻

1. Abstract

RFC-0019 defines the Veda Attention Engine.

The Attention Engine determines:

* what Veda should notice
* what Veda should ignore
* what requires immediate processing
* what can wait
* what should be remembered
* what should interrupt current cognition
* what should be delegated to background processing
* what deserves deeper reasoning
* what requires human attention

Attention is therefore a resource-allocation mechanism between:

World
Events
Goals
Processes
Memory
Knowledge
Brain
Actions
Human

The fundamental model is:

Reality
   ↓
Events / Observations
   ↓
Attention Candidates
   ↓
Relevance Evaluation
   ↓
Priority
   ↓
Attention Decision
   ↓
Brain

The Attention Engine does not determine ultimate truth.

It determines what deserves cognitive resources.

⸻

2. Motivation

A real personal AI operates in a continuous environment.

At any moment Veda may receive:

100 system events
20 application events
10 network events
5 user events
3 scheduled tasks
2 failures
1 security warning

Processing everything equally is impossible and unnecessary.

Therefore Veda requires selective attention.

The central problem is:

What deserves computation now?

⸻

3. Design Goals

The Attention Engine MUST:

1. detect important events
2. rank attention candidates
3. consider active goals
4. consider urgency
5. consider risk
6. consider expected benefit
7. consider uncertainty
8. consider user preferences
9. prevent notification overload
10. support interruption
11. support background processing
12. protect critical events from starvation
13. support attention budgets
14. explain attention decisions
15. preserve auditability
16. adapt to changing world state
17. distinguish event importance from truth
18. distinguish attention from authorization
19. support human attention routing
20. prevent attention loops

⸻

4. Non-Goals

RFC-0019 does NOT define:

* full reasoning
* long-term memory architecture
* planning algorithms
* authorization
* action execution
* final decision policy
* consciousness
* emotions
* human psychology simulation

The Attention Engine decides what deserves attention, not what is ultimately true or what may be executed.

⸻

5. Core Principle

Attention is not importance alone.

Attention is:

Attention
=
Relevance
×
Urgency
×
Impact
×
Goal Alignment
×
Risk
×
Information Value
×
Need for Cognition

subject to:

Policy
+
Resource Constraints
+
Privacy
+
Current Cognitive State
+
Human Preferences

⸻

6. Attention Pipeline

┌───────────────────────┐
│ World / Event Stream  │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ Candidate Detection   │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ Deduplication         │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ Context Enrichment    │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ Relevance Evaluation  │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ Priority Calculation  │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ Attention Policy      │
└───────────┬───────────┘
            ↓
     ┌──────┴──────┐
     ↓             ↓
  Immediate     Background
     ↓             ↓
    Brain       Queue

⸻

7. Attention Candidate

Every potentially relevant event becomes an Attention Candidate.

Conceptual schema:

attention_candidate:
  attention_id: string
  version: integer
  source_event_refs: []
  subject: string
  event_type: string
  detected_at: timestamp
  relevance:
    value: number
    reasons: []
  urgency:
    value: number
    deadline: timestamp
  impact:
    value: number
  risk:
    value: number
  goal_alignment:
    value: number
    goal_refs: []
  information_value:
    value: number
  uncertainty:
    value: number
  novelty:
    value: number
  user_relevance:
    value: number
  attention_cost:
    value: number
  priority:
    value: number
  recommended_mode: string
  recommended_target:
    type: string
    id: string
  status: string

⸻

8. Attention States

Candidates MAY transition through:

DETECTED
   ↓
ENRICHING
   ↓
EVALUATING
   ↓
RANKED
   ↓
QUEUED
   ↓
ATTENDING
   ↓
PROCESSED

Alternative states:

DEFERRED
SUPPRESSED
IGNORED
EXPIRED
MERGED
ESCALATED
CANCELLED

⸻

9. Relevance

Relevance measures how strongly an event relates to Veda’s current context.

Factors MAY include:

active goal
active process
user request
current project
known preferences
current world state
dependencies
recent activity
entity relationships

Example:

Git repository changed

If Veda is currently deploying that repository:

Relevance = High

If the repository has not been touched for six months:

Relevance = Low

The event itself has not changed.

The context has.

⸻

10. Urgency

Urgency measures how much delay matters.

Possible factors:

deadline
time decay
rapidly changing state
security window
financial exposure
system failure
user waiting
scheduled event
dependency blockage

Example:

Server disk:
92%

may have moderate urgency.

Server disk:
99.9%

may have critical urgency.

⸻

11. Impact

Impact estimates the consequence of ignoring an event.

Possible levels:

NONE
LOW
MEDIUM
HIGH
CRITICAL

Example:

Temporary cache miss
→ LOW
Production database corruption
→ CRITICAL

Impact is predictive.

It MUST NOT be treated as confirmed outcome.

⸻

12. Risk

Risk represents potential harm associated with ignoring or mishandling the event.

Risk MAY include:

security
privacy
financial
operational
data loss
legal
reputation
system integrity
user safety

High-risk events SHOULD receive stronger attention.

⸻

13. Goal Alignment

Attention MUST consider current goals.

Example:

Goal:
Deploy Veda v0.1

Events related to:

build
tests
Git
server
deployment
dependencies

receive increased relevance.

Unrelated events SHOULD generally receive lower priority.

⸻

14. Information Value

Some events matter because they significantly reduce uncertainty.

Example:

Hypothesis:
Deployment failure caused by dependency conflict.

New event:

Dependency resolver error confirms conflict.

This event has high information value because it changes the hypothesis space.

⸻

15. Novelty

Repeated identical events SHOULD NOT consume unlimited attention.

Example:

CPU 40%
CPU 41%
CPU 40%
CPU 42%

should not generate four independent high-priority cognitive tasks.

Attention SHOULD detect repetition and aggregation.

⸻

16. Deduplication

Equivalent events MAY be merged.

Example:

100 identical network timeout events

becomes:

NetworkTimeoutBurst
count = 100
first_seen = ...
last_seen = ...

This prevents event storms.

⸻

17. Attention Aggregation

Related events MAY form an Attention Group.

Example:

Database connection failure
API timeout
Queue backlog
Retry storm

may represent one larger incident:

Potential infrastructure failure

The Attention Engine SHOULD aggregate when evidence indicates a shared cause.

⸻

18. Priority Score

A conceptual scoring function:

Priority =
Wr × Relevance
+ Wu × Urgency
+ Wi × Impact
+ Wg × GoalAlignment
+ Wk × Risk
+ Wv × InformationValue
+ Wn × Novelty
+ Wu2 × UserRelevance
+ Wc × Uncertainty
− Wcost × AttentionCost

Weights MUST be policy-controlled.

They MUST NOT be silently rewritten by an intelligence provider.

⸻

19. Hard Attention Rules

Some events MUST bypass normal scoring.

Examples:

active security breach
imminent destructive action
critical system failure
explicit emergency policy
required human approval
data corruption detection
safety-critical event

These events may trigger:

CRITICAL ATTENTION

even if their calculated score is low.

⸻

20. Attention Classes

Initial classes:

CRITICAL
URGENT
IMPORTANT
NORMAL
BACKGROUND
NOISE

CRITICAL

Immediate cognitive processing.

URGENT

Process quickly.

IMPORTANT

Queue for near-term processing.

NORMAL

Process according to available resources.

BACKGROUND

Process asynchronously.

NOISE

Suppress or aggregate.

⸻

21. Attention Budget

Attention is a finite resource.

The Brain SHOULD have a configurable budget:

attention_budget:
  concurrent_tasks: 4
  critical_reserved: 1
  background_tasks: 8
  cpu_budget: 40%
  memory_budget: 8GB
  cloud_cost_budget: 0.50

Budgets prevent a flood of low-value work from starving important cognition.

⸻

22. Critical Reservation

The system SHOULD reserve resources for critical events.

Example:

Current:
4 normal cognitive tasks running
Critical security event arrives

Veda MUST be able to:

pause low-priority cognition
reserve resources
start critical cognition

⸻

23. Starvation Prevention

High-priority events must not permanently prevent lower-priority work from occurring.

The system SHOULD use:

aging
fair scheduling
deadlines
priority decay
resource quotas

Example:

Background task waiting:
3 hours

Its priority MAY increase gradually.

⸻

24. Attention Decay

Attention relevance MAY decay with time.

Example:

New log warning:
High attention
After successful verification:
Low attention

However, critical unresolved events MUST NOT decay below their safety threshold.

⸻

25. Attention Persistence

Some events should remain active until resolved.

Examples:

unresolved security incident
failed backup
data corruption
blocked critical goal
verification failure

These become persistent attention items.

⸻

26. Attention Interruption

Attention MAY interrupt active cognition.

Interruption levels:

NONE
LOW
MEDIUM
HIGH
CRITICAL

Example:

LOW:
new notification
HIGH:
production outage
CRITICAL:
security breach

⸻

27. Interrupt Policy

The Attention Engine MUST consider:

current task importance
current task progress
new event severity
interruptibility
user preferences
reversibility

A critical event MAY interrupt nearly anything.

A normal event SHOULD NOT interrupt critical cognition.

⸻

28. Human Attention

Not every event should be sent to the Brain.

Some events should go directly to the human.

Example:

"Approve deletion of production database."

The Attention Engine MAY route:

Human Attention

rather than:

Further AI Reasoning

The human is part of the cognitive system.

Not an error handler.

⸻

29. Human Notification Policy

Notifications SHOULD consider:

importance
urgency
current user activity
quiet hours
notification frequency
previous acknowledgement
channel availability

Possible channels:

iPhone
Desktop
Voice
Watch
Web
Terminal

⸻

30. Notification Aggregation

Instead of:

Notification 1
Notification 2
Notification 3
...
Notification 97

Veda SHOULD produce:

Infrastructure Incident
97 related failures detected
First seen: 14:02
Current state: unresolved
Impact: High
Recommended action: investigate database connectivity

Attention should reduce noise rather than become the source of it.

⸻

31. Attention and Memory

Attention MAY influence memory formation.

Important events MAY become Experience Candidates.

Example:

Critical deployment failure
      ↓
High attention
      ↓
Processed
      ↓
Verified outcome
      ↓
Experience
      ↓
Reflection
      ↓
Memory / Knowledge

Attention alone MUST NOT create permanent memory.

⸻

32. Attention and Knowledge

Knowledge can influence attention.

Example:

Knowledge:
"This server normally experiences CPU spikes at 03:00."

Then:

03:00 CPU spike

may receive lower attention because it is expected.

Unexpected deviation:

CPU spike at 15:00

may receive higher attention.

⸻

33. Attention and Prediction

Future predictions MAY generate attention candidates.

Example:

Prediction:
Disk will reach 95% within 2 hours.

Attention may create:

Preventive Attention

before failure occurs.

This connects to future and scenario systems.

⸻

34. Attention and Causality

Attention SHOULD prioritize events likely to explain important changes.

Example:

Service failed

Candidate causes:

dependency update
configuration change
network failure
resource exhaustion

Evidence strongly connecting one cause to the failure increases attention toward it.

⸻

35. Attention and Planning

When a goal is active:

Goal
 ↓
Relevant Events
 ↓
Attention
 ↓
Brain
 ↓
Planner

Attention may identify:

blocked dependency
missing resource
failed task
new information
goal deviation

that requires replanning.

⸻

36. Goal Deviation Detection

Attention SHOULD monitor active goals for deviation.

Example:

Goal:
Deploy successfully.

Expected:

Tests pass
Build passes
Deployment succeeds

Observed:

Build passes
Deployment repeatedly fails

Attention should elevate:

Goal Deviation

⸻

37. Attention Hysteresis

The system SHOULD avoid rapidly switching attention between nearly equal candidates.

Example:

Event A priority = 0.81
Event B priority = 0.80

then:

A → B → A → B

should not occur continuously.

Hysteresis or minimum commitment windows SHOULD be used.

⸻

38. Attention Stability

Attention decisions SHOULD be stable enough for meaningful cognition.

The system SHOULD avoid:

attention thrashing
context switching
provider switching
mode switching
notification storms

⸻

39. Attention Cost

Attention itself has a cost.

Costs include:

CPU
RAM
GPU
tokens
latency
energy
context switching
human interruption
cloud cost

The engine SHOULD evaluate:

Expected Benefit of Attention
/
Cost of Attention

⸻

40. Expected Value of Attention

A candidate MAY be prioritized using:

EVA =
Expected Information Gain
+
Expected Goal Benefit
+
Expected Risk Reduction
+
Expected Failure Prevention
−
Attention Cost

High EVA candidates SHOULD be processed first.

⸻

41. Attention Confidence

The Attention Engine MUST represent uncertainty in its own prioritization.

Example:

Candidate:
Potential security incident
Priority:
0.94
Confidence:
0.61
Reason:
Limited evidence

This is different from saying:

Security incident confirmed.

⸻

42. Attention Explanation

Every significant attention decision SHOULD be explainable.

Example:

Attention Decision
Event:
Production database latency
Priority:
0.91
Reasons:
+ Active production goal
+ High operational impact
+ Increasing rapidly
+ Affects dependent services
+ No known resolution
Action:
Interrupt current low-priority reasoning

⸻

43. Attention Provenance

The engine MUST preserve why a candidate received its priority.

Example:

attention_reason:
  factor: goal_alignment
  source: goal:G-102
  contribution: 0.21

This allows later audit and tuning.

⸻

44. Attention Feedback

The system SHOULD evaluate whether attention decisions were useful.

After processing:

Attention Decision
      ↓
Outcome
      ↓
Was attention useful?
      ↓
Update attention statistics

Metrics MAY include:

false positives
missed events
late detections
unnecessary interruptions
processing value
resolution time
attention cost

⸻

45. Learning Attention

Attention parameters MAY improve from verified outcomes.

Example:

Repeatedly ignored event type
      ↓
Later caused critical failure
      ↓
Attention policy learns
      ↓
Future priority increases

Learning MUST remain subordinate to policy.

The Attention Engine MUST NOT rewrite safety constraints autonomously.

⸻

46. Attention Security

Threats include:

Attention Flooding

Generate huge numbers of events.

Attention Hijacking

Make irrelevant events appear critical.

Priority Manipulation

Inject fake goal relationships.

Notification Abuse

Cause excessive user interruptions.

Starvation

Prevent important background tasks from receiving resources.

Suppression

Hide critical events behind noise.

⸻

47. Attention Trust Boundaries

External systems MUST NOT directly assign Veda’s attention priority.

For example:

External application:
"Set this event to CRITICAL."

must be treated as:

Priority Claim

not:

Attention Authority

Final attention policy belongs to Veda.

⸻

48. Attention Queue

Conceptual queue:

┌───────────────┐
│ CRITICAL      │
├───────────────┤
│ URGENT        │
├───────────────┤
│ IMPORTANT     │
├───────────────┤
│ NORMAL        │
├───────────────┤
│ BACKGROUND    │
└───────────────┘

Each queue SHOULD support:

* priority
* deadline
* aging
* cancellation
* merging
* deduplication
* resource limits

⸻

49. Attention Scheduler

The scheduler determines:

which candidate
when
with what resources
using which cognitive mode
using which provider
for which duration

It SHOULD coordinate with RFC-0016.

Example:

Attention:
High-priority coding problem
↓
Recommended Mode:
CODING
↓
Router:
Local coding model available
↓
Brain:
Start cognitive task

⸻

50. Attention State Machine

DETECTED
   ↓
ENRICHING
   ↓
EVALUATING
   ↓
RANKED
   ↓
QUEUED
   ↓
ATTENDING
   ├──→ DEFERRED
   ├──→ SUPPRESSED
   ├──→ MERGED
   └──→ ESCALATED
          ↓
       PROCESSED
          ↓
       RESOLVED

⸻

51. Attention Events

The following events SHOULD be supported:

AttentionCandidateDetected
AttentionCandidateMerged
AttentionCandidateEvaluated
AttentionPriorityCalculated
AttentionQueued
AttentionStarted
AttentionInterrupted
AttentionDeferred
AttentionSuppressed
AttentionEscalated
AttentionExpired
AttentionProcessed
AttentionResolved
AttentionFeedbackReceived
AttentionPolicyApplied
AttentionPolicyChanged
AttentionBudgetExceeded
AttentionStarvationDetected
AttentionFloodDetected

These events integrate with RFC-0031.

⸻

52. Attention API

Conceptual interface:

detect(event)
evaluate(candidate_id)
rank(candidate_id)
queue(candidate_id)
interrupt(candidate_id)
defer(candidate_id)
suppress(candidate_id)
merge(candidate_ids)
escalate(candidate_id)
resolve(candidate_id)
get_priority(candidate_id)
get_attention_state(candidate_id)
get_queue()
get_budget()
explain(candidate_id)
get_feedback(candidate_id)

⸻

53. Example: System Failure

World:

Database latency increasing.

Events:

E1 CPU +10%
E2 DB latency +40%
E3 API timeout
E4 queue backlog
E5 user unrelated notification

Attention evaluates:

E1 → 0.42
E2 → 0.91
E3 → 0.87
E4 → 0.79
E5 → 0.03

Aggregation detects:

E2 + E3 + E4

are likely related.

Attention Group:

Possible Database / Infrastructure Incident

Brain receives the group rather than four unrelated tasks.

⸻

54. Example: Security Event

Event:

Unknown process accessed credential store.

Even if:

Relevance = uncertain

the event may still become:

CRITICAL

because:

Potential impact = severe
Potential risk = severe

Attention therefore does not require certainty before allocating defensive cognition.

⸻

55. Example: Goal Monitoring

Goal:

Build Veda prototype.

Events:

RFC completed
test failed
disk space decreasing
new GitHub notification
unrelated OS update

Attention:

test failed
    ↓
HIGH
disk space decreasing
    ↓
MEDIUM
RFC completed
    ↓
NORMAL
GitHub notification
    ↓
LOW
OS update
    ↓
BACKGROUND

The system remains focused on the active goal.

⸻

56. Example: Human Escalation

Event:

Production database deletion requested.

Attention:

Impact: Critical
Risk: Critical
Reversibility: Low
Authority requirement: High

Result:

Human Attention Required

The system SHOULD NOT attempt to reason itself into permission.

⸻

57. Attention Invariants

ATT-1

Attention MUST NOT be treated as truth evaluation.

ATT-2

Attention MUST NOT grant authority.

ATT-3

Critical events MUST be capable of bypassing normal priority queues.

ATT-4

Attention decisions MUST be auditable.

ATT-5

Attention SHOULD preserve the reason for prioritization.

ATT-6

Attention MUST consider active goals.

ATT-7

Attention MUST consider urgency.

ATT-8

Attention SHOULD consider impact.

ATT-9

Attention SHOULD consider risk.

ATT-10

Attention SHOULD consider information value.

ATT-11

Attention SHOULD consider attention cost.

ATT-12

Attention MUST support event deduplication.

ATT-13

Attention SHOULD support event aggregation.

ATT-14

Repeated events MUST NOT automatically create unlimited cognitive tasks.

ATT-15

Critical attention MUST have reserved resources.

ATT-16

Lower-priority tasks MUST have starvation protection.

ATT-17

Attention SHOULD support aging.

ATT-18

Attention SHOULD support interruption.

ATT-19

Interruptions MUST be policy-governed.

ATT-20

Human attention MUST be treated as a valid destination.

ATT-21

External systems MUST NOT directly control Veda’s attention policy.

ATT-22

Attention priority MUST be distinguishable from confidence.

ATT-23

Attention confidence MUST be distinguishable from truth.

ATT-24

Attention decisions SHOULD use current world context.

ATT-25

Stale attention candidates MUST be re-evaluated.

ATT-26

Resolved events MUST decay or leave active attention.

ATT-27

Persistent critical events MUST remain visible until resolved or explicitly acknowledged.

ATT-28

Attention learning MUST remain policy-constrained.

ATT-29

Attention MUST prevent notification flooding.

ATT-30

Attention MUST provide a path for human override.

⸻

58. Relationship to Brain

The relationship is:

                    WORLD
                      ↓
                    EVENTS
                      ↓
              ┌───────────────┐
              │   ATTENTION   │
              └───────┬───────┘
                      ↓
                    BRAIN
                      ↓
                  COGNITION

Attention answers:

"What deserves cognition?"

Brain answers:

"How should we think about it?"

⸻

59. Relationship to Planner

Planner answers:

"How do we achieve this goal?"

Attention answers:

"What deserves attention right now?"

Brain answers:

"What does this information mean?"

Decision Engine answers:

"Which option has the best value under the rules?"

Authorization answers:

"Is this action permitted?"

Action Fabric answers:

"How is the action executed?"

This separation is intentional.

⸻

60. Complete Cognitive Chain

Veda now has:

WORLD
  ↓
EVENTS
  ↓
ATTENTION
  ↓
BRAIN
  ↓
MEMORY / KNOWLEDGE
  ↓
REASONING
  ↓
GOAL
  ↓
PLANNER
  ↓
DECISION
  ↓
AUTHORIZATION
  ↓
ACTION
  ↓
VERIFICATION
  ↓
WORLD UPDATE
  ↓
EXPERIENCE
  ↓
LEARNING

The next major question is no longer:

"What should Veda look at?"

That is RFC-0019.

The next question becomes:

"Once Veda knows what matters, how does it systematically determine what to do?"

That is the purpose of RFC-0020: Planner.

⸻

61. Final Principle

Attention is the mechanism by which Veda allocates cognition to the parts of reality that matter most under current goals, risks, constraints, and uncertainty.

The Attention Engine must prevent Veda from becoming either:

A system that notices nothing

or:

A system that notices everything

The correct architecture is:

Notice selectively.
Prioritize explicitly.
Interrupt carefully.
Preserve critical signals.
Suppress noise.
Spend cognition where it has value.
