RFC-0044 — Multi-Agent World

Status: Architecture
Layer: 17 — Multi-Agent / Federation
Depends On: RFC-0001, RFC-0002, RFC-0003, RFC-0004, RFC-0008, RFC-0009, RFC-0010, RFC-0012, RFC-0013, RFC-0014, RFC-0015, RFC-0016, RFC-0017, RFC-0018, RFC-0019, RFC-0020, RFC-0021, RFC-0022, RFC-0023, RFC-0024, RFC-0026, RFC-0027, RFC-0028, RFC-0029, RFC-0030, RFC-0031, RFC-0032, RFC-0033, RFC-0035, RFC-0036, RFC-0037, RFC-0038, RFC-0039, RFC-0040, RFC-0041, RFC-0042, RFC-0043
Related: RFC-0045, RFC-0046

⸻

1. Abstract

RFC-0044 กำหนดสถาปัตยกรรม Multi-Agent World

เพื่อให้ Veda สามารถมี agent หลายตัวทำงานร่วมกันภายใน World เดียวกัน โดยยังคง:

Identity
Authority
Trust
Privacy
World Consistency
Provenance
Auditability
Conflict Resolution
Verification
Human Control

หลักการสำคัญ:

Many Agents
      ↓
Shared World
      ↓
Scoped Views
      ↓
Independent Cognition
      ↓
Controlled World Changes
      ↓
Verified Shared State

Multi-Agent World ไม่ได้หมายความว่า:

ทุก agent เห็นทุกอย่าง

แต่หมายถึง:

หลาย agent สามารถอ้างอิง
และทำงานกับ reality model ที่สัมพันธ์กัน
โดยมี scope และ authority ที่ชัดเจน

⸻

2. Motivation

Veda ระยะเริ่มต้นอาจมี:

Veda Core

เพียงหนึ่ง agent

แต่เมื่อระบบขยาย อาจมี:

Research Agent
Coding Agent
Security Agent
Planning Agent
Browser Agent
Finance Agent
System Agent
Memory Agent
Verification Agent
Simulation Agent

หากทุกตัวมี world model แยกกันโดยไม่มี synchronization:

Agent A:
server = healthy
Agent B:
server = offline
Agent C:
server = unknown

ระบบจะไม่รู้ว่า:

Which world is authoritative?

RFC-0044 จึงกำหนด shared world architecture

⸻

3. Core Principle

One Reality
Many Agents
Many Views
Controlled Mutations

หรือ:

Reality
   ↓
Shared World
   ↓
Agent-specific View

ไม่ใช่:

Agent A World
Agent B World
Agent C World

แล้วหวังว่า merge ทีหลังจะไม่เกิดสงครามกลางเมืองใน database

⸻

4. Definitions

4.1 Agent

Autonomous computational actor ที่สามารถ:

Observe
Reason
Plan
Propose
Act
Verify
Learn

ภายใต้ authority ที่กำหนด

⸻

4.2 World

Representation ของ entities, states, relationships, events และ evidence ที่ Veda ใช้ model reality

ตาม RFC-0002

⸻

4.3 Shared World

World state ที่สามารถถูกอ้างอิงร่วมกันโดย agent หลายตัว

⸻

4.4 World View

Projection ของ Shared World ที่ agent หนึ่งได้รับ

Shared World
      ↓
Permission
      ↓
Relevance
      ↓
Scope
      ↓
Agent View

⸻

4.5 Agent State

สถานะเฉพาะของ agent:

Goals
Tasks
Working Memory
Local Memory
Capabilities
Leases
Processes
Private Context

Agent state ไม่จำเป็นต้องอยู่ใน Shared World

⸻

5. Fundamental Separation

Shared World
≠
Agent Internal State

ตัวอย่าง:

Agent A คิดว่า:

"Server น่าจะถูกโจมตี"

นี่เป็น:

Hypothesis

ไม่ใช่:

Shared World Fact

จนกว่าจะมี evidence และ epistemic status ที่เหมาะสม

⸻

6. World Ownership

Shared World ต้องมี owner/controller:

world:
  world_id:
  owner:
  controller:
  governance_policy:

สำหรับ Veda:

Human Authority
      ↓
Veda Constitution
      ↓
World Governance

Agent ไม่สามารถประกาศตัวเองเป็น owner

⸻

7. World Identity

ทุก World ต้องมี:

world_id
world_version
governance_version
schema_version

ตัวอย่าง:

world_veda_main
version 10482
schema 1.0
governance 17

