RFC-0043 — World Delta Protocol (WDP)

Status: Architecture
Layer: 16 — Neural Protocol
Depends On: RFC-0001, RFC-0002, RFC-0003, RFC-0004, RFC-0008, RFC-0009, RFC-0010, RFC-0012, RFC-0013, RFC-0014, RFC-0021, RFC-0022, RFC-0026, RFC-0027, RFC-0029, RFC-0031, RFC-0032, RFC-0033, RFC-0039, RFC-0041, RFC-0042
Related: RFC-0044, RFC-0045, RFC-0046

⸻

1. Abstract

RFC-0043 กำหนด World Delta Protocol (WDP)

WDP คือ protocol สำหรับการอธิบาย:

"อะไรใน World เปลี่ยนไป"

โดยไม่จำเป็นต้องส่ง World ทั้งหมดใหม่

WDP ใช้สำหรับ:

World Model
Agent
Brain
Planner
Simulator
External World Interface
Memory
Knowledge
Multi-Agent System
Federation

เพื่อแลกเปลี่ยน:

State Changes
Entity Changes
Relationship Changes
Event Effects
Evidence Updates
Validity Changes
Conflict Information

Core principle:

World(t)
    +
Delta
    ↓
World(t+1)

แต่:

Delta ≠ Truth
Delta ≠ Authorization
Delta ≠ Verification
Delta ≠ Final World State

Delta เป็น ข้อเสนอหรือบันทึกการเปลี่ยนแปลง จนกว่าจะผ่าน validation, authorization และ verification ตามบริบท

⸻

2. Motivation

ถ้า Veda มี agent 10 ตัว และทุกตัวถือ World Model:

Agent A → World
Agent B → World
Agent C → World
...
Agent J → World

เมื่อ A เปลี่ยน:

server.status = degraded

ไม่ควรส่ง:

entire_world.json

ทุกครั้ง

ควรส่ง:

Delta:
server.status
healthy → degraded

แต่ปัญหาที่ยากกว่าคือ:

What if B changed the same state?
What if A is stale?
What if the change is unauthorized?
What if the change conflicts with reality?
What if the change depends on an old version?
What if two changes are both individually valid but incompatible?

WDP จึงเป็น protocol สำหรับ controlled state transition

⸻

3. Core Model

World W0
   │
   │ Delta D1
   ▼
Candidate W1
   │
   ├── Validate
   ├── Check Authority
   ├── Check Conflicts
   ├── Verify Preconditions
   └── Commit
        │
        ▼
World W1

ดังนั้น:

Delta
→ Validate
→ Reconcile
→ Authorize where required
→ Apply
→ Verify
→ Commit

⸻

4. Delta Definition

World Delta:

D = {Base, Operations, Preconditions, Provenance, Authority, Evidence}

โดย:

Base

บอกว่า delta อ้างอิง World version ไหน

Operations

บอกว่าต้องเปลี่ยนอะไร

Preconditions

บอกว่าการเปลี่ยนนี้ valid ภายใต้เงื่อนไขอะไร

Provenance

บอกว่าใครสร้าง

Authority

บอกว่ามีสิทธิ์เสนอ/ดำเนินการหรือไม่

Evidence

บอกว่าการเปลี่ยนมีหลักฐานอะไร

⸻

5. Delta Object

world_delta:
  delta_id:
  version:
  world_id:
  base_world_version:
  target_world_version:
  producer:
  source_agent:
  source_process:
  operation_type:
  operations:
  preconditions:
  postconditions:
  evidence_refs:
  verification_refs:
  intent_ref:
  goal_ref:
  action_ref:
  process_ref:
  authority_context:
  capability_ref:
  lease_ref:
  causal_context:
  temporal_context:
  conflict_policy:
  idempotency_key:
  created_at:
  valid_from:
  valid_until:
  integrity:
    hash:
    signature:
  status:

⸻

6. Delta Status

PROPOSED
VALIDATING
VALID
AUTHORIZED
APPLYING
APPLIED
VERIFYING
COMMITTED

Alternative:

