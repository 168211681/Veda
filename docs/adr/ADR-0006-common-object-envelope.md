---
id: ADR-0006
title: Common Object Envelope
status: Accepted
owner: Phupha
created: 2026-09-16
updated: 2026-09-16
review_cycle: Quarterly
architecture_stage: ADR_FREEZE

supersedes: null
superseded_by: null
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

Append this header and governance sections to `ADR-0006.md`.

ADR-0006: Common Object Envelope

* Status: Accepted
* Date: 2026-09-16
* Decision Type: Core Architecture / Data Contract
* Scope: All persistent, auditable, addressable Veda domain objects
* Supersedes: None
* Superseded by: None
* Related: ADR-0001, ADR-0002, ADR-0003, ADR-0004, ADR-0005

⸻

1. Context

Veda มี domain objects จำนวนมาก:

World
Entity
Relationship
Event
Observation
Action
Intent
Goal
Plan
Decision
Authorization
Capability
Lease
Evidence
Claim
Knowledge
Memory
Experience
Verification
World Transition
Agent
Tool
Execution
Scenario
Simulation
Learning
Evolution Proposal

Object เหล่านี้ต้องสามารถ:

* อ้างอิงกันได้
* trace กลับไปยังต้นทางได้
* อยู่ใน World เดียวกันได้
* มี temporal semantics
* มี provenance
* มี authorization context
* มี verification state
* รองรับ audit
* รองรับ versioning
* รองรับ replay
* รองรับ multi-agent
* รองรับ privacy/sensitivity

หากแต่ละ object ออกแบบ metadata เอง จะเกิดปัญหา:

Event.created_at
Action.timestamp
Evidence.collected_at
Memory.time
Knowledge.valid_from

และในที่สุดไม่มีวิธีที่เชื่อถือได้ในการตอบคำถามง่าย ๆ เช่น:

object นี้เกิดเมื่อไร, ถูกสร้างโดยใคร, อยู่ใน World ไหน, มาจากอะไร และเกี่ยวข้องกับ action ไหน?

ดังนั้น Veda ต้องมี Common Object Envelope เป็น metadata contract กลาง

⸻

2. Decision

Veda จะกำหนด Common Object Envelope เป็น metadata envelope มาตรฐานสำหรับ domain objects ที่ต้อง:

* identity
* addressing
* provenance
* temporal reasoning
* correlation
* authorization trace
* verification trace
* audit
* versioning
* privacy classification

Envelope จะถูกแยกออกจาก domain payload

Object
├── Envelope
└── Payload

โดย:

Envelope = Common Cross-Cutting Metadata
Payload = Domain-Specific Semantics

⸻

3. Canonical Structure

Conceptual structure:

CommonObject
├── envelope
│   ├── id
│   ├── type
│   ├── schema_version
│   ├── world_id
│   ├── actor_id
│   ├── created_at
│   ├── observed_at
│   ├── valid_from
│   ├── valid_to
│   ├── correlation_id
│   ├── causation_id
│   ├── parent_id
│   ├── provenance
│   ├── epistemic_status
│   ├── confidence
│   ├── sensitivity
│   ├── authorization_ref
│   ├── verification_ref
│   └── content_hash
│
└── payload
    └── domain-specific data

⸻

4. Envelope Is Not Domain Meaning

Envelope fields provide cross-cutting metadata.

ตัวอย่าง:

Action
├── Envelope
│   ├── id
│   ├── actor_id
│   ├── world_id
│   ├── created_at
│   └── ...
│
└── Payload
    ├── operation
    ├── target
    ├── parameters
    ├── preconditions
    └── expected_outcome

Envelope ไม่ควรใส่ domain-specific fields เช่น:

action.operation

ลงไปใน common envelope

เพราะจะทำให้ envelope กลายเป็น giant schema ที่รู้ทุกอย่าง

⸻

5. Identity

ทุก addressable persistent object ต้องมี:

id

id ต้อง:

* unique ภายใน identity domain ที่กำหนด
* stable ตลอด lifecycle
* ไม่ reuse
* ไม่เปลี่ยนเมื่อ payload ถูก version
* สามารถ reference จาก object อื่นได้

ตัวอย่าง:

event_id
action_id
evidence_id
knowledge_id
memory_id
experience_id