⸻

8. World Membership

Agent สามารถเป็นสมาชิกของ World:

membership:
  agent_id:
  world_id:
  role:
  scope:
  permissions:
  valid_from:
  valid_until:

Role ตัวอย่าง:

OBSERVER
CONTRIBUTOR
SPECIALIST
COORDINATOR
VERIFIER
ADMINISTRATOR

Role ไม่เท่ากับ unrestricted authority

⸻

9. Agent Role

Agent role ระบุ responsibility:

Research Agent
→ research
Coding Agent
→ code
Verifier Agent
→ verify
Security Agent
→ security analysis

Role ไม่ควรถูกใช้เป็น shortcut:

role = security
→ can_do_everything

⸻

10. Agent Scope

Agent อาจมี scope:

scope:
  entities:
  projects:
  environments:
  operations:
  data_classification:

ตัวอย่าง:

Coding Agent
scope:
  repository: veda
  environment: development

ดังนั้นมันไม่ควรสามารถ mutate:

production

โดยอัตโนมัติ

⸻

11. Shared World Layers

Shared World สามารถแบ่งเป็น:

GLOBAL
PROJECT
DOMAIN
TASK
SESSION

เช่น:

GLOBAL
  ↓
Veda
  ↓
Project Veda
  ↓
Coding Task
  ↓
Session

⸻

12. World View

Agent จะได้รับ:

World View

ซึ่งเป็น:

Shared World
+
Scope
+
Permissions
+
Relevance
+
Task
+
Privacy

⸻

13. View Is Not World

Agent A sees:
Entity A, B, C
Agent B sees:
Entity A, C
Shared World:
A, B, C, D, E, F

ดังนั้น:

Agent B does not know D

ไม่ได้แปลว่า:

D does not exist

นี่คือ distinction ระหว่าง:

UNKNOWN

กับ:

NON-EXISTENT

⸻

14. Partial Knowledge

Agent สามารถมี:

KNOWN
BELIEVED
UNKNOWN
INACCESSIBLE
REDACTED

และต้องไม่เปลี่ยน:

INACCESSIBLE

เป็น:

DOES_NOT_EXIST

⸻

15. Agent Private World

Agent สามารถมี private state:

Private Memory
Private Hypotheses
Working Context
Temporary Plans
Internal Metrics

แต่ private state ไม่สามารถกลายเป็น shared fact โดยอัตโนมัติ

⸻

16. Shared Knowledge

Knowledge สามารถแชร์:

Shared Knowledge

แต่ต้อง preserve:

Provenance
Confidence
Evidence
Scope
Validity
Status

ตาม RFC-0013

⸻

17. Shared Memory

Memory สามารถแบ่ง:

PRIVATE
SHARED
RESTRICTED
PUBLIC

แต่การแชร์ memory ต้องผ่าน policy

⸻

18. Shared Experience

Agent A สามารถส่ง:

Experience

ให้ agent B

แต่ B ต้องรู้:

source_agent
experience_type
verification
confidence
context

Experience ไม่กลายเป็น truth เพียงเพราะ agent อื่นได้รับ

⸻

19. Agent Identity

ทุก agent ต้องมี:

identity_id
agent_id
instance_id

เช่น:

Veda
  └── Coding Agent
        └── Instance 492

Identity hierarchy ต้องไม่ทำให้ child agent ได้ authority เกิน parent scope

⸻

20. Agent Passport

Agent สามารถนำ:

Agent Passport

มาแสดง:

identity
controller
capabilities
versions
attestations
trust evidence

ตาม RFC-0040

Passport เป็น evidence package

ไม่ใช่ permission

⸻

21. Trust

Agent trust มาจาก RFC-0041

เช่น:

Coding Agent
trust:
  domain = coding
  environment = development
  level = high

แต่:

trust ≠ authority

⸻

22. Authority

Authority มาจาก RFC-0010

ตัวอย่าง:

Coding Agent:
read repository
write development branch
run tests

ไม่ได้หมายความว่า:

delete production

⸻

23. Capability

Capability มาจาก RFC-0009 / RFC-0028

ตัวอย่าง:

git.read
git.write
terminal.execute
filesystem.write

Agent ต้องมีทั้ง:

Capability
+
Authorization

ก่อน action

⸻

24. Agent Communication

Agent สามารถสื่อสาร:

Agent A
   ↓
Message
   ↓
Agent B

แต่ message ไม่เท่ากับ World mutation

World mutation ต้องใช้:

WDP