REJECTED
CONFLICTED
STALE
INVALID
UNAUTHORIZED
PARTIALLY_APPLIED
UNKNOWN
ROLLED_BACK
SUPERSEDED
EXPIRED

⸻

7. Base World Version

ทุก delta ต้องระบุ:

base_world_version

ตัวอย่าง:

World v100
Agent A creates:
Delta A
base = v100

Agent B ก็สร้าง:

Delta B
base = v100

จากนั้น:

A → v101
B → v102?

ไม่ได้หมายความว่า B สามารถเขียนต่อจาก v101 ได้ทันที

ต้อง reconcile:

v100
├── Delta A
└── Delta B

⸻

8. World Lineage

World history เป็น DAG ได้:

             v100
            /    \
         D-A      D-B
          │        │
         v101     v102
            \     /
             Merge
               │
              v103

หรือ:

D-A
   \
    conflict
   /
D-B

⸻

9. Delta Operations

WDP รองรับ operation หลัก:

CREATE_ENTITY
UPDATE_ENTITY
DELETE_ENTITY
SET_STATE
UNSET_STATE
ADD_RELATIONSHIP
REMOVE_RELATIONSHIP
CREATE_EVENT
UPDATE_VALIDITY
ADD_EVIDENCE
REMOVE_EVIDENCE_REFERENCE
ADD_CLAIM
SUPERSEDE_CLAIM
INVALIDATE_CLAIM
CREATE_CONSTRAINT
REMOVE_CONSTRAINT

แต่ operation ที่ destructive ต้องมี policy เพิ่ม

⸻

10. Entity Delta

operation:
  type: UPDATE_ENTITY
  entity_id: server_01
  changes:
    status:
      from: healthy
      to: degraded

หาก from ไม่ตรง:

current = offline
expected = healthy

delta ต้องไม่ถูก apply เงียบๆ

⸻

11. Relationship Delta

operation:
  type: ADD_RELATIONSHIP
  subject: service_a
  predicate: depends_on
  object: database_b

Relationship ก็เป็นส่วนหนึ่งของ World

ดังนั้นต้องมี:

identity
provenance
validity
version

⸻

12. State Delta

operation:
  type: SET_STATE
  entity: server_01
  path: status
  expected_previous:
    value: healthy
  new_value:
    value: degraded

นี่คือ compare-and-set semantics

⸻

13. Preconditions

Delta สามารถกำหนด:

preconditions:
  - entity_exists: server_01
  - state_equals:
      entity: server_01
      path: status
      value: healthy

หาก precondition fail:

Delta ≠ automatically applicable

⸻

14. Postconditions

postconditions:
  - state_equals:
      entity: server_01
      path: status
      value: degraded

Postcondition จะถูกตรวจโดย Verification Engine

⸻

15. Atomic Delta

Delta สามารถเป็น:

ATOMIC

หมายถึง:

all operations apply

หรือ:

none apply

ตัวอย่าง:

Transfer:
Account A -100
Account B +100

ไม่ควรเกิด:

A -100
B unchanged

⸻

16. Composite Delta

Delta อาจมีหลาย operation:

D1:
  create entity
  add relationship
  update state
  create event

ระบบต้องกำหนด:

atomicity
ordering
dependency

⸻

17. Operation Dependency

ตัวอย่าง:

Create Entity
     ↓
Set State
     ↓
Add Relationship

ห้าม:

Add Relationship

ก่อน entity มีอยู่จริง

⸻

18. Delta Ordering

Operations สามารถมี:

sequence

เช่น:

operations:
  - sequence: 1
  - sequence: 2
  - sequence: 3

แต่ temporal ordering ต้องไม่ถูกตีความเป็น causal truth โดยอัตโนมัติ

⸻

19. Delta Causality

Delta สามารถอ้าง:

caused_by:
event_123

แต่ต้องผ่าน Causal Model

ดังนั้น:

Delta A happened after Event B

ไม่ได้แปลว่า:

Event B caused Delta A

⸻

20. Delta Evidence

Delta ที่มาจาก external observation ควรมี:

evidence_refs:
  - evidence_123
  - observation_456

