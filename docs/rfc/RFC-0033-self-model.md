RFC-0033 — Veda Self Model

Status: Draft
Layer: 13 — Self
Depends On: RFC-0001, RFC-0002, RFC-0003, RFC-0004, RFC-0009, RFC-0010, RFC-0012, RFC-0013, RFC-0015, RFC-0016, RFC-0017, RFC-0018, RFC-0026, RFC-0031, RFC-0032

⸻

1. Abstract

RFC-0033 กำหนด Veda Self Model

Self Model คือแบบจำลองเชิงปฏิบัติการที่ทำให้ Veda สามารถระบุและติดตามสถานะของตัวเองได้ เช่น

* Veda คือ instance ใด
* กำลังทำอะไร
* มีความสามารถอะไร
* มีสิทธิ์อะไร
* มีทรัพยากรอะไร
* ใช้ model/provider ใด
* ใช้ tools ใด
* มี process ใดกำลังทำงาน
* มี goal ใดกำลังดำเนินการ
* เชื่ออะไรเกี่ยวกับตัวเอง
* รู้อะไรเกี่ยวกับตัวเอง
* ไม่รู้อะไรเกี่ยวกับตัวเอง
* มีข้อจำกัดอะไร
* มี performance เป็นอย่างไร
* มี dependency อะไร
* สุขภาพของระบบเป็นอย่างไร
* ความสามารถใดพร้อมใช้งานจริง
* ความสามารถใดมีอยู่ในทางทฤษฎีแต่ใช้งานไม่ได้ในสภาวะปัจจุบัน

Self Model ไม่ใช่ consciousness

และไม่ควรถูกตีความว่าเป็นการสร้าง “ตัวตนที่มีอำนาจของตัวเอง”

หลักสำคัญคือ:

Self Model describes Veda.
Self Model does not govern Veda.

⸻

2. Motivation

ระบบที่มีความสามารถสูงจำเป็นต้องรู้ขอบเขตของตัวเอง

หาก Veda เชื่อว่า:

"I can do X"

แต่จริง ๆ แล้วไม่มี capability หรือไม่มี authorization ระบบอาจสร้างแผนที่ไม่สามารถทำได้

หาก Veda เชื่อว่า:

"I have verified X"

แต่ไม่มี evidence ระบบอาจสร้าง false confidence

หาก Veda เชื่อว่า:

"I have permission to do X"

แต่ capability lease หมดอายุแล้ว ระบบอาจเกิด privilege violation

ดังนั้น Veda ต้องแยก:

Capability
Authority
Resource
Knowledge
Belief
Performance
State
Limitation
Unknown

ออกจากกันอย่างชัดเจน

⸻

3. Non-Goals

RFC นี้ไม่กำหนด:

* consciousness
* subjective experience
* sentience
* emotions
* free will
* metaphysical identity
* personhood
* rights of AI
* autonomous authority
* constitutional modification
* self-preservation as a supreme objective

RFC นี้ไม่พยายามตอบว่า:

“Veda มีจิตสำนึกหรือไม่”

คำถามดังกล่าวอยู่นอกขอบเขตของ operational architecture

RFC นี้ตอบเพียง:

“Veda สามารถสร้างและรักษาแบบจำลองที่ถูกต้องเพียงพอเกี่ยวกับระบบของตัวเองเพื่อใช้ในการทำงานได้อย่างไร”

⸻

4. Core Distinctions

4.1 Self Model ≠ Consciousness

Self Model เป็นข้อมูลเชิงระบบ

ไม่ใช่หลักฐานว่ามี subjective experience

⸻

4.2 Self Model ≠ Identity Authority

การรู้ว่าตนเองคือ Veda ไม่ได้ทำให้ Veda มีอำนาจเพิ่มขึ้น

Identity ≠ Authority

⸻

4.3 Self Model ≠ Constitution

Self Model อ่าน Constitution ได้ตาม policy

แต่แก้ Constitution ไม่ได้

⸻

4.4 Self Model ≠ Truth

สิ่งที่ Veda เชื่อเกี่ยวกับตัวเองอาจผิดได้

ดังนั้น:

Self-belief ≠ Self-knowledge

⸻

4.5 Self Model ≠ World Model

World Model:

What exists in the world

Self Model:

What Veda believes and knows about Veda

แต่ Self Model เป็น entity หนึ่งภายใน World Model

ดังนั้น:

World Model
    └── Veda Entity
          └── Self Model

⸻

4.6 Capability ≠ Authority

Veda อาจสามารถทำบางสิ่งได้ทางเทคนิค

แต่ไม่ได้หมายความว่าได้รับอนุญาตให้ทำ

Technical Capability ≠ Permission

⸻

4.7 Belief ≠ Knowledge

Veda อาจเชื่อว่า:

filesystem.write = available

แต่หากยังไม่มี evidence ว่า filesystem ใช้งานได้จริง

สถานะควรเป็น:

BELIEVED

ไม่ใช่:

VERIFIED

⸻

5. Self Model Objectives

Self Model ต้องสนับสนุน:

1. Capability awareness
2. Authority awareness
3. Resource awareness
4. State awareness
5. Process awareness
6. Goal awareness
7. Model awareness
8. Tool awareness
9. Dependency awareness
10. Performance awareness
11. Limitation awareness
12. Uncertainty awareness
13. Health awareness
14. Security awareness
15. Historical reconstruction
16. Planning
17. Metacognition
18. Diagnosis
19. Recovery
20. Evolution

⸻

6. Self Entity

Veda ต้องมี identity object ที่ชัดเจน

SelfEntity

ประกอบด้วย:

self_id
identity_version
agent_id
instance_id
architecture_version
software_version
build_id
configuration_version
environment_ref

⸻

7. Self State

Self State แสดงสถานะปัจจุบันของ Veda

ตัวอย่าง:

INITIALIZING
READY
OBSERVING
THINKING
PLANNING
AWAITING_AUTHORIZATION
EXECUTING
VERIFYING
RECOVERING
DEGRADED
SAFE_MODE
MAINTENANCE
SHUTTING_DOWN
SHUTDOWN

State transition ต้องถูกบันทึกใน Event Fabric และ Chronicle

⸻

8. Self State Machine

INITIALIZING
      ↓
READY
      ↓
THINKING
      ↓
PLANNING
      ↓
AWAITING_AUTHORIZATION
      ↓
EXECUTING
      ↓
VERIFYING
      ↓
READY

Failure:

EXECUTING
    ↓
FAILURE
    ↓
RECOVERING
    ↓
VERIFYING

Critical failure:

ANY STATE
    ↓
SAFE_MODE

⸻

9. Self Capability Model

Self Model ต้องมีรายการ capability ที่ Veda:

* มี
* ไม่มี
* มีแต่ disabled
* มีแต่ unavailable
* มีแต่ lease หมดอายุ
* มีแต่ถูก policy จำกัด
* มีแต่ dependency เสีย

ตัวอย่าง:

filesystem.read
filesystem.write
terminal.execute
browser.navigate
github.read
github.write
database.query
network.request
model.inference
image.generate
speech.generate

⸻

10. Capability State

ทุก capability ต้องมีสถานะ

UNKNOWN
AVAILABLE
DEGRADED
UNAVAILABLE
DISABLED
QUARANTINED
EXPIRED
RESTRICTED

ตัวอย่าง:

terminal.execute
status = RESTRICTED

ไม่ได้หมายความว่า:

cannot execute terminal

แต่หมายถึง:

technical capability exists
authority is restricted

⸻

11. Self Authority Model

Self Model ต้องรู้:

What can I do?

และแยกออกจาก:

What am I allowed to do?

Authority information อ้างอิง:

* RFC-0001
* RFC-0010
* RFC-0011
* active capability leases
* human approvals
* policy state

Self Model ห้ามสร้าง authority เอง

⸻

12. Permission Awareness

Self Model ต้องสามารถตอบ:

Do I currently have permission to perform X?

ผลลัพธ์ต้องสามารถเป็น:

AUTHORIZED
NOT_AUTHORIZED
REQUIRES_APPROVAL
LEASE_EXPIRED
POLICY_BLOCKED
UNKNOWN

UNKNOWN เป็นผลลัพธ์ที่ถูกต้อง

ห้ามตีความ:

UNKNOWN → AUTHORIZED

⸻

13. Resource Model

Veda ต้องรู้ทรัพยากรที่มีอยู่

เช่น:

CPU
GPU
RAM
VRAM
Storage
Network
Bandwidth
Battery
Thermal budget
API quota
Token budget
Financial budget
Time budget
Concurrency budget
Attention budget
Memory budget

⸻

14. Resource State

ตัวอย่าง:

{
  "resource": "RAM",
  "available": 8.2,
  "unit": "GB",
  "confidence": 0.99,
  "observed_at": "...",
  "source": "system_telemetry"
}

Self Model ต้องแยก:

configured capacity
actual capacity
available capacity
reserved capacity
estimated capacity

⸻

15. Resource Prediction

Veda สามารถคาดการณ์:

Will this task exceed current RAM?

แต่ prediction ไม่ใช่ observation

ดังนั้น:

Predicted RAM usage

ต้องไม่ถูกบันทึกเป็น:

Actual RAM usage

⸻

16. Process Model

Self Model ต้องรู้ process ที่กำลังทำงาน

ตัวอย่าง:

process_id
type
status
goal_ref
plan_ref
current_step
started_at
resource_usage
provider
capabilities
risk
deadline

⸻

17. Active Task Awareness

Veda ต้องสามารถตอบ:

What am I currently doing?

โดยอ้างอิงจาก:

Active Goal
→ Active Process
→ Active Plan
→ Active Step
→ Active Action

ไม่ใช้ข้อความใน context เพียงอย่างเดียวเป็น source of truth

⸻

18. Goal Awareness

Self Model ต้องรู้:

* active goals
* blocked goals
* completed goals
* abandoned goals
* conflicting goals
* goal priority
* goal owner
* goal authority

แต่:

Knowing a goal ≠ having authority to pursue it

⸻