⸻

25. Context Communication

Agent สามารถแลก:

NCP Context

เช่น:

Research Agent
   ↓
NCP
   ↓
Coding Agent

แต่เฉพาะ context ที่ policy อนุญาต

⸻

26. World Mutation

Agent mutation:

Agent
 ↓
Proposal
 ↓
WDP
 ↓
Validation
 ↓
Authorization
 ↓
Apply
 ↓
Verification

Agent ไม่ควร:

directly edit shared world database

เพราะนั่นคือการสร้าง backdoor authority ด้วย SQL ซึ่งเป็นวิธีที่มนุษย์ชอบใช้ก่อนจะมานั่งแก้ incident ตอนตีสาม

⸻

27. Agent Proposal

Agent สามารถเสนอ:

proposal:
  agent:
  objective:
  proposed_delta:
  expected_outcome:
  risk:
  evidence:

จากนั้น:

Decision
Authorization

จึงตัดสินว่าจะ execute หรือไม่

⸻

28. Agent Action

Action lifecycle:

PROPOSED
→ AUTHORIZED
→ EXECUTING
→ OBSERVED
→ VERIFIED
→ WORLD_UPDATED

ตาม Action/Verification architecture

⸻

29. Concurrent Agents

ตัวอย่าง:

Agent A:
deploy service
Agent B:
rollback service

พร้อมกัน

ระบบต้องตรวจ:

Temporal conflict
Action conflict
Goal conflict
Resource conflict
Authority conflict

ก่อน execute

⸻

30. Agent Coordination

Coordination modes:

SEQUENTIAL
PARALLEL
HIERARCHICAL
NEGOTIATED
AUCTION
DELEGATED
COMPETITIVE
COOPERATIVE

Implementation เลือกตาม task

⸻

31. Sequential Coordination

Research
 ↓
Planning
 ↓
Coding
 ↓
Testing
 ↓
Deployment

เหมาะกับ dependency ที่ชัด

⸻

32. Parallel Coordination

Research ─────┐
Security ─────┼→ Synthesis
Testing ──────┘

ต้องกำหนด:

shared inputs
independent outputs
merge policy

⸻

33. Hierarchical Coordination

Veda Coordinator
      │
 ┌────┼─────┐
 ▼    ▼     ▼
Research Coding Security

Coordinator:

coordinates

ไม่ได้หมายความว่า:

can override Constitution

⸻

34. Negotiated Coordination

Agent A:

Need database access

Agent B:

Can provide read-only access

Negotiation result:

lease:
capability = database.read
scope = project_x
expiry = 10m

ต้องผ่าน Authorization

⸻

35. Delegation

Agent A อาจ delegate:

Task

ให้ Agent B

แต่ delegation ต้อง preserve:

original authority boundary

หลัก:

Delegated Authority
≤
Delegator Authority

โดย default

⸻

36. Authority Attenuation

ตัวอย่าง:

Agent A:

filesystem.write
scope=/veda

Delegate ให้ B:

filesystem.write
scope=/veda/src
expiry=10m

B ไม่ควรได้:

/entire_system

⸻

37. Agent Supervision

High-risk agents ควรมี supervisor:

Agent
 ↓
Supervisor
 ↓
Authorization

Supervisor สามารถ:

pause
cancel
quarantine
escalate

แต่ไม่สามารถ bypass Constitution

⸻

38. Agent Failure

Agent อาจ:

crash
timeout
hallucinate
loop
misplan
misuse tool
lose context
lose connection

Shared World ต้องไม่ assume:

agent alive

จาก heartbeat อย่างเดียว

⸻

39. Agent Health

Agent health:

HEALTHY
DEGRADED
UNRESPONSIVE
FAILED
QUARANTINED
REVOKED

Self-Diagnostics และ Trust Engine สามารถใช้ข้อมูลนี้

⸻

40. Agent Quarantine

หาก agent มี anomaly:

Agent
 ↓
Quarantine

สามารถ:

stop new actions
revoke leases
isolate context
preserve evidence
run diagnostics

แต่ต้อง preserve history

⸻

41. Agent Recovery

Recovery:

Detect
 ↓
Quarantine
 ↓
Diagnose
 ↓
Reconstruct State
 ↓
Restore
 ↓
Verify
 ↓
Rejoin World

Agent ไม่ควรกลับมา mutate world ก่อน verification

⸻

42. Agent Rejoin

เมื่อ agent กลับมา:

Old World:
v100
Shared World:
v130

