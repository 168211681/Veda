RFC-0031 — Event / Audit / Trace Fabric

Status: Draft
Layer: Layer 12 — Audit
Depends On: RFC-0001 through RFC-0030
Related: RFC-0003 Event Model, RFC-0004 State & World Transition, RFC-0008 Action Model, RFC-0010 Authorization & Policy, RFC-0026 Verification, RFC-0027 Rollback & Recovery, RFC-0028 Tool Registry, RFC-0029 External World Interface, RFC-0030 MCP Integration

⸻

1. Abstract

RFC-0031 กำหนดระบบ Event / Audit / Trace Fabric ของ Veda

ระบบนี้มีหน้าที่ทำให้ Veda สามารถตอบได้อย่างเป็นระบบว่า:

เกิดอะไรขึ้น?
เกิดเมื่อไร?
เกิดที่ไหน?
ใครเป็นผู้กระทำ?
ใครเป็นผู้ร้องขอ?
ใครอนุญาต?
ทำไปเพราะอะไร?
ใช้ capability อะไร?
ใช้ tool อะไร?
ข้อมูลอะไรถูกใช้?
ผลลัพธ์คืออะไร?
เกิด external effect หรือไม่?
ตรวจสอบอย่างไร?
World Model เปลี่ยนอย่างไร?
ถ้าผิดพลาด แก้อย่างไร?

RFC นี้กำหนดความแตกต่างระหว่าง:

Event
Log
Audit Record
Trace
Span
Evidence
World Event
Decision Record
Verification Record

และกำหนดวิธีเชื่อมโยงทั้งหมดเข้าด้วยกัน

หลักการสำคัญ:

If Veda cannot reconstruct why and how something happened, Veda does not truly have operational accountability.

⸻

2. Motivation

ระบบ AI ที่สามารถ:

* อ่านไฟล์
* เขียนไฟล์
* รัน command
* ใช้ internet
* ส่งข้อความ
* deploy software
* แก้ code
* เรียก external API
* ควบคุมอุปกรณ์

ต้องมี auditability

มิฉะนั้นเหตุการณ์:

User asks something
 ↓
Brain reasons
 ↓
Tool executes
 ↓
Something changes

อาจเกิดขึ้นโดยไม่มีคำตอบว่า:

Why?
Who authorized?
Which policy?
Which capability?
Which exact input?
Which external system?
What actually happened?

RFC-0031 จึงสร้าง accountability fabric ของ Veda

⸻

3. Design Principle

Everything consequential must be traceable.

แต่:

Traceability ≠ Store Everything Forever

Veda ต้องรักษาสมดุลระหว่าง:

* Auditability
* Privacy
* Security
* Storage
* Performance
* Retention
* Data minimization

⸻

4. Core Distinctions

4.1 Event

สิ่งที่เกิดขึ้น ณ จุดหนึ่งในเวลา

Event = Occurrence

ตัวอย่าง:

ToolCalled
AuthorizationGranted
FileModified
VerificationCompleted

⸻

4.2 Log

ข้อความหรือ record ที่ใช้ operational diagnosis

Log = Operational Record

ไม่จำเป็นต้องมี semantic meaning เท่ากับ domain event

⸻

4.3 Audit Record

Record ที่สร้างขึ้นเพื่อ accountability

NIST ใช้คำว่า audit record สำหรับ entry แต่ละรายการใน audit log ที่เกี่ยวข้องกับ audited event (NIST Computer Security Resource Center)

Veda:

AuditRecord = Accountability Record

⸻

4.4 Trace

ชุดของ operations/events ที่เชื่อมโยงกันเป็น execution lineage

ตัวอย่าง:

User Request
 ↓
Intent
 ↓
Goal
 ↓
Plan
 ↓
Decision
 ↓
Authorization
 ↓
Action
 ↓
Tool
 ↓
External Effect
 ↓
Verification

ทั้งหมดมี:

trace_id

ร่วมกัน

⸻

4.5 Span

ช่วงเวลาของ operation หนึ่งรายการ

เช่น:

planner.generate_plan

มี:

start_time
end_time
status
attributes
parent_span

OpenTelemetry ใช้ spans เป็นตัวแทนของ operations ที่เกิดขึ้นในและระหว่างระบบ (OpenTelemetry)

Veda สามารถใช้ span เป็น telemetry representation

แต่ Veda domain semantics ต้องอยู่ใน Event/Audit model ของตัวเอง

⸻

5. Architectural Position

                    VEDA
                      │
       ┌──────────────┼──────────────┐
       │              │              │
       ▼              ▼              ▼
    Brain          Planner        Tools
       │              │              │
       └──────────────┼──────────────┘
                      │
                      ▼
             Event / Trace Fabric
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       Audit        Trace        Metrics
          │           │           │
          └───────────┼───────────┘
                      ▼
               Chronicle
                 RFC-0032

RFC-0031 คือ fabric

RFC-0032 จะเป็น durable historical chronicle

⸻

6. Event vs Chronicle

ต้องไม่สับสน:

Event Fabric
=
สร้าง / ส่ง / เชื่อม / ประมวลผล events
Chronicle
=
เก็บ historical record

เปรียบเทียบ:

Event Fabric
    ↓
"เกิดอะไรขึ้น"
Chronicle
    ↓
"ประวัติทั้งหมดที่เกิดขึ้น"

⸻

7. Event Object

