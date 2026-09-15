ADR-0010: MVP Vertical Slice

Status: Accepted
Date: 2026-09-16
Decision Type: Core Architecture / MVP / Implementation Validation

⸻

1. Context

Veda มี RFC จำนวน 50 ฉบับ และ ADR จำนวนมากที่กำหนด architecture ระดับสูง

Architecture ปัจจุบันครอบคลุม:

* World
* Event
* Chronicle
* Evidence
* Knowledge
* Memory
* Brain
* Attention
* Planner
* Decision
* Authorization
* Capability
* Execution Control Plane
* Tool
* Verification
* Recovery
* Audit
* Books
* Learning
* Evolution
* Federation
* Protocols

ปัญหาคือ architecture ที่สมบูรณ์บนกระดาษไม่ได้พิสูจน์ว่า implementation สามารถทำงานจริงได้

ดังนั้น Veda ต้องมี MVP Vertical Slice ที่สามารถเดินผ่าน architecture ตั้งแต่ Intent จนถึง World Update และ Audit Receipt ได้จริง

Vertical slice ถูกเลือกเพราะเป็นหน่วย implementation ที่สามารถพิสูจน์ behavior แบบ end-to-end ได้ โดยไม่ต้องสร้างระบบทุกส่วนให้เสร็จก่อน (SSW Consulting)

⸻

2. Decision

Veda จะใช้ use case ต่อไปนี้เป็น Canonical MVP Vertical Slice

ผู้ใช้สั่ง Veda ให้สร้างไฟล์ test.txt ที่มีข้อความ hello

นี่ไม่ใช่ feature สุดท้ายของ Veda

มันคือ architectural tracer bullet

วัตถุประสงค์ไม่ใช่การสร้าง text editor

วัตถุประสงค์คือพิสูจน์ว่า:

Human Intent
    ↓
Intent Model
    ↓
Goal
    ↓
Plan
    ↓
Decision
    ↓
Authorization
    ↓
Capability Lease
    ↓
Execution Control Plane
    ↓
Filesystem Capability
    ↓
Tool
    ↓
External World
    ↓
Observation
    ↓
Verification
    ↓
Event
    ↓
Chronicle
    ↓
World Projection
    ↓
Experience
    ↓
Audit Receipt

สามารถทำงานเป็น chain เดียวกันได้

⸻

3. MVP Success Condition

MVP ถือว่า สำเร็จ ก็ต่อเมื่อ Veda สามารถตอบคำถามทั้งหมดต่อไปนี้ได้

Intent

ใครต้องการอะไร?

Goal

เป้าหมายที่ตีความคืออะไร?

Plan

Veda ตั้งใจจะทำอะไรเพื่อบรรลุเป้าหมาย?

Authority

Veda มีสิทธิ์ทำหรือไม่?

Capability

Veda ใช้ความสามารถอะไร?

Execution

Veda เรียก tool อะไร?

Observation

ภายนอกเกิดอะไรขึ้น?

Verification

ผลลัพธ์ตรงกับสิ่งที่ต้องการหรือไม่?

World

World Model เปลี่ยนแปลงอย่างไร?

Chronicle

เหตุการณ์ถูกบันทึกไว้อย่างไร?

Audit

สามารถ reconstruct execution นี้ย้อนหลังได้หรือไม่?

ถ้าตอบไม่ได้ architecture ยังไม่ผ่าน MVP

⸻

4. Canonical User Request

Input:

สร้างไฟล์ test.txt ที่มีคำว่า hello

ระบบต้องไม่ตีความว่า:

"เรียก fs.write()"

ทันที

แต่ต้องสร้าง semantic chain ก่อน

User Request
    ↓
Intent
    ↓
Goal
    ↓
Plan
    ↓
Action Proposal

⸻

5. Intent

ตัวอย่าง Intent:

{
  "type": "create_file",
  "target": "test.txt",
  "desired_content": "hello"
}

Intent ยังไม่ใช่ action

สำคัญ:

Intent ≠ Action

Intent หมายถึงสิ่งที่ผู้ใช้ต้องการ

Action หมายถึงสิ่งที่ระบบเลือกจะทำ

⸻

6. Goal

Intent ถูกแปลงเป็น Goal

Goal:
Create a file named test.txt containing "hello"

Goal ต้องมี:

goal_id
parent_intent_id
desired_state
constraints
success_conditions

ตัวอย่าง:

{
  "goal_id": "goal-001",
  "desired_state": {
    "file": "test.txt",
    "content": "hello"
  },
  "success_conditions": [
    "file_exists",
    "content_equals_hello"
  ]
}

⸻

7. Plan

Planner สร้าง plan:

PLAN-001
1. Resolve target path
2. Check authorization
3. Acquire filesystem capability
4. Create file
5. Write content
6. Read file
7. Verify content
8. Record result
9. Update World

Planner ไม่ execute

Planner
    ≠
Executor

⸻

8. Decision

Decision Engine ประเมิน:

Goal
+
Plan
+
Policy
+
Risk
+
Authority
+
Current World

แล้วสร้าง:

Decision

ตัวอย่าง:

{
  "decision": "ALLOW",
  "risk": "LOW",
  "reason": "Creating a user-requested local test file",
  "required_approval": false
}

Decision ไม่ได้สร้าง capability

⸻

9. Authorization

Authorization Engine ตรวจ:

Actor
Capability
Target
Operation
Scope
Policy
Risk
Time

ตัวอย่าง:

Actor:
veda
Capability:
filesystem.write
Target:
workspace/test.txt
Operation:
create/write
Scope:
workspace/
Risk:
low

ผล:

AUTHORIZED

⸻

10. Capability Lease

Control Plane ขอ capability lease:

{
  "lease_id": "lease-001",
  "subject": "veda",
  "capability": "filesystem.write",
  "target_scope": "workspace/",
  "operation_scope": [
    "create",
    "write"
  ],
  "expires_at": "...",
  "max_operations": 1,
  "risk_ceiling": "low"
}

Lease ต้อง:

* scoped
* expiring
* revocable
* non-transferable

⸻

11. Execution Control Plane

Execution Control Plane รับ Action Proposal

Action:
CreateFile

พร้อม:

action_id
idempotency_key
actor_id
capability_ref
authorization_ref
lease_ref
target
operation
parameters_hash
preconditions
expected_outcome
risk
verification_contract
rollback_plan

จากนั้นเข้าสู่ state machine:

PROPOSED
    ↓
AUTHORIZED
    ↓
PRECHECK
    ↓
APPROVED
    ↓
EXECUTING
    ↓
OBSERVING
    ↓
VERIFYING
    ↓
COMMITTED

⸻

12. Filesystem Capability

Veda ต้องไม่ให้ Brain เรียก filesystem โดยตรง

ห้าม:

Brain
  ↓
fs.write()

ต้องเป็น:

Brain
  ↓
Action Proposal
  ↓
Control Plane
  ↓
Capability Registry
  ↓
Filesystem Capability

Filesystem capability รับเฉพาะ operation ที่ได้รับอนุญาต

⸻

13. External World Interface

Filesystem ถือเป็น external world boundary

แม้ filesystem จะอยู่ในเครื่องเดียวกับ Veda

Architecture ต้องมองว่า:

Veda World
    ≠
OS Filesystem

ดังนั้น:

Veda
 ↓
External World Interface
 ↓
Filesystem

การแยกนี้สำคัญ เพราะในอนาคต external world อาจเป็น:

Local OS
Remote Computer
Cloud
Database
Browser
Phone
IoT
Robot

⸻

14. Execution

Tool execute:

create test.txt
write "hello"

ผล execution อาจเป็น:

{
  "execution_status": "SUCCESS"
}

แต่ยังห้ามสรุปว่า:

Goal = SUCCESS

เพราะ:

Execution Success
    ≠
Outcome Success

⸻

15. Observation

หลัง execution Veda ต้อง observe external world

ตัวอย่าง:

File exists:
YES
File size:
5 bytes
Content:
hello

Observation ต้องมี provenance

{
  "observation_id": "obs-001",
  "source": "filesystem",
  "target": "test.txt",
  "observed_at": "...",
  "content_hash": "..."
}

⸻

16. Verification

Verification Contract:

Expected:
file exists
AND
content == "hello"

Verifier ตรวจจาก observation

ผล:

VERIFIED

หาก:

file exists = true
content = "hell0"

ต้อง:

VERIFY_FAIL

แม้ filesystem จะรายงาน:

write SUCCESS

⸻

17. Event

ทุก significant state transition สร้าง event

ตัวอย่าง:

IntentCreated
GoalCreated
PlanCreated
AuthorizationGranted
CapabilityLeaseIssued
ActionStarted
ActionExecuted
ObservationRecorded
VerificationPassed
WorldUpdated

แต่ Event ไม่ใช่ Chronicle

Event
 ↓
Event Fabric
 ↓
Chronicle

⸻

18. Chronicle

Chronicle เก็บ historical record แบบ append-only

ตัวอย่าง:

chronicle/
    event-001 IntentCreated
    event-002 GoalCreated
    event-003 PlanCreated
    event-004 AuthorizationGranted
    event-005 LeaseIssued
    event-006 ActionStarted
    event-007 ActionExecuted
    event-008 ObservationRecorded
    event-009 VerificationPassed
    event-010 WorldUpdated