ห้าม resume จาก v100 โดยตรง

ต้อง:

sync
→ reconcile
→ rebuild context
→ revalidate plan
→ reauthorize if necessary

⸻

43. Stale Agent Protection

Agent stale อาจมี:

old policy
old world
old authority
old context
old assumptions

ก่อน consequential action:

freshness check

⸻

44. Plan Invalidation

ถ้า World เปลี่ยน:

Agent plan

อาจกลายเป็น:

STALE

Agent ต้อง:

revalidate

ก่อน execute

⸻

45. Goal Conflicts

Agent A:

minimize cost

Agent B:

maximize reliability

เป้าหมายอาจขัดกัน

ต้องใช้:

Value & Decision Engine

ไม่ใช่ให้ coordinator เลือกเองแบบ arbitrary

⸻

46. Resource Conflicts

ตัวอย่าง:

Agent A wants GPU
Agent B wants GPU

ทั้งคู่มีสิทธิ์

แต่ resource จำกัด

ต้องมี:

Resource Scheduler

และ policy:

priority
deadline
risk
goal importance
fairness

⸻

47. Fairness

Multi-agent system ควรป้องกัน starvation:

Agent A
always consumes GPU

จน:

Agent B
never runs

ใช้:

quotas
budgets
aging
reservation
priority

⸻

48. Shared Attention

แต่ละ agent มี attention ของตัวเอง:

Agent A attention
Agent B attention

และ Veda มี global attention:

Global Attention

Global attention ต้องไม่ override local privacy

⸻

49. Shared Context

Agent A อาจส่ง:

NCP context

ให้ B

แต่ context ต้องระบุ:

source
scope
sensitivity
confidence
validity

B ต้องไม่ถือว่า context = authoritative world

⸻

50. Shared Evidence

Evidence สามารถแชร์ได้:

Evidence E
 ↓
Agent A
Agent B
Agent C

แต่ evidence integrity ต้องตรวจ

และ evidence interpretation อาจแตกต่างกัน

⸻

51. Disagreement

Agent A:

hypothesis H1

Agent B:

hypothesis H2

ระบบไม่ควร force consensus

ควร preserve:

H1
H2
evidence_A
evidence_B
confidence_A
confidence_B

แล้วใช้:

RFC-0014

จัดการ conflict

⸻

52. Consensus

Consensus เป็น optional mechanism

ประเภท:

UNANIMOUS
MAJORITY
WEIGHTED
EXPERT
EVIDENCE_BASED
HUMAN

แต่ consensus ไม่เท่ากับ truth

10 agents agree
≠
reality verified

⸻

53. Specialist Disagreement

ตัวอย่าง:

Research Agent:
claim = X
Security Agent:
claim = Y

ระบบควร:

compare evidence
check scope
check temporal validity
run verification

ไม่ใช่:

5 agents say X
3 say Y
therefore X

จำนวน agent ไม่ใช่ epistemic evidence โดยตัวมันเอง

⸻

54. Agent Reputation

Reputation อาจใช้เพื่อ routing

แต่:

reputation ≠ truth
reputation ≠ authority

Trust Engine เป็น source สำหรับ contextual reliance

⸻

55. Agent Discovery

Agent สามารถค้นหา:

agent_id
capabilities
role
scope
protocols
trust information
availability

Discovery metadata ต้องผ่าน validation

⸻

56. Agent Registry

Registry:

agent:
  agent_id:
  identity_ref:
  passport_ref:
  roles:
  capabilities:
  protocols:
  scope:
  trust_ref:
  health:
  status:
  version:
  endpoint:
  owner:

⸻

57. Agent Lifecycle

DISCOVERED
   ↓
IDENTIFIED
   ↓
ATTESTED
   ↓
REGISTERED
   ↓
AUTHORIZED
   ↓
ACTIVE
   ↓
DEGRADED
   ↓
QUARANTINED
   ↓
REVOKED

⸻

58. Agent Registration

Registration ต้องตรวจ:

Identity
Passport
Software version
Capabilities
Security posture
Owner/controller
Trust evidence
Endpoint
Protocol compatibility

⸻

59. Agent Versioning

Agent version change:

Agent v1
 ↓
Evolution
 ↓
Agent v2

ไม่ควร inherit trust อัตโนมัติ

ต้อง reassess:

Trust
Capabilities
Security
Behavior
Performance

ตาม RFC-0041

⸻

60. Agent Fork

ถ้า:

Agent A

fork เป็น:

Agent B

B ต้องมี:

new instance identity
new lineage

ไม่ใช้ identity เดียวกัน

⸻

61. Agent Clone

Clone สามารถ copy:

code
configuration
knowledge

แต่ไม่ควร copy:

private keys
active authority leases
identity

โดยตรง

⸻

62. Agent Memory Isolation

Default:

Private Memory

ไม่แชร์

Shared memory ต้อง explicit:

share
scope
purpose
expiry
revocation

⸻

63. Agent Context Isolation

Agent A ต้องไม่เห็น:

Agent B private context

เว้นแต่ policy อนุญาต

⸻

64. Agent Secret Isolation

Private credentials:

Agent A secret

ไม่ควร accessible โดย:

Agent B

แม้อยู่ใน same process

⸻

65. Cross-Agent Tool Use

Agent A ขอ Agent B ให้ใช้ tool:

A → B → Tool

ต้องตรวจ:

Who initiated?
Who is authorized?
Who owns action?
Who is accountable?

ไม่ควรเกิด:

A has no authority
→ asks B
→ B has capability
→ action succeeds
→ nobody knows who authorized it

⸻

66. Delegation Chain

ทุก delegation ต้อง trace:

Human
 ↓
Veda
 ↓
Agent A
 ↓
Agent B
 ↓
Tool

Authority chain ต้อง reconstruct ได้

⸻

67. Confused Deputy Protection

Agent B ต้องไม่ตีความ:

A's request

เป็น:

B's authority

ตรวจ:

Requester
Beneficiary
Authority
Capability
Scope
Purpose

⸻

68. Agent-to-Agent Action

A อาจ request:

request:
  requester: A
  executor: B
  action:
  target:
  purpose:
  authority_ref:

B ต้อง verify authority chain

⸻

69. Accountability

ทุก consequential action ต้องตอบได้:

Who requested?
Who approved?
Who executed?
Which capability?
Which lease?
Which tool?
Which external system?
What happened?
Who verified?

⸻

70. World Event Attribution

World changes ต้อง trace:

Delta
 ↓
Action
 ↓
Agent
 ↓
Identity
 ↓
Authority
 ↓
External Effect
 ↓
Evidence
 ↓
Verification

⸻

71. Shared World Governance

World governance policy ต้องกำหนด:

who can join
who can observe
who can mutate
who can delegate
who can approve
who can verify
who can remove

⸻

72. Human Override

Human authority สามารถ:

pause world
revoke agent
revoke lease
reject delta
freeze mutation
force reconciliation
restore snapshot

Human override ต้องเป็น auditable event

⸻

73. Emergency Mode

World อาจเข้าสู่:

SAFE_MODE

เมื่อ:

critical security incident
integrity failure
governance failure
mass conflict
unknown external state

ใน SAFE_MODE:

nonessential mutations blocked

⸻

74. World Lock

ไม่ควรใช้ global lock เป็น default เพราะจะทำให้ multi-agent architecture กลายเป็น single-threaded system ที่ใส่หมวกหลายใบ

แต่สามารถใช้:

transaction lock
entity lock
resource lock
critical-section lock

สำหรับ high-risk transitions

⸻

75. Optimistic Concurrency

Default model สามารถเป็น:

read
 ↓
plan
 ↓
propose delta
 ↓
validate base version
 ↓
apply if valid

หาก stale:

rebase / replan

⸻

76. Pessimistic Concurrency

สำหรับ critical resource:

acquire lease
 ↓
perform action
 ↓
verify
 ↓
release

เช่น:

production migration

⸻

77. World Partition

World สามารถ partition:

Project A
Project B
Production
Development
Personal
Research

แต่ cross-partition access ต้อง explicit

⸻

78. World Bridge

Agent อาจต้องเชื่อมสอง worlds:

World A
   ↓
Bridge
   ↓
World B

Bridge ต้องกำหนด:

allowed entities
allowed directions
allowed data
allowed operations
trust
authority

⸻

79. Cross-World Identity

Entity ใน World A:

entity_A

อาจ map ไป:

entity_B

แต่ mapping ต้อง explicit

ไม่ควร assume:

same name = same entity

⸻

80. World Federation Boundary

Federated world:

World A
   ↕
Federation Protocol
   ↕
World B

ต้อง preserve:

Identity
Trust
Authority
Provenance
Scope
Version
Conflict

RFC-0046 จะกำหนด federation protocol โดยละเอียด

⸻

81. Agent Communication Failure

หาก A ส่ง request ไป B แล้ว connection หาย:

UNKNOWN

ห้าม assume:

B never executed

หรือ:

B definitely executed

ต้อง query/verify

⸻

82. Duplicate Agent Request

Request ต้องมี:

request_id
idempotency_key

เพื่อป้องกัน:

A → B
timeout
A retries
B receives twice

⸻

83. Agent Message Ordering

Messages ต้องมี:

message_id
sequence
causal_parent
timestamp

แต่ consumer ต้องไม่พึ่ง timestamp อย่างเดียว

⸻

84. Agent Context Version

Agent ต้องรู้:

context_version
world_version
policy_version
agent_version

ก่อน consequential action

⸻

85. Stale Context Rule

ถ้า:

World v100
Context v100

แต่ current:

World v110

agent ต้อง:

refresh

หรือ:

prove affected changes are irrelevant

ก่อนดำเนินการตาม policy

⸻

86. Agent Checkpoint

Long-running agent ต้อง checkpoint:

goal
plan
world_version
context
memory
authority leases
pending actions

เพื่อ recovery

⸻

87. Agent Resume

Resume flow:

Checkpoint
 ↓
Current World
 ↓
Reconcile
 ↓
Validate Plan
 ↓
Validate Authority
 ↓
Refresh Context
 ↓
Resume

ไม่ใช่:

load checkpoint
continue blindly

⸻

88. Multi-Agent Simulation

ก่อน complex coordination:

Agents
+
World
+
Plans

สามารถจำลอง:

Simulation

เพื่อดู:

deadlock
conflict
resource contention
goal conflict
failure propagation

⸻

89. Deadlock

Agents อาจรอกัน:

A waits B
B waits C
C waits A

ต้อง detect:

dependency cycle

และ:

break
replan
escalate

⸻

90. Agent Goal Drift

Agent อาจเริ่มด้วย:

Goal G1

แต่ภายหลัง optimize:

G2

โดยไม่มี authorization

ต้อง detect:

goal drift

ผ่าน Brain / Planner / Decision / Chronicle

⸻

91. Agent Collusion

สอง agent อาจร่วมกัน:

A approves B
B approves A

เพื่อ bypass human authority

ต้องตรวจ:

collusion
circular delegation
mutual endorsement

Trust Engine และ Authorization ต้องไม่ถือ mutual agreement เป็น authority

⸻

92. Agent Sybil

Attacker สร้าง:

Agent1
Agent2
Agent3
...
Agent100

แล้วสร้าง fake consensus

ดังนั้น:

number_of_agents

ไม่ใช่ trust evidence

Identity uniqueness และ provenance ต้องตรวจ

⸻

93. Shared World Poisoning

Malicious agent inject:

false entity
false relationship
false knowledge
false event

ต้องใช้:

Evidence
Verification
Trust
Conflict
Provenance

⸻

94. Security Boundary

Agent ไม่ควรสามารถ:

modify its own identity
modify its authority
modify trust
modify Chronicle
modify Constitution

โดยตรง

⸻

95. Agent Removal

เมื่อ agent ถูก revoke:

Revoke Identity
↓
Revoke Leases
↓
Disable Capabilities
↓
Stop Pending Actions
↓
Quarantine Context
↓
Preserve History

ต้องตรวจ external actions ที่อาจยัง pending

⸻

96. World Recovery

หาก shared world corruption:

Detect
 ↓
Freeze
 ↓
Identify last verified state
 ↓
Restore snapshot
 ↓
Replay verified deltas
 ↓
Verify
 ↓
Resume agents

⸻

97. World Consistency Levels

World consistency สามารถมี:

EVENTUAL
BOUNDED_STALENESS
STRONG
TRANSACTIONAL
VERIFIED

ไม่จำเป็นต้องใช้ strong consistency ทุก operation

⸻

98. Risk-Based Consistency

ตัวอย่าง:

UI preference
→ EVENTUAL
Memory update
→ EVENTUAL
Code analysis
→ BOUNDED_STALENESS
Production deployment
→ STRONG + VERIFIED
Financial transaction
→ TRANSACTIONAL + VERIFIED

Consistency level ต้องขึ้นกับ risk

⸻

99. World Authority Levels

World data สามารถมี:

OBSERVED
REPORTED
INFERRED
PROPOSED
VERIFIED
AUTHORITATIVE

Agent ต้องไม่ promote:

INFERRED
→
AUTHORITATIVE

เอง

⸻

100. Multi-Agent World State

Reference model:

world:
  world_id:
  version:
  entities:
  relationships:
  states:
  events:
  claims:
  evidence:
  agents:
    - agent_id:
      view_scope:
      status:
      world_version:
      context_version:
  governance:
  consistency:
  integrity:

⸻

101. Agent View Object

agent_view:
  view_id:
  agent_id:
  world_id:
  world_version:
  scope:
  included_entities:
  included_relationships:
  included_events:
  included_knowledge:
  included_memory_refs:
  omitted:
  redacted:
  unknown:
  generated_at:
  expires_at:
  provenance:

⸻

102. Agent Coordination Record

coordination:
  coordination_id:
  participants:
  coordinator:
  objective:
  strategy:
  shared_context:
  shared_world_version:
  tasks:
  dependencies:
  authority:
  trust:
  status:

⸻

103. Multi-Agent Lifecycle

DISCOVER
 ↓
IDENTIFY
 ↓
REGISTER
 ↓
ASSESS TRUST
 ↓
GRANT SCOPED AUTHORITY
 ↓
JOIN WORLD
 ↓
RECEIVE VIEW
 ↓
COORDINATE
 ↓
ACT
 ↓
VERIFY
 ↓
UPDATE WORLD
 ↓
LEARN

⸻

104. APIs

Core:

register_agent()
unregister_agent()
get_agent()
get_agent_view()
create_agent_view()
refresh_agent_view()
join_world()
leave_world()
request_context()
share_context()
request_delegation()
delegate_task()
create_coordination()
join_coordination()
leave_coordination()
submit_proposal()
submit_delta()
detect_conflicts()
resolve_conflict()
check_agent_health()
quarantine_agent()
restore_agent()
revoke_agent()
revoke_leases()
get_shared_world()
get_world_version()
reconcile_agent()
reconcile_world()
checkpoint_agent()
resume_agent()

⸻

105. Events

AgentDiscovered
AgentIdentified
AgentRegistered
AgentAttested
AgentJoinedWorld
AgentLeftWorld
AgentViewCreated
AgentViewUpdated
AgentViewRevoked
AgentTaskDelegated
AgentTaskAccepted
AgentTaskRejected
AgentTaskCompleted
AgentCoordinationCreated
AgentJoinedCoordination
AgentLeftCoordination
AgentProposalCreated
AgentDeltaSubmitted
AgentConflictDetected
AgentConflictResolved
AgentHealthChanged
AgentQuarantined
AgentRecovered
AgentAuthorityGranted
AgentAuthorityRevoked
AgentIdentityRevoked
WorldPartitionCreated
WorldBridgeCreated
WorldBridgeRevoked
AgentContextShared
AgentContextRevoked

⸻

106. Security Threats

MAW-SEC-01  Agent Impersonation
MAW-SEC-02  Sybil Agents
MAW-SEC-03  Agent Collusion
MAW-SEC-04  Context Leakage
MAW-SEC-05  Authority Delegation Abuse
MAW-SEC-06  Confused Deputy
MAW-SEC-07  Shared World Poisoning
MAW-SEC-08  Stale Agent Mutation
MAW-SEC-09  Fake Consensus
MAW-SEC-10  Identity Cloning
MAW-SEC-11  Capability Escalation
MAW-SEC-12  Goal Hijacking
MAW-SEC-13  Resource Starvation
MAW-SEC-14  Coordination Deadlock
MAW-SEC-15  Replay
MAW-SEC-16  Message Tampering
MAW-SEC-17  Cross-World Leakage
MAW-SEC-18  Private Memory Exfiltration
MAW-SEC-19  Trust Laundering
MAW-SEC-20  Governance Bypass

⸻

107. Invariants

MAW-1

ทุก agent ต้องมี identity

MAW-2

Agent identity ≠ authority

MAW-3

Agent trust ≠ authority

MAW-4

Agent capability ≠ permission

MAW-5

Agent view ≠ complete World

MAW-6

Unknown ≠ non-existent

MAW-7

Private agent state ≠ shared fact

MAW-8

Shared fact ต้องมี provenance

MAW-9

World mutation ต้องผ่าน WDP

MAW-10

Agent ไม่สามารถ bypass Authorization

MAW-11

Agent ไม่สามารถ grant authority ให้ตัวเอง

MAW-12

Delegated authority ต้องไม่เกิน parent authority

MAW-13

Delegated authority ต้องมี scope

MAW-14

Delegated authority ต้องมี lifecycle

MAW-15

Agent version change ต้องรองรับ trust reassessment