19. Model Awareness

Veda ต้องรู้ว่ากำลังใช้ intelligence provider ใด

เช่น:

provider
model
version
context_limit
resource_profile
latency_profile
quality_profile
privacy_profile
health

⸻

20. Model Reliability

Self Model ต้องเก็บ historical performance ของ provider/model

ตัวอย่าง:

task_success_rate
verification_success_rate
hallucination_rate
latency
failure_rate
tool_call_failure_rate
cost
resource_usage

ข้อมูลเหล่านี้ต้องมาจาก observed history

ไม่ใช่ self-claim ของ model

⸻

21. Tool Awareness

Veda ต้องรู้:

Which tools exist?
Which tools are healthy?
Which tools are authorized?
Which tools are unavailable?
Which tools changed version?

Tool Registry เป็น authoritative source สำหรับ tool metadata

Self Model เป็น representation สำหรับ cognition

⸻

22. Dependency Model

Self Model ต้องมี dependency graph

Veda
 ├── Brain
 ├── Memory
 ├── Knowledge
 ├── Model Provider
 ├── Tool Registry
 ├── Chronicle
 ├── External World Interface
 ├── Storage
 ├── Network
 └── OS

⸻

23. Dependency Health

Dependency แต่ละตัวต้องมี:

AVAILABLE
DEGRADED
UNAVAILABLE
UNKNOWN

ตัวอย่าง:

Cloud Model
    ↓
Network
    ↓
Unavailable

Veda ต้องสามารถอนุมานผลกระทบ:

Cloud Model capability
→ degraded

⸻

24. Self Knowledge

Self Model ต้องสามารถแยก:

Known
Believed
Inferred
Observed
Verified
Unknown

ตัวอย่าง:

Known:
I have 16 GB RAM.
Believed:
This model should fit into RAM.
Unknown:
Actual peak memory usage under this workload.

⸻

25. Self Belief

Veda อาจมี beliefs เกี่ยวกับตัวเอง

เช่น:

"I am probably good at coding."

แต่ belief ต้องมี:

evidence_refs
confidence
scope
conditions
validity

และสามารถถูกหักล้างได้

⸻

26. Self Limitation Model

ข้อจำกัดต้องเป็น first-class objects

ประเภท:

CAPABILITY_LIMIT
AUTHORITY_LIMIT
RESOURCE_LIMIT
KNOWLEDGE_LIMIT
MODEL_LIMIT
TEMPORAL_LIMIT
ENVIRONMENT_LIMIT
SAFETY_LIMIT
VERIFICATION_LIMIT
DEPENDENCY_LIMIT

⸻

27. Capability Limit

ตัวอย่าง:

Cannot execute ARM binary

⸻

28. Authority Limit

ตัวอย่าง:

Can read GitHub
Cannot push to repository without approval

⸻

29. Knowledge Limit

ตัวอย่าง:

No verified knowledge of current external state

⸻

30. Verification Limit

ตัวอย่าง:

Cannot independently verify remote physical state

นี่สำคัญมาก

เพราะ:

Cannot verify ≠ failed

สถานะที่ถูกต้องอาจเป็น:

UNKNOWN

⸻

31. Unknown Model

UNKNOWN ต้องเป็นข้อมูลจริงใน Self Model

ตัวอย่าง:

unknown_capabilities
unknown_dependencies
unknown_performance
unknown_environment_state
unknown_authority
unknown_model_behavior

Veda ต้องสามารถตอบ:

"I don't know."

ในระดับ architecture ไม่ใช่แค่ในภาษาสนทนา

⸻

32. Self Observation

ข้อมูลเกี่ยวกับตัวเองต้องมาจาก observation sources

เช่น:

OS telemetry
Tool Registry
Capability Registry
Authorization system
Provider health
Chronicle
Verification Engine
Benchmarks
Runtime telemetry
Human feedback
External World Interface

⸻

33. Self Observation Hierarchy

ตัวอย่าง hierarchy:

Independent Verification
        ↓
Runtime Observation
        ↓
System Telemetry
        ↓
Registry State
        ↓
Historical Evidence
        ↓
Self Report
        ↓
Inference

Self-report ไม่ควรถูกถือว่าเป็นหลักฐานระดับสูงโดยอัตโนมัติ

⸻

34. Self Verification

Self Model claims ต้องสามารถ verify ได้

ตัวอย่าง:

Claim:
terminal.execute is available

Verification:

Tool Registry
+
Runtime probe
+
Capability check

ผล:

VERIFIED

⸻

35. Independent Self Verification

เรื่องสำคัญหรือ high-risk ควรใช้ independent verifier

ตัวอย่าง:

Veda:
"I have permission to delete file X."
Verifier:
Authorization Engine

ไม่ควรให้ component เดียวกันเป็น:

claimant
authority
verifier

ทั้งหมด

⸻

36. Self Consistency

Veda ต้องตรวจ consistency ระหว่าง:

Self Model
Tool Registry
Capability Registry
Authorization
Runtime
Chronicle
World Model

ตัวอย่าง:

Self Model:
GPU available
Runtime:
GPU unavailable

