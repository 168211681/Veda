---
id: ADR-0005
title: Execution Control Plane
status: Accepted
owner: Phupha
created: 2026-09-16
updated: 2026-09-16
review_cycle: Quarterly
architecture_stage: ADR_FREEZE
---

## Decision Drivers

| Driver | Priority |
|---|---|
| Architectural consistency | P0 |
| Security boundary | P0 |
| Auditability | P1 |

## Non-Goals

This ADR does not define implementation-specific code or package layout.

## Risks

| Risk | Mitigation |
|---|---|
| Future implementation drift | SPEC documents |
| Semantic ambiguity | ADR Governance |

# Patch Instructions

Append this header and governance sections to `ADR-0005.md`.

ADR-0005: Execution Control Plane

* Status: Accepted
* Date: 2026-09-16
* Decision Type: Core Architecture / Security / Execution
* Scope: Intent, Goal, Plan, Decision, Authorization, Capability, Lease, Tool, External World Interface, Verification, World Kernel
* Supersedes: None
* Superseded by: None
* Related: ADR-0001, ADR-0002, ADR-0003, ADR-0004

⸻

1. Context

Veda ต้องสามารถเปลี่ยนแปลงสิ่งต่าง ๆ ในโลกจริง เช่น:

* สร้าง/แก้/ลบไฟล์
* รันโปรแกรม
* แก้ source code
* commit Git
* เรียก API
* ส่งข้อความ
* ใช้บริการภายนอก
* ควบคุมอุปกรณ์
* เปลี่ยน configuration
* ทำงานแทนผู้ใช้

การให้ Brain เรียก Tool โดยตรงถูกปฏิเสธใน ADR-0004

ดังนั้นต้องมี layer กลางที่รับผิดชอบการเปลี่ยนจาก:

Intent

ไปเป็น:

Authorized External Action

พร้อมควบคุม:

* policy
* authorization
* capability
* scope
* lease
* execution
* verification
* recovery
* audit
* world transition

⸻

2. Decision

Veda จะใช้ Execution Control Plane เป็น mandatory control boundary สำหรับทุก consequential action

Canonical execution path:

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
Capability Lease
  ↓
Capability Registry
  ↓
External World Interface
  ↓
Tool / MCP
  ↓
External System
  ↓
Observation
  ↓
Evidence
  ↓
Verification
  ↓
Event
  ↓
Chronicle
  ↓
World Kernel
  ↓
World State

ไม่มี component อื่นสามารถ bypass Control Plane เพื่อสร้าง external side effect ที่อยู่นอก scope ของ policy

⸻

3. Core Principle

All consequential actions must pass through the Execution Control Plane.

หรือ:

No Control Plane
      ↓
No Consequential Execution

Control Plane ไม่ใช่ Brain

Control Plane ไม่ใช่ Tool

Control Plane ไม่ใช่ World Kernel

แต่เป็น boundary ที่ควบคุมการเปลี่ยนจาก:

Cognitive Proposal

ไปเป็น:

Authorized Execution

⸻

4. Why a Control Plane Is Required

หากไม่มี Control Plane จะเกิดเส้นทางจำนวนมาก:

Brain → Tool
Agent → Tool
Plugin → Tool
Script → Tool
Model → API
Memory → Tool
MCP → External System

แต่ละเส้นทางอาจใช้ authorization คนละแบบ

ผลคือ:

Security Boundary Fragmentation

และสุดท้ายจะไม่มีใครตอบได้ว่า:

“ใครอนุญาตให้ action นี้เกิดขึ้น?”

Control Plane จึงเป็น single architectural boundary สำหรับ consequential execution

⸻

5. Control Plane Responsibilities

Execution Control Plane รับผิดชอบ:

Intent Resolution
Goal Binding
Plan Validation
Decision Evaluation
Authorization
Capability Resolution
Lease Issuance
Precondition Checking
Execution Dispatch
Execution Monitoring
Verification Coordination
Failure Handling
Recovery Coordination
Audit Reference
World Transition Request

Control Plane ไม่รับผิดชอบ:

General Reasoning
Long-Term Memory
Knowledge Ownership
Model Training
Constitution Ownership
Current World State Ownership

⸻