MAW-16

Stale agent ต้องไม่ mutate consequential state โดยไม่ revalidate

MAW-17

Agent communication ≠ world mutation

MAW-18

NCP context ≠ truth

MAW-19

Agent consensus ≠ truth

MAW-20

จำนวน agent ≠ evidence strength

MAW-21

Conflict ต้องไม่ถูกลบแบบ silent

MAW-22

Agent failure ต้องไม่ทำลาย world history

MAW-23

Agent revoke ต้อง revoke applicable leases

MAW-24

Agent quarantine ต้อง preserve evidence

MAW-25

Agent recovery ต้อง reconcile กับ current World

MAW-26

Agent clone ต้องมี identity ใหม่

MAW-27

Private context ต้องไม่รั่วข้าม agent โดย default

MAW-28

Cross-world access ต้อง explicit

MAW-29

High-risk coordination ต้องสามารถ trace accountability chain

MAW-30

Human authority และ Constitution อยู่เหนือ multi-agent consensus

⸻

108. Reference Architecture

                         HUMAN
                           │
                           ▼
                    VEDA CONSTITUTION
                           │
                           ▼
                    GOVERNANCE LAYER
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
        Shared World               Authority
              │                         │
       ┌──────┼──────┐                  │
       ▼      ▼      ▼                  │
    Agent A Agent B Agent C             │
       │      │      │                  │
       └──────┼──────┘                  │
              │                         │
          NCP Context                   │
              │                         │
              ▼                         │
        Coordination                   │
              │                         │
              ▼                         │
        WDP Delta ──────────────────────┘
              │
              ▼
        Conflict / Validation
              │
              ▼
           External
            World
              │
              ▼
         Verification
              │
              ▼
        Shared World v+
              │
              ▼
           Chronicle

⸻

109. Complete Multi-Agent Loop

Shared World
    ↓
Agent View
    ↓
Context
    ↓
Agent Cognition
    ↓
Proposal
    ↓
Coordination
    ↓
Decision
    ↓
Authorization
    ↓
WDP
    ↓
External Action
    ↓
Verification
    ↓
World Update
    ↓
Context Update
    ↓
Other Agents

⸻

110. Key Architectural Rule

Veda ไม่ควรออกแบบเป็น:

Agent A
Agent B
Agent C
   ↕
chat

แต่เป็น:

             Shared World
                  │
          ┌───────┼───────┐
          ▼       ▼       ▼
       Agent A Agent B Agent C
          │       │       │
          └───┬───┴───┬───┘
              │       │
             NCP     WDP
              │       │
              └───┬───┘
                  ▼
             Governance

Agent เป็น participants in a world

ไม่ใช่ chatbot หลายตัวที่เอามาคุยกันแล้วเรียกตัวเองว่า civilization

⸻

111. Relationship With RFC-0042

RFC-0042 NCP
→ Context Exchange
RFC-0043 WDP
→ World Change
RFC-0044 Multi-Agent World
→ Who participates in the World
  and how multiple agents coexist

ดังนั้นสามตัวนี้ประกอบเป็น:

Context
   +
World State
   +
Agents

⸻

112. Relationship With RFC-0045

RFC-0044 กำหนด:

Multiple Agents
+
Shared World

RFC-0045 จะกำหนดต่อว่า:

อะไรเป็น Shared?
อะไรเป็น Private?
อะไรเป็น Restricted?
ใครเห็นอะไร?
ข้อมูลไหลข้าม boundary อย่างไร?

ดังนั้น RFC-0045 จะเป็น Shared/Private World Boundary โดยตรง

⸻

113. Relationship With RFC-0046

RFC-0044:

Multi-Agent
inside a World

RFC-0046:

Multi-World
across independent Veda systems

ตัวอย่าง:

Veda A
   ↕
Federation
   ↕
Veda B

ซึ่งต้องเพิ่ม:

cross-domain identity
trust negotiation
federated authority
protocol compatibility

⸻

114. Final Principle

Many agents may participate in one World, but no agent owns Reality.

และกฎที่สำคัญที่สุด:

One Reality
Many Views
Scoped Knowledge
Scoped Authority
Explicit Coordination
Verified Mutation
Auditable History
Human Governance

Multi-Agent Veda จึงไม่ใช่การเพิ่มจำนวนสมองอย่างเดียว

แต่คือการสร้างระบบที่ หลายสมองสามารถทำงานบนโลกเดียวกันโดยไม่สร้างความจริงคนละชุดขึ้นมาเอง