ไม่ควรใช้ semantic meaning เป็น identity

ตัวอย่างที่ไม่ควรทำ:

id = "delete-temp-files"

ควรเป็น opaque identifier:

id = "evt_..."

⸻

6. Object Type

ทุก object ต้องประกาศ:

type

ตัวอย่าง:

veda.event
veda.action
veda.evidence
veda.knowledge
veda.memory
veda.experience
veda.verification
veda.world_transition

Type ต้องเป็น:

* stable
* namespaced
* machine-readable
* version-independent

ตัวอย่าง:

veda.action

ไม่ควร encode version:

veda.action.v3

Version แยกอยู่ที่:

schema_version

⸻

7. Schema Version

ทุก object ที่ใช้ schema ต้องระบุ:

schema_version

ตัวอย่าง:

type:
veda.action
schema_version:
1.0

Schema version ใช้สำหรับ:

* parsing
* migration
* compatibility
* replay
* historical interpretation

Schema migration ต้องไม่ทำลายความสามารถในการอ่าน historical records

⸻

8. World Identity

Object ที่อยู่ภายใต้ Veda World ต้องสามารถระบุ:

world_id

เพื่อป้องกันการปะปนของ state ระหว่าง:

World A
World B
Simulation World
Federated World
Historical World

ตัวอย่าง:

world_id = world:veda-main

Simulation ต้องใช้ World identity ที่แยกจาก authoritative World

⸻

9. Actor Identity

Object ที่เกิดจาก agent/action/process ต้องสามารถระบุ:

actor_id

Actor อาจเป็น:

Human
Veda
Sub-agent
Tool
System Process
External Agent
Federated Agent

Actor identity ต้องไม่เท่ากับ authorization

Actor ≠ Authority

การรู้ว่าใครสร้าง object ไม่ได้หมายความว่าคนนั้นมีสิทธิ์สร้างมัน

⸻

10. Time Model

Envelope จะใช้ temporal semantics ที่สอดคล้องกับ RFC-0021

อย่างน้อย:

created_at
observed_at
valid_from
valid_to

ความหมายต้องแยกกัน

created_at

เวลาที่ Veda สร้าง object record

observed_at

เวลาที่ observation เกิดขึ้นใน external/internal reality

valid_from

เวลาที่ domain fact/state มีผล

valid_to

เวลาที่ domain fact/state สิ้นสุด

ดังนั้น:

created_at ≠ observed_at

เสมอไป

⸻

11. Temporal Example

External system อาจรายงาน:

Event happened:
10:00

แต่ Veda ได้รับข้อมูล:

10:05

ดังนั้น:

observed_at = 10:00
created_at  = 10:05

ห้ามใช้ ingestion time แทน event time โดยอัตโนมัติ

⸻

12. Correlation

Envelope รองรับ:

correlation_id

เพื่อเชื่อม object ที่อยู่ใน logical workflow เดียวกัน

ตัวอย่าง:

User Request
 ↓
Intent
 ↓
Goal
 ↓
Plan
 ↓
Action
 ↓
Execution
 ↓
Verification

ทั้งหมดอาจมี:

correlation_id = corr_123

ทำให้สามารถ reconstruct workflow ได้

⸻

13. Causation

Envelope รองรับ:

causation_id

เพื่อระบุ object ที่ก่อให้เกิด object ปัจจุบันโดยตรง

ตัวอย่าง:

Intent
   ↓
Action

Action:

causation_id = intent_id

และ:

Verification
   ↓
World Transition

World Transition:

causation_id = verification_id

Correlation และ causation ต้องไม่ถูกใช้แทนกัน

Correlation = same workflow/context
Causation = direct causal predecessor reference

⸻

14. Parent Relationship

Envelope รองรับ:

parent_id

สำหรับ hierarchical structures

ตัวอย่าง:

Goal
└── Milestone
    └── Task
        └── Action

Action อาจมี:

parent_id = task_id

Parent ไม่ได้แปลว่า causal predecessor

ดังนั้น:

parent_id ≠ causation_id

⸻

15. Provenance

ทุก object ที่มี epistemic significance ต้องสามารถ trace provenance

Conceptual structure:

provenance
├── source
├── source_type
├── collected_by
├── collected_at
├── transformation
├── derivation
├── parent_refs
└── integrity