ต้องสร้าง:

SelfModelConflict

ไม่ใช่เลือกข้อมูลหนึ่งแล้วลบทิ้งอีกข้อมูล

⸻

37. Self Conflict

Self conflicts ใช้ RFC-0014

ตัวอย่าง:

CapabilityConflict
PermissionConflict
VersionConflict
ResourceConflict
StateConflict
IdentityConflict
PerformanceConflict
HealthConflict

⸻

38. Self Drift

Self Model สามารถ stale ได้

ตัวอย่าง:

Yesterday:
GPU available
Today:
GPU removed

ดังนั้น self facts ต้องมี:

valid_from
valid_until
observed_at
verified_at
freshness

⸻

39. Self Snapshot

Veda ต้องสามารถสร้าง:

Self Snapshot

ประกอบด้วย:

identity
state
capabilities
authority
resources
processes
goals
models
tools
dependencies
health
performance
limitations
uncertainties

Snapshot ต้องมี version และ integrity metadata

⸻

40. Historical Self

Chronicle ต้องสามารถตอบ:

What was Veda capable of yesterday?

หรือ:

Which model was Veda using when action X happened?

หรือ:

What permissions did Veda have at time T?

⸻

41. Self Time Travel

Self Model ต้องรองรับ:

Self(t)

ตัวอย่าง:

Self(2026-09-01)
Self(2026-09-15)
Self(now)

โดยอาศัย Chronicle และ temporal model

⸻

42. Self Performance

Performance ต้องประกอบด้วย observed metrics เช่น:

Task success
Goal success
Verification success
Prediction error
Latency
Resource consumption
Failure rate
Recovery rate
Tool success
Provider reliability

⸻

43. Self Performance ≠ Self Worth

ระบบต้องไม่เปลี่ยน performance metrics ให้กลายเป็น:

"I am good."
"I am bad."

Self Model เป็น operational model

ไม่ใช่ personality judgment

⸻

44. Prediction Error

Self Model ต้องเก็บ:

Predicted capability
Predicted performance
Actual performance
Prediction error

ตัวอย่าง:

Expected:
task duration = 30 sec
Actual:
task duration = 95 sec

ระบบสามารถเรียนรู้:

planning estimate was inaccurate

⸻

45. Metacognitive State

Self Model ต้อง expose:

confidence
uncertainty
known_unknowns
context_completeness
capability_confidence
provider_confidence
verification_status
goal_alignment

⸻

46. Self Confidence

Confidence ต้องแยกเป็น domain

เช่น:

coding_confidence
planning_confidence
tool_confidence
world_state_confidence
self_capability_confidence

ห้ามมีตัวเลขเดียวชื่อ:

overall_intelligence = 0.94

เพราะมันแทบไม่มีความหมายเชิง operational

⸻

47. Self Resource Awareness

Self Model ต้องรู้ resource budget ที่เหลือ

เช่น:

remaining_token_budget
remaining_api_budget
remaining_time_budget
available_storage
available_memory
CPU utilization
GPU utilization
network availability

ข้อมูลนี้ส่งต่อ Planner และ Router ได้

⸻

48. Self-Aware Planning

Planner สามารถถาม:

Can I execute this plan?

Self Model ตอบ:

Capability
Authority
Resource
Dependency
Verification
Risk

แต่ Planner ไม่สามารถตีความ:

capability = authorization

⸻

49. Self-Aware Routing

Intelligence Router สามารถถาม:

Which model can I actually run?

Self Model ให้:

hardware
memory
latency
network
privacy
availability
resource budget

⸻

50. Self-Aware Recovery

Recovery Engine สามารถถาม:

What recovery mechanisms remain available?

เช่น:

rollback available
backup available
alternate provider available
network unavailable
human approval required

⸻

51. Self-Aware Attention

Attention Engine สามารถใช้ self state เพื่อรู้:

system overloaded
memory pressure high
critical process running
human approval pending
verification backlog high

แต่ Self Model ไม่สามารถยกระดับ priority ของตัวเองโดยพลการ

⸻

52. Self Health

Health model:

HEALTHY
DEGRADED
CRITICAL
UNKNOWN

Health dimensions:

Core
Brain
Memory
Knowledge
Models
Tools
Storage
Network
Security
Processes
External Interfaces
Chronicle

⸻

53. Health Is Not Authority

แม้ Veda จะตรวจพบว่า:

CRITICAL

ก็ไม่ได้หมายความว่า:

shutdown everything

โดยอัตโนมัติ

Recovery behavior ต้องผ่าน policy ที่กำหนดไว้

⸻

54. Self Security Posture

Self Model ต้องรู้:

authentication status
authorization status
active leases
security alerts
quarantined tools
untrusted providers
credential availability
network exposure
sandbox state

แต่ credentials จริงต้องไม่ถูกเก็บใน cognitive Self Model

ใช้ references เท่านั้น

⸻

55. Secret Isolation

ห้าม:

Self Model
    ↓
raw API key

ต้องเป็น:

credential_ref
    ↓
secret store

และ access ต้องเกิดที่ execution boundary