Event {
    event_id
    event_type
    event_version
    trace_id
    span_id
    parent_event_id
    actor_id
    subject_id
    target_id
    source
    origin
    event_time
    observed_time
    received_time
    world_version
    process_id
    goal_id
    intent_id
    action_id
    authorization_ref
    capability_ref
    lease_ref
    payload
    payload_schema
    outcome
    severity
    evidence_refs
    verification_refs
    provenance
    sensitivity
    retention_policy
    integrity
}

⸻

8. Event Identity

ทุก event ต้องมี unique identity:

event_id

Event ID ต้องไม่ถูก reuse

ตัวอย่าง:

evt_01J...

Event identity ต้องรองรับ:

* deduplication
* replay
* audit
* correlation
* idempotency

⸻

9. Event Version

Event schema ต้อง versioned:

event_type:
    ActionExecuted
event_version:
    2

Consumer ต้องสามารถรองรับ version migration

ห้ามแก้ semantic ของ event เดิมแบบเงียบ ๆ

⸻

10. Event Time

Event ต้องแยก:

event_time
observed_time
received_time
processed_time

ตัวอย่าง:

External event happened:
10:00
Veda observed:
10:00:04
Veda received:
10:00:05
Veda processed:
10:00:06

นี่สอดคล้องกับแนวคิด temporal separation ที่กำหนดไว้ใน RFC-0021

⸻

11. Clock Integrity

Timestamp ต้องระบุ:

clock_source
clock_quality
timezone
utc_time
monotonic_time
clock_offset

หาก clock มี anomaly:

ClockAnomalyDetected

ต้องถูกบันทึก

NIST audit guidance เน้น timestamp ที่สามารถตรวจสอบกับแหล่งเวลาที่เชื่อถือได้และการตรวจจับความคลาดเคลื่อนของเวลา (NIST Pages)

⸻

12. Event Sources

Events สามารถมาจาก:

Human
Veda Core
Brain
Planner
Decision Engine
Authorization Engine
Tool
MCP Server
External System
Sensor
Network
Database
Filesystem
Scheduler
Other Agent

ทุก event ต้องระบุ source

⸻

13. Event Origin

origin:
    INTERNAL
    EXTERNAL
    HUMAN
    DERIVED
    SYNTHETIC
    REPLAY
    SIMULATION

สำคัญมาก:

SIMULATION EVENT
≠
REAL EVENT

⸻

14. Event Classes

แบ่ง event เป็น:

LIFECYCLE
STATE
ACTION
AUTHORIZATION
SECURITY
DATA
WORLD
VERIFICATION
ERROR
RECOVERY
DECISION
LEARNING
SYSTEM
HUMAN
EXTERNAL

⸻

15. Domain Events

Domain event มี semantic meaning

ตัวอย่าง:

GoalCreated
PlanCreated
ActionAuthorized
ActionExecuted
FileModified
PaymentSubmitted
VerificationCompleted
WorldStateUpdated

⸻

16. Telemetry Events

Telemetry event ใช้สำหรับ observability

เช่น:

CPUHigh
MemoryPressure
LatencySpike
ModelTimeout
QueueDepthChanged

ไม่ใช่ทุก telemetry event จะเป็น World Event

⸻

17. Security Events

Security event ต้องมี priority สูง

ตัวอย่าง:

UnauthorizedActionAttempt
CapabilityEscalationAttempt
CredentialAccess
PromptInjectionDetected
ToolPoisoningDetected
SandboxEscapeAttempt
PolicyViolation
AuditTamperingDetected

Security event ต้องไม่ถูกลดความสำคัญเพียงเพราะ telemetry volume สูง

⸻

18. Trace Model

Trace:

Trace {
    trace_id
    root_operation
    parent_trace_id
    actor
    intent
    goal
    process
    start_time
    end_time
    status
    outcome
    spans
    events
    evidence
    verification
}

⸻

19. Trace Tree

ตัวอย่าง:

Trace: user_request_001
├── intent.resolve
├── goal.create
├── planning
│   ├── retrieve.memory
│   ├── retrieve.knowledge
│   └── simulate
├── decision
├── authorization
├── action
│   └── mcp.invoke
│       └── external.api
├── verification
│   └── external.read
└── world.update

ทำให้สามารถ reconstruct causal execution chain

⸻

20. Trace Context Propagation

ทุก subsystem ต้องสามารถรับ:

trace_id
span_id
parent_span_id

ตัวอย่าง:

Brain
 ↓
Planner
 ↓
Action
 ↓
MCP
 ↓
External API

Trace context ต้อง propagate

แต่ต้องไม่ leak sensitive context

⸻

21. Correlation IDs

นอกจาก trace ID อาจมี:

request_id
intent_id
goal_id
plan_id
process_id
action_id
transaction_id
verification_id
recovery_id

ทุก ID ต้องมี semantic distinction

ห้ามใช้ ID เดียวแทนทุกอย่าง

⸻

22. Causality

Event chain ต้องรองรับ causal links:

event_A
   ↓ causes
event_B
   ↓ causes
event_C

ตัวอย่าง:

UserIntentCreated
        ↓
PlanCreated
        ↓
ActionAuthorized
        ↓
ActionExecuted
        ↓
ExternalStateChanged
        ↓
VerificationCompleted

Trace เป็น execution lineage

Causal relation ต้องยังอยู่ภายใต้ RFC-0022

⸻

23. Event Ordering

Events ต้องรองรับ:

sequence_number
logical_clock
causal_parent
event_time

เพราะ distributed systems สามารถส่ง event out-of-order

ดังนั้น:

timestamp order
≠
causal order

⸻

24. Late Events

หาก event มาถึงช้า:

EventLate

ต้องไม่แก้ history แบบ destructive

ให้เพิ่ม correction/reconciliation event

⸻

25. Duplicate Events

Events อาจถูกส่งซ้ำ

ต้องรองรับ:

event_id
idempotency_key
source_sequence

Duplicate:

DEDUPLICATED

ไม่ควรสร้าง semantic event ซ้ำ

⸻

26. Event Integrity

ทุก event ต้องมี integrity metadata

integrity {
    hash
    previous_hash
    signature
    signer
    algorithm
}

ระดับความเข้มขึ้นกับ risk

⸻

27. Hash Chain

สำหรับ critical audit stream:

Event1
  hash1
    ↓
Event2
  hash2 = H(hash1 + event2)
    ↓
Event3
  hash3 = H(hash2 + event3)

ถ้า event ถูกแก้:

chain verification fails

⸻

28. Merkle Aggregation

สำหรับ event จำนวนมาก สามารถใช้:

Events
 ↓
Merkle Tree
 ↓
Root Hash
 ↓
Signed Checkpoint

ช่วยลดค่าใช้จ่ายในการ integrity verification

⸻

29. Digital Signatures

High-value events อาจต้อง sign:

Authorization
Human Approval
Financial Action
Security Decision
Policy Change
Evolution Deployment
Constitution Change Proposal

Signature ต้องผูกกับ:

event_id
payload_hash
identity
timestamp
scope

⸻

30. Audit Levels

กำหนดระดับ:

A0 Diagnostic
A1 Operational
A2 Accountability
A3 Security
A4 Critical
A5 Constitutional

ตัวอย่าง:

UI animation:
A0
File write:
A2
Credential access:
A3
Financial transfer:
A4
Constitution change:
A5

⸻

31. Audit Requirements by Risk

Risk สูงขึ้น:

Risk ↑
 ↓
Audit Depth ↑
 ↓
Evidence ↑
 ↓
Integrity Protection ↑
 ↓
Retention ↑

ไม่จำเป็นต้องเก็บทุกอย่างด้วยระดับ A5 เพราะ storage ก็ไม่ได้งอกบนต้นไม้

⸻

32. Sensitive Data

Audit ต้องไม่กลายเป็น data exfiltration vector

ห้ามเก็บ secret ตรง ๆ:

password
API key
private key
session token
OAuth token
raw credential

แทนด้วย:

secret_ref
credential_id
redacted_value
hash

⸻

33. Payload Redaction

Payload สามารถ:

FULL
PARTIAL
REDACTED
HASH_ONLY
REFERENCE_ONLY

ตาม policy

⸻

34. Privacy Boundary

Audit access ต้องมี authorization

User
 ↓
Audit Query
 ↓
Policy
 ↓
Sensitivity Check
 ↓
Redaction
 ↓
Result

ไม่ใช่:

Admin
 ↓
Everything

แม้จะเป็นเจ้าของระบบก็ตาม

⸻

35. Immutable Core

Critical audit records ต้องเป็น:

APPEND_ONLY

แก้ไขไม่ได้

หากข้อมูลผิด:

CorrectionEvent

ไม่ใช่:

UPDATE original event

⸻

36. Event Correction

ตัวอย่าง:

Event:
PaymentCompleted
ภายหลังพบว่า:
PaymentFailed

ห้ามลบ event แรก

สร้าง:

EventCorrectionCreated

พร้อม evidence

History:

PaymentCompleted
    ↓
Correction
    ↓
PaymentFailed

⸻

37. Audit Query

API:

query_events()
query_trace()
query_audit()
query_by_actor()
query_by_action()
query_by_goal()
query_by_time()
query_by_world_version()
query_security_events()
query_failed_actions()
query_unverified_actions()

⸻

38. Causal Reconstruction

Veda ต้องตอบ:

Why did this happen?

ผ่าน:

trace
intent
goal
plan
decision
authorization
action
external effect
verification

⸻

39. Decision Trace

Decision ต้องเก็บ:

candidates
constraints
values
risks
evidence
utility
tradeoffs
selected_candidate
confidence
decision_reason

ไม่จำเป็นต้องเก็บ chain-of-thought ภายในของ model

สำคัญ:

Decision Trace
≠
Raw Model Chain-of-Thought

Veda ต้องเก็บ decision-relevant provenance ไม่ใช่เปิดหรือเก็บ private reasoning token-by-token โดยไม่มีเหตุผล

⸻

40. Authorization Trace

ทุก authorization decision ต้อง trace:

request
actor
capability
scope
policy
constraints
risk
approval
lease
decision

ตัวอย่าง:

ActionRequested
 ↓
PolicyEvaluated
 ↓
CapabilityChecked
 ↓
RiskEvaluated
 ↓
HumanApproval
 ↓
AuthorizationGranted

⸻

41. Action Trace

ActionProposed
 ↓
ActionAuthorized
 ↓
PreconditionsChecked
 ↓
ActionDispatched
 ↓
ExecutionStarted
 ↓
ExecutionCompleted
 ↓
EffectObserved
 ↓
OutcomeVerified

⸻

42. Verification Trace

VerificationRequested
 ↓
ExpectationResolved
 ↓
EvidenceCollected
 ↓
EvidenceValidated
 ↓
Comparison
 ↓
VerificationDecision

ต้องเชื่อมกลับ:

action_id

เสมอ

⸻

43. Recovery Trace

FailureDetected
 ↓
ExternalStateResolved
 ↓
RecoveryPlanCreated
 ↓
RecoveryAuthorized
 ↓
Compensation
 ↓
Verification
 ↓
Recovered

ถ้าไม่สำเร็จ:

Escalated

⸻

44. World Transition Trace

World state change ต้องสามารถอธิบาย:

World(t)
   ↓
Action
   ↓
External Effect
   ↓
Observation
   ↓
Verification
   ↓
World(t+1)

นี่เป็น backbone ของ auditability

⸻

45. Event to World Mapping

ไม่ใช่ทุก event เปลี่ยน World

ตัวอย่าง:

ModelGeneratedText

อาจไม่เปลี่ยน world

แต่:

FileModified

เปลี่ยน

ดังนั้น event ต้องประกาศ:

world_effect:
    NONE
    POSSIBLE
    CONFIRMED

⸻

46. External Events

External event:

Webhook
Sensor
Database Change
Filesystem Change
Email
Calendar Event

ต้องผ่าน:

Identity
Integrity
Freshness
Deduplication
Ordering
Validation

ก่อนถูกใช้เป็น authoritative world update

⸻

47. Event Provenance

ทุก event ต้องตอบ:

Where did this come from?

เช่น:

source_type
source_id
source_version
source_event_id
adapter
interface
observation_method

⸻

48. Evidence Link

Event สามารถอ้าง evidence:

event.evidence_refs[]

ตัวอย่าง:

FileModified
 ↓
filesystem_stat
 ↓
hash
 ↓
Evidence

⸻

49. Verification Link

event.verification_refs[]

ทำให้ query ได้:

Which actions were never verified?

⸻

50. Audit Completeness

Veda ต้องสามารถตรวจ:

Action exists
BUT
Authorization missing

หรือ:

Authorization exists
BUT
Execution trace missing

หรือ:

Execution exists
BUT
Verification missing

เหล่านี้คือ:

AuditIntegrityViolation

⸻

51. Audit Gap Detection

ระบบต้องตรวจ:

orphan action
orphan authorization
missing parent trace
missing verification
missing actor
timestamp anomaly
sequence gap
hash mismatch
duplicate critical event

⸻

52. Event Schema Registry

ต้องมี registry:

EventType
Schema
Version
Required Fields
Sensitivity
Retention
Integrity Level
Consumers

ตัวอย่าง:

ActionAuthorized v1
ActionAuthorized v2

⸻

53. Schema Evolution

เมื่อ schema เปลี่ยน:

v1
 ↓
Migration
 ↓
v2

Historical records ต้องยังอ่านได้

ห้ามทำลาย history เพียงเพราะ schema ใหม่ดูสวยกว่า

⸻

54. Event Bus

Architecture:

Producer
 ↓
Event Bus
 ↓
Consumers

Consumers เช่น:

Audit
Chronicle
Attention
Security
Metrics
Learning
World Model
Notification
Diagnostics

⸻

55. Event Delivery

รองรับ:

AT_MOST_ONCE
AT_LEAST_ONCE
EXACTLY_ONCE_SEMANTICS

อย่างไรก็ตาม implementation ต้องไม่สมมติว่า network-level exactly-once เป็นสิ่งมหัศจรรย์ที่มีอยู่จริง

Veda ต้องใช้:

idempotency
deduplication
event identity

เพื่อสร้าง exactly-once semantic behavior เมื่อจำเป็น

⸻

56. Backpressure

ถ้า event volume สูง:

Event Producer
 ↓
Queue
 ↓
Backpressure

Critical events ต้องมี reserved capacity

เช่น:

Security Event
Authorization Event
Financial Event
Constitution Event

ไม่ควรถูกทิ้งเพราะ telemetry เต็ม

⸻

57. Event Priority

CRITICAL
HIGH
NORMAL
LOW
DEBUG

Priority ไม่ควรเปลี่ยน semantic meaning

ใช้เพื่อ:

* scheduling
* retention
* delivery
* storage

⸻

58. Event Loss Policy

ถ้า storage เต็ม:

DEBUG
 ↓
DROP / SAMPLE

แต่:

CRITICAL AUDIT
 ↓
MUST NOT SILENTLY DROP

ถ้าบันทึกไม่ได้:

AuditStorageFailure

ต้องเกิดและเข้าสู่ fail-safe policy

⸻

59. Audit Failure

Critical action ถ้า audit infrastructure ใช้งานไม่ได้:

Policy สามารถกำหนด:

BLOCK
DEGRADE
ALLOW_WITH_LOCAL_BUFFER

สำหรับ high-risk action:

BLOCK

เป็น default ที่ปลอดภัยกว่า

⸻

60. Local Audit Buffer

ถ้า network หรือ Chronicle ล่ม:

Core
 ↓
Local Append-Only Buffer
 ↓
Persist
 ↓
Retry
 ↓
Chronicle

ห้ามเสีย critical audit เพียงเพราะ network หาย

⸻

61. Audit Replication

Critical records สามารถ replicate:

Primary Audit Store
        │
        ├── Secondary
        └── Offline Archive

สำหรับ disaster recovery

⸻

62. Retention

Retention policy ตาม:

event type
risk
sensitivity
legal policy
user policy
storage budget

ตัวอย่าง:

DEBUG:
7 days
Operational:
30 days
Security:
1 year
Critical:
long-term

ค่าจริงต้องมาจาก policy ไม่ hard-code ใน fabric

⸻

63. Deletion

การลบ audit record ต้อง:

policy-controlled
audited
authorized

และไม่ควรทำให้ integrity chain เสียโดยไม่มี tombstone/correction record

⸻

64. Tombstone

เมื่อจำเป็นต้องลบข้อมูล sensitive:

Original Event
 ↓
Redacted / Deleted
 ↓
Tombstone

Tombstone เก็บ:

event_id
deletion_reason
policy_ref
authorized_by
timestamp
original_hash

⸻

65. Security Monitoring

Event Fabric เป็น input ให้ Security subsystem

Events
 ↓
Detection
 ↓
Anomaly
 ↓
Attention
 ↓
Response

ตัวอย่าง:

50 denied capability requests
within 10 seconds

อาจกลายเป็น:

SecurityAnomaly

⸻

66. Tamper Detection

ถ้า:

hash mismatch
signature mismatch
sequence gap
unexpected writer

สร้าง:

AuditTamperingDetected

และ elevate Attention

⸻

67. Audit Access Logging

การอ่าน audit ก็ต้องถูก audit

User reads audit
 ↓
AuditReadEvent

ไม่เช่นนั้น attacker อาจไม่แตะ operational data แต่ล้วงประวัติทั้งหมดแทน

⸻

68. Audit of Audit

Critical audit infrastructure ต้องมี self-audit:

Can events be forged?
Can events disappear?
Can timestamps be altered?
Can writers bypass authorization?
Can storage be rewritten?
Can queries hide records?

⸻

69. Trace Sampling

Distributed telemetry อาจมี volume สูง

สามารถ sample:

DEBUG
NORMAL

แต่ห้าม sample away:

critical authorization
security incident
financial action
irreversible action
constitutional action

⸻

70. Full Trace Retention

Critical traces ต้อง preserve full lineage:

Intent
Goal
Plan
Decision
Authorization
Action
External Effect
Verification
World Update

⸻

71. Privacy-Aware Trace

Trace context ต้องไม่ส่ง:

password
secret
private content
sensitive personal data

โดยไม่จำเป็น

ใช้:

reference
hash
redaction
classification

⸻

72. Cross-System Trace

เมื่อ Veda เรียก external service:

Veda trace_id
 ↓
MCP
 ↓
HTTP
 ↓
External Service

ควร propagate trace context เมื่อปลอดภัยและ protocol รองรับ

แต่ external service ต้องไม่สามารถใช้ trace metadata เพื่อขอ authority

⸻

73. OpenTelemetry Integration

OpenTelemetry สามารถเป็น telemetry substrate:

Veda Event
    ↓
OTel Event / Log / Span
    ↓
OTLP
    ↓
Collector
    ↓
Backend

OTLP เป็น protocol สำหรับ encoding, transport และ delivery ของ telemetry และรองรับ traces, metrics และ logs (OpenTelemetry)

แต่:

OTel
≠
Veda Chronicle

OTel เป็น observability transport

Veda Chronicle เป็น authoritative historical record

⸻

74. Semantic Boundary

Veda Event
    ↓
Semantic Mapping
    ↓
OTel

ไม่ใช่:

OTel
    ↓
Automatically defines Veda semantics

⸻

75. Metrics

Event Fabric สามารถ derive metrics:

action_success_rate
verification_success_rate
authorization_denial_rate
unknown_effect_rate
recovery_success_rate
tool_failure_rate
planning_latency
decision_latency
audit_gap_rate

Metrics ไม่แทน events

⸻

76. Logs

Operational logs:

debug
info
warn
error

ไม่ควรถูกใช้แทน domain events

ตัวอย่าง:

"starting tool..."

เป็น log

แต่:

ToolExecutionStarted

เป็น domain event

⸻

77. Event Naming

ใช้รูปแบบ:

<Noun><PastTenseVerb>

ตัวอย่าง:

ActionAuthorized
PlanCreated
VerificationCompleted
WorldUpdated

ไม่ใช้:

doThing
processStuff
handleEvent

ชื่อ event ต้องมี semantic meaning

⸻

78. Event Categories

ทุก event ต้องมี:

category
event_type
severity

เช่น:

category = AUTHORIZATION
event_type = ActionAuthorized
severity = HIGH

⸻

79. Actor Model

Actor สามารถเป็น:

HUMAN
VEDA
AGENT
MODEL
TOOL
EXTERNAL_SYSTEM
SENSOR
SCHEDULER

แต่:

MODEL

ไม่ถือเป็น authority เพียงเพราะเป็น actor

⸻

80. Delegation

หาก Veda มอบหมายให้ agent:

Veda
 ↓
Agent
 ↓
Tool

Trace ต้องเก็บ delegation chain:

delegated_by
delegated_to
delegation_scope
delegation_policy

⸻

81. Human Interaction Trace

User interaction ต้องสามารถเชื่อม:

User Message
 ↓
Intent
 ↓
Goal
 ↓
Action

แต่ไม่จำเป็นต้องเก็บ raw conversation ถ้า policy ไม่อนุญาต

สามารถใช้:

message_id
content_hash
reference

แทน

⸻

82. Model Invocation Trace

Model invocation:

provider
model
version
task
context_ref
input_hash
output_hash
latency
token_usage
cost
status

ไม่จำเป็นต้องเก็บ raw hidden reasoning

⸻

83. Intelligence Provenance

ทุก intelligence output ต้องสามารถตอบ:

Which model?
Which version?
Which provider?
Which context?
Which task?
When?
What constraints?

เพื่อ reproducibility

⸻

84. Reproducibility

Critical decision ต้องเก็บ:

model_version
prompt_version
context_version
knowledge_version
world_version
policy_version
tool_registry_version

เพื่อให้สามารถ reconstruct decision environment

⸻

85. Configuration Trace

Configuration change ต้อง audit:

Old Config
 ↓
Change
 ↓
New Config
 ↓
Actor
 ↓
Authorization
 ↓
Reason

⸻

86. Policy Change Trace

Policy changes ต้องมี:

policy_version_old
policy_version_new
change
reason
actor
authorization
effective_time

⸻

87. Evolution Trace

Evolution deployment ต้อง trace:

Proposal
 ↓
Simulation
 ↓
Benchmark
 ↓
Security Review
 ↓
Authorization
 ↓
Deployment
 ↓
Monitoring
 ↓
Outcome

⸻

88. Constitutional Trace

Constitution-level operations ต้องมี maximum audit level:

A5

ต้อง preserve:

actor
authority
proposal
diff
evidence
approval
deployment
rollback

⸻

89. Queryable Accountability

Veda ต้องสามารถตอบ query เช่น:

"Why did Veda delete this file?"

Result:

Intent
Goal
Plan
Decision
Authorization
Action
Tool
Parameters
External Effect
Verification

⸻

90. Incident Reconstruction

Security incident:

Incident
 ↓
Trace Search
 ↓
Related Events
 ↓
Actor
 ↓
Capabilities
 ↓
Actions
 ↓
External Effects
 ↓
World Changes

ต้อง reconstruct timeline ได้

⸻

91. Blast Radius Analysis

จาก event สามารถหา:

Affected entities
Affected files
Affected systems
Affected credentials
Affected users
Affected processes
Affected world states

เพื่อช่วย RFC-0027 recovery

⸻

92. Event Dependencies

Event สามารถมี:

depends_on[]
caused_by[]
supersedes[]
corrects[]

ทำให้ history เป็น graph ไม่ใช่แค่ list

⸻

93. World Reconstruction

สามารถ reconstruct:

World(t)

จาก:

initial_snapshot
+
validated world events

แต่ต้องแยก:

World Event

จาก:

Audit Event

เพราะ audit event ไม่จำเป็นต้องเปลี่ยนโลก

⸻

94. Event Sourcing Boundary

Veda สามารถใช้ event sourcing สำหรับ state ที่เหมาะสม:

State
=
Snapshot
+
Event Stream

แต่ไม่ควรบังคับ event sourcing กับทุก subsystem

Telemetry เช่น:

CPU = 92%

ไม่จำเป็นต้องกลายเป็น authoritative world transition ทุกครั้ง

⸻

95. Snapshotting

เพื่อ performance:

Events 1...100000

สามารถสร้าง:

Snapshot @ 100000

จากนั้น reconstruct:

Snapshot
+
Events 100001...

Snapshot ต้องมี:

snapshot_hash
world_version
schema_version
created_at

⸻

96. Snapshot Verification

ก่อนใช้ snapshot:

verify_integrity
verify_schema
verify_version
verify provenance

หาก fail:

SnapshotRejected

⸻

97. Event Replay

Replay modes:

REAL_REPLAY
SIMULATION_REPLAY
DEBUG_REPLAY
RECOVERY_REPLAY
AUDIT_REPLAY

ห้าม replay external side effects โดยอัตโนมัติ

⸻

98. Safe Replay

สำหรับ external actions:

Replay
 ↓
DRY_RUN

ก่อน

ห้าม:

Replay
 ↓
Real Payment

เพราะนั่นไม่ใช่ replay แล้ว มันคือการจ่ายเงินจริงรอบสอง

⸻

99. Audit Export

รองรับ:

JSON
JSONL
CSV
Parquet
OpenTelemetry
Signed Archive

Critical export ต้องมี integrity metadata

⸻

100. Audit Import

Imported audit data ต้อง tagged:

IMPORTED

และห้ามกลายเป็น native trusted history โดยอัตโนมัติ

⸻

101. External Audit

สามารถเปิด read-only audit stream:

Auditor
 ↓
Audit API
 ↓
Policy
 ↓
Redaction
 ↓
Records

Auditor ไม่สามารถแก้ history

⸻

102. Event Bus Security

Event producer ต้อง authenticate

Consumer ต้อง authorize

Producer
 ↓
Identity
 ↓
Event Validation
 ↓
Event Bus
 ↓
Consumer Authorization

ห้าม arbitrary component publish privileged events เช่น:

HumanApprovalGranted
AuthorizationGranted
VerificationCompleted

โดยไม่มี authority

⸻

103. Event Forgery Protection

Critical event types ต้องตรวจ:

who may emit

เช่น:

AuthorizationGranted

ควร emit ได้เฉพาะ:

Authorization Engine

หรือ delegated trusted component

⸻

104. Event Capability

Publishing event บางประเภทเป็น capability

ตัวอย่าง:

emit.security_event
emit.authorization_event
emit.verification_event
emit.world_event

ต้องมี access control

⸻

105. Event Validation

ก่อนรับ event:

Identity
 ↓
Schema
 ↓
Timestamp
 ↓
Sequence
 ↓
Signature
 ↓
Authorization
 ↓
Provenance

⸻

106. Event Rejection

ถ้า invalid:

EventRejected

พร้อมเหตุผล

เช่น:

INVALID_SIGNATURE
INVALID_SCHEMA
UNKNOWN_SOURCE
TIMESTAMP_ANOMALY
UNAUTHORIZED_EMITTER
DUPLICATE

⸻

107. Audit Integrity Monitor

Background process ตรวจ:

hash chains
signatures
sequence gaps
missing records
orphan events
schema mismatches
unexpected writers

⸻

108. Chronicle Handoff

Event Fabric:

Event
 ↓
Validate
 ↓
Enrich
 ↓
Correlate
 ↓
Publish

Chronicle:

Persist
Index
Compress
Archive
Reconstruct

RFC-0032 จะกำหนดส่วนหลัง

⸻

109. Performance

Fabric ต้องไม่ทำให้ cognitive loop ช้าจนใช้งานไม่ได้

แบ่ง:

Synchronous Audit
Asynchronous Telemetry

Critical:

Authorization
Financial
Security
Irreversible

ต้อง synchronous ตาม policy

Noncritical:

Debug
Metrics
Performance

สามารถ asynchronous

⸻

110. Failure Modes

EventBusUnavailable
AuditStoreUnavailable
ClockUnavailable
SchemaRegistryUnavailable
ChronicleUnavailable
IntegrityVerificationFailed
StorageFull
QueueOverflow
ConsumerFailure

แต่ละ failure ต้องมี recovery policy

⸻

111. Fail-Closed

สำหรับ:

Critical authorization
Financial operation
Irreversible action
Constitutional change
Security response

ถ้า audit requirement ไม่สามารถ satisfy:

BLOCK

เป็น default

⸻

112. Fail-Open

สำหรับ low-risk telemetry:

DEBUG
METRICS

สามารถ:

DROP
BUFFER
SAMPLE

ได้ตาม policy

⸻

113. Self Observability

Event Fabric ต้อง monitor ตัวเอง:

events/sec
queue_depth
drop_rate
latency
storage_usage
integrity_failures
schema_failures
consumer_lag

⸻

114. Audit Metrics

Minimum:

audit_coverage
audit_gap_rate
critical_event_loss
verification_coverage
trace_completeness
integrity_failure_rate
event_latency
event_rejection_rate

⸻

115. Trace Completeness

Metric:

TraceCompleteness =
RequiredLinksPresent / RequiredLinks

ตัวอย่าง:

Intent ✓
Goal ✓
Plan ✓
Decision ✓
Authorization ✓
Action ✓
Verification ✗

trace completeness ต่ำ

⸻

116. Audit Coverage

AuditCoverage =
AuditableActionsWithTrace /
TotalAuditableActions

Critical target:

≈ 100%

⸻

117. Verification Coverage

VerificationCoverage =
ConsequentialActionsVerified /
ConsequentialActions

Critical actions:

must approach 100%

⸻

118. Event Fabric API

emit(event)
publish(event)
subscribe(filter)
query(filter)
get(event_id)
get_trace(trace_id)
start_trace()
start_span()
end_span()
correlate()
link_event()
add_evidence_ref()
add_verification_ref()
verify_integrity()
verify_trace()
create_snapshot()
replay()
export_audit()

⸻

119. Trace API

Trace {
    start()
    child()
    event()
    link()
    annotate()
    finish()
    fail()
}

⸻

120. Audit API

Audit {
    record()
    query()
    inspect()
    verify()
    export()
    reconstruct()
}

⸻

121. Event Storage Model

แนะนำแยก:

Hot Store
Warm Store
Cold Archive

Hot

recent operational data

Warm

audit/search

Cold

long-term immutable archive

⸻

122. Indexing

Index:

event_id
trace_id
actor_id
action_id
goal_id
timestamp
event_type
severity
source
world_version
verification_status

⸻

123. Partitioning

Partition ตาม:

time
tenant/world
security domain
event class

Critical events อาจใช้ dedicated partition

⸻

124. Encryption

Audit storage:

at_rest
in_transit

Critical recordsอาจมี:

field-level encryption

แต่ encryption key lifecycle ต้องแยกจาก audit writer

⸻

125. Key Rotation

Audit integrity keys ต้อง support:

rotation
revocation
historical verification

Historical records ต้องยังสามารถ verify ได้แม้ key รุ่นเก่าถูก rotate

⸻

126. Trust Separation

ผู้ที่สร้าง event:

Producer

ไม่ควรมีสิทธิ์แก้:

Audit Store

แยก:

Write Authority
Read Authority
Retention Authority
Integrity Authority

⸻

127. Anti-Tampering Architecture

Application
 ↓
Event Fabric
 ↓
Append-only Store
 ↓
Integrity Layer
 ↓
Archive

Application ไม่ควรเขียน historical audit database โดยตรง

⸻

128. No Silent Mutation

ห้าม:

UPDATE event
DELETE event

โดยไม่มี explicit audit operation

ใช้:

Correction
Supersession
Revocation
Redaction
Tombstone

แทน

⸻

129. Audit vs Memory

Audit:

What happened

Memory:

What Veda retains for future cognition