ตัวอย่าง:

Knowledge
   ↓
derived from
   ↓
Claim
   ↓
supported by
   ↓
Evidence
   ↓
originated from
   ↓
External Source

Provenance ต้องไม่ถูกลดเหลือเพียง:

source_url

เพราะ source กับ derivation lineage เป็นคนละเรื่อง

⸻

16. Epistemic Status

Object ที่เกี่ยวข้องกับ knowledge/reasoning สามารถระบุ:

epistemic_status

ตัวอย่าง:

OBSERVED
SUPPORTED
VERIFIED
INFERRED
HYPOTHESIS
PREDICTED
SIMULATED
UNKNOWN
CONTRADICTED
DISPUTED

ตัวอย่าง:

Observed fact:
OBSERVED
Causal hypothesis:
HYPOTHESIS
Future scenario:
PREDICTED
Simulation result:
SIMULATED

ห้ามเปลี่ยน:

PREDICTED

เป็น:

OBSERVED

โดยไม่มี evidence ใหม่

⸻

17. Confidence

Object บางประเภทสามารถมี:

confidence

แต่ confidence ต้องมี semantics ชัดเจน

เช่น:

0.95

ต้องรู้ว่าเป็น:

* model confidence
* evidence confidence
* verification confidence
* classification confidence

ไม่ควรมีค่า confidence ตัวเดียวที่ทุก subsystem ตีความต่างกัน

ดังนั้น domain-specific confidence อาจอยู่ใน payload

ส่วน envelope confidence ใช้เฉพาะเมื่อมี standardized semantics

⸻

18. Sensitivity

ทุก object ที่อาจมีข้อมูลอ่อนไหวต้องมี:

sensitivity

ตัวอย่าง:

PUBLIC
INTERNAL
RESTRICTED
PRIVATE
SECRET

Sensitivity ไม่ใช่ authorization

Sensitivity = data classification
Authorization = who may access/use it

Object ที่เป็น SECRET ไม่ได้แปลว่า system จะเปิดให้ใครก็ตามที่มี identity เข้าถึงได้

⸻

19. Authorization Reference

Envelope สามารถมี:

authorization_ref

เพื่อ trace object กลับไปยัง authorization decision

ตัวอย่าง:

Action
 └── authorization_ref
       ↓
Authorization Decision

Reference นี้ไม่ใช่ authorization เอง

authorization_ref ≠ authorization

มันเป็น pointer สำหรับ traceability

⸻

20. Verification Reference

Envelope สามารถมี:

verification_ref

เพื่อระบุ verification record ที่เกี่ยวข้อง

ตัวอย่าง:

World Transition
 └── verification_ref
       ↓
Verification

แต่ reference ไม่ได้ทำให้ object verified โดยอัตโนมัติ

Verification status ต้องอยู่ใน Verification domain object

⸻

21. Content Hash

Object สามารถมี:

content_hash

เพื่อรองรับ integrity และ provenance

Hash ควรคำนวณจาก canonical representation

ตัวอย่าง:

canonical_payload
      ↓
canonical serialization
      ↓
hash
      ↓
content_hash

ห้ามรวม mutable transport metadata ใน hash หาก metadata เหล่านั้นสามารถเปลี่ยนระหว่าง transport ได้

⸻

22. Envelope vs Payload Hash

ต้องแยก:

payload_hash

จาก:

object_hash

หากระบบต้องการ cryptographic identity ระดับ object

เพราะ:

payload

และ:

envelope metadata

มี lifecycle ที่แตกต่างกัน

MVP สามารถเริ่มจาก:

content_hash = canonical(payload + immutable identity metadata)

และกำหนด canonicalization อย่างชัดเจนภายหลังใน implementation specification

⸻

23. Immutable vs Mutable Fields

Envelope fields ต้องถูกแบ่งเป็น:

Immutable

id
type
initial actor identity
initial causation
initial content identity
creation event identity

Append-only / Historical

provenance additions
verification references
audit references
lineage

Mutable projection metadata

บาง metadata อาจเปลี่ยนได้เฉพาะใน projection layer

เช่น:

current visibility projection
derived indexes
cache metadata

แต่ต้องไม่แก้ historical source object เงียบ ๆ