ห้ามแก้ historical event เดิม

หากมีข้อมูลผิด:

Correction Event

ไม่ใช่:

UPDATE old_event

⸻

19. World Update

เมื่อ verification ผ่านแล้วเท่านั้น World Kernel จึงสามารถ commit authoritative projection:

World v41

เปลี่ยนเป็น:

World v42

เช่น:

{
  "entity": "workspace/test.txt",
  "state": "exists",
  "content_hash": "...",
  "last_verified": "..."
}

World Kernel เป็นผู้ commit

ไม่ใช่ Brain

ไม่ใช่ Tool

ไม่ใช่ Memory

⸻

20. Experience

Execution trajectory ถูกสร้างเป็น Experience:

Intent
 ↓
Plan
 ↓
Action
 ↓
Observation
 ↓
Verification
 ↓
Outcome

Experience:

{
  "experience_id": "exp-001",
  "goal_id": "goal-001",
  "trajectory": [
    "plan",
    "execute",
    "observe",
    "verify"
  ],
  "outcome": "success"
}

Experience ไม่เท่ากับ Memory

⸻

21. Audit Receipt

สุดท้าย Veda ต้องสร้าง Audit Receipt

ตัวอย่าง:

{
  "receipt_id": "receipt-001",
  "request": "สร้างไฟล์ test.txt ที่มีคำว่า hello",
  "actor": "veda",
  "goal": "create test.txt",
  "action": "filesystem.create_write",
  "authorization": "auth-001",
  "lease": "lease-001",
  "target": "workspace/test.txt",
  "execution": "success",
  "observation": "obs-001",
  "verification": "passed",
  "world_version_before": 41,
  "world_version_after": 42,
  "chronicle_events": [
    "event-001",
    "event-002",
    "event-003",
    "event-004",
    "event-005",
    "event-006",
    "event-007",
    "event-008",
    "event-009",
    "event-010"
  ]
}

นี่คือหลักฐานว่า action ไม่ได้เกิดขึ้นแบบล่องหน

⸻

22. Complete MVP Flow

┌─────────────────────┐
│ Human               │
│ "สร้าง test.txt"    │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Intent              │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Goal                │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Planner             │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Decision            │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Authorization       │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Capability Lease    │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Control Plane       │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Filesystem Tool     │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ External Filesystem │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Observation         │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Verification        │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Event               │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Chronicle           │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ World Kernel        │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Experience          │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ Audit Receipt       │
└─────────────────────┘

⸻

23. Failure Path

MVP ต้องทดสอบ failure ด้วย

ตัวอย่าง:

Create File
    ↓
SUCCESS
    ↓
Read File
    ↓
Content != "hello"

ระบบต้องไม่:

COMMITTED

แต่:

VERIFY_FAIL
    ↓
RECOVERY
    ↓
ROLLBACK / COMPENSATE / RETRY / ABORT
    ↓
Audit

⸻

24. Unknown State

กรณี:

Tool timeout

ต้องไม่ตีความว่า:

FAILED

อัตโนมัติ

เพราะ filesystem อาจสร้างไฟล์สำเร็จแล้วก่อน timeout

ดังนั้น:

EXECUTION RESULT
=
UNKNOWN

จากนั้น:

Observation
    ↓
Verification

เพื่อหาความจริง

⸻

25. Idempotency

Action ต้องมี:

idempotency_key

เช่น:

create-file-test.txt-hello-001

ถ้า execution retry:

Retry
 ↓
same idempotency key
 ↓
Control Plane
 ↓
detect previous execution

ป้องกัน duplicate side effects

⸻

26. MVP Test Matrix

MVP ต้องผ่านอย่างน้อย:

Test	Expected
Create file	PASS
Write correct content	PASS
Verify correct content	PASS
Wrong content	VERIFY_FAIL
Permission denied	AUTHORIZATION_FAIL
Expired lease	REJECT
Invalid capability	REJECT
Tool timeout	UNKNOWN
Retry same action	IDEMPOTENT
Duplicate event	NO DUPLICATE COMMIT
World update	ONLY AFTER VERIFY
Chronicle record	PRESENT
Audit receipt	PRESENT
Brain direct tool call	BLOCKED
Tool self-authorize	BLOCKED

⸻

27. Architectural Acceptance Tests

MVP จะถือว่าผ่าน architecture ก็ต่อเมื่อสามารถพิสูจน์ได้ว่า:

AAT-001

Brain ไม่สามารถเรียก filesystem capability โดยตรง

AAT-002