เช่น:

External server
     ↓
Observation
     ↓
Evidence
     ↓
Delta

⸻

21. Delta vs Event

สำคัญ:

Event:
"What happened?"
Delta:
"What changed in our representation of the World?"

ตัวอย่าง:

External Event:
server crashed
Delta:
server.status = healthy → offline

หนึ่ง event อาจก่อหลาย delta

และหนึ่ง delta อาจอ้างหลาย evidence

⸻

22. Delta vs Action

Action:
"Do X."
Delta:
"World changed from A to B."

Action:

deploy(version=20)

Delta:

service.version:
19 → 20

Action สำเร็จไม่ได้แปลว่า delta ถูกต้อง

⸻

23. Delta vs Verification

Delta:

"server.status = online"

Verification:

external probe confirms online

ดังนั้น:

Delta ≠ Verified State

⸻

24. Delta Sources

Delta สามารถเกิดจาก:

External World Interface
Human
Agent
Tool
Sensor
Database
Webhook
Event Processor
Planner
Recovery Engine
Simulation
Federation
World Reconciliation

แต่ทุก source ต้องระบุ provenance

⸻

25. Simulation Delta

Simulation สามารถสร้าง delta ได้:

simulation_world
     ↓
Delta

แต่:

SIMULATED DELTA

ห้ามถูก apply เข้าสู่ real World โดยตรง

ต้องมี explicit transition:

Simulation
→ Proposal
→ Authorization
→ Real Action
→ Real Observation
→ Real Delta

⸻

26. Proposed Delta

Agent อาจเสนอ:

delta:
  status: PROPOSED

เช่น:

"server should be restarted"

นี่ไม่ใช่ world change

มันเป็น:

proposed transition

⸻

27. Applied Delta

หลัง apply:

status = APPLIED

แต่ยังไม่เท่ากับ:

VERIFIED

ต้องผ่าน:

Verification Engine

⸻

28. Commit Rule

World authoritative state ควร commit consequential delta เมื่อ:

Identity resolved
+
Authority satisfied
+
Preconditions satisfied
+
Conflict resolved
+
Application successful
+
Verification requirements satisfied

ตาม policy

⸻

29. Conflict Detection

Conflict มีหลายประเภท:

VERSION_CONFLICT
STATE_CONFLICT
ENTITY_CONFLICT
RELATIONSHIP_CONFLICT
TEMPORAL_CONFLICT
CAUSAL_CONFLICT
AUTHORITY_CONFLICT
POLICY_CONFLICT
RESOURCE_CONFLICT
SEMANTIC_CONFLICT
GOAL_CONFLICT

⸻

30. Version Conflict

ตัวอย่าง:

Current World = v105
Delta:
base_world = v100

Delta stale

แต่ไม่จำเป็นต้อง reject ทันที

ต้องดู:

Did intervening changes affect this delta?

⸻

31. Non-Overlapping Delta

v100
A changes:
server.status
B changes:
server.region

ถ้าไม่มี dependency:

A + B

อาจ merge ได้

⸻

32. Overlapping Delta

A:
server.status = degraded
B:
server.status = offline

ทั้งสองแก้ field เดียวกัน

ต้อง:

CONFLICT

ไม่ใช่:

last_write_wins

โดย default

⸻

33. Semantic Conflict

บางครั้ง field ต่างกันแต่ความหมายขัดกัน

A:
service.available = true
B:
service.outage = active

ไม่ใช่ same field

แต่ semantic conflict

ต้องใช้:

Knowledge
Conflict Engine
Causal Model

ช่วยประเมิน

⸻

34. Temporal Conflict

Delta A:
server down at 10:00
Delta B:
server healthy at 10:05

อาจไม่ conflict

เพราะ validity ต่างกัน

ดังนั้น temporal semantics ต้องมาก่อน conflict resolution

⸻

35. Authority Conflict

Agent A:

authorized for project A

แต่พยายาม:

change project B

Delta:

UNAUTHORIZED

ไม่ใช่ merge conflict

เป็น authority failure

⸻

36. Policy Conflict

Delta:

delete production database

แม้ agent มี capability:

database.delete

แต่ policy อาจ:

DENY

WDP ต้อง preserve:

authorization decision

และ reject delta

⸻

37. Conflict Resolution

กลยุทธ์:

REJECT
MERGE
REBASE
SPLIT
TEMPORAL_SEPARATION
ESCALATE
HUMAN_REVIEW
DEFER
REPLAN
ROLLBACK

⸻

38. Never Silent Resolution

ห้าม:

A says X
B says Y
system:
choose X
delete Y

โดยไม่สร้าง conflict record

ต้อง:

ConflictDetected
→ Resolution
→ New Delta / Resolution Record

⸻

39. Rebase

Delta stale:

D based on v100
current v105

ระบบสามารถ:

rebase D

โดย:

D(v100)
+
changes v100→v105

แต่ต้อง revalidate preconditions

⸻

40. Revalidation

หลัง rebase:

Original precondition:
balance >= 100

ถ้า world changed:

balance = 40

delta ต้อง:

BLOCK

ไม่ใช่ apply เพราะ “เมื่อกี้ยังใช้ได้”

มนุษย์สร้างระบบ distributed แล้วค่อยค้นพบว่าเวลาเดินไปข้างหน้า น่าประหลาดใจจริงๆ

⸻

41. Decision Conflict

Version change ไม่จำเป็นต้อง invalidate action

ตัวอย่าง:

User limit:
100

เปลี่ยนเป็น:

90

Action:

refund 80

ยัง valid

แต่ถ้าเปลี่ยนเป็น:

50

action invalid

ดังนั้น WDP ต้อง expose changed fields ให้ Decision/Authorization ตรวจ justification

⸻

42. Dependency-Aware Reconciliation

Delta reconciliation ต้องพิจารณา:

data dependency
control dependency
authorization dependency
goal dependency
verification dependency

ไม่ใช่แค่ field equality

⸻

43. World Merge

Merge function:

Merge(W, D1, D2)

ผลลัพธ์อาจเป็น:

MERGED
PARTIALLY_MERGED
CONFLICTED
REJECTED

⸻

44. Partial Merge

ตัวอย่าง:

D1:
status = degraded
region = asia
D2:
status = offline
owner = Veda

สามารถ merge:

region = asia
owner = Veda

แต่:

status

conflict

ดังนั้น result:

PARTIALLY_MERGED

⸻

45. Conflict Graph

Conflict สามารถสร้าง graph:

Delta A
   │
   ├── conflicts_with → Delta B
   │
   └── depends_on → Delta C

ทำให้รู้ blast radius

⸻

46. Delta Impact

Delta ต้องสามารถคำนวณ:

affected_entities
affected_relationships
affected_goals
affected_plans
affected_actions
affected_verifications
affected_contexts

⸻

47. World Invalidation

ถ้า:

Delta A:
server status changed

context ที่สร้างจาก:

server status = healthy

อาจต้อง:

STALE

WDP ต้องแจ้ง downstream systems

⸻

48. Context Integration

World Delta
   ↓
Affected Context
   ↓
NCP Context Update

ดังนั้น RFC-0042 และ RFC-0043 เชื่อมกันโดยตรง:

WDP = world state change
NCP = cognitive context transport

⸻

49. Memory Integration

ถ้า delta เป็น consequential:

World Delta
   ↓
Event
   ↓
Experience
   ↓
Memory

แต่ memory ต้อง preserve:

source_delta
verification

⸻

50. Knowledge Integration

ถ้า delta เปลี่ยนสถานะของ claim:

Knowledge K1

อาจกลายเป็น:

STALE
SUPERSEDED
DISPUTED

ผ่าน Knowledge Model

ไม่ใช่แก้ claim เดิมเงียบๆ

⸻

51. Chronicle Integration

ทุก consequential delta ต้องสร้าง Chronicle trace:

DeltaCreated
DeltaValidated
DeltaAuthorized
DeltaApplied
DeltaVerified
DeltaCommitted

หรือ failure equivalent

⸻

52. Idempotency