⸻

24. Envelope Does Not Mean Everything Is Mutable

การมี envelope ไม่ได้หมายความว่า object สามารถถูก update แบบ CRUD ได้ตามใจ

สำหรับ historical/auditable object:

Original Record
     ↓
Immutable

Correction:

New Record
     ↓
References Previous Record

ดังนั้น:

Correction ≠ Mutation of History

สอดคล้องกับ Event/Chronicle architecture ใน ADR-0002

⸻

25. Object Lifecycle

Object ที่ persistent และ auditable ต้องสามารถติดตาม lifecycle ตาม domain ของมัน

Envelope ไม่ควรกำหนด lifecycle เดียวให้ทุก object

ตัวอย่าง:

Action:
PROPOSED → AUTHORIZED → EXECUTING → VERIFIED
Evidence:
COLLECTED → VALIDATED → SUPERSEDED
Knowledge:
DRAFT → EVALUATED → ACTIVE → SUPERSEDED
Memory:
FORMED → CONSOLIDATED → FORGOTTEN

ดังนั้น:

Envelope ≠ Universal State Machine

Lifecycle state อยู่ใน domain payload

⸻

26. Reference Semantics

Object references ต้องระบุประเภท relationship อย่างชัดเจน

ตัวอย่าง:

causation_ref
parent_ref
supports_ref
derived_from_ref
verifies_ref
supersedes_ref
depends_on_ref

ไม่ควรใช้ field เดียว:

related_id

แล้วให้ developer เดาว่ามันเกี่ยวข้องกันแบบไหน

เพราะมนุษย์สร้าง database แล้วก็ชอบสร้าง related_id หนึ่งตัวเพื่อโยนความยุ่งยากให้ future-us

⸻

27. Cross-World References

หาก reference ข้าม World:

world_id
object_id

ต้องระบุทั้งคู่

ตัวอย่าง:

world://world-a/object/123

ห้ามสมมติว่า:

object_id = 123

มีความหมายเดียวทั่ว Federation

⸻

28. Simulation Objects

Simulation object ต้องระบุ World context ที่ชัดเจน:

world_id = simulation-world-123

และ:

epistemic_status = SIMULATED

Simulation object ห้ามถูกตีความเป็น authoritative World state โดยไม่มี explicit transition

⸻

29. Prediction Objects

Prediction:

epistemic_status = PREDICTED

ต้องสามารถระบุ:

prediction_time
target_time
model_ref
assumptions
scenario_ref

อย่างน้อยใน domain payload

Prediction ห้ามเขียนทับ observed state

⸻

30. Unknown

Envelope และ domain model ต้องรองรับ:

UNKNOWN

อย่างถูกต้อง

ตัวอย่าง:

verification_status = UNKNOWN

ไม่ควรถูกแปลงอัตโนมัติเป็น:

FAILED

หรือ:

SUCCESS

Unknown เป็น epistemic state ที่ถูกต้อง

⸻

31. Security Boundary

Common Envelope ห้ามทำหน้าที่เป็น authorization mechanism

ตัวอย่าง:

authorization_ref = auth_123

ไม่ได้หมายความว่า:

AUTHORIZED = true

ระบบต้อง resolve reference และตรวจ policy state จริง

เช่นเดียวกัน:

actor_id = admin

ไม่ได้หมายความว่า actor มี authority ทุกอย่าง

⸻

32. Privacy Boundary

Envelope metadata เองอาจมี sensitive information เช่น:

actor_id
world_id
provenance
authorization_ref

ดังนั้นการสร้าง envelope ไม่ควรทำให้ metadata หลุดผ่าน:

* logs
* telemetry
* model context
* external API
* federation
* package export

โดยอัตโนมัติ

Visibility ต้องถูกกำหนดตาม policy

⸻

33. Serialization

Common Envelope ต้องมี canonical serialization สำหรับ:

* persistence
* hashing
* signing
* transport
* replay
* comparison

MVP อาจใช้:

JSON

แต่ domain contract ต้องไม่ผูกติดกับ JSON โดยตรง

ในอนาคตสามารถมี:

JSON
CBOR
MessagePack
Protobuf

ได้ โดย semantics ต้องเท่าเดิม

⸻

34. Backward Compatibility

Schema evolution ต้องรองรับ:

old object
+
new reader

และตาม policy ที่กำหนด:

new object
+
compatible old reader

Migration ต้อง:

* explicit
* versioned
* testable
* reversible เมื่อทำได้
* preserve historical meaning

⸻

35. Common Envelope Does Not Become a God Object

Envelope ต้องมีเฉพาะ metadata ที่ cross-cutting จริง

ไม่ควรเพิ่ม field เพียงเพราะ:

"เผื่ออนาคต"

กฎ:

ถ้า field มีความหมายเฉพาะกับ domain หนึ่ง ให้เก็บใน domain payload

ตัวอย่าง:

Action:
operation
Evidence:
source
Knowledge:
claim
Memory:
retention_policy
Verification:
verification_method

ไม่ควรย้ายทั้งหมดมาไว้ใน envelope

⸻

36. Canonical Envelope

Conceptual schema:

Envelope {
    id
    type
    schema_version
    world_id
    actor_id
    created_at
    observed_at
    valid_from
    valid_to
    correlation_id
    causation_id
    parent_id
    provenance
    epistemic_status
    confidence
    sensitivity
    authorization_ref
    verification_ref
    content_hash
}

Domain object:

Object {
    envelope: Envelope
    payload: DomainPayload
}

⸻

37. Invariants

COE-001

Every addressable persistent domain object has a stable identity.

COE-002

Object type is explicit.

COE-003

Schema version is explicit.

COE-004

World identity is explicit where World-scoped.

COE-005

Actor identity is explicit where actor semantics apply.

COE-006

Creation time is distinct from observation time.

COE-007

Temporal validity is distinct from record creation.

COE-008

Correlation does not imply causation.

COE-009

Causation does not imply parent hierarchy.

COE-010

Provenance must not be silently discarded.

COE-011

Epistemic status must not be silently upgraded.

COE-012

Prediction must not become observation without evidence.

COE-013

Simulation must not become authoritative World state automatically.

COE-014

Unknown is a valid epistemic state.

COE-015

Authorization references do not themselves grant authorization.

COE-016

Verification references do not themselves prove verification.

COE-017

Sensitivity does not equal authorization.

COE-018

Domain semantics remain in payloads.

COE-019

Common Envelope must not become a universal domain object.

COE-020

Historical records cannot be silently rewritten.

COE-021

Cross-World references identify their World context.

COE-022

Schema evolution must preserve historical interpretation.

COE-023

Canonical serialization must be deterministic for integrity operations.

COE-024

Derived indexes are not the source of truth.

COE-025

Envelope metadata must obey privacy and visibility policy.

⸻

38. Alternatives Considered

Alternative A: Each Domain Defines Its Own Metadata

Event → own metadata
Action → own metadata
Memory → own metadata
Knowledge → own metadata

Advantages

* local simplicity
* flexible

Disadvantages

* inconsistent identity
* inconsistent time semantics
* difficult tracing
* difficult federation
* duplicated implementation
* poor auditability

Rejected.

⸻

Alternative B: One Giant Universal Object Schema

VedaObject {
    every_possible_field
}

Advantages

* one schema

Disadvantages

* massive coupling
* sparse meaningless fields
* domain contamination
* impossible clean evolution
* effectively a God Object

Rejected.

⸻

Alternative C: Common Envelope + Domain Payload

Envelope
+
Domain Payload

Advantages

* shared cross-cutting semantics
* domain independence
* consistent identity
* consistent temporal metadata
* traceability
* easier evolution
* supports federation

Disadvantages

* additional schema discipline
* canonicalization complexity
* migration requirements

Accepted.

⸻

39. Consequences

Positive

Veda ได้:

* common identity semantics
* common temporal semantics
* consistent provenance
* traceable workflows
* standardized audit references
* safer schema evolution
* easier federation
* easier replay
* consistent World scoping
* easier debugging

Object ต่าง ๆ จะสามารถเชื่อมกันได้โดยไม่ต้องรู้ implementation ภายในของกันและกัน

⸻

Negative

มี complexity เพิ่ม:

* envelope schema ต้องดูแล
* canonical serialization ต้องกำหนด
* schema migration ต้องทำ
* privacy policy ต้องครอบคลุม metadata
* developers ต้องแยก envelope กับ payload ให้ถูก