6. Separation of Responsibilities

Brain
  → Thinks / Proposes
Planner
  → Plans
Decision Engine
  → Selects among permitted options
Authorization
  → Determines permission
Capability Registry
  → Describes available technical abilities
Capability Lease
  → Grants temporary scoped execution authority
External World Interface
  → Controls external boundary
Tool
  → Performs technical operation
Verification
  → Determines whether expected outcome occurred
Chronicle
  → Preserves history
World Kernel
  → Commits current modeled World

ไม่มี component ใดควรทำหน้าที่ทั้งหมด

⸻

7. Action Lifecycle

ทุก consequential Action ใช้ lifecycle:

PROPOSED
   ↓
AUTHORIZED
   ↓
PRECHECK
   ↓
SIMULATING
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

Failure path:

VERIFY_FAIL
   ↓
ROLLBACK
   ↓
DIAGNOSE
   ↓
RETRY / REPLAN / ABORT

และถ้าไม่สามารถรู้สถานะจริง:

UNKNOWN
   ↓
RECONCILE
   ↓
VERIFY

ห้าม assume success

⸻

8. Intent Is Not Authorization

Intent:

"ลบไฟล์ temp"

ไม่ได้หมายความว่า:

DELETE AUTHORIZED

Flow:

Intent
  ↓
Interpretation
  ↓
Goal
  ↓
Action Proposal
  ↓
Authorization

Intent เป็น semantic request

Authorization เป็น permission decision

ดังนั้น:

Intent ≠ Permission

⸻

9. Goal Binding

Action ต้องสามารถ trace กลับไปยัง Goal

ตัวอย่าง:

Goal:
Clean temporary files
Action:
Delete /tmp/a.log

ต้องสามารถตอบได้:

action_id
goal_id
intent_id
actor_id

ถ้า Action ไม่มี semantic relationship กับ Goal ที่ถูกต้อง:

REJECT

เว้นแต่เป็น system action ที่ policy อนุญาตโดยตรง

⸻

10. Plan Validation

Brain/Planner สามารถสร้าง Plan:

Plan
├── Step 1
├── Step 2
├── Step 3
└── Step 4

แต่ Plan ไม่ได้ authorize ตัวเอง

Control Plane ต้องตรวจ:

dependencies
resources
capabilities
risk
scope
preconditions
authorization

ก่อน execution

⸻

11. Decision Boundary

Decision Engine สามารถเลือก:

Action A
Action B
Action C

จาก:

* goals
* constraints
* values
* risk
* cost
* uncertainty
* expected outcome

แต่ Decision ไม่เท่ากับ Authorization

ตัวอย่าง:

Decision:
เลือก Action A
Authorization:
DENIED

ผลลัพธ์:

DO NOT EXECUTE

⸻

12. Authorization

Authorization ต้องประเมินอย่างน้อย:

actor
capability
target
operation
scope
context
policy
risk
goal
intent
time
lease

Conceptual function:

authorize(
    actor,
    capability,
    target,
    operation,
    context,
    policy,
    risk
)
→
ALLOW / DENY / ESCALATE

Authorization Engine ต้องไม่เชื่อ Brain เพียงเพราะ Brain บอกว่า:

"I need this."

⸻

13. Capability

Capability หมายถึง:

ระบบสามารถทำสิ่งนี้ได้หรือไม่?

ตัวอย่าง:

filesystem.read
filesystem.write
filesystem.delete
shell.execute
github.read
github.write
network.request
device.camera
device.microphone

Capability ไม่ได้หมายความว่า actor ใช้ได้ทุกเวลา

Capability ≠ Permission

⸻

14. Capability Registry

Registry เป็น source สำหรับ technical capability metadata

ตัวอย่าง:

Capability:
filesystem.delete
Metadata:
scope
risk
parameters
side_effects
reversibility
verification
required_authority
provider
version
health

Registry ไม่สามารถ grant permission เพียงเพราะ capability ถูก register

⸻

15. Capability Lease

ทุก action ที่เหมาะสมต้องใช้ temporary scoped authority:

Capability Lease

Lease ควรระบุ:

lease_id
subject
capability
scope
issued_at
expires_at
max_operations
risk_limit
target_limit
authorization_ref
revocation_ref

