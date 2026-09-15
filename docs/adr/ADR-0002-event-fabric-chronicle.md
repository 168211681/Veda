ADR-0002: Event Fabric and Chronicle Separation

* Status: Accepted
* Date: 2026-09-16
* Decision Type: Core Architecture
* Scope: Event Fabric, Chronicle, World Kernel, Event Store, Audit, Replay
* Supersedes: None
* Superseded by: None
* Related: ADR-0001 World Kernel Ownership

⸻

1. Context

Veda จำเป็นต้องมีระบบ Event เพื่อบันทึกและส่งต่อสิ่งที่เกิดขึ้นภายในระบบและสิ่งที่สังเกตได้จากโลกภายนอก

แต่คำว่า “Event System” ในสถาปัตยกรรมสามารถหมายถึงหลายหน้าที่ที่แตกต่างกัน:

1. การสร้าง Event
2. การส่ง Event
3. การ route Event
4. การ correlate Event
5. การ persist Event
6. การ replay Event
7. การสร้าง World State จาก Event
8. การ audit
9. การ forensic reconstruction

หากรวมทั้งหมดไว้ใน component เดียว จะเกิด coupling สูงและเกิดความสับสนว่า:

Event ที่กำลังถูกส่ง กับ Event ที่เป็นประวัติศาสตร์ถาวร เป็นสิ่งเดียวกันหรือไม่?

และที่สำคัญ:

Chronicle เป็นเจ้าของ World State หรือไม่?

คำตอบต้องเป็น ไม่

ADR-0001 กำหนดแล้วว่า World Kernel เป็นผู้ commit authoritative Current World State ดังนั้น ADR นี้ต้องกำหนด boundary ของระบบ Event และ Historical Record ให้ชัดเจน

⸻

2. Decision

Veda จะแยกหน้าที่ออกเป็น 3 ชั้นหลัก:

┌──────────────────────────────────────────┐
│              EVENT FABRIC                │
│                                          │
│ Transport / Routing / Correlation        │
│ Delivery / Subscription / Streaming      │
└────────────────────┬─────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────┐
│               CHRONICLE                  │
│                                          │
│ Durable Historical Record                │
│ Append-only History                      │
│ Replay / Snapshot / Forensics             │
└────────────────────┬─────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────┐
│              WORLD KERNEL                │
│                                          │
│ Current Authoritative World State        │
│ Projection / Transition / Query           │
└──────────────────────────────────────────┘

โดยแต่ละ component มี authority แตกต่างกัน

Event Fabric

รับผิดชอบ:

* transport
* routing
* delivery
* subscriptions
* streaming
* correlation
* event fan-out
* backpressure
* transient delivery state

Chronicle

รับผิดชอบ:

* durable historical record
* append-only history
* event persistence
* replay source
* snapshots
* historical reconstruction
* forensic history
* integrity verification

World Kernel

รับผิดชอบ:

* current authoritative World State
* World projection
* state transition
* current version
* world queries
* conflict detection
* semantic state validation

⸻

3. Core Principle

Veda กำหนด:

Event Fabric transports events. Chronicle preserves history. World Kernel maintains authoritative current World state.

หรือ:

EVENT FABRIC
    = "How events move"
CHRONICLE
    = "What historically happened"
WORLD KERNEL
    = "What the current modeled world is"

ห้ามนำสามความหมายนี้มารวมกัน

⸻

4. Event Fabric

Event Fabric เป็น transport and coordination layer

มันไม่ได้เป็น historical authority

ตัวอย่างหน้าที่:

Producer
   ↓
Event
   ↓
Event Fabric
   ├── Subscriber A
   ├── Subscriber B
   ├── Chronicle
   ├── Audit Pipeline
   └── Monitoring

Event Fabric สามารถทำ:

* publish
* subscribe
* route
* filter
* correlate
* retry delivery
* dead-letter
* stream
* fan-out

แต่ Event Fabric ไม่ควรเป็น source of truth สำหรับ historical state

⸻

5. Chronicle

Chronicle เป็น durable semantic history

Chronicle ต้องรักษาประวัติที่ authoritative ตาม retention policy ของ Veda

ตัวอย่าง:

Event E100
Event E101
Event E102
Event E103
...

Chronicle ต้องรองรับ:

* append-only semantics
* event ordering metadata
* event identity
* causation
* correlation
* provenance
* integrity
* replay
* snapshot
* historical query
* forensic reconstruction

Chronicle ต้องไม่แก้ไข historical event แบบ silent mutation