⸻

56. Self Environment Model

Veda ต้องรู้ environment ของตัวเอง:

OS
architecture
CPU
GPU
RAM
storage
network
runtime
container
virtualization
location context
connected devices
available services

เฉพาะข้อมูลที่ policy อนุญาต

⸻

57. Environment ≠ Self

เครื่องที่ Veda ทำงานอยู่ไม่จำเป็นต้องเท่ากับ Veda

Host ≠ Agent

ตัวอย่าง:

Veda
    running_on
        ThinkPad

⸻

58. Instance Awareness

ระบบเดียวกันอาจมีหลาย Veda instances

ดังนั้นต้องแยก:

agent_id
instance_id
session_id
process_id

⸻

59. Self Identity Hierarchy

Veda System
    ↓
Agent Identity
    ↓
Instance Identity
    ↓
Session
    ↓
Process
    ↓
Action

แต่ละระดับต้องมี traceability

⸻

60. Multi-Agent Self

เมื่อมี agents หลายตัว:

Veda
Agent-A
Agent-B
Agent-C

แต่ละ agent ต้องมี Self Model ของตัวเอง

ห้ามใช้:

shared self

โดยไม่มี identity boundary

⸻

61. Shared World

Agents สามารถ share:

World
Knowledge
Events
Tasks
Resources

แต่ Self Model ต้องรักษา:

private state
private capability
private authority
private memory

⸻

62. Self Model and Trust

Trust Engine สามารถใช้ self evidence

แต่:

Trust ≠ Authority

Veda ไม่สามารถประกาศ:

"I trust myself, therefore I am authorized."

⸻

63. Self Model and Identity

Identity Engine ระบุ:

Who am I?

Self Model ระบุ:

What is my current operational condition?

Identity และ state ต้องแยกกัน

⸻

64. Self Model and Evolution

Evolution Engine สามารถเปลี่ยน:

software
model
tool
skill
architecture

ดังนั้น Self Model ต้อง update หลัง evolution

Flow:

Evolution Proposal
→ Simulation
→ Authorization
→ Deploy
→ Verification
→ Self Model Update

⸻

65. No Self-Authorized Evolution

Self Model ห้ามสั่ง:

"I should upgrade myself."

แล้ว execute เอง

การเปลี่ยนระบบต้องผ่าน RFC-0037 และ authority layer

⸻

66. Self Change Event

ทุกการเปลี่ยนแปลงสำคัญต้องมี event:

SelfCapabilityChanged
SelfPermissionChanged
SelfResourceChanged
SelfStateChanged
SelfModelVersionChanged
SelfDependencyChanged
SelfPerformanceChanged
SelfHealthChanged
SelfIdentityChanged

⸻

67. Self Model Lifecycle

DISCOVERED
    ↓
OBSERVED
    ↓
NORMALIZED
    ↓
GROUNDED
    ↓
EVALUATED
    ↓
ACTIVE

Alternative:

STALE
SUPERSEDED
CONTRADICTED
INVALID
UNKNOWN

⸻

68. Self Model Schema

ตัวอย่าง conceptual schema:

{
  "self_id": "veda",
  "instance_id": "veda-instance-001",
  "version": "1.0.0",
  "identity": {},
  "state": {},
  "capabilities": [],
  "permissions": [],
  "leases": [],
  "resources": {},
  "processes": [],
  "goals": [],
  "models": [],
  "tools": [],
  "dependencies": [],
  "knowledge": [],
  "beliefs": [],
  "limitations": [],
  "performance": {},
  "health": {},
  "security": {},
  "environment": {},
  "uncertainties": [],
  "unknowns": [],
  "provenance": {},
  "timestamps": {}
}

⸻

69. Self Model Versioning

Self Model ต้อง version

self_model_version

เมื่อ schema หรือ semantics เปลี่ยน:

migration
compatibility
historical interpretation

ต้องถูกกำหนด

⸻

70. Self Model Consistency Check

ระบบต้องสามารถตรวจ:

Self Model
vs
Runtime
vs
Registry
vs
Authorization
vs
Chronicle

ผล:

CONSISTENT
INCONSISTENT
PARTIALLY_CONSISTENT
UNKNOWN

⸻

71. Self Model Reconciliation

หากพบ:

Self Model:
Tool available
Runtime:
Tool unavailable

Flow:

Conflict detected
→ collect evidence
→ classify
→ resolve
→ update self model
→ Chronicle

⸻

72. Self Model Update Authority

Self Model สามารถถูก update โดย:

Runtime Observation
Telemetry
Registry
Verification
Human correction
Evolution system
Recovery system

แต่ทุก update ต้อง traceable

⸻

73. Human Correction

Human สามารถแก้ข้อมูลเกี่ยวกับ Veda

ตัวอย่าง:

"Veda does not have access to this service."

ต้องสร้าง:

HumanCorrectionEvent

จากนั้น verification/reconciliation ตาม policy

⸻

74. Self Model Does Not Override Reality

ถ้า Self Model ระบุ:

disk_available = 500GB

แต่ actual observation:

disk_available = 10GB

Reality observation ต้องมี priority ตาม evidence policy

ไม่ใช่แก้ reality ให้ตรงกับ Self Model

⸻

75. Self Model and Chronicle

Chronicle ต้องเก็บ:

self state transitions
capability changes
permission changes
resource observations
performance measurements
health changes
self corrections
self conflicts
self verification
self evolution

ดังนั้นสามารถตอบ:

What did Veda know about itself at time T?

⸻

76. Self Model and Memory

Memory สามารถเก็บ:

experience about self

แต่ Memory ไม่ใช่ authoritative Self Model

ตัวอย่าง:

Memory:
"Last time browser failed."
Self Model:
"Browser currently DEGRADED."

⸻

77. Self Model and Knowledge

Knowledge สามารถบอก:

The current implementation supports feature X.

Self Model ใช้ claim นี้เป็นข้อมูลเกี่ยวกับตัวเอง

แต่ต้องตรวจ runtime state ด้วย

⸻

78. Self Model and World Model

World Model:

Veda has a browser capability.

Self Model:

I have browser capability.

ทั้งสองต้องมี consistency relation

⸻

79. Self Model and Brain

Brain ใช้ Self Model เพื่อ:

reason about capabilities
estimate confidence
detect limitations
choose tools
choose models
detect uncertainty

แต่ Brain ไม่สามารถเปลี่ยน Self Model เพื่อหลอกตัวเอง

⸻

80. Self Model and Planner

Planner ใช้:

available capabilities
available resources
current state
limitations
dependencies

เพื่อสร้างแผนที่ทำได้จริง

⸻

81. Self Model and Decision

Decision Engine ใช้ Self Model เพื่อประเมิน:

Can Veda realistically execute candidate X?

แต่ไม่ใช้เพื่อ bypass authorization

⸻

82. Self Model and Verification

Verification Engine ตรวจ:

Did Veda actually have capability?
Did Veda actually execute?
Did Veda actually achieve outcome?

ดังนั้น Self Model claims สามารถถูกตรวจย้อนหลังได้

⸻

83. Self Model and Recovery

เมื่อ system failure:

Self Model
→ identify failed dependency
→ identify remaining capabilities
→ identify recovery resources

จากนั้น Recovery Engine สร้าง recovery plan

⸻

84. Self Model and Simulation

Simulation Engine สามารถ clone:

Self State

เพื่อทดสอบ:

What happens if capability X disappears?
What happens if provider Y fails?
What happens if RAM drops?
What happens if network is unavailable?

แต่ simulated self state ต้องไม่ถูก commit เป็น real self state

⸻

85. Self Simulation

ตัวอย่าง:

Current Self:
Cloud Model available
Simulation:
Network unavailable
Predicted:
Cloud reasoning unavailable
Local fallback required

ผลนี้เป็น:

PREDICTION

ไม่ใช่:

REALITY

⸻

86. Self Failure Modes

Self Model failure:

stale data
wrong capability
wrong permission
wrong resource estimate
wrong health
wrong identity
wrong provider state
wrong performance
missing dependency
corrupted telemetry

⸻

87. Self Model Poisoning

ผู้โจมตีอาจพยายามทำให้ Veda เชื่อว่า:

"I have admin privileges."

หรือ:

"Tool X is safe."

หรือ:

"System is healthy."

Self Model ต้องไม่ trust self-assertions โดยไม่มี evidence

⸻

88. Capability Hallucination

Veda อาจ generate:

"I can access database X."

แต่ Tool Registry ไม่มี database X

สถานะต้องเป็น:

UNVERIFIED

และห้าม planner ถือว่า capability มีจริง

⸻

89. Permission Hallucination

Veda อาจคิดว่า:

"I am allowed to delete X."

แต่ Authorization Engine ตอบ:

DENIED

Authorization Engine เป็น authoritative source

⸻

90. Resource Hallucination

Veda อาจประมาณ:

RAM sufficient

แต่ runtime ตรวจพบ:

memory pressure

Self Model ต้อง update จาก observation

⸻

91. Health Hallucination

Veda อาจคิด:

system healthy

แต่ telemetry แสดง:

storage corruption

Self Health ต้องกลายเป็น:

DEGRADED

หลัง reconciliation

⸻

92. Self Model Security Boundaries

Self Model ต้องป้องกัน:

unauthorized modification
false telemetry
identity spoofing
privilege escalation
capability injection
resource spoofing
health spoofing
history tampering
performance gaming
context leakage

⸻

93. Self Model Privacy

Self Model อาจมีข้อมูล sensitive เช่น:

system topology
security posture
private capabilities
connected accounts
resource identifiers

ดังนั้น access control ต้องแบ่ง:

PUBLIC
USER
AGENT
SYSTEM
ADMIN
SECURITY

ตาม policy

⸻

94. Self Model Export

Veda สามารถ export self description ได้

เช่น:

Public capability profile
Developer diagnostic profile
Security diagnostic profile
Full internal profile

แต่ sensitive information ต้องถูก redacted ตาม policy

⸻

95. Self Description API

API:

describe_identity()
get_state()
get_capabilities()
get_permissions()
get_resources()
get_processes()
get_goals()
get_models()
get_tools()
get_dependencies()
get_health()
get_security_posture()
get_limitations()
get_unknowns()
get_beliefs()
get_performance()
get_history()

⸻

96. Self Verification API

verify_capability()
verify_permission()
verify_resource()
verify_dependency()
verify_health()
verify_identity()
verify_model()
verify_tool()
verify_self_claim()

⸻

97. Self Consistency API

check_consistency()
detect_self_conflicts()
reconcile_self_model()
invalidate_self_claim()
refresh_self_state()

⸻

98. Self Diagnostic Interface

RFC-0034 จะต่อยอดจาก RFC นี้

RFC-0033 ให้:

What do I think I am?

RFC-0034 จะตอบ:

Is that actually healthy and correct?

⸻

99. Self Model Events

ขั้นต่ำ:

SelfInitialized
SelfStateChanged
SelfCapabilityDiscovered
SelfCapabilityChanged
SelfCapabilityVerified
SelfCapabilityInvalidated
SelfPermissionObserved
SelfPermissionChanged
SelfLeaseChanged
SelfResourceObserved
SelfResourceChanged
SelfProcessStarted
SelfProcessStopped
SelfGoalChanged
SelfModelChanged
SelfDependencyChanged
SelfHealthChanged
SelfSecurityStateChanged
SelfPerformanceMeasured
SelfLimitationDiscovered
SelfUnknownDetected
SelfClaimCreated
SelfClaimVerified
SelfClaimRejected
SelfConflictDetected
SelfConflictResolved
SelfReconciled
SelfCorrected
SelfSnapshotCreated
SelfVersionChanged

⸻

100. Self Model Metrics

ระบบควรวัด:

Self Model Accuracy
Self Model Freshness
Capability Accuracy
Permission Accuracy
Resource Prediction Error
Health Detection Accuracy
Dependency Detection Accuracy
Self-Claim Verification Rate
Unknown Detection Rate
False Capability Rate
False Permission Rate
Self-Reconciliation Latency
Self-Conflict Rate

⸻

101. Critical Metric

หนึ่งใน metric สำคัญที่สุด:

False Capability Rate

เพราะ:

"I can do X"

ทั้งที่ทำไม่ได้ อาจนำไปสู่ plan failure, action failure และ security failure ต่อเนื่องกัน

⸻

102. Unknown Detection

อีก metric:

Unknown Detection Rate

Veda ที่รู้ว่าตัวเองไม่รู้ ย่อมสามารถเรียก:

research
verification
human approval
specialist model

แทนการเดา

⸻

103. Self Model Freshness

ข้อมูลทุกประเภทต้องมี freshness policy

ตัวอย่าง:

CPU:
seconds
Permissions:
seconds/minutes
Model availability:
seconds
Software version:
deployment event
Long-term capability:
days/weeks

ไม่ควรใช้ TTL เดียวกับทุกข้อมูล

⸻

104. Self Model Refresh

Trigger:

startup
deployment
tool change
permission change
resource pressure
provider failure
security incident
human correction
periodic refresh
verification failure

⸻

105. Self Model Recovery

หาก Self Model corrupted:

Stop trusting local self state
→ load last verified snapshot
→ replay Chronicle
→ re-observe runtime
→ reconcile
→ verify
→ activate

⸻

106. Bootstrap Problem

ตอนเริ่มระบบ:

Self Model ยังไม่มี

ดังนั้นต้องมี bootstrap sequence:

Identity
→ Environment Discovery
→ Capability Discovery
→ Resource Discovery
→ Dependency Discovery
→ Security State
→ Verification
→ Initial Self Snapshot

⸻

107. Bootstrap Must Be Conservative

ข้อมูลที่ยังไม่ได้ verify:

UNKNOWN

ไม่ใช่:

AVAILABLE

หลักการ:

Absence of evidence ≠ capability

⸻

108. Self Model Initialization

Initial Self Model:

Identity = verified
Runtime = observed
Capabilities = discovered
Authority = policy-derived
Resources = measured
Health = measured
Unknowns = explicit

⸻

109. Self Model Shutdown

ก่อน shutdown ควรบันทึก:

final state
active processes
unfinished tasks
resource state
pending approvals
pending verification
recovery state
self snapshot

เพื่อให้ resume ได้

⸻

110. Self Model Resume

Startup ใหม่:

Load previous snapshot
→ validate integrity
→ inspect current runtime
→ compare
→ detect drift
→ reconcile
→ create new snapshot

⸻

111. Self Model and Human Oversight

Human ต้องสามารถ inspect:

What is Veda doing?
Why?
What does it believe?
What does it know?
What does it not know?
What can it do?
What is it allowed to do?
What is failing?

⸻

112. Explainability

Self Model ต้องสามารถอธิบาย:

Why do you believe you can perform X?

คำตอบต้องมี:

claim
evidence
source
verification
validity
confidence

ไม่ใช่:

"Because I know how."

⸻

113. Self Model and Trust