แต่ complexity นี้เป็น structural complexity ที่เราต้องมีอยู่แล้ว ถ้า Veda จะเป็นระบบที่ตรวจสอบย้อนหลังได้จริง

⸻

40. Implementation Guidance

MVP ควรมี package:

packages/
└── veda-common/
    ├── envelope/
    ├── ids/
    ├── time/
    ├── provenance/
    ├── references/
    ├── hashing/
    └── serialization/

ตัวอย่าง conceptual type:

CommonEnvelope
DomainObject<T>
ObjectRef
Provenance
TemporalMetadata
EpistemicStatus
Sensitivity

Domain packages import veda-common

ตัวอย่าง:

veda-action
    ↓
veda-common
veda-event
    ↓
veda-common
veda-evidence
    ↓
veda-common
veda-knowledge
    ↓
veda-common

แต่:

veda-common

ต้องไม่ import domain packages กลับ

เพื่อป้องกัน circular dependency

⸻

41. Dependency Rule

Dependency direction:

                    ┌──────────────┐
                    │ veda-common  │
                    └──────▲───────┘
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
      veda-event      veda-action      veda-memory
          │                │                │
          ▼                ▼                ▼
       Chronicle        Control          Brain

Common package เป็น foundation

แต่ไม่ใช่ owner ของ domain semantics

⸻

42. Testing Requirements

Common Envelope ต้องมี contract tests สำหรับ:

identity
schema compatibility
serialization
hash stability
time semantics
reference semantics
provenance preservation
privacy filtering
unknown handling
cross-world references

โดยเฉพาะ:

same semantic object
→ same canonical representation
→ same content hash

ภายใต้ canonicalization rules เดียวกัน

⸻

43. Dependencies

Depends on:

RFC-0003 Event Model
RFC-0004 State & World Transition
RFC-0012 Evidence Model
RFC-0013 Knowledge Model
RFC-0017 Memory Model
RFC-0021 Temporal Model
RFC-0031 Event/Audit/Trace Fabric
RFC-0032 Veda Chronicle
RFC-0039 Veda Identity
RFC-0043 World Delta Protocol
ADR-0001 World Kernel
ADR-0002 Event/Chronicle Boundary
ADR-0003 Knowledge/Memory/Experience Boundary
ADR-0005 Execution Control Plane

Provides foundational contract for:

World
Event
Action
Evidence
Knowledge
Memory
Experience
Verification
Agent
Capability
Lease
World Delta

⸻

44. Revisit Conditions

ADR นี้ควรถูกทบทวนหาก:

1. Common Envelope ทำให้ domain schemas เกิด coupling ที่ไม่สามารถยอมรับได้
2. Federation ต้องการ identity/temporal semantics ที่ incompatible
3. Cryptographic object identity ต้องใช้ content-addressed architecture แบบใหม่
4. Serialization model เปลี่ยนอย่าง fundamental
5. World Kernel ต้องรองรับ schema model ที่ Common Envelope ปัจจุบันไม่สามารถ represent ได้

การเปลี่ยน fundamental semantics ต้องสร้าง ADR ใหม่เพื่อ supersede ADR-0006

⸻

45. Final Decision

Veda จะใช้:

┌──────────────────────────────┐
│      Common Object Envelope  │
│                              │
│ identity                     │
│ type                         │
│ schema version               │
│ world                        │
│ actor                        │
│ time                         │
│ correlation                  │
│ causation                    │
│ parent                       │
│ provenance                   │
│ epistemic status             │
│ sensitivity                  │
│ authorization reference     │
│ verification reference      │
│ integrity                    │
└──────────────┬───────────────┘
               │
               ▼
       ┌─────────────────┐
       │ Domain Payload  │
       ├─────────────────┤
       │ Event           │
       │ Action          │
       │ Evidence       │
       │ Knowledge      │
       │ Memory         │
       │ Experience     │
       │ Verification   │
       │ ...            │
       └─────────────────┘

Canonical rule:

Cross-cutting metadata belongs in the Common Object Envelope. Domain semantics belong in the Domain Payload.

และ:

The Common Object Envelope provides interoperability and traceability, but never grants authority, establishes truth, or replaces domain semantics.

Status: ACCEPTED