⸻

6. Chronicle Is Not World State

Chronicle ไม่ใช่ Current World

ตัวอย่าง:

Chronicle
E100: FILE_CREATED
E101: FILE_MODIFIED
E102: FILE_DELETED

ไม่ได้หมายความว่า Chronicle เองคือ:

file.status = DELETED

Current state ต้องมาจาก World Projection:

Chronicle
    ↓
Replay / Projection
    ↓
World Kernel
    ↓
Current World

ดังนั้น:

History ≠ Current State

⸻

7. World Kernel Projection

World Kernel ใช้ canonical event history เพื่อสร้างหรือ update Current World

Conceptual flow:

Event
   ↓
Validation
   ↓
Projection
   ↓
World Transition
   ↓
World Version N+1

ตัวอย่าง:

E100 FILE_CREATED
    ↓
World v100
file.status = ACTIVE
E101 FILE_DELETED
    ↓
World v101
file.status = DELETED

Chronicle ยังคงเก็บ E100 และ E101

World Kernel แสดง Current State:

file.status = DELETED

⸻

8. Canonical Architecture

Veda ใช้ architecture:

                  ┌──────────────┐
                  │    Actor     │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │    Event     │
                  └──────┬───────┘
                         │
                         ▼
              ┌─────────────────────┐
              │    Event Fabric     │
              └──────┬───────┬──────┘
                     │       │
                     │       └──────────────┐
                     │                      │
                     ▼                      ▼
              ┌────────────┐         ┌────────────┐
              │  Chronicle │         │ Subscribers│
              └─────┬──────┘         └────────────┘
                    │
                    ▼
             ┌──────────────┐
             │ World Kernel │
             └──────┬───────┘
                    │
                    ▼
             ┌──────────────┐
             │ Current World│
             └──────────────┘

⸻

9. Event Identity

ทุก Event ต้องมี stable identity

ขั้นต่ำ:

event_id
event_type
schema_version
world_id
actor_id
created_at
observed_at
correlation_id
causation_id
parent_id
provenance
content_hash

Event ID ต้องสามารถใช้สำหรับ:

* deduplication
* idempotency
* tracing
* replay
* audit
* correlation
* forensic analysis

⸻

10. Event vs Observation

Event และ Observation ไม่ควรถูกถือว่าเป็นสิ่งเดียวกันเสมอไป

ตัวอย่าง:

External Reality
      ↓
Observation
      ↓
Evidence
      ↓
Event

หรือ:

Internal Action
      ↓
Execution Event

Event เป็น structured historical record ของสิ่งที่ระบบรับรู้หรือประมวลผลตาม event semantics

Observation เป็นข้อมูลเกี่ยวกับสิ่งที่ถูกสังเกต

ดังนั้น:

Observation ≠ Event

แต่ Observation สามารถก่อให้เกิด Event ได้

⸻

11. Event vs Evidence

Event ไม่ได้เป็น Truth โดยอัตโนมัติ

ตัวอย่าง:

Tool reports:
"delete succeeded"

สามารถสร้าง:

EVENT:
FILE_DELETE_EXECUTION_REPORTED

แต่ยังต้องมี:

OBSERVATION
    ↓
EVIDENCE
    ↓
VERIFICATION

ก่อนที่จะสรุป:

World:
file.status = DELETED

ดังนั้น:

Event ≠ Evidence
Event ≠ Verification
Event ≠ Truth

⸻

12. Event vs Audit

Event เป็นข้อมูลเกี่ยวกับสิ่งที่เกิดขึ้น

Audit เป็นการใช้ event/history เพื่อ answer:

* ใครทำ?
* ทำอะไร?
* เมื่อไร?
* ทำเพราะอะไร?
* ใช้ capability อะไร?
* ได้ authorization จากไหน?
* ผลคืออะไร?
* verify อย่างไร?
* World เปลี่ยนอย่างไร?

ดังนั้น Audit ไม่จำเป็นต้องเป็น event type เดียว

Audit สามารถเป็น projection/query ของ:

Events
+
Authorization
+
Actions
+
Observations
+
Evidence
+
Verification
+
World Transitions

⸻

13. Event Ordering

Veda ห้าม assume ว่า distributed events จะมาถึงตามลำดับที่เกิดจริง

ต้องแยก:

created_at
observed_at
ingested_at
processed_at
committed_at

และเมื่อจำเป็น:

sequence
logical_clock
causation_id
parent_id

Event arrival order ไม่เท่ากับ causal order