ตัวอย่าง:

Lease:
filesystem.delete
Scope:
/tmp/*.log
Expires:
5 minutes
Max Operations:
100

ไม่ใช่:

filesystem.delete
ALL_FILES
FOREVER

⸻

16. Least Authority

Control Plane ต้องใช้หลัก:

Minimum Capability
+
Minimum Scope
+
Minimum Duration
+
Minimum Operations

ตัวอย่าง:

ผู้ใช้ต้องการลบ:

/tmp/a.log

ไม่ควรออก lease:

filesystem.delete:/**

แม้ technically ทำงานได้

⸻

17. Capability Attenuation

เมื่อ authority ถูกส่งต่อ:

Human
 ↓
Veda
 ↓
Agent
 ↓
Tool

authority ต้องสามารถลด scope ได้ แต่ห้ามขยายเอง

ตัวอย่าง:

Parent:
filesystem.write:/project
Child:
filesystem.write:/project/src

ถูกต้อง

แต่:

Parent:
filesystem.write:/project/src
Child:
filesystem.write:/

ต้อง:

DENY

⸻

18. Execution Dispatch

เมื่อ Action ผ่าน authorization:

AUTHORIZED

Control Plane จะ resolve:

Capability
→ Provider
→ Tool
→ External World Interface

จากนั้นจึง execute

ห้าม Brain เลือก bypass route:

Brain → arbitrary shell

⸻

19. External World Interface

ทุก external side effect ต้องผ่าน:

External World Interface

ตัวอย่าง:

Veda
 ↓
External World Interface
 ↓
Filesystem

หรือ:

Veda
 ↓
External World Interface
 ↓
GitHub

หรือ:

Veda
 ↓
External World Interface
 ↓
Device

Interface นี้เป็น security and semantic boundary

⸻

20. MCP Position

MCP เป็น integration mechanism

ดังนั้น:

Control Plane
 ↓
Capability Registry
 ↓
External World Interface
 ↓
MCP
 ↓
MCP Server
 ↓
External System

ไม่ใช่:

Brain
 ↓
MCP
 ↓
External System

MCP tool ไม่สามารถ bypass:

* Authorization
* Capability Lease
* External World Interface
* Verification

⸻

21. Precondition Checking

ก่อน execution ต้องตรวจ:

target exists
scope valid
lease valid
resource available
state compatible
authorization active
dependencies satisfied

ตัวอย่าง:

Expected:
file exists

แต่ก่อน execute:

file already deleted

Action ต้อง:

ABORT / REPLAN

ไม่ใช่ blindly execute

⸻

22. Simulation Gate

สำหรับ risk level ที่กำหนด Control Plane สามารถบังคับ:

Action
 ↓
Simulation
 ↓
Risk Evaluation
 ↓
Authorization
 ↓
Execution

Simulation ไม่ได้ authorize action

Simulation เพียงให้ข้อมูลเกี่ยวกับ:

expected effects
risk
possible outcomes
resource impact

⸻

23. Human Approval Gate

Policy สามารถกำหนด:

REQUIRE_HUMAN_APPROVAL

ตัวอย่าง:

delete critical data
change security policy
send high-impact message
transfer money
modify authority
deploy production system

Flow:

Proposal
 ↓
Policy
 ↓
Human Approval Required
 ↓
Approval Event
 ↓
Capability Lease
 ↓
Execution

Approval ต้องเป็น auditable event

⸻

24. Approval Is Scoped

Human approval ไม่ควรกลายเป็น:

"Do anything."

Approval ต้องผูกกับ:

action
scope
target
parameters
risk
time
expiration

ตัวอย่าง:

Approve:
delete /tmp/a.log
Not:
delete filesystem

⸻

25. Stale Approval

Approval ต้องมี expiration/validity

ตัวอย่าง:

User approved action at 10:00

แต่ execution เกิด:

12:00

หาก policy กำหนด approval อายุ 10 นาที:

EXPIRED

ต้องขอ approval ใหม่

⸻

26. Parameter Integrity

Approval ต้องผูกกับ parameters ที่อนุมัติ

หาก user อนุมัติ:

delete /tmp/a.log

แต่ Brain เปลี่ยนเป็น:

delete /home/user/*

ต้อง:

REJECT

แม้ action type จะเหมือนกัน

⸻

27. Execution Result

Tool execution ต้องรายงาน:

execution_id
action_id
started_at
completed_at
status
provider
tool
raw_result_ref
error
side_effect_report

แต่:

execution_result

ยังไม่เท่ากับ:

verified_outcome

⸻

28. Observation After Execution

หลัง execution ต้องมี observation

ตัวอย่าง:

Tool:
"file deleted"

จากนั้น:

Filesystem observation:
file does not exist

จึงเข้าสู่ Verification

⸻

29. Verification

Verification ประเมิน:

Expected Outcome
vs
Observed State

ตัวอย่าง:

Expected:
file.status = DELETED
Observed:
filesystem.exists(file) = false
Verification:
PASS

ถ้า:

Expected:
file deleted
Observed:
file still exists

ผล:

VERIFY_FAIL

⸻

30. World Commit

เฉพาะ verified outcome ที่ผ่าน policy จึงเข้าสู่ World Kernel:

Verification
 ↓
Event
 ↓
Chronicle
 ↓
World Kernel
 ↓
World State

ตัวอย่าง:

file.status:
ACTIVE
→
DELETED

⸻

31. Unknown Outcome

กรณี:

Tool timeout

ไม่ได้หมายความว่า:

FAILED

หรือ:

SUCCESS

อาจเป็น:

UNKNOWN

ตัวอย่าง:

Request sent
Network timeout
Server state unknown

Control Plane ต้อง:

RECONCILE

ก่อน retry หาก duplicate side effect มีความเสี่ยง

⸻

32. Idempotency

ทุก mutation ที่สามารถ retry ได้ควรมี:

action_id
idempotency_key

ตัวอย่าง:

Action A
idempotency_key = X

Retry:

Action A'
idempotency_key = X

ระบบภายนอกหรือ Veda boundary ต้องพยายามป้องกัน duplicate effect ตาม capability contract

⸻

33. Rollback and Compensation

ไม่ทุก action rollback ได้

Action ต้องระบุ:

reversibility
rollback_method
compensation_method

ประเภท:

REVERSIBLE
COMPENSATABLE
IRREVERSIBLE
UNKNOWN

ตัวอย่าง:

Create file
→ delete file

อาจ reversible

แต่:

Send email

อาจไม่สามารถ rollback ได้

ดังนั้น Control Plane ต้องรู้:

What can be undone?
What can only be compensated?
What cannot be undone?

⸻

34. Partial Failure

Action อาจทำสำเร็จบางส่วน

ตัวอย่าง:

Delete 100 files

ผล:

70 deleted
30 failed

ห้ามรายงาน:

SUCCESS

แบบ binary หาก operation มี partial semantics

ต้องบันทึก:

completed
failed
unknown

ระดับ item หรือ sub-operation ตาม contract

⸻

35. Retry Policy

Retry ต้องไม่เกิดจาก:

"ลองอีกครั้งเผื่อได้"

ต้องมี policy:

retryable?
max_attempts
backoff
idempotency
risk
side_effects
deadline

ตัวอย่าง:

Network timeout
→ retry possible
Money transfer unknown
→ reconcile before retry

⸻

36. Recovery

Recovery path:

Execution
 ↓
Failure
 ↓
Diagnose
 ↓
Rollback / Compensate
 ↓
Reconcile
 ↓
Retry / Replan / Abort

Recovery เองเป็น Action ที่ต้องผ่าน Control Plane

ห้าม recovery code กลายเป็นช่อง bypass authorization

⸻

37. Audit Receipt

ทุก consequential action ต้องสร้าง receipt ที่สามารถตอบ:

who
what
why
when
where
which capability
which authorization
which lease
which tool
which external system
what result
what evidence
what verification
what World change

ตัวอย่าง:

ActionReceipt
├── action_id
├── intent_id
├── goal_id
├── plan_id
├── actor_id
├── authorization_ref
├── lease_ref
├── capability_ref
├── execution_ref
├── observation_refs
├── evidence_refs
├── verification_ref
├── event_ref
├── world_transition_ref
└── final_status

⸻

38. Auditability

Control Plane ต้องไม่เพียง log:

"action executed"

ต้องสามารถ reconstruct:

Intent
→ Decision
→ Authorization
→ Lease
→ Execution
→ Observation
→ Verification
→ World Commit

นี่คือ traceability

ไม่ใช่แค่ logging

⸻

39. Security Invariants

ECP-001

Every consequential action passes through the Control Plane.

ECP-002

Brain cannot bypass Control Plane.

ECP-003

Tools cannot grant themselves authority.

ECP-004

Capability does not imply permission.

ECP-005

Authorization must precede execution.

ECP-006

Capability Lease must be scoped.

ECP-007

Capability Lease must expire or be revocable.

ECP-008

Human approval must be scoped where required.

ECP-009

Approval parameters cannot be silently expanded.

ECP-010

Execution success is not verification success.

ECP-011

Verification must precede authoritative World commit where required.

ECP-012

Unknown external state must remain UNKNOWN until reconciled.

ECP-013

Retries must respect idempotency semantics.

ECP-014

Recovery cannot bypass authorization.

ECP-015

MCP cannot bypass Control Plane.

ECP-016

External World Interface cannot bypass authorization.

ECP-017

Every consequential action must be auditable.

ECP-018

Authority must be attenuated during delegation.

ECP-019

Expired approval cannot authorize execution.

ECP-020

Control Plane cannot grant itself authority beyond governing policy.

⸻

40. Alternatives Considered

Alternative A: Brain → Tool Directly

Brain
 ↓
Tool

Advantages

* extremely simple
* low latency
* easy prototype

Disadvantages

* no central authorization
* weak auditability
* prompt injection blast radius
* impossible consistent capability leases
* difficult recovery
* difficult human approval

Rejected.

⸻

Alternative B: Each Tool Implements Its Own Authorization

Brain
 ↓
Tool
 ↓
Tool-specific policy

Advantages

* distributed
* tool owners control security

Disadvantages

* inconsistent policy
* duplicated logic
* authorization drift
* difficult global auditing
* difficult capability attenuation

Rejected as primary architecture.

Tools may perform additional local checks, but Control Plane remains mandatory.

⸻

Alternative C: API Gateway as Control Plane

ใช้ generic API gateway เป็น authorization boundary

Rejected as complete semantic architecture.

API gateway can provide network-level controls but does not inherently understand:

* Intent
* Goal
* Capability
* Lease
* Verification
* World Transition
* Recovery semantics

สามารถใช้ API gateway เป็น implementation component ในบาง deployment ได้

แต่ไม่ใช่ semantic Control Plane

⸻

Alternative D: Dedicated Execution Control Plane

Brain
 ↓
Control Plane
 ↓
Authorization
 ↓
Lease
 ↓
External Interface
 ↓
Tool

Accepted.

⸻

41. Consequences

Positive

Veda ได้:

* centralized execution policy
* consistent authorization
* capability scoping
* temporary leases
* human approval
* verification gates
* recovery controls
* complete action trace
* safer tool integration
* safer MCP integration
* easier multi-agent security

และที่สำคัญ:

Brain intelligence

ไม่กลายเป็น:

System authority

⸻

Negative

มี latency และ complexity เพิ่มขึ้น

Action ที่เคย:

Brain → Tool

จะกลายเป็น:

Brain
→ Proposal
→ Authorization
→ Lease
→ Tool
→ Observation
→ Verification
→ Commit

ต้องมี:

* policy engine
* capability registry
* lease management
* execution tracking
* verification
* recovery
* audit

แต่สำหรับ Veda สิ่งเหล่านี้เป็น core architecture ไม่ใช่ optional enterprise decoration

⸻

42. MVP Implementation

MVP สามารถ implement Control Plane เป็น modular monolith:

veda-control/
├── decision/
├── authorization/
├── leases/
├── execution/
├── verification/
├── recovery/
└── audit/

ไม่จำเป็นต้องแยก microservices

ใช้:

single process
+
strict interfaces
+
transaction boundaries

ก่อน

เมื่อ scale จริงค่อยแยก process/service ตาม evidence

⸻

43. Minimum API

Conceptual API:

submit_action_proposal()
evaluate_authorization()
issue_capability_lease()
validate_preconditions()
simulate_action()
approve_action()
execute_action()
observe_execution()
verify_outcome()
commit_world_transition()
recover_action()
get_action_receipt()

แต่ไม่มี:

execute_without_authorization()

แม้แต่เป็น internal helper

เพราะ “internal” คือคำที่ software ใช้ก่อนกลายเป็น security vulnerability ในอีกหกเดือนถัดมา

⸻

44. Control Plane State Machine

PROPOSED
   │
   ├── DENIED ───────────────→ TERMINAL
   │
   ↓
AUTHORIZED
   │
   ├── EXPIRED ──────────────→ TERMINAL
   │
   ↓
PRECHECK
   │
   ├── FAILED ───────────────→ REPLAN
   │
   ↓
SIMULATING
   │
   ├── HIGH_RISK ────────────→ HUMAN_APPROVAL
   │
   ↓
APPROVED
   │
   ↓
EXECUTING
   │
   ├── ERROR ────────────────→ RECOVERY
   │
   ↓
OBSERVING
   │
   ↓
VERIFYING
   │
   ├── FAIL ─────────────────→ RECOVERY
   │
   ├── UNKNOWN ──────────────→ RECONCILIATION
   │
   ↓
COMMITTED

⸻

45. Dependencies

Depends on:

RFC-0001 Constitution
RFC-0005 Intent Model
RFC-0006 Goal Model
RFC-0008 Action Model
RFC-0009 Capability Model
RFC-0010 Authorization & Policy
RFC-0011 Capability Lease & Token
RFC-0020 Planner
RFC-0025 Value & Decision Engine
RFC-0026 Verification Engine
RFC-0027 Rollback & Recovery
RFC-0028 Tool & Capability Registry
RFC-0029 External World Interface
RFC-0030 MCP Integration
RFC-0031 Event/Audit/Trace Fabric
RFC-0032 Veda Chronicle
ADR-0001
ADR-0002
ADR-0004

Constrains:

RFC-0037 Evolution Engine
RFC-0040 Agent Passport
RFC-0041 Trust Engine
RFC-0044 Multi-Agent World
RFC-0046 Federation Protocol

⸻

46. Revisit Conditions

ADR นี้ควรถูกทบทวนหาก:

1. Veda พบวิธีที่สามารถรักษา authority invariants โดยไม่ต้องมี Control Plane boundary นี้
2. Execution model เปลี่ยนจาก centralized policy enforcement ไปเป็น formally verified distributed authority
3. Federation ต้องมี execution semantics ที่แตกต่างอย่างมีนัยสำคัญ
4. Performance evidence แสดงว่า Control Plane เป็น bottleneck ที่ไม่สามารถแก้ด้วย architecture ภายในเดิมได้
5. Security model ของ Veda เปลี่ยนอย่าง fundamental

การเปลี่ยน decision ต้องสร้าง ADR ใหม่เพื่อ supersede ADR-0005

⸻

47. Final Decision

Veda จะใช้:

                 ┌──────────────┐
                 │    Brain     │
                 │ Intelligence │
                 └──────┬───────┘
                        │
                        ▼
                 ┌──────────────┐
                 │   Proposal   │
                 └──────┬───────┘
                        │
                        ▼
        ┌───────────────────────────────┐
        │       EXECUTION CONTROL       │
        │                               │
        │ Decision                      │
        │ Authorization                 │
        │ Capability                    │
        │ Lease                         │
        │ Preconditions                 │
        │ Approval                      │
        │ Dispatch                      │
        │ Recovery                      │
        └───────────────┬───────────────┘
                        │
                        ▼
              ┌──────────────────┐
              │ External World   │
              └────────┬─────────┘
                       │
                       ▼
                 Observation
                       │
                       ▼
                 Verification
                       │
                       ▼
                  Chronicle
                       │
                       ▼
                 World Kernel
                       │
                       ▼
                  World State

Canonical rule:

No consequential action may move from cognition to external side effect without passing the Execution Control Plane.

และ:

Authorization is a separate architectural act from intelligence, capability, execution, verification, and World State commitment.

Status: ACCEPTED