ทุก delta ที่มี side effect ต้องมี:

idempotency_key

ถ้าส่งซ้ำ:

D123
D123
D123

ระบบต้องไม่ทำ:

apply
apply
apply

โดยไม่ตั้งใจ

⸻

53. Duplicate Detection

ตรวจ:

delta_id
idempotency_key
base_version
producer
sequence
content_hash

⸻

54. Ordering

WDP รองรับ:

logical_clock
sequence_number
causal_parent

แต่ไม่ assume distributed clocks perfectly synchronized

⸻

55. Causal Ordering

ถ้า:

D2 depends_on D1

ต้อง:

D1 before D2

แต่:

D2 timestamp > D1

เพียงอย่างเดียวไม่พิสูจน์ dependency

⸻

56. Concurrent Delta

สอง agent สามารถสร้าง:

D1
D2

พร้อมกัน

ระบบต้องรองรับ:

concurrent = true

และไม่บังคับ artificial ordering หากไม่มี causal relationship

⸻

57. Logical Clock

WDP สามารถใช้:

Lamport clock
Vector clock
Hybrid logical clock

ตาม implementation

Protocol ต้อง preserve causal metadata แต่ไม่จำเป็นต้องบังคับ algorithm เดียวใน architecture stage

⸻

58. Vector Clock

สำหรับ federation:

clock:
  agent_a: 42
  agent_b: 17
  agent_c: 9

สามารถช่วยระบุ:

before
after
concurrent

⸻

59. Content Addressing

Delta สามารถมี:

content_hash

ทำให้:

same delta

สามารถตรวจ duplicate/integrity ได้

⸻

60. Delta Signature

integrity:
  hash:
  signature:
  key_ref:

Signature ระบุ:

who issued delta

ไม่ได้พิสูจน์:

delta is true

Verification ยังจำเป็น

⸻

61. Delta Authority

Delta authority context:

authority:
  actor:
  capability:
  lease:
  scope:
  policy_version:
  authorization_ref:

หากไม่มี authority:

UNAUTHORIZED

⸻

62. Capability Lease

สำหรับ temporary mutation:

lease:
  capability: world.write
  scope: project.veda
  expires_at:
  operation_limit:

Delta ต้องอ้าง lease ถ้ามี

⸻

63. Human Delta

มนุษย์ก็สามารถสร้าง delta:

User:
"Set project status to paused."

แต่ system ต้อง resolve:

Identity
Intent
Authority
Scope

ก่อน commit

⸻

64. External Delta

External world:

GitHub
Database
Filesystem
OS
Cloud
Phone
Sensor

ส่ง observation

จากนั้น:

Observation
→ Evidence
→ Delta Candidate

ไม่ควร:

External Event
→ Direct authoritative mutation

โดยไม่มี verification policy

⸻

65. Reconciliation

WDP ต้องมี reconciliation loop:

Local World
     ↕
External World
     ↓
Observations
     ↓
Delta Candidates
     ↓
Conflict Detection
     ↓
Verification
     ↓
World Commit

⸻

66. Unknown State

ถ้า external connection หาย:

Request sent
Connection lost

ห้ามสร้าง:

success delta

ต้อง:

UNKNOWN

แล้ว resolve external state

⸻

67. Tombstones

เมื่อ entity ถูกลบ:

DELETE_ENTITY

ควรเก็บ tombstone:

tombstone:
  entity_id:
  deleted_at:
  delta_ref:
  authorization_ref:
  verification_ref:

เพื่อป้องกัน stale agent resurrect entity โดยไม่รู้ตัว

⸻

68. Resurrection

หาก entity ถูกสร้างกลับ:

new identity/version

ไม่ควรตีความว่าเป็น entity เดิมเสมอไป

ต้องมี:

recreated_from

หรือ explicit identity continuity

⸻

69. World Snapshot

WDP สามารถอ้าง snapshot:

world_snapshot_ref

Delta ถูก apply:

Snapshot v500
+
D501...D550

เพื่อ reconstruct state

⸻

70. Delta Compaction

สามารถ compact:

D1
D2
D3
D4

เป็น:

Snapshot + Compact Delta

แต่ Chronicle ต้องรักษา original history ตาม retention policy

ดังนั้น:

Compaction ≠ historical deletion

⸻

71. Rollback

Rollback ต้องสร้าง delta ใหม่:

D1:
status healthy → degraded
Rollback:
D2:
status degraded → healthy

ไม่ควร:

delete D1

เพราะจะทำลาย history

⸻

72. Compensation

บาง external action rollback ไม่ได้

เช่น:

send_money
send_email
delete_external_data

อาจต้อง:

compensating delta

แทน rollback

⸻

73. Verification

หลัง commit:

Delta
 ↓
Expected State
 ↓
External Observation
 ↓
Evidence
 ↓
Verification

ถ้า fail:

VerificationFailed

→ RFC-0027 Recovery

⸻

74. Delta Lifecycle

PROPOSED
   ↓
VALIDATING
   ↓
VALID
   ↓
AUTHORIZED
   ↓
APPLYING
   ↓
APPLIED
   ↓
VERIFYING
   ↓
COMMITTED

Failure:

STALE
CONFLICTED
REJECTED
UNAUTHORIZED
INVALID
PARTIAL
UNKNOWN
ROLLED_BACK

⸻

75. API

Core APIs:

create_delta()
validate_delta()
get_delta()
check_preconditions()
check_authority()
check_conflicts()
apply_delta()
apply_atomic_delta()
rebase_delta()
merge_deltas()
split_delta()
compare_world_versions()
get_delta_lineage()
detect_conflicts()
resolve_conflict()
verify_delta()
commit_delta()
rollback_delta()
compensate_delta()
invalidate_delta()
supersede_delta()
get_affected_entities()
get_impact_graph()
create_snapshot()
reconstruct_world()

⸻

76. Delta Validation Pipeline

Delta Received
      ↓
Identity Validation
      ↓
Schema Validation
      ↓
Base Version Check
      ↓
Integrity Check
      ↓
Provenance Check
      ↓
Precondition Check
      ↓
Authority Check
      ↓
Conflict Detection
      ↓
Semantic Validation
      ↓
Apply
      ↓
Postcondition Check
      ↓
Verification
      ↓
Commit

⸻

77. Conflict Resolution Pipeline

Conflict Detected
      ↓
Classify
      ↓
Determine Scope
      ↓
Determine Dependencies
      ↓
Assess Impact
      ↓
Generate Resolution Candidates
      ↓
Policy Check
      ↓
Simulation if needed
      ↓
Authorization if needed
      ↓
Resolve / Reject / Escalate
      ↓
Verify

⸻

78. Multi-Agent Example

World:

v100

Agent A:

D-A
server.status = degraded

Agent B:

D-B
server.status = offline

ทั้งคู่ base จาก:

v100

WDP:

D-A
     \
      CONFLICT
     /
D-B

จากนั้น Conflict Engine ตรวจ evidence:

A:
health probe at 10:00
B:
health probe at 10:01

Temporal Model อาจพบว่า:

degraded at 10:00
offline at 10:01

จึงอาจไม่ใช่ contradiction

สามารถ reconstruct:

healthy
  ↓
degraded
  ↓
offline

นี่คือเหตุผลที่ WDP ต้องทำงานร่วมกับ Temporal + Evidence + Conflict Model

⸻

79. Multi-Agent Example: True Conflict

A:

balance = 1000

B:

balance = 800

ทั้งคู่:

valid_at = same time
same account
same scope

แต่ evidence ต่างกัน

ผล:

CONFLICT

WDP ห้ามเลือก:

1000

หรือ:

800

เอง

ต้องส่ง RFC-0014

⸻

80. Delta and Planner

Planner สามารถใช้ WDP เพื่อ:

observe world change
invalidate affected plan steps
revalidate assumptions
replan

ดังนั้น:

World Change
 ↓
Delta
 ↓
Plan Impact
 ↓
Revalidation
 ↓
Continue / Replan / Abort

⸻

81. Delta and Attention

Critical delta:

production database failure

สามารถสร้าง:

AttentionCandidate

ผ่าน RFC-0019

แต่ WDP ไม่ตัดสิน priority เอง

⸻

82. Delta and Future Engine

World change:

interest rate changed

อาจ invalidate scenarios

Scenario S1
Scenario S2
Scenario S3

WDP แจ้ง:

affected assumptions

Future Engine recalculates

⸻

83. Delta and Simulation

Simulation ใช้:

Base World
+
Candidate Delta

แล้วสร้าง:

Simulated World

ก่อน real commit

⸻

84. Delta and Trust

Trust ของ source agent อาจเป็น factor ใน:

delta acceptance

แต่:

Trust ≠ Authority

และ:

High Trust
+
No Authority
=
Reject

⸻

85. Delta and Identity

ทุก delta ต้องรู้:

WHO produced it

ผ่าน RFC-0039 / RFC-0040

แต่ identity ไม่ได้บอก:

WHO IS ALLOWED

Authorization แยกต่างหาก

⸻

86. Delta and Chronicle

Chronicle records:

DeltaCreated
DeltaValidated
DeltaAuthorized
DeltaConflictDetected
DeltaApplied
DeltaVerificationStarted
DeltaVerified
DeltaCommitted
DeltaRejected
DeltaRolledBack

ทำให้สามารถ:

reconstruct world

ย้อนหลังได้

⸻

87. Security Threats

WDP ต้องป้องกัน:

DELTA-SEC-01  Forged Delta
DELTA-SEC-02  Replay
DELTA-SEC-03  Duplicate Mutation
DELTA-SEC-04  Stale Delta
DELTA-SEC-05  Unauthorized Mutation
DELTA-SEC-06  Version Confusion
DELTA-SEC-07  Conflict Suppression
DELTA-SEC-08  Provenance Forgery
DELTA-SEC-09  Timestamp Manipulation
DELTA-SEC-10  Causal Spoofing
DELTA-SEC-11  Semantic Poisoning
DELTA-SEC-12  Tombstone Bypass
DELTA-SEC-13  Rollback Abuse
DELTA-SEC-14  Cross-Agent Injection
DELTA-SEC-15  Delta Flooding

⸻

88. Important Security Rule

ไม่มี delta ใดสามารถเขียน:

authority = granted

ด้วยตัวเอง

เช่น:

operation:
  type: GRANT_AUTHORITY

ต้องผ่าน Authorization/Policy architecture

และในระดับ critical governance ต้องมี human authority ตาม Constitution

⸻

89. Important Truth Rule

ไม่มี delta ใดสามารถประกาศ:

verified = true

เพียงเพราะ producer เขียน field นี้

Verification record ต้องมาจาก:

Verification Engine

⸻

90. Important History Rule

ห้าม:

delete delta

เพื่อปกปิด:

failure
conflict
unauthorized mutation
rollback

Correction ต้องเป็น:

new record

⸻

91. WDP Invariants

WDP-1

ทุก delta ต้องมี identity

WDP-2

ทุก delta ต้องมี base world version

WDP-3

ทุก delta ต้องมี provenance

WDP-4

ทุก consequential delta ต้องมี integrity protection

WDP-5

Delta ไม่เท่ากับ truth

WDP-6

Delta ไม่เท่ากับ verification

WDP-7

Delta ไม่สามารถ grant authority

WDP-8

Delta ไม่สามารถ bypass policy

WDP-9

Delta ต้องระบุ producer

WDP-10

Delta ต้องระบุ scope

WDP-11

Delta ต้องรองรับ temporal validity

WDP-12

Delta ต้องรองรับ preconditions

WDP-13

Delta ต้องรองรับ postconditions

WDP-14

Stale delta ต้องถูกตรวจสอบก่อน apply

WDP-15

Overlapping delta ต้องตรวจ conflict

WDP-16

Non-overlapping delta สามารถ merge ได้เมื่อ dependency ไม่ขัดกัน

WDP-17

Conflict ห้ามถูก resolve แบบ silent

WDP-18

Conflict resolution ต้องสร้าง provenance

WDP-19

Rebase ต้อง revalidate