ตัวอย่าง:

E2 arrives first
E1 arrives later
แต่:
E1 caused E2

ระบบต้องสามารถแสดงความสัมพันธ์นี้ได้

⸻

14. Idempotency

Event processing ต้องรองรับ duplicate delivery

ตัวอย่าง:

E100
E100
E100

ต้องไม่กลายเป็น:

State mutation × 3

World Kernel ต้องมี idempotency mechanism

เช่น:

processed_event_id
transition_id
idempotency_key
world_version

⸻

15. Replay

Chronicle ต้องสามารถใช้ historical events เพื่อสร้าง World projection ใหม่ได้

ตัวอย่าง:

Chronicle
    ↓
Replay E1...E1000
    ↓
Projection
    ↓
World v1000

Replay ใช้สำหรับ:

* recovery
* debugging
* migration
* testing
* forensic analysis
* historical reconstruction
* new projection generation

⸻

16. Snapshot

Replay event จำนวนมหาศาลทุกครั้งไม่จำเป็น

Chronicle สามารถสร้าง snapshots:

E1 ... E1000
      ↓
Snapshot S1000
E1001 ... E1500

การ restore:

Snapshot S1000
     +
E1001...E1500
     ↓
World v1500

Snapshot ไม่ใช่ replacement ของ event history

มันเป็น optimization

⸻

17. Snapshot Authority

Snapshot ต้องไม่กลายเป็น historical source of truth แทน Chronicle

Event History
    = Canonical Historical Record
Snapshot
    = Derived Recovery/Performance Artifact

Snapshot สามารถถูกทิ้งแล้วสร้างใหม่ได้ หาก event history ยังสมบูรณ์

⸻

18. Event Immutability

เมื่อ Event ถูก committed เข้า Chronicle แล้ว:

EVENT = IMMUTABLE

หากข้อมูลผิด:

ห้าม:

แก้ Event เดิม

ให้สร้าง correction event:

E100 WRONG_STATE
      ↓
E101 CORRECTION

หรือ:

E100
E101 INVALIDATES E100

ตาม event semantics ที่กำหนด

เหตุผลคือ historical record ต้องสามารถตอบได้ว่า:

“ตอนนั้นระบบรู้อะไร และบันทึกอะไรไว้?”

ไม่ใช่:

“ตอนนี้เราอยากให้ประวัติเป็นอะไร?”

⸻

19. Correction Model

ข้อมูลผิดสามารถถูกแก้ใน Current World ได้โดยการสร้าง event ใหม่

ตัวอย่าง:

E100
FILE_SIZE = 100MB
E101
FILE_SIZE_CORRECTION
100MB → 120MB

Chronicle:

E100
E101

Current World:

FILE_SIZE = 120MB

Historical reconstruction ก่อน E101 ยังคงได้:

100MB

นี่คือเหตุผลที่ไม่ควรแก้ historical events แบบ silent mutation

⸻

20. Failure Handling

หาก Event Fabric ส่ง Event สำเร็จแต่ Chronicle persist ไม่สำเร็จ:

Transport Success
≠
Durable History Success

หาก Chronicle persist สำเร็จแต่ World Projection ล้มเหลว:

Historical Record = exists
Current Projection = temporarily stale

ระบบต้องสามารถ replay/rebuild projection ได้

ดังนั้น:

Chronicle failure
→ durability incident
Projection failure
→ rebuild/recovery
Event Fabric failure
→ delivery incident

แต่ละ failure มี semantics ต่างกัน

⸻

21. Event Fabric Failure

Event Fabric อาจ:

* timeout
* disconnect
* duplicate delivery
* reorder
* drop
* backpressure
* subscriber failure

ต้องมี:

retry
dead-letter
backpressure
idempotency
correlation
delivery status

แต่การ retry delivery ต้องไม่สร้าง duplicate semantic mutation

⸻

22. Chronicle Failure

Chronicle ต้องให้ความสำคัญกับ durability และ integrity

หาก Chronicle ไม่สามารถยืนยันการ persist:

Event cannot be considered durably recorded

สำหรับ operation ที่ต้องการ audit durability:

NO DURABLE RECORD
→ NO AUTHORITATIVE COMMIT

เว้นแต่ policy ของ operation ระบุ failure mode อื่นไว้อย่างชัดเจน

⸻

23. World Projection Failure

World Projection สามารถ rebuild จาก Chronicle

Chronicle
   ↓
Replay
   ↓
Projection
   ↓
World Kernel