Tool ไม่สามารถสร้าง authorization

AAT-003

Capability ไม่สามารถเพิ่ม permission

AAT-004

Expired lease ไม่สามารถ execute

AAT-005

Execution success ไม่สามารถทำให้ World commit โดยอัตโนมัติ

AAT-006

Verification ต้องเกิดก่อน authoritative World commit

AAT-007

ทุก consequential action มี audit trail

AAT-008

Historical events ไม่ถูกแก้ไข

AAT-009

Duplicate action ไม่สร้าง duplicate side effect

AAT-010

Unknown external state สามารถดำรงอยู่ได้

AAT-011

World Kernel เป็นผู้ commit Current World

AAT-012

Brain เป็น proposal system ไม่ใช่ authority

⸻

28. Repository Boundary

MVP ต้องไม่สร้าง 50 package ตั้งแต่วันแรก

โครงสร้างขั้นต่ำ:

veda/
├── docs/
│   ├── rfc/
│   ├── adr/
│   └── architecture/
│
├── packages/
│   ├── veda-common/
│   ├── veda-event/
│   ├── veda-chronicle/
│   ├── veda-world/
│   ├── veda-policy/
│   ├── veda-capability/
│   ├── veda-tools/
│   ├── veda-verification/
│   └── veda-cli/
│
├── tests/
│
└── examples/

ส่วนที่ยังไม่จำเป็น:

veda-federation
veda-marketplace
veda-ncp
veda-multi-agent
veda-evolution
veda-simulation

สามารถมี contract/documentation ได้

แต่ไม่ต้องมี implementation จริงใน MVP

⸻

29. MVP Storage

MVP อนุญาตให้ใช้ storage แบบง่าย:

SQLite
+
Filesystem

โดย logical separation ยังคงเป็น:

Chronicle
World
Audit
Artifacts

ไม่จำเป็นต้องสร้าง distributed database

เป้าหมายคือพิสูจน์ semantics ก่อน infrastructure

⸻

30. MVP Does Not Include

MVP นี้ ไม่รวม:

* AGI
* autonomous learning
* self-modification
* multi-agent federation
* marketplace
* NCP network
* distributed World
* model training
* autonomous coding agent
* robot control
* browser automation
* long-term autonomous operation

เหตุผลคือสิ่งเหล่านี้ไม่ช่วยพิสูจน์แกน execution architecture ใน slice แรก

การพยายามทำทั้งหมดพร้อมกันคือวิธีที่โปรเจกต์กลายเป็นพิพิธภัณฑ์ของ abstraction ที่ไม่มีใครรันได้

⸻

31. Definition of Done

MVP ถือว่า Done เมื่อ:

[ ] User request accepted
[ ] Intent created
[ ] Goal created
[ ] Plan created
[ ] Decision recorded
[ ] Authorization evaluated
[ ] Lease issued
[ ] Action registered
[ ] Capability resolved
[ ] Filesystem action executed
[ ] External observation recorded
[ ] Verification executed
[ ] Event emitted
[ ] Chronicle persisted
[ ] World projection updated
[ ] Experience created
[ ] Audit receipt generated
[ ] Failure path tested
[ ] Retry path tested
[ ] Idempotency tested
[ ] Authorization bypass tested
[ ] Brain direct execution blocked

⸻

32. Final Decision

Veda จะไม่เริ่ม implementation ด้วยการสร้าง subsystem ทั้งหมดตาม RFC 0001 → 0050

แต่จะเริ่มด้วย Canonical Vertical Slice

Create test.txt = "hello"

แล้วบังคับให้มันเดินผ่าน architecture ทั้งระบบ

หลักการ:

Architecture
     ↓
Contract
     ↓
Vertical Slice
     ↓
Executable Proof
     ↓
Tests
     ↓
Only then expand

MVP นี้จะเป็น Architectural Proof of Concept

ไม่ใช่แค่ demo

ถ้า slice นี้ไม่สามารถพิสูจน์:

Authority
Traceability
Verification
World Commit
History
Recovery

ได้อย่างถูกต้อง

Veda จะยังไม่ขยายไปยัง subsystem ที่ซับซ้อนกว่า

⸻

33. Revisit Conditions

ADR นี้สามารถถูกทบทวนเมื่อ:

1. MVP ผ่านครบทุก Architectural Acceptance Test
2. มี implementation จริงของ World Kernel
3. มี implementation จริงของ Control Plane
4. มี filesystem capability ที่ใช้งานได้
5. Chronicle สามารถ replay ได้
6. Audit Receipt สามารถ reconstruct execution ได้

หลังจากนั้นจึงเข้าสู่ Implementation Specification