WDP-20

Simulation delta ห้าม mutate real world โดยตรง

WDP-21

External unknown state ต้องคงสถานะ UNKNOWN

WDP-22

Idempotency ต้องป้องกัน duplicate side effects

WDP-23

Atomic delta ต้อง all-or-none ตาม contract

WDP-24

Partial application ต้องถูกเปิดเผย

WDP-25

Rollback ต้องสร้าง history ใหม่

WDP-26

Compensation ต้องไม่ถูกเรียกว่า rollback หากย้อนผลไม่ได้จริง

WDP-27

Tombstone ต้องป้องกัน stale resurrection

WDP-28

Delta ต้องสามารถ trace กลับไปยัง source

WDP-29

Consequential delta ต้องสามารถตรวจ impact

WDP-30

World history ห้ามถูกเปลี่ยนแบบ silent เพื่อปกปิด failure หรือ unauthorized mutation

⸻

92. Reference Architecture

                 EXTERNAL WORLD
                       │
                       ▼
              External Interface
                       │
                       ▼
                  Observation
                       │
                       ▼
                    Evidence
                       │
                       ▼
                Delta Candidate
                       │
                       ▼
              ┌────────────────┐
              │      WDP       │
              └───────┬────────┘
                      │
          ┌───────────┼────────────┐
          ▼           ▼            ▼
      Validation    Conflict    Authority
          │           │            │
          └───────────┼────────────┘
                      ▼
                    Apply
                      │
                      ▼
                  Verification
                      │
             ┌────────┴────────┐
             ▼                 ▼
          Commit             Reject
             │
             ▼
          World v+1
             │
      ┌──────┼────────┐
      ▼      ▼        ▼
   NCP      Brain   Chronicle

⸻

93. Relationship With RFC-0042

RFC-0042 NCP
=
"What context should cognition receive?"
RFC-0043 WDP
=
"What changed in the World?"

เชื่อมกัน:

World
  │
  ├── WDP → Change
  │
  └── NCP → Cognitive Context

หรือ:

World(t)
   ↓
Delta
   ↓
World(t+1)
   ↓
Context Update
   ↓
Cognition

⸻

94. Relationship With MCP

MCP ใน specification ปัจจุบันยังเป็น protocol สำหรับ context/tool/resource integration และ version 2026-07-28 มีการแยก protocol core, versioning, authorization และ server features อย่างชัดเจน

ดังนั้น Veda architecture ควรรักษา separation:

MCP
  ↓
Tools / Resources / External Integration
NCP
  ↓
Cognitive Context
WDP
  ↓
World State Change

ไม่ควรยัดทั้งสามหน้าที่รวมกัน

⸻

95. Complete World Transition

หลัง RFC-0043 Veda มี transition model ที่ชัดขึ้น:

Reality
   ↓
Observation
   ↓
Evidence
   ↓
Delta Candidate
   ↓
Validation
   ↓
Conflict / Authority Check
   ↓
Apply
   ↓
Verification
   ↓
Committed World
   ↓
NCP Context Update
   ↓
Cognition

และถ้าเป็น Veda ที่ต้องการ “อยู่กับโลกจริง” นี่เป็นแกนสำคัญมาก เพราะ Veda ไม่ควรคิดว่า world เปลี่ยนเพราะมันเขียนตัวแปรใน database สำเร็จ

World เปลี่ยนเมื่อการเปลี่ยนแปลงได้รับการยืนยันตาม reality boundary ที่เหมาะสม

⸻

96. Final Principle

A Delta describes a transition.
It does not prove the transition is true.
A Delta may propose change.
It does not grant authority.
A Delta may be applied.
It does not imply successful outcome.
A Delta may be verified.
It does not erase the history of how it happened.
World state is authoritative only
at the boundary where reality has been established
according to the system's verification policy.

หรือสั้นที่สุด:

WDP ทำให้ Veda เปลี่ยนโลกอย่างมีร่องรอย ไม่ใช่แค่เขียนทับโลกแล้วหวังว่า log จะจำได้ว่าเกิดอะไรขึ้น