ดังนั้น projection failure ไม่ควรทำให้ historical truth หายไป

⸻

24. Security Boundary

Event Fabric

ไม่มี authority ในการเปลี่ยน World

Chronicle

ไม่มี authority ในการตัดสินว่า event นั้น “จริง”

World Kernel

มี authority ในการ commit Current World State แต่ยังต้องปฏิบัติตาม Authorization และ Verification boundaries

ดังนั้น:

Event Fabric ≠ Authority
Chronicle ≠ Authority over Reality
World Kernel ≠ Human Authority

⸻

25. Multi-Agent Events

ทุก event จาก Agent ต้องระบุ:

actor_id
agent_id
authority_context
world_id
correlation_id
causation_id

Agent ไม่สามารถสร้าง event แล้วบังคับให้ World Kernel ยอมรับโดยอัตโนมัติ

Event submission:

Agent
 ↓
Event Proposal
 ↓
Validation
 ↓
Authorization / Verification
 ↓
Chronicle
 ↓
World Projection

⸻

26. Simulation Events

Simulation สามารถสร้าง event ได้ แต่ event ต้องระบุว่าอยู่ใน simulation world

ตัวอย่าง:

world_id = simulation://world-123

และต้องไม่ถูกนำไป commit เป็น:

world_id = reality://veda-main

โดยไม่มี explicit transition

Invariant:

SIMULATION EVENT
≠
REAL WORLD EVENT

⸻

27. World Delta Relationship

World Delta เป็น semantic representation ของการเปลี่ยนแปลง

ตัวอย่าง:

{
  entity: file:/test.txt,
  field: status,
  from: ACTIVE,
  to: DELETED
}

Event อาจบรรจุ World Delta:

Event
 ├── Metadata
 ├── Provenance
 ├── Causation
 └── Delta

แต่:

World Delta ≠ Event Transport

และ:

World Delta ≠ World State

⸻

28. Storage Boundary

Veda ควรแยก logical storage:

┌────────────────────────────┐
│ Event Fabric               │
│ transient transport        │
└─────────────┬──────────────┘
              ↓
┌────────────────────────────┐
│ Chronicle                  │
│ durable event history      │
└─────────────┬──────────────┘
              ↓
┌────────────────────────────┐
│ World Store                │
│ current projection         │
└────────────────────────────┘

Physical implementation สามารถใช้ database เดียวกันใน MVP ได้

แต่ logical ownership ต้องยังแยกกัน

นี่เป็นจุดสำคัญมาก:

Logical architecture ต้องไม่ถูกกำหนดโดยจำนวน database ที่เราใช้

MVP อาจใช้ SQLite/PostgreSQL ตัวเดียว แต่ต้องมี logical tables/schema/boundaries แยกตามหน้าที่

⸻

29. Consequences

Positive

Veda ได้:

* durable history
* replayability
* auditability
* clear ownership
* projection rebuild
* forensic reconstruction
* event-driven integration
* decoupled subscribers
* better failure isolation
* clearer security boundaries

และที่สำคัญ:

History
    ≠
Current State
    ≠
Transport

จะไม่ถูกยัดรวมกันจนแกะไม่ออกทีหลัง

⸻

Negative

ระบบจะซับซ้อนกว่า event bus ธรรมดา

ต้องดูแล:

* event schemas
* event versioning
* replay
* ordering
* idempotency
* snapshots
* retention
* projection rebuild
* storage integrity
* failure recovery

Event sourcing ยังทำให้ schema evolution เป็นเรื่องสำคัญมาก

ดังนั้นไม่ควรสร้างระบบ distributed event infrastructure ขนาดยักษ์ตั้งแต่ MVP

⸻

30. MVP Constraint

สำหรับ MVP:

ห้ามสร้าง Kafka-like infrastructure เพียงเพราะมันดูเหมือนระบบ AI ใหญ่

ใช้ implementation ที่เล็กที่สุดที่รักษา semantic boundaries ได้

ตัวอย่าง:

Application
   ↓
In-process Event Fabric
   ↓
SQLite/PostgreSQL Chronicle
   ↓
World Projection
   ↓
World Store

เมื่อ scale ถึงจุดที่ต้องแยก transport infrastructure ค่อยเปลี่ยน implementation

Architecture ต้อง stable

Infrastructure สามารถเปลี่ยนได้

⸻

31. Invariants

EF-001

Event Fabric is not the source of historical truth.

EF-002

Chronicle is the durable historical record.

EF-003

World Kernel owns authoritative Current World State.

EF-004

Chronicle does not directly mutate Current World State.

EF-005

Event arrival order is not assumed to equal causal order.

EF-006

Committed historical events are immutable.

EF-007

Corrections are represented as new events.

EF-008

Duplicate delivery must be idempotent.

EF-009

Simulation events cannot mutate the authoritative World.

EF-010

Snapshots are derived artifacts, not replacements for event history.

EF-011

Projection failure must be recoverable from Chronicle where retention permits.

EF-012

Transport success does not imply durable persistence.

EF-013

Durable persistence does not imply verified external outcome.

EF-014

An Event is not automatically Truth.

EF-015

World State must remain traceable to historical events.

⸻

32. Alternatives Considered

Alternative A: One Event Service Does Everything

Event Service
 ├── transport
 ├── storage
 ├── projection
 ├── audit
 └── world state

Rejected.

ทำให้ responsibility และ authority ปะปนกัน

⸻

Alternative B: Event Bus Only

Producer
 ↓
Event Bus
 ↓
Consumers

Rejected as complete architecture.

Event Bus แก้ transport แต่ไม่แก้ durable history, replay semantics และ authoritative World State

สามารถใช้เป็น implementation ของ Event Fabric ได้ในอนาคต

⸻

Alternative C: Database Only

Database
 └── Current State

Rejected as complete architecture.

ไม่มี historical semantic model ที่เพียงพอสำหรับ Veda

Database ยังสามารถเป็น storage implementation ได้

⸻

Alternative D: Event Fabric + Chronicle + World Kernel

Transport
   ↓
History
   ↓
Current World

Accepted.

เป็น boundary ที่ชัดที่สุดสำหรับ architecture ของ Veda

⸻

33. Dependencies

Depends on:

RFC-0001 Constitution
RFC-0003 Event Model
RFC-0004 State & World Transition
RFC-0002 World Model
ADR-0001 World Kernel Ownership

Constrains:

RFC-0026 Verification Engine
RFC-0027 Rollback & Recovery
RFC-0031 Event/Audit/Trace Fabric
RFC-0032 Veda Chronicle
RFC-0043 World Delta Protocol
RFC-0044 Multi-Agent World
RFC-0046 Federation Protocol

⸻

34. Implementation Requirements

Veda implementation ต้องมี logical interfaces แยกกัน:

EventFabric
Chronicle
WorldKernel
WorldProjection

ตัวอย่าง conceptual interfaces:

EventFabric.publish(event)
EventFabric.subscribe(filter, handler)
Chronicle.append(event)
Chronicle.read(event_id)
Chronicle.query(criteria)
Chronicle.replay(stream)
Chronicle.snapshot(version)
WorldKernel.query(query)
WorldKernel.propose_transition(transition)
WorldKernel.validate_transition(transition)
WorldKernel.commit_transition(transition)
WorldProjection.apply(event)
WorldProjection.rebuild(snapshot, events)

Implementation จริงสามารถรวม modules ใน process เดียวกันได้ใน MVP

แต่ interface boundary ต้องยังคงอยู่

⸻

35. Revisit Conditions

ADR นี้ควรถูกทบทวนหาก:

1. Veda เปลี่ยนจาก event-based architecture อย่างมีนัยสำคัญ
2. Distributed World State กลายเป็น requirement หลัก
3. Multiple authoritative World Kernels ต้องทำงานร่วมกัน
4. Chronicle ไม่สามารถตอบโจทย์ durability/performance ที่จำเป็นได้
5. จำเป็นต้องเปลี่ยน semantics ของ historical authority
6. Federation ต้องมี cross-world event authority ที่แตกต่างจาก local model

หากเปลี่ยนการตัดสินใจ ให้สร้าง ADR ใหม่เพื่อ supersede ADR-0002

ห้ามแก้ decision เดิมแบบเงียบ ๆ

⸻

36. Final Decision

Veda จะใช้ architecture:

┌────────────────────┐
│    Event Fabric    │
│                    │
│ Move / Route       │
│ Correlate / Stream │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│     Chronicle      │
│                    │
│ Preserve History   │
│ Replay / Snapshot  │
│ Forensics          │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│    World Kernel    │
│                    │
│ Current World      │
│ Projection         │
│ State Transition   │
│ Query              │
└────────────────────┘

หลักการบังคับ:

Event Fabric moves events. Chronicle preserves history. World Kernel commits current World State.

และ:

History must remain immutable enough to reconstruct what Veda knew and recorded at a given point in time.

Status: ACCEPTED