ดังนั้น:

Audit ≠ Memory

Memory สามารถสรุปจาก audit

แต่ไม่ควรทำลาย audit เพียงเพราะ memory ถูก compressed

⸻

130. Audit vs Knowledge

Audit:

Event record

Knowledge:

Claim about world

ตัวอย่าง:

Audit:
Veda received HTTP 200.
Knowledge:
Server accepted request.

Knowledge ต้องมี evidence

⸻

131. Audit vs Truth

Audit record สามารถพิสูจน์:

Veda recorded X

ไม่ได้แปลว่า:

X is objectively true

Audit เป็น evidence about system behavior

⸻

132. Audit vs World

Audit:
Veda believes file changed.
World:
File actually changed.

ต้องใช้ verification

⸻

133. Security Invariants

AUD-SEC-1

Audit records must not silently disappear.

AUD-SEC-2

Critical audit records must be tamper-evident.

AUD-SEC-3

Audit access must itself be auditable.

AUD-SEC-4

Secrets must not be logged by default.

AUD-SEC-5

Unauthorized components cannot emit privileged events.

AUD-SEC-6

Critical audit failure must trigger policy-defined fail-safe behavior.

AUD-SEC-7

Imported audit data is not automatically trusted.

AUD-SEC-8

Simulation events must never masquerade as real events.

AUD-SEC-9

Redaction must preserve accountability metadata where possible.

AUD-SEC-10

Audit integrity failures must trigger security attention.

⸻

134. Core Invariants

AUD-1

Every consequential action must have a trace.

AUD-2

Every consequential action must identify its actor.

AUD-3

Every consequential action must identify its authorization.

AUD-4

Every consequential action must identify its capability.

AUD-5

Every consequential action must identify its outcome.

AUD-6

Every consequential action must link verification when required.

AUD-7

Events must have unique identities.

AUD-8

Events must be versioned.

AUD-9

Event time must be distinguishable from processing time.

AUD-10

Event provenance must be preserved.

AUD-11

Events must support causal correlation.

AUD-12

Events must support distributed trace correlation.

AUD-13

Out-of-order events must be supported.

AUD-14

Duplicate events must be safely deduplicated.

AUD-15

Critical history must be append-only.

AUD-16

Corrections must not erase historical accountability.

AUD-17

Audit records must support integrity verification.

AUD-18

Critical events must not be silently dropped.

AUD-19

Telemetry must not be confused with domain events.

AUD-20

Audit must not be confused with truth.

AUD-21

Audit must not be confused with memory.

AUD-22

Audit must not be confused with knowledge.

AUD-23

World updates must reference validated evidence where required.

AUD-24

Verification results must link to the operation they verify.

AUD-25

Recovery operations must remain traceable.

AUD-26

Human approvals must be traceable.

AUD-27

Policy changes must be traceable.

AUD-28

Evolution changes must be traceable.

AUD-29

Constitutional operations require highest auditability.

AUD-30

Veda must be able to reconstruct consequential execution history.

⸻

135. Reference Flow

                     USER
                       │
                       ▼
                    INTENT
                       │
                       ▼
                     GOAL
                       │
                       ▼
                    PLAN
                       │
                       ▼
                   DECISION
                       │
                       ▼
                AUTHORIZATION
                       │
                       ▼
                    ACTION
                       │
                       ▼
                 TOOL / MCP
                       │
                       ▼
               EXTERNAL WORLD
                       │
                       ▼
                  OBSERVATION
                       │
                       ▼
                   EVIDENCE
                       │
                       ▼
                 VERIFICATION
                       │
                       ▼
                  WORLD UPDATE
        ┌─────────────────────────────────┐
        │       EVENT / TRACE FABRIC       │
        │                                 │
        │  Intent ────────┐              │
        │  Goal ──────────┤              │
        │  Plan ──────────┤              │
        │  Decision ──────┤              │
        │  Authorization ─┤── TRACE ─────│
        │  Action ────────┤              │
        │  External Effect┤              │
        │  Verification ──┤              │
        │  World Update ──┘              │
        └─────────────────────────────────┘
                       │
                       ▼
                   CHRONICLE

⸻

136. Final Principle

Veda ต้องสามารถเดินย้อนกลับจาก:

World Change

ไปถึง:

Verification
← External Effect
← Action
← Authorization
← Decision
← Plan
← Goal
← Intent
← Human

และเดินไปข้างหน้าได้:

Intent
→ Goal
→ Plan
→ Decision
→ Authorization
→ Action
→ External Effect
→ Verification
→ World

ดังนั้น:

Every consequential action must leave a reconstructable history.

และ:

The audit trail records what Veda did and observed. It does not, by itself, decide what is true.

⸻

137. RFC-0031 Summary

RFC-0031 ทำให้ Veda มี:

Event Fabric
Trace Fabric
Audit Fabric
Correlation
Provenance
Integrity
Replay
Reconstruction
Security Monitoring
Audit Coverage
Failure Detection

และสร้างสะพาน:

Cognition
   ↓
Execution
   ↓
Reality
   ↓
Evidence
   ↓
History

นี่คือรากฐานที่ทำให้ Veda สามารถเป็นระบบที่ ตรวจสอบตัวเองย้อนหลังได้จริง แทนที่จะเป็นกล่องดำที่ตอบว่า “ผมทำแล้วครับ” แล้วมนุษย์ก็ต้องหวังว่ามันพูดความจริง