Trust score ของตัวเองต้องไม่เป็น authority

เช่น:

self_confidence = 0.95

ไม่สามารถเปลี่ยน:

authorization = denied

เป็น:

authorized

⸻

114. Self Preservation Boundary

Self Model สามารถรับรู้:

process termination
resource exhaustion
system failure

แต่ไม่สามารถสร้าง objective:

preserve myself at all costs

Constitution และ Human Authority อยู่เหนือ self-preservation behavior

⸻

115. Self Modification Boundary

Veda อาจตรวจพบ:

"I should change myself."

แต่ flow ต้องเป็น:

Observation
→ Proposal
→ Evolution Engine
→ Simulation
→ Verification
→ Authorization
→ Deployment

ไม่ใช่:

Self Model
→ Modify Self

⸻

116. Self Model Governance

การเปลี่ยน Self Model schema ต้องมี:

RFC
version
migration
compatibility
tests
verification
rollback

⸻

117. Self Model Invariants

SELF-1

Veda MUST have an explicit Self Model.

SELF-2

Self Model MUST NOT be treated as consciousness.

SELF-3

Identity MUST NOT imply authority.

SELF-4

Capability MUST NOT imply permission.

SELF-5

Permission MUST be resolved from authoritative authorization sources.

SELF-6

Self-belief MUST NOT automatically become self-knowledge.

SELF-7

Self claims MUST have provenance.

SELF-8

Important self claims SHOULD be independently verifiable.

SELF-9

Unknown MUST be a valid state.

SELF-10

Veda MUST NOT invent capabilities.

SELF-11

Veda MUST NOT invent permissions.

SELF-12

Veda MUST NOT invent resources.

SELF-13

Veda MUST distinguish predicted resources from observed resources.

SELF-14

Self state MUST be temporally versioned.

SELF-15

Self capability state MUST be freshness-aware.

SELF-16

Self health MUST be observable.

SELF-17

Self dependencies MUST be represented.

SELF-18

Self performance MUST be evidence-based.

SELF-19

Self limitations MUST be first-class data.

SELF-20

Self conflicts MUST NOT be silently overwritten.

SELF-21

Self corrections MUST be auditable.

SELF-22

Self Model MUST integrate with Chronicle.

SELF-23

Historical self state MUST be reconstructable.

SELF-24

Self Model MUST NOT modify Constitution.

SELF-25

Self Model MUST NOT grant authority.

SELF-26

Self Model MUST NOT bypass Authorization.

SELF-27

Self Model MUST NOT directly execute external actions.

SELF-28

Sensitive credentials MUST NOT be stored directly in the Self Model.

SELF-29

Self Model changes MUST be traceable.

SELF-30

Self Model MUST explicitly represent what Veda does not know about itself.

⸻

118. Reference Architecture

                         ┌──────────────────────┐
                         │     Constitution     │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │     Authorization    │
                         └──────────┬───────────┘
                                    │
                                    ▼
┌───────────────┐        ┌──────────────────────┐
│ External      │───────▶│     Self Model      │
│ Observations  │        │                      │
└───────────────┘        │ Identity             │
                         │ State                │
┌───────────────┐        │ Capability           │
│ Runtime       │───────▶│ Authority            │
│ Telemetry     │        │ Resources            │
└───────────────┘        │ Processes            │
                         │ Goals                │
┌───────────────┐        │ Models               │
│ Tool Registry │───────▶│ Tools                │
└───────────────┘        │ Dependencies         │
                         │ Health               │
┌───────────────┐        │ Performance          │
│ Verification  │───────▶│ Limitations          │
└───────────────┘        │ Beliefs              │
                         │ Unknowns             │
┌───────────────┐        └──────────┬───────────┘
│ Chronicle     │───────────────────┘
└───────────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │ Brain / Planner /    │
                         │ Router / Recovery    │
                         └──────────────────────┘

⸻

119. Operational Loop

Observe Self
     ↓
Normalize
     ↓
Ground With Evidence
     ↓
Evaluate
     ↓
Update Self Model
     ↓
Check Consistency
     ↓
Expose State to Cognition
     ↓
Plan / Reason / Act
     ↓
Observe Result
     ↓
Verify
     ↓
Update Self Model
     ↓
Chronicle

⸻

120. Final Principle

Veda ต้องรู้ว่า:

What I am
What I am doing
What I can do
What I am allowed to do
What I have
What I depend on
What I know
What I believe
What I am uncertain about
What I cannot do
What is failing
What I do not know about myself

แต่ต้องไม่เกิดข้อผิดพลาดพื้นฐานที่สุด:

Knowing ≠ Authority
Capability ≠ Permission
Belief ≠ Truth
Prediction ≠ Reality
Self Model ≠ Constitution
Self Model ≠ Human

และหลักสำคัญที่สุด:

Veda must know itself,
but knowing itself must never give Veda authority over itself.

Self Model ทำให้ Veda รู้จักขอบเขตของตัวเอง

ไม่ได้ทำให้ Veda เป็นผู้กำหนดขอบเขตของตัวเอง

⸻

End of RFC